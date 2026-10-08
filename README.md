<!DOCTYPE html>
<html lang="en">

<head>

<meta charset="UTF-8">

<meta name="viewport"
      content="width=device-width, initial-scale=1.0">

<meta name="theme-color"
      content="#071421">

<title>Water Quality Monitor</title>


<style>

/* =====================================================
   GLOBAL
===================================================== */

* {
    box-sizing: border-box;
}

body {

    margin: 0;

    padding: 0;

    font-family:
        Arial,
        Helvetica,
        sans-serif;

    background:
        linear-gradient(
            180deg,
            #071421 0%,
            #0b1d2a 100%
        );

    color: white;

    min-height: 100vh;
}


/* =====================================================
   MAIN APP
===================================================== */

.app {

    width: 100%;

    max-width: 600px;

    margin: auto;

    padding: 20px;

}


/* =====================================================
   HEADER
===================================================== */

header {

    display: flex;

    align-items: center;

    gap: 15px;

    margin-bottom: 20px;

}

.water-icon {

    width: 55px;

    height: 55px;

    border-radius: 16px;

    background: #102b3c;

    display: flex;

    align-items: center;

    justify-content: center;

    font-size: 30px;

}

header h1 {

    margin: 0;

    font-size: 25px;

}

header p {

    margin: 5px 0 0 0;

    color: #8ea4b5;

    font-size: 13px;

}


/* =====================================================
   CONNECTION CARD
===================================================== */

.connection-card {

    background: #102b3c;

    border-radius: 20px;

    padding: 18px;

    margin-bottom: 20px;

    border: 1px solid #1d4157;

}

.connection-left {

    display: flex;

    align-items: center;

    gap: 15px;

}

.bluetooth-icon {

    width: 48px;

    height: 48px;

    border-radius: 14px;

    display: flex;

    align-items: center;

    justify-content: center;

    font-size: 25px;

    background: #243746;

}

.bluetooth-icon.connected {

    background: #123d35;

}

.bluetooth-icon.disconnected {

    background: #243746;

}

.small-title {

    color: #8ea4b5;

    font-size: 12px;

    margin-bottom: 5px;

}

.status {

    color: #ffcc66;

    font-size: 16px;

    font-weight: bold;

}


/* =====================================================
   SENSOR GRID
===================================================== */

.sensor-grid {

    display: grid;

    grid-template-columns: repeat(2, 1fr);

    gap: 15px;

    margin-bottom: 20px;

}


/* =====================================================
   SENSOR CARD
===================================================== */

.sensor-card {

    background: #102b3c;

    border-radius: 20px;

    padding: 20px;

    border: 1px solid #1d4157;

    text-align: center;

}


/* TDS FULL WIDTH */

.sensor-card:first-child {

    grid-column: span 2;

}


/* =====================================================
   SENSOR ICON
===================================================== */

.sensor-icon {

    font-size: 28px;

    margin-bottom: 8px;

}

.sensor-name {

    color: #8ea4b5;

    font-size: 13px;

    margin-bottom: 8px;

}

.sensor-value {

    color: white;

    font-size: 28px;

    font-weight: bold;

}

.sensor-unit {

    color: #6f8798;

    font-size: 11px;

    margin-top: 5px;

}


/* =====================================================
   WATER QUALITY CARD
===================================================== */

.quality-card {

    border-radius: 20px;

    padding: 25px;

    margin-bottom: 20px;

    text-align: center;

    border: 1px solid #1d4157;

    background: #102b3c;

    transition: 0.3s;

}

.quality-icon {

    width: 55px;

    height: 55px;

    border-radius: 50%;

    margin: auto;

    margin-bottom: 12px;

    display: flex;

    align-items: center;

    justify-content: center;

    font-size: 25px;

    background: #243746;

}

.quality-title {

    color: #8ea4b5;

    font-size: 12px;

    letter-spacing: 2px;

    margin-bottom: 8px;

}

.quality-value {

    font-size: 28px;

    font-weight: bold;

}


