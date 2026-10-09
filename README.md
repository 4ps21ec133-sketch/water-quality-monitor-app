
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="theme-color" content="#071421">
<title>ESP32 BLE Connection Test</title>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 20px;
  background: #071421;
  color: #eaf4ff;
  font-family: Arial, sans-serif;
}

.container {
  max-width: 850px;
  margin: auto;
}

h1 {
  text-align: center;
  color: #50e3c2;
}

.subtitle {
  text-align: center;
  color: #a8bacd;
  margin-bottom: 25px;
}

.panel {
  background: #102336;
  border: 1px solid #294258;
  border-radius: 14px;
  padding: 18px;
  margin-bottom: 18px;
}

.status {
  padding: 12px;
  border-radius: 8px;
  background: #26364a;
  text-align: center;
  font-weight: bold;
  margin-bottom: 15px;
  overflow-wrap: anywhere;
}

.connected {
  color: #50e3c2;
}

.disconnected {
  color: #ff7777;
}

button {
  width: 100%;
  border: none;
  border-radius: 9px;
  padding: 14px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  margin: 5px 0;
}

#connectButton {
  background: #50e3c2;
  color: #071421;
}

#disconnectButton {
  background: #ff7777;
  color: #071421;
}

button:disabled {
  opacity: 0.45;
  cursor: not-allowed;
}

.grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 12px;
}

.item {
  background: #172e43;
  border-radius: 10px;
  padding: 14px;
  min-width: 0;
}

.label {
  font-size: 13px;
  color: #a8bacd;
  margin-bottom: 9px;
}

.value {
  color: #50e3c2;
  font-size: 21px;
  font-weight: bold;
  overflow-wrap: anywhere;
}

#rawData {
  background: #06101b;
  border-radius: 8px;
  padding: 12px;
  min-height: 90px;
  white-space: pre-wrap;
  overflow-wrap: anywhere;
  color: #c5f8e9;
  font-family: monospace;
  font-size: 13px;
}

#log {
  background: #06101b;
  border-radius: 8px;
  padding: 12px;
  min-height: 65px;
  max-height: 180px;
  overflow-y: auto;
  white-space: pre-wrap;
  overflow-wrap: anywhere;
  font-family: monospace;
  font-size: 12px;
  color: #b7c9dc;
}

.note {
  color: #a8bacd;
  font-size: 13px;
  line-height: 1.6;
}

@media (max-width: 480px) {
  body {
    padding: 12px;
  }

  .grid {
    grid-template-columns: 1fr 1fr;
    gap: 8px;
  }

  .value {
    font-size: 17px;
  }
}
</style>
</head>

<body>
<div class="container">

  <h1>ESP32 BLE Monitor</h1>
  <p class="subtitle">Water Quality + Smart Bottle</p>

  <div class="panel">
    <div id="status" class="status disconnected">
      DISCONNECTED
    </div>

    <button id="connectButton">CONNECT TO ESP32</button>
    <button id="disconnectButton" disabled>DISCONNECT</button>

    <p class="note">
      Device name: Water Quality Monitor<br>
      Protocol: Bluetooth Low Energy (BLE)
    </p>
  </div>

  <div class="panel">
    <h3>Water Quality Readings</h3>
    <div class="grid">
      <div class="item">
        <div class="label">TDS</div>
        <div class="value" id="tds">--</div>
      </div>

      <div class="item">
        <div class="label">Turbidity ADC</div>
        <div class="value" id="turbidity">--</div>
      </div>

      <div class="item">
        <div class="label">Temperature</div>
        <div class="value" id="temperature">--</div>
      </div>

      <div class="item">
        <div class="label">Water Quality</div>
        <div class="value" id="quality">--</div>
      </div>
    </div>
  </div>

  <div class="panel">
    <h3>MPU6050 Readings</h3>
    <div class="grid">
      <div class="item">
        <div class="label">X Axis</div>
        <div class="value" id="axisX">--</div>
      </div>

      <div class="item">
        <div class="label">Y Axis</div>
        <div class="value" id="axisY">--</div>
      </div>

      <div class="item">
        <div class="label">Z Axis</div>
        <div class="value" id="axisZ">--</div>
      </div>

      <div class="item">
        <div class="label">Drink Count</div>
        <div class="value" id="drinks">--</div>
      </div>

      <div class="item">
        <div class="label">Bottle Status</div>
        <div class="value" id="bottleStatus">--</div>
      </div>

      <div class="item">
        <div class="label">Water Reminder</div>
        <div class="value" id="reminder">--</div>
      </div>
    </div>
  </div>

  <div class="panel">
    <h3>Latest Bluetooth Message</h3>
    <div id="rawData">Waiting for BLE data...</div>
  </div>

  <div class="panel">
    <h3>Connection Log</h3>
    <div id="log">Website ready.</div>
  </div>

</div>

<script>
"use strict";

// ==========================================
// ESP32 BLE UUIDS
// ==========================================

const SERVICE_UUID =
  "6e400001-b5a3-f393-e0a9-e50e24dcca9e";

const TX_UUID =
  "6e400003-b5a3-f393-e0a9-e50e24dcca9e";

// ==========================================
// VARIABLES
// ==========================================

let bleDevice = null;
let txCharacteristic = null;

const connectButton =
  document.getElementById("connectButton");

const disconnectButton =
  document.getElementById("disconnectButton");

const statusBox =
  document.getElementById("status");

