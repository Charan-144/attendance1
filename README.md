
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="theme-color" content="#0f4d68">
  <title>LeapStart | Employee Attendance</title>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      padding: 20px 14px 40px;
      font-family: Arial, sans-serif;
      background: #f0f5fa;
      color: #18344b;
    }

    .container {
      max-width: 650px;
      margin: 0 auto;
    }

    .header {
      text-align: center;
      margin: 15px 0 24px;
    }

    .header h1 {
      color: #0f4d68;
      margin-bottom: 8px;
    }

    .header p {
      color: #617385;
      line-height: 1.6;
      font-size: 14px;
    }

    .card {
      background: #fff;
      border: 1px solid #e0e8ef;
      border-radius: 16px;
      padding: 20px;
      margin-bottom: 16px;
      box-shadow: 0 8px 28px rgba(24, 52, 75, 0.05);
    }

    label {
      display: block;
      font-size: 14px;
      font-weight: bold;
      margin-bottom: 8px;
    }

    select, button {
      font: inherit;
      width: 100%;
      padding: 13px;
      border-radius: 9px;
    }

    select {
      border: 1px solid #cbd7e2;
      background: #fff;
      margin-bottom: 15px;
      color: #18344b;
    }

    button {
      border: 0;
      background: #0f4d68;
      color: #fff;
      font-weight: bold;
      cursor: pointer;
      margin-top: 9px;
    }

    button.secondary {
      background: #277da1;
    }

    button.capture {
      background: #15803d;
    }

    button:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }

    .camera-box {
      background: #101820;
      border-radius: 12px;
      overflow: hidden;
      min-height: 180px;
      display: flex;
      justify-content: center;
      align-items: center;
      color: white;
    }

    video, canvas {
      width: 100%;
      height: auto;
      display: block;
    }

    video, canvas {
      display: none;
    }

    .placeholder {
      padding: 35px 15px;
      text-align: center;
      line-height: 1.6;
      font-size: 14px;
      color: #e1e8ef;
    }

    .details {
      margin-top: 15px;
      padding: 14px;
      border-radius: 9px;
      background: #f3f7fa;
      font-size: 13px;
      line-height: 1.8;
      overflow-wrap: anywhere;
      white-space: pre-wrap;
    }

    .status {
      margin-top: 14px;
      font-size: 14px;
      line-height: 1.6;
      white-space: pre-wrap;
      overflow-wrap: anywhere;
    }

    .success {
      color: #15803d;
    }

    .error {
      color: #c62828;
    }

    .small {
      font-size: 12px;
      color: #6b7885;
      line-height: 1.6;
      margin-top: 14px;
    }

    .hidden {
      display: none !important;
    }

    .required-note {
      font-size: 12px;
      color: #617385;
      line-height: 1.5;
    }

    .employee-info {
      margin: 0 0 12px;
      padding: 12px;
      border-radius: 9px;
      background: #f3f7fa;
      font-size: 13px;
      line-height: 1.7;
      text-align: left;
    }

    .employee-info strong {
      color: #0f4d68;
    }
  </style>
</head>

<body>
  <main class="container">

    <header class="header">
      <h1>LeapStart Attendance</h1>
      <p>
        Employee attendance with a selfie and GPS location.
      </p>
    </header>

    <section class="card">
      <label for="employee">Select employee</label>

      <select id="employee">
        <option value="">-- Select your name --</option>
      </select>

      <div id="employeeInfo" class="employee-info hidden"></div>

      <button id="locationBtn" type="button" onclick="getLocation()">
        1. Get Current Location
      </button>

      <div id="details" class="details">Date: Not captured