/* =====================================================
   QUALITY COLORS
===================================================== */

.quality-pure {

    background: #123d35;

    border-color: #1f7665;

}

.quality-pure .quality-value {

    color: #5ff2cf;

}

.quality-excellent {

    background: #173b3e;

    border-color: #2c7e86;

}

.quality-excellent .quality-value {

    color: #5ee7f2;

}

.quality-good {

    background: #142f46;

    border-color: #25618b;

}

.quality-good .quality-value {

    color: #54baff;

}

.quality-fair {

    background: #3b3420;

    border-color: #76672a;

}

.quality-fair .quality-value {

    color: #f6d75d;

}

.quality-high {

    background: #3d2225;

    border-color: #81353d;

}

.quality-high .quality-value {

    color: #ff6975;

}


/* =====================================================
   MPU6050 CARD
===================================================== */

.mpu-card {

    background: #102b3c;

    border-radius: 20px;

    padding: 22px 18px;

    margin-bottom: 20px;

    border: 1px solid #1d4157;

}

.mpu-title {

    color: white;

    font-size: 16px;

    font-weight: bold;

    margin-bottom: 18px;

    text-align: center;

}

.mpu-values {

    display: grid;

    grid-template-columns: repeat(2, 1fr);

    gap: 12px;

}

.mpu-item {

    background: #0c202d;

    border-radius: 14px;

    padding: 15px;

    text-align: center;

    border: 1px solid #1d4157;

}

.mpu-label {

    color: #8ea4b5;

    font-size: 12px;

    margin-bottom: 7px;

    letter-spacing: 1px;

}

.mpu-value {

    color: #22d3ee;

    font-size: 24px;

    font-weight: bold;

    word-break: break-word;

}


/* =====================================================
   CONNECT BUTTON
===================================================== */

.connect-button {

    width: 100%;

    border: none;

    border-radius: 16px;

    padding: 17px;

    background: #164b67;

    color: white;

    font-size: 15px;

    font-weight: bold;

    cursor: pointer;

    margin-bottom: 20px;

}

.connect-button:active {

    transform: scale(0.98);

}


/* =====================================================
   INFO BOX
===================================================== */

.info-box {

    background: #0c202d;

    border: 1px solid #1d4157;

    border-radius: 16px;

    padding: 16px;

    margin-bottom: 20px;

}

.info-box p {

    margin: 7px 0;

    color: #8ea4b5;

    font-size: 12px;

    line-height: 1.5;

}

.info-box strong {

    color: white;

}


/* =====================================================
   DEBUG BOX
===================================================== */

.debug-box {

    background: #091923;

    border: 1px solid #1d4157;

    border-radius: 16px;

    padding: 16px;

    margin-bottom: 20px;

}

.debug-title {

    color: white;

    font-size: 13px;

    font-weight: bold;

    margin-bottom: 10px;

}

#rawData {

    color: #6f8798;

    font-size: 11px;

    line-height: 1.6;

    word-break: break-all;

}


/* =====================================================
   MOBILE
===================================================== */

@media (max-width: 480px) {

    .app {

        padding: 15px;

    }

    header h1 {

        font-size: 22px;

    }

    .sensor-value {

        font-size: 24px;

    }

    .quality-value {

        font-size: 25px;

    }

    .mpu-value {

        font-size: 21px;

    }

}


/* =====================================================
   LARGE SCREEN
===================================================== */

@media (min-width: 700px) {

    .app {

        padding-top: 35px;

    }

}

</style>

</head>


<body>


<div class="app">


<!-- =================================================
     HEADER
================================================== -->

<header>

    <div class="water-icon">
        💧
    </div>

    <div>

        <h1>
            Water Quality
        </h1>

        <p>
            ESP32 • Bluetooth Monitor
        </p>

    </div>

</header>



<!-- =================================================
     BLUETOOTH STATUS
================================================== -->

