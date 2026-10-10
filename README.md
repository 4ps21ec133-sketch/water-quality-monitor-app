```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="theme-color" content="#071421">
<title>ESP32 Water Quality Monitor</title>

<style>
* { box-sizing: border-box; }

body {
    margin: 0;
    padding: 16px;
    background: #071421;
    color: #eaf4ff;
    font-family: Arial, sans-serif;
}

.container { max-width: 900px; margin: auto; }

h1 {
    text-align: center;
    color: #50e3c2;
    margin-bottom: 8px;
}

h3 { margin-top: 0; color: #eaf4ff; }

.subtitle {
    text-align: center;
    color: #a8bacd;
    margin-bottom: 24px;
}

.panel {
    background: #102336;
    border: 1px solid #294258;
    border-radius: 14px;
    padding: 18px;
    margin-bottom: 16px;
}

.status {
    padding: 12px;
    border-radius: 8px;
    background: #26364a;
    text-align: center;
    font-weight: bold;
    margin-bottom: 14px;
    overflow-wrap: anywhere;
}

.connected { color: #50e3c2; }
.disconnected { color: #ff7777; }

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

#connectButton { background: #50e3c2; color: #071421; }
#disconnectButton { background: #ff7777; color: #071421; }
button:disabled { opacity: 0.45; }

.note {
    color: #a8bacd;
    font-size: 13px;
    line-height: 1.6;
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

#rawData, #log {
    background: #06101b;
    border-radius: 8px;
    padding: 12px;
    min-height: 65px;
    white-space: pre-wrap;
    overflow-wrap: anywhere;
    font-family: monospace;
    font-size: 13px;
}

#rawData { color: #c5f8e9; }

#log {
    color: #b7c9dc;
    max-height: 180px;
    overflow-y: auto;
}

.reminder-active { color: #ffcc66; }

@media (max-width: 480px) {
    body { padding: 10px; }
    .panel { padding: 13px; }
    .grid { gap: 8px; }
    .item { padding: 10px; }
    .value { font-size: 17px; }
}
</style>
</head>

<body>
<div class="container">

    <h1>ESP32 Water Quality Monitor</h1>
    <p class="subtitle">Water Quality + Smart Bottle</p>

    <div class="panel">
        <div id="status" class="status disconnected">
            WAITING FOR CONNECTION
        </div>

        <button id="connectButton">CONNECT TO ESP32</button>
        <button id="disconnectButton" disabled>DISCONNECT</button>

        <p class="note" id="connectionNote">
            Device: Water Quality Monitor<br>
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
        <h3>Latest Received Message</h3>
        <div id="rawData">Waiting for sensor data...</div>
    </div>

    <div class="panel">
        <h3>Connection Log</h3>
        <div id="log">Website ready.</div>
    </div>

</div>

<script>
"use strict";

// ==================================================
// ESP32 BLE UUIDS
// ==================================================

const SERVICE_UUID =
    "6e400001-b5a3-f393-e0a9-e50e24dcca9e";

const TX_UUID =
    "6e400003-b5a3-f393-e0a9-e50e24dcca9e";

// ==================================================
// VARIABLES
// ==================================================

let bleDevice = null;
let txCharacteristic = null;

let appBridgeMode = false;
let lastAppData = "";
let lastDataTime = 0;

const $ = id => document.getElementById(id);

const connectButton = $("connectButton");
const disconnectButton = $("disconnectButton");
const statusBox = $("status");
const rawData = $("rawData");
const logBox = $("log");
const connectionNote = $("connectionNote");

// ==================================================
// LOGGING
// ==================================================

function log(message) {
    const time = new Date().toLocaleTimeString();

    logBox.textContent +=
        "\n[" + time + "] " + message;

    // Prevent unlimited log growth
    const lines = logBox.textContent.split("\n");
    if (lines.length > 35) {
        logBox.textContent = lines.slice(-35).join("\n");
    }

    logBox.scrollTop = logBox.scrollHeight;
}

// ==================================================
// STATUS
// ==================================================

function setStatus(message, connected) {
    statusBox.textContent = message;
    statusBox.className =
        "status " + (connected ? "connected" : "disconnected");
}

// ==================================================
// MIT APP INVENTOR BRIDGE
// ==================================================

function getAppInventorBridge() {
    try {
        return !!(
            window.AppInventor &&
            typeof window.AppInventor.getWebViewString === "function"
        );
    } catch (error) {
        return false;
    }
}

function detectAppInventor() {
    if (!getAppInventorBridge()) {
        return false;
    }

    if (!appBridgeMode) {
        appBridgeMode = true;

        connectButton.style.display = "none";
        disconnectButton.style.display = "none";

        connectionNote.textContent =
            "Bluetooth is managed by MIT App Inventor.";

        setStatus("WAITING FOR APP DATA", false);

        log("MIT App Inventor bridge detected.");
        log("Waiting for Bluetooth data.");
    }

    return true;
}

// ==================================================
// DIRECT WEB BLUETOOTH
// Used when opened in a compatible browser
// ==================================================

connectButton.addEventListener("click", async function() {
    try {
        if (appBridgeMode) {
            log("Bluetooth is managed by MIT App Inventor.");
            return;
        }

        if (!navigator.bluetooth) {
            throw new Error(
                "Web Bluetooth is unavailable. Use MIT App Inventor."
            );
        }

        log("Searching for ESP32...");

        bleDevice = await navigator.bluetooth.requestDevice({
            filters: [{ name: "Water Quality Monitor" }],
            optionalServices: [SERVICE_UUID]
        });

        bleDevice.addEventListener(
            "gattserverdisconnected",
            handleDisconnect
        );

        setStatus("CONNECTING...", false);

        const server = await bleDevice.gatt.connect();
        const service = await server.getPrimaryService(SERVICE_UUID);

        txCharacteristic =
            await service.getCharacteristic(TX_UUID);

        await txCharacteristic.startNotifications();

        txCharacteristic.addEventListener(
            "characteristicvaluechanged",
            handleNotification
        );

        connectButton.disabled = true;
        disconnectButton.disabled = false;

        setStatus("CONNECTED - WAITING FOR DATA", true);
        log("BLE connected. Notifications enabled.");

    } catch (error) {
        setStatus("CONNECTION FAILED", false);
        log("ERROR: " + error.message);
    }
});

// ==================================================
// DIRECT BLE MESSAGE RECEIVER
// ==================================================

function handleNotification(event) {
    const view = event.target.value;

    const bytes = new Uint8Array(
        view.buffer,
        view.byteOffset,
        view.byteLength
    );

    const message = new TextDecoder("utf-8")
        .decode(bytes)
        .trim();

    if (message) {
        displayReceivedMessage(message, "BLE RX");
    }
}

// ==================================================
// MIT APP INVENTOR MESSAGE RECEIVER
//
// App Inventor Blocks must set:
// WebViewer1.WebViewString = received BLE text
// ==================================================

function receiveFromAppInventor() {
    if (!detectAppInventor()) return;

    let message;

    try {
        message = window.AppInventor.getWebViewString();
    } catch (error) {
        return;
    }

    if (message === null || message === undefined) return;

    message = String(message).trim();

    if (!message || message === lastAppData) return;

    lastAppData = message;
    lastDataTime = Date.now();

    displayReceivedMessage(message, "APP RX");
}

// Keep checking for new App Inventor data.
setInterval(receiveFromAppInventor, 250);

// Also check when the page loads.
window.addEventListener("load", function() {
    detectAppInventor();
    receiveFromAppInventor();
});

// ==================================================
// DISPLAY MESSAGE
// ==================================================

function displayReceivedMessage(message, source) {
    rawData.textContent = message;

    setStatus("CONNECTED - DATA RECEIVED", true);

    log(source + ": " + message);

    parseSensorData(message);
}

// ==================================================
// PARSE SENSOR DATA
//
// Combined example:
// TDS:378,TURB:1250,TEMP:27.50,Q:GOOD,
// X:5.0,Y:-83.5,Z:4.0,DRINKS:3,
// STATUS:READY,REMINDER:NONE
//
// Separate examples:
// COUNT:3,X:-2.1,Y:-1.8,Z:-87.4,STATUS:DRINK
// REMINDER:DRINK WATER
// ==================================================

function parseSensorData(message) {
    const values = {};

    // Accept comma-separated and semicolon-separated fields.
    const parts = message.split(/[,;\r\n]+/);

    parts.forEach(function(part) {
        const separator = part.indexOf(":");

        if (separator < 0) return;

        const key = part.substring(0, separator)
            .trim()
            .toUpperCase();

        const value = part.substring(separator + 1).trim();

        if (key) {
            values[key] = value;
        }
    });

    // Water quality readings
    if (values.TDS !== undefined) {
        $("tds").textContent = values.TDS + " ppm";
    }

    if (values.TURB !== undefined) {
        $("turbidity").textContent = values.TURB;
    }

    if (values.TEMP !== undefined) {
        $("temperature").textContent = values.TEMP + " °C";
    }

    if (values.Q !== undefined) {
        $("quality").textContent = values.Q;
    }

    // MPU6050 axes
    if (values.X !== undefined) {
        $("axisX").textContent = values.X + "°";
    }

    if (values.Y !== undefined) {
        $("axisY").textContent = values.Y + "°";
    }

    if (values.Z !== undefined) {
        $("axisZ").textContent = values.Z + "°";
    }

    // Drink count: accept either DRINKS or COUNT
    if (values.DRINKS !== undefined) {
        $("drinks").textContent = values.DRINKS;
    } else if (values.COUNT !== undefined) {
        $("drinks").textContent = values.COUNT;
    }

    // Bottle status
    if (values.STATUS !== undefined) {
        $("bottleStatus").textContent = values.STATUS;
    }

    // Water reminder
    if (values.REMINDER !== undefined) {
        updateReminder(values.REMINDER);
    }

    // Also support a standalone reminder message.
    if (/^\s*REMINDER\s*:/i.test(message)) {
        updateReminder(message.replace(/^\s*REMINDER\s*:/i, "").trim());
    }
}

function updateReminder(value) {
    const reminder = $("reminder");

    reminder.textContent = value;

    reminder.classList.toggle(
        "reminder-active",
        value.toUpperCase().includes("DRINK WATER")
    );
}

// ==================================================
// DISCONNECT
// ==================================================

function handleDisconnect() {
    setStatus("DISCONNECTED", false);

    connectButton.disabled = false;
    disconnectButton.disabled = true;

    txCharacteristic = null;

    log("ESP32 disconnected.");
}

disconnectButton.addEventListener("click", function() {
    if (bleDevice && bleDevice.gatt && bleDevice.gatt.connected) {
        bleDevice.gatt.disconnect();
    } else {
        handleDisconnect();
    }
});

// ==================================================
// INITIALIZATION
// ==================================================

if (!detectAppInventor() && !navigator.bluetooth) {
    setStatus("WAITING FOR APP DATA", false);

    log("Waiting for MIT App Inventor Bluetooth data.");
}
</script>

</body>
</html>
```