Time: Not captured
Location: Not captured</div>

      <button
        id="cameraBtn"
        class="secondary"
        type="button"
        onclick="startCamera()"
        disabled>
        2. Open Camera
      </button>

      <p class="required-note">
        Allow camera and location access when your browser asks.
        This website must be opened over HTTPS.
      </p>
    </section>

    <section class="card">
      <h3>Live camera and selfie</h3>

      <div class="camera-box">
        <div id="placeholder" class="placeholder">
          Your camera preview will appear here.
        </div>

        <video id="video" autoplay playsinline muted></video>
        <canvas id="canvas"></canvas>
      </div>

      <button
        id="captureBtn"
        class="capture"
        type="button"
        onclick="capturePhoto()"
        disabled>
        3. Capture Selfie
      </button>

      <button
        id="retakeBtn"
        class="secondary hidden"
        type="button"
        onclick="retakePhoto()">
        Retake Photo
      </button>

      <button
        id="submitBtn"
        type="button"
        onclick="submitAttendance()"
        disabled>
        4. Submit Attendance
      </button>

      <div
        id="status"
        class="status"
        role="status"
        aria-live="polite"></div>
    </section>

    <p class="small">
      Attendance details and the captured selfie will be sent to the
      LeapStart attendance backend. GPS information is supplied by
      the browser and should not be treated as independently verified
      proof of physical presence.
    </p>

    <iframe
      id="submitFrame"
      name="submitFrame"
      title="Attendance submission response"
      class="hidden"></iframe>

    <form
      id="submitForm"
      method="post"
      target="submitFrame"
      class="hidden">

      <input
        type="hidden"
        name="payload"
        id="payloadInput">
    </form>

  </main>

  <script>
    "use strict";

    // Your deployed Apps Script web app URL.
    const SCRIPT_URL =
      "https://script.google.com/macros/s/AKfycbyQSIir9sTPtzlncX0Thktz8lJcYWu5x1DlThpbAjbIodGXBXxx9_Ap168WXSw4O_tq/exec";

    /*
      SAMPLE EMPLOYEE DIRECTORY.
      Replace the names and roles below with your actual staff details.
      Format: { name: "Full Name", role: "Job Title" }
    */
    const employees = [
      { name: "Aarav Reddy", role: "Mentor" },
      { name: "Priya Sharma", role: "Operations" },
      { name: "Rohit Verma", role: "Software Engineer" },
      { name: "Ananya Iyer", role: "Data Scientist" },
      { name: "Karthik Naidu", role: "Tech Lead" },
      { name: "Sneha Kapoor", role: "HR Executive" },
      { name: "Vikram Singh", role: "Admissions" },
      { name: "Meera Nair", role: "QA Engineer" },
      { name: "Arjun Patel", role: "Designer" },
      { name: "Divya Menon", role: "Accounts" }
    ];

    const $ = id => document.getElementById(id);

    let stream = null;
    let locationData = null;
    let capturedPhoto = null;
    let busy = false;
    let submitTimeout = null;

    const video = $("video");
    const canvas = $("canvas");
    const ctx = canvas.getContext("2d");

    $("submitForm").action = SCRIPT_URL;

    // Populate dropdown and show a useful message if no employees exist.
    function populateEmployees() {
      const dropdown = $("employee");

      employees.forEach((person, index) => {
        const option = document.createElement("option");
        option.value = String(index);
        option.textContent = person.name + " — " + person.role;
        dropdown.appendChild(option);
      });

      if (employees.length === 0) {
        showStatus(
          "No employees are configured. Add employees to the employees array in index.html.",
          "error"
        );
      }
    }

    populateEmployees();

    function selectedEmployee() {
      const value = $("employee").value;

      if (value === "") return null;

      const index = Number(value);

      if (
        !Number.isInteger(index) ||
        index < 0 ||
        index >= employees.length
      ) {
        return null;
      }

      return employees[index];
    }

    function showStatus(message, type) {
      $("status").textContent = message;
      $("status").className = "status " + (type || "");
    }

    $("employee").addEventListener("change", () => {
      capturedPhoto = null;

      $("submitBtn").disabled = true;
      $("captureBtn").disabled = true;
      $("retakeBtn").classList.add("hidden");

      canvas.style.display = "none";
      $("placeholder").classList.remove("hidden");

      const person = selectedEmployee();

      if (!person) {
        $("employeeInfo").classList.add("hidden");
        $("cameraBtn").disabled = true;
        stopCamera();
        return;
      }

      $("employeeInfo").textContent =
        "Employee: " + person.name +
        "\nRole: " + person.role;

      $("employeeInfo").classList.remove("hidden");
      $("cameraBtn").disabled = !locationData;

      showStatus("", "");
    });

    // STEP 1: GET GPS LOCATION.
    function getLocation() {
      if (!navigator.geolocation) {
        showStatus(
          "Your browser does not support GPS location.",
          "error"
        );
        return;
      }

      $("locationBtn").disabled = true;
      $("locationBtn").textContent = "Getting location...";

      navigator.geolocation.getCurrentPosition(
        async position => {
          try {
            const c = position.coords;

            if (
              !Number.isFinite(c.latitude) ||
              !Number.isFinite(c.longitude) ||
              !Number.isFinite(c.accuracy) ||
              c.accuracy <= 0
            ) {
              throw new Error(
                "The browser returned invalid GPS information."
              );
            }

            locationData = {
              lat: c.latitude,
              lng: c.longitude,
              accuracy: c.accuracy,
              address: ""
            };

            showStatus("Getting your address...");

            const url =
              "https://nominatim.openstreetmap.org/reverse?format=jsonv2&lat=" +
              encodeURIComponent(c.latitude) +
              "&lon=" +
              encodeURIComponent(c.longitude);

            try {
              const response = await fetch(url);

              if (response.ok) {
                const result = await response.json();
                locationData.address = result.display_name || "";
              }
            } catch (error) {
              // GPS coordinates remain available if address lookup fails.
            }

            updateLocationDetails();

            $("cameraBtn").disabled = !selectedEmployee();
            $("locationBtn").textContent = "Location Captured";

            showStatus(
              "Location captured. You can open the camera now.",
              "success"
            );

          } catch (error) {
            showStatus(
              error.message || "Could not process location.",
              "error"
            );

            $("locationBtn").disabled = false;
            $("locationBtn").textContent =
              "1. Get Current Location";
          }
        },

        error => {
          const messages = {
            1: "Location permission denied. Allow location access in browser settings.",
            2: "Your device could not determine the location. Check GPS settings.",
            3: "Location request timed out. Please try again."
          };

          showStatus(
            messages[error.code] || "Could not get your location.",
            "error"
          );

          $("locationBtn").disabled = false;
          $("locationBtn").textContent =
            "1. Get Current Location";
        },

        {
          enableHighAccuracy: true,
          timeout: 25000,
          maximumAge: 0
        }
      );
    }

    function updateLocationDetails() {
      if (!locationData) return;

      const now = new Date();

      const date = now.toLocaleDateString("en-IN", {
        timeZone: "Asia/Kolkata",
        day: "2-digit",
        month: "short",
        year: "numeric"
      });

      const time = now.toLocaleTimeString("en-IN", {
        timeZone: "Asia/Kolkata",
        hour: "2-digit",
        minute: "2-digit",
        second: "2-digit",
        hour12: true
      });

      $("details").textContent =
        "Date: " + date +
        "\nTime: " + time +
        "\nLatitude: " + locationData.lat.toFixed(6) +
        "\nLongitude: " + locationData.lng.toFixed(6) +
        "\nGPS accuracy: approximately " +
        Math.round(locationData.accuracy) + " m" +
        "\nAddress: " +
        (locationData.address || "Address unavailable");
    }

    // STEP 2: OPEN CAMERA.
    async function startCamera() {
      if (!selectedEmployee()) {
        showStatus("Please select your name first.", "error");
        return;
      }

      if (!locationData) {
        showStatus(
          "Get your current location before opening the camera.",
          "error"
        );
        return;
      }

      if (
        !navigator.mediaDevices ||
        !navigator.mediaDevices.getUserMedia
      ) {
        showStatus(
          "Camera access is unavailable. Open the HTTPS website in a supported browser.",
          "error"
        );
        return;
      }

      stopCamera();

      $("cameraBtn").disabled = true;
      $("cameraBtn").textContent = "Opening camera...";

      try {
        stream = await navigator.mediaDevices.getUserMedia({
          audio: false,
          video: {
            facingMode: { ideal: "user" },
            width: { ideal: 1280 },
            height: { ideal: 960 }
          }
        });

        video.srcObject = stream;
        video.style.display = "block";
        canvas.style.display = "none";

        $("placeholder").classList.add("hidden");

        await video.play();

        $("captureBtn").disabled = false;
        $("retakeBtn").classList.add("hidden");
        $("submitBtn").disabled = true;

        capturedPhoto = null;

        $("cameraBtn").textContent = "Camera Open";

        showStatus(
          "Camera ready. Position your face and capture your selfie.",
          "success"
        );

      } catch (error) {
        showStatus(
          "Unable to open camera. " +
          (error.name || "") + " " +
          (error.message || "") +
          "\nCheck browser camera permissions.",
          "error"
        );

        $("cameraBtn").disabled = false;
        $("cameraBtn").textContent = "2. Open Camera";
      }
    }

    // STEP 3: CAPTURE SELFIE AND STAMP LOCATION DETAILS.
    function capturePhoto() {
      if (!stream || !locationData || !selectedEmployee()) {
        showStatus(
          "Select your name, capture location and open the camera first.",
          "error"
        );
        return;
      }

      if (!video.videoWidth || !video.videoHeight) {
        showStatus(
          "Camera is not ready yet. Please wait and retry.",
          "error"
        );
        return;
      }

      const person = selectedEmployee();
      const now = new Date();

      const date = now.toLocaleDateString("en-IN", {
        timeZone: "Asia/Kolkata",
        day: "2-digit",
        month: "short",
        year: "numeric"
      });

      const time = now.toLocaleTimeString("en-IN", {
        timeZone: "Asia/Kolkata",
        hour: "2-digit",
        minute: "2-digit",
        second: "2-digit",
        hour12: true
      });

      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;

      ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

      const scale = canvas.width / 640;
      const fontSize = Math.max(14, Math.round(16 * scale));
      const lineHeight = Math.round(fontSize * 1.5);
      const padding = Math.round(14 * scale);

      const lines = [
        "LEAPSTART ATTENDANCE",
        "Employee: " + person.name,
        "Role: " + person.role,
        "Date: " + date + " | Time: " + time,
        "Latitude: " + locationData.lat.toFixed(6),
        "Longitude: " + locationData.lng.toFixed(6),
        "GPS accuracy: approx. " +
          Math.round(locationData.accuracy) + " m",
        "Address: " +
          (locationData.address || "Address unavailable")
      ];

      ctx.font = fontSize + "px Arial";

      const maxTextWidth = canvas.width - padding * 2;
      const wrappedLines = [];

      lines.forEach(line => {
        let current = "";

        line.split(/\s+/).forEach(word => {
          const test = current ? current + " " + word : word;

          if (
            ctx.measureText(test).width > maxTextWidth &&
            current
          ) {
            wrappedLines.push(current);
            current = word;
          } else {
            current = test;
          }
        });

        if (current) wrappedLines.push(current);
      });

      const overlayHeight =
        padding * 2 + wrappedLines.length * lineHeight;

      const top = canvas.height - overlayHeight;

      ctx.fillStyle = "rgba(0,0,0,0.75)";
      ctx.fillRect(0, top, canvas.width, overlayHeight);

      ctx.fillStyle = "#fff";

      wrappedLines.forEach((line, index) => {
        ctx.font =
          (index === 0 ? "bold " : "") +
          fontSize + "px Arial";

        ctx.fillText(
          line,
          padding,
          top + padding +
            (index + 1) * lineHeight -
            Math.round(lineHeight * 0.2)
        );
      });

      capturedPhoto = canvas.toDataURL("image/jpeg", 0.68);

      video.style.display = "none";
      canvas.style.display = "block";

      stopCamera();

      $("cameraBtn").disabled = false;
      $("cameraBtn").textContent = "2. Open Camera";

      $("captureBtn").disabled = true;
      $("retakeBtn").classList.remove("hidden");
      $("submitBtn").disabled = false;

      showStatus(
        "Photo captured. Review it before submitting.",
        "success"
      );
    }

    function retakePhoto() {
      capturedPhoto = null;
      $("submitBtn").disabled = true;
      $("retakeBtn").classList.add("hidden");
      startCamera();
    }

    function stopCamera() {
      if (stream) {
        stream.getTracks().forEach(track => track.stop());
        stream = null;
      }

      video.srcObject = null;
    }

    // STEP 4: SUBMIT TO APPS SCRIPT.
    function submitAttendance() {
      const person = selectedEmployee();

      if (!person || !locationData || !capturedPhoto) {
        showStatus(
          "Select your name, capture location and take a selfie first.",
          "error"
        );
        return;
      }

      if (busy) return;

      if (capturedPhoto.length > 5 * 1024 * 1024) {
        showStatus(
          "Photo is too large. Retake the selfie and try again.",
          "error"
        );
        return;
      }

      busy = true;

      $("submitBtn").disabled = true;
      $("retakeBtn").disabled = true;
      $("employee").disabled = true;
      $("locationBtn").disabled = true;

      showStatus(
        "Submitting attendance and photo. Please wait..."
      );

      const payload = {
        name: person.name,
        role: person.role,
        lat: locationData.lat,
        lng: locationData.lng,
        accuracy: locationData.accuracy,
        address: locationData.address,
        photoData: capturedPhoto
      };

      $("payloadInput").value = JSON.stringify(payload);

      window.addEventListener(
        "message",
        handleSubmissionMessage
      );

      $("submitForm").submit();

      submitTimeout = window.setTimeout(() => {
        if (!busy) return;

        busy = false;

        window.removeEventListener(
          "message",
          handleSubmissionMessage
        );

        $("submitBtn").disabled = false;
        $("retakeBtn").disabled = false;
        $("employee").disabled = false;
        $("locationBtn").disabled = false;

        showStatus(
          "No confirmation was received. Check the Attendance sheet before retrying to avoid duplicate submissions.",
          "error"
        );
      }, 45000);
    }

    // RESPONSE HANDLER.
    function handleSubmissionMessage(event) {
      /*
        This checks the response format only.
        It does not authenticate the sender. A production deployment
        should use a more robust, verified response mechanism.
      */
      if (
        !event.data ||
        event.data.type !== "leapstart-attendance-result"
      ) {
        return;
      }

      window.removeEventListener(
        "message",
        handleSubmissionMessage
      );

      if (submitTimeout) {
        clearTimeout(submitTimeout);
        submitTimeout = null;
      }

      busy = false;

      const result = event.data.result || {};

      $("retakeBtn").disabled = false;

      if (!result.ok) {
        $("submitBtn").disabled = false;
        $("employee").disabled = false;
        $("locationBtn").disabled = false;

        showStatus(
          result.message ||
          "Attendance could not be recorded. Check the sheet before retrying.",
          "error"
        );

        return;
      }

      showStatus(
        "Attendance recorded successfully!\n" +
        "Employee: " + (result.name || "") +
        "\nDate: " + (result.date || "") +
        "\nTime: " + (result.time || "") +
        "\nPhoto saved to Google Drive.",
        "success"
      );

      $("submitBtn").disabled = true;
    }

    window.addEventListener("pagehide", stopCamera);
  </script>
</body>
</html>