<div class="connection-card">

    <div class="connection-left">

        <div id="bluetoothIcon"
             class="bluetooth-icon disconnected">

            📡

        </div>

        <div>

            <div class="small-title">
                Bluetooth Status
            </div>

            <div id="status"
                 class="status">

                Disconnected

            </div>

        </div>

    </div>

</div>



<!-- =================================================
     WATER SENSOR VALUES
================================================== -->

<div class="sensor-grid">


    <!-- TDS -->

    <div class="sensor-card">

        <div class="sensor-icon tds">
            🧪
        </div>

        <div class="sensor-name">
            TDS
        </div>

        <div id="tds"
             class="sensor-value">

            --

        </div>

        <div class="sensor-unit">
            Raw ADC
        </div>

    </div>



    <!-- TURBIDITY -->

    <div class="sensor-card">

        <div class="sensor-icon turbidity">
            💧
        </div>

        <div class="sensor-name">
            Turbidity
        </div>

        <div id="turbidity"
             class="sensor-value">

            --

        </div>

        <div class="sensor-unit">
            Raw ADC
        </div>

    </div>



    <!-- TEMPERATURE -->

    <div class="sensor-card">

        <div class="sensor-icon temperature">
            🌡️
        </div>

        <div class="sensor-name">
            Temperature
        </div>

        <div id="temperature"
             class="sensor-value">

            --

        </div>

        <div class="sensor-unit">
            °C
        </div>

    </div>


</div>



<!-- =================================================
     WATER QUALITY
================================================== -->

<div id="qualityCard"
     class="quality-card">

    <div id="qualityIcon"
         class="quality-icon">

        ✓

    </div>

    <div class="quality-title">

        WATER QUALITY

    </div>

    <div id="quality"
         class="quality-value">

        --

    </div>

</div>



<!-- =================================================
     MPU6050
================================================== -->

<div class="mpu-card">

    <div class="mpu-title">

        MPU6050 Bottle Status

    </div>


    <div class="mpu-values">


        <!-- MPU X -->

        <div class="mpu-item">

            <div class="mpu-label">
                MPU X
            </div>

            <div id="mpuX"
                 class="mpu-value">

                --

            </div>

        </div>



        <!-- MPU Y -->

        <div class="mpu-item">

            <div class="mpu-label">
                MPU Y
            </div>

            <div id="mpuY"
                 class="mpu-value">

                --

            </div>

        </div>



        <!-- MPU Z -->

        <div class="mpu-item">

            <div class="mpu-label">
                MPU Z
            </div>

            <div id="mpuZ"
                 class="mpu-value">

                --

            </div>

        </div>



        <!-- DRINK COUNT -->

        <div class="mpu-item">

            <div class="mpu-label">
                DRINKS
            </div>

            <div id="drinkCount"
                 class="mpu-value">

                --

            </div>

        </div>


    </div>

</div>



<!-- =================================================
     CONNECT BUTTON
================================================== -->

<button id="connectButton"
        class="connect-button">

    <span id="buttonIcon">
        🔵
    </span>

    <span id="buttonText">
        CONNECT BLUETOOTH
    </span>

</button>



<!-- =================================================
     INFORMATION
================================================== -->

<div class="info-box">

    <p>
        <strong>ESP32:</strong>
        Water Quality Monitor
    </p>

    <p>
        TDS and Turbidity are displayed
        as raw ADC values.
    </p>

    <p>
        Quality is calculated by the ESP32
        using the TDS value.
    </p>

    <p>
        MPU6050 displays the current
        bottle orientation.
    </p>

    <p>
        Drinks shows the total detected
        drinking events.
    </p>

</div>



<!-- =================================================
     DEBUG
================================================== -->

<div class="debug-box">

    <div class="debug-title">

        Last BLE Data

    </div>

    <div id="rawData">

        Waiting for data...

    </div>

</div>


</div>



<script>

/* =====================================================
   HTML ELEMENTS
===================================================== */

const connectButton =
    document.getElementById(
        "connectButton"
    );

const buttonText =
    document.getElementById(
        "buttonText"
    );