const rawData =
  document.getElementById("rawData");

const logBox =
  document.getElementById("log");

// ==========================================
// LOG MESSAGE
// ==========================================

function log(message) {
  const time = new Date().toLocaleTimeString();

  logBox.textContent +=
    "\n[" + time + "] " + message;

  logBox.scrollTop = logBox.scrollHeight;
}

// ==========================================
// CONNECTION STATUS
// ==========================================

function setStatus(message, connected) {
  statusBox.textContent = message;

  statusBox.className =
    "status " +
    (connected ? "connected" : "disconnected");
}

// ==========================================
// CONNECT TO ESP32
// ==========================================

connectButton.addEventListener("click", async () => {
  try {
    if (!navigator.bluetooth) {
      throw new Error(
        "Web Bluetooth is unavailable. Use Chrome on Android or a supported desktop browser over HTTPS."
      );
    }

    log("Searching for Water Quality Monitor...");

    bleDevice = await navigator.bluetooth.requestDevice({
      filters: [
        { name: "Water Quality Monitor" }
      ],
      optionalServices: [SERVICE_UUID]
    });

    bleDevice.addEventListener(
      "gattserverdisconnected",
      handleDisconnect
    );

    log("Device selected: " + bleDevice.name);
    setStatus("CONNECTING...", false);

    const server = await bleDevice.gatt.connect();

    log("BLE GATT connected.");

    const service =
      await server.getPrimaryService(SERVICE_UUID);

    log("BLE service found.");

    txCharacteristic =
      await service.getCharacteristic(TX_UUID);

    log("TX characteristic found.");

    await txCharacteristic.startNotifications();

    txCharacteristic.addEventListener(
      "characteristicvaluechanged",
      handleNotification
    );

    setStatus("CONNECTED - WAITING FOR DATA", true);

    connectButton.disabled = true;
    disconnectButton.disabled = false;

    log("Notifications enabled. Waiting for sensor readings...");

  } catch (error) {
    setStatus("CONNECTION FAILED", false);
    log("ERROR: " + error.message);
    console.error(error);
  }
});

// ==========================================
// RECEIVE BLE NOTIFICATIONS
// ==========================================

function handleNotification(event) {
  const dataView = event.target.value;

  const bytes = new Uint8Array(
    dataView.buffer,
    dataView.byteOffset,
    dataView.byteLength
  );

  const message = new TextDecoder("utf-8").decode(bytes).trim();

  if (!message) return;

  rawData.textContent = message;

  setStatus("CONNECTED - DATA RECEIVED", true);

  log("BLE RX: " + message);

  parseSensorData(message);
}

// ==========================================
// PARSE ESP32 DATA
// ==========================================

function parseSensorData(message) {
  const values = {};

  // Supports the complete sensor message and
  // separate COUNT / READY / REMINDER messages.

  message.split(",").forEach(part => {
    const separator = part.indexOf(":");

    if (separator < 0) return;

    const key = part
      .slice(0, separator)
      .trim()
      .toUpperCase();

    const value = part
      .slice(separator + 1)
      .trim();

    values[key] = value;
  });

  // Water quality

  if (values.TDS !== undefined) {
    document.getElementById("tds").textContent =
      values.TDS + " ppm";
  }

  if (values.TURB !== undefined) {
    document.getElementById("turbidity").textContent =
      values.TURB;
  }

  if (values.TEMP !== undefined) {
    document.getElementById("temperature").textContent =
      values.TEMP + " °C";
  }

  if (values.Q !== undefined) {
    document.getElementById("quality").textContent =
      values.Q;
  }

  // MPU6050 angles

  if (values.X !== undefined) {
    document.getElementById("axisX").textContent =
      values.X + "°";
  }

  if (values.Y !== undefined) {
    document.getElementById("axisY").textContent =
      values.Y + "°";
  }

  if (values.Z !== undefined) {
    document.getElementById("axisZ").textContent =
      values.Z + "°";
  }

  // Drink count can arrive as DRINKS or COUNT

  if (values.DRINKS !== undefined) {
    document.getElementById("drinks").textContent =
      values.DRINKS;
  }

  if (values.COUNT !== undefined) {
    document.getElementById("drinks").textContent =
      values.COUNT;
  }

  // Status can arrive in regular data or event data

  if (values.STATUS !== undefined) {
    document.getElementById("bottleStatus").textContent =
      values.STATUS;
  }

  // Reminder messages

  if (values.REMINDER !== undefined) {
    document.getElementById("reminder").textContent =
      values.REMINDER;
  } else if (message.startsWith("REMINDER:")) {
    document.getElementById("reminder").textContent =
      message.substring("REMINDER:".length).trim();
  }
}

// ==========================================
// DISCONNECTION
// ==========================================

function handleDisconnect() {
  setStatus("DISCONNECTED", false);

  connectButton.disabled = false;
  disconnectButton.disabled = true;

  txCharacteristic = null;

  log("ESP32 disconnected.");
}

disconnectButton.addEventListener("click", () => {
  if (bleDevice && bleDevice.gatt.connected) {
    bleDevice.gatt.disconnect();
  } else {
    handleDisconnect();
  }
});

// ==========================================
// INITIAL CHECK
// ==========================================

if (!navigator.bluetooth) {
  setStatus("WEB BLUETOOTH NOT AVAILABLE", false);

  log(
    "Open this page in a supported browser using HTTPS or localhost."
  );
}
</script>

</body>
</html>