const buttonIcon =
    document.getElementById(
        "buttonIcon"
    );

const statusElement =
    document.getElementById(
        "status"
    );

const rawDataElement =
    document.getElementById(
        "rawData"
    );

const tdsElement =
    document.getElementById(
        "tds"
    );

const turbidityElement =
    document.getElementById(
        "turbidity"
    );

const temperatureElement =
    document.getElementById(
        "temperature"
    );

const qualityElement =
    document.getElementById(
        "quality"
    );

const qualityCard =
    document.getElementById(
        "qualityCard"
    );

const qualityIcon =
    document.getElementById(
        "qualityIcon"
    );

const bluetoothIcon =
    document.getElementById(
        "bluetoothIcon"
    );


/* =====================================================
   MPU ELEMENTS
===================================================== */

const mpuXElement =
    document.getElementById(
        "mpuX"
    );

const mpuYElement =
    document.getElementById(
        "mpuY"
    );

const mpuZElement =
    document.getElementById(
        "mpuZ"
    );

const drinkCountElement =
    document.getElementById(
        "drinkCount"
    );


/* =====================================================
   CHECK APP INVENTOR WEBVIEW
===================================================== */

function isAppInventorWebView() {

    return (
        window.AppInventor &&
        typeof
        window.AppInventor
            .getWebViewString ===
        "function"
    );

}


/* =====================================================
   RECEIVE DATA FROM APP INVENTOR
===================================================== */

function receiveFromAppInventor() {

    if (!isAppInventorWebView()) {

        setWaitingStatus();

        return;

    }


    try {

        const data =
            window.AppInventor
                .getWebViewString();


        if (
            data === null ||
            data === undefined ||
            data === ""
        ) {

            return;

        }


        /* ---------------------------------------------
           SHOW RAW BLE DATA
        --------------------------------------------- */

        rawDataElement.textContent =
            data;


        /* ---------------------------------------------
           PARSE BLE DATA
        --------------------------------------------- */

        parseBLEData(data);


        /* ---------------------------------------------
           CONNECTION STATUS
        --------------------------------------------- */

        setConnectedStatus();

    }

    catch (error) {

        console.log(
            "WebViewString error:",
            error
        );

    }

}


/* =====================================================
   PARSE BLE DATA
===================================================== */

function parseBLEData(data) {


    /* =================================================
       TDS
    ================================================= */

    const tdsMatch =
        data.match(
            /TDS:([0-9]+)/
        );


    if (tdsMatch) {

        tdsElement.textContent =
            tdsMatch[1];

    }



    /* =================================================
       TURBIDITY
    ================================================= */

    const turbidityMatch =
        data.match(
            /TURB:([0-9]+)/
        );


    if (turbidityMatch) {

        turbidityElement.textContent =
            turbidityMatch[1];

    }



    /* =================================================
       TEMPERATURE
    ================================================= */

    const temperatureMatch =
        data.match(
            /TEMP:([-+]?[0-9]*\.?[0-9]+)/
        );


    if (temperatureMatch) {

        const temperature =
            parseFloat(
                temperatureMatch[1]
            );


        /*
         * DS18B20 returns -127 when
         * the sensor is not connected.
         */

        if (temperature === -127) {

            temperatureElement.textContent =
                "Not Connected";

        }

        else {

            temperatureElement.textContent =
                temperature.toFixed(2) +
                " °C";

        }

    }



    /* =================================================
       WATER QUALITY
    ================================================= */

    const qualityMatch =
        data.match(
            /Q:([A-Za-z]+)/
        );


    if (qualityMatch) {

        const quality =
            qualityMatch[1]
                .toUpperCase();


        qualityElement.textContent =
            quality;


        updateQualityStyle(
            quality
        );

    }



    /* =================================================
       DRINK COUNT
    ================================================= */

    const drinkCountMatch =
        data.match(
            /DRINKS:([0-9]+)/
        );


    if (drinkCountMatch) {

        const count =
            parseInt(
                drinkCountMatch[1]
            );


        drinkCountElement.textContent =
            count;

    }



    /* =================================================
       MPU X
    ================================================= */

    const mpuXMatch =
        data.match(
            /MPUX:([-+]?[0-9]*\.?[0-9]+)/
        );


    if (mpuXMatch) {

        const value =
            parseFloat(
                mpuXMatch[1]
            );


        mpuXElement.textContent =
            value.toFixed(1) +
            "°";

    }



    /* =================================================
       MPU Y
    ================================================= */

    const mpuYMatch =
        data.match(
            /MPUY:([-+]?[0-9]*\.?[0-9]+)/
        );


    if (mpuYMatch) {

        const value =
            parseFloat(
                mpuYMatch[1]
            );


        mpuYElement.textContent =
            value.toFixed(1) +
            "°";

    }



    /* =================================================
       MPU Z
    ================================================= */

    const mpuZMatch =
        data.match(
            /MPUZ:([-+]?[0-9]*\.?[0-9]+)/
        );


    if (mpuZMatch) {

        const value =
            parseFloat(
                mpuZMatch[1]
            );


        mpuZElement.textContent =
            value.toFixed(1) +
            "°";

    }

}


/* =====================================================
   UPDATE QUALITY STYLE
===================================================== */

function updateQualityStyle(
    quality
) {


    /* Remove old classes */

    qualityCard.classList.remove(
        "quality-pure",
        "quality-excellent",
        "quality-good",
        "quality-fair",
        "quality-high"
    );


    /* =================================================
       PURE
    ================================================= */

    if (quality === "PURE") {

        qualityCard.classList.add(
            "quality-pure"
        );

        qualityIcon.textContent =
            "✓";

    }


    /* =================================================
       EXCELLENT
    ================================================= */

    else if (
        quality === "EXCELLENT"
    ) {

        qualityCard.classList.add(
            "quality-excellent"
        );

        qualityIcon.textContent =
            "✓";

    }


    /* =================================================
       GOOD
    ================================================= */

    else if (
        quality === "GOOD"
    ) {

        qualityCard.classList.add(
            "quality-good"
        );

        qualityIcon.textContent =
            "✓";

    }


    /* =================================================
       FAIR
    ================================================= */

    else if (
        quality === "FAIR"
    ) {

        qualityCard.classList.add(
            "quality-fair"
        );

        qualityIcon.textContent =
            "!";

    }


    /* =================================================
       HIGH
    ================================================= */

    else if (
        quality === "HIGH"
    ) {

        qualityCard.classList.add(
            "quality-high"
        );

        qualityIcon.textContent =
            "!";

    }

}


/* =====================================================
   CONNECTED STATUS
===================================================== */

function setConnectedStatus() {

    statusElement.textContent =
        "Connected";

    statusElement.style.color =
        "#5ff2cf";


    bluetoothIcon.classList.remove(
        "disconnected"
    );

    bluetoothIcon.classList.add(
        "connected"
    );


    buttonText.textContent =
        "BLUETOOTH CONNECTED";

    buttonIcon.textContent =
        "🟢";

}


/* =====================================================
   WAITING STATUS
===================================================== */

function setWaitingStatus() {

    statusElement.textContent =
        "Waiting for App Inventor";

    statusElement.style.color =
        "#ffcc66";


    bluetoothIcon.classList.remove(
        "connected"
    );

    bluetoothIcon.classList.add(
        "disconnected"
    );

}


/* =====================================================
   CONNECT BUTTON
===================================================== */

connectButton.addEventListener(
    "click",
    function () {

        alert(
            "Bluetooth connection is controlled by MIT App Inventor."
        );

    }
);


/* =====================================================
   START
===================================================== */

window.addEventListener(
    "load",
    function () {

        setWaitingStatus();

        /*
         * Check App Inventor every
         * 500 milliseconds.
         */

        setInterval(
            receiveFromAppInventor,
            500
        );

    }
);

</script>


</body>

</html>
