
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
      box-shadow: 0 8px 28px #18344b0b;
    }

    label {
      display: block;
      font-size: 14px;
      font-weight: bold;
      margin-bottom: 8px;
    }

    select,
    button {
      font: inherit;
      width: 100%;
      padding: 13px;
      border-radius: 9px;
    }

    select {
      border: 1px solid #cbd7e2;
      background: #fff;
      margin-bottom: 15px;
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

    video,
    canvas {
      width: 100%;
      height: auto;
      display: block;
    }

    video {
      display: none;
    }

    canvas {
      display: none;
      background: #101820;
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
  </style>
</head>

<body>
  <div class="container">

    <div class="header">
      <h1>LeapStart Attendance</h1>
      <p>
        Capture a live selfie and submit your attendance with GPS details.
      </p>
    </div>

    <section class="card">
      <label for="employee">Select employee</label>

      <select id="employee">
        <option value="">-- Select your name --</option>
      </select>

      <button id="locationBtn" onclick="getLocation()">
        1. Get Current Location
      </button>

      <div id="details" class="details">Date: Not captured
Time: Not captured
Location: Not captured</div>

      <button
        id="cameraBtn"
        class="secondary"
        onclick="startCamera()"
        disabled>
        2. Open Camera
      </button>

      <p class="required-note">
        Allow camera and location access when prompted.
        Camera access requires HTTPS and a supported browser.
      </p>
    </section>

    <section class="card">
      <h3>Live camera and geotagged photo</h3>

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
        onclick="capturePhoto()"
        disabled>
        3. Capture Selfie
      </button>

      <button
        id="retakeBtn"
        class="secondary hidden"
        onclick="retakePhoto()">
        Retake Photo
      </button>

      <button
        id="submitBtn"
        onclick="submitAttendance()"
        disabled>
        4. Submit Attendance
      </button>

      <div
        id="status"
        class="status"
        role="status"
        aria-live="polite">
      </div>
    </section>

    <p class="small">
      Your photo and attendance information will be submitted to the
      LeapStart attendance system. Take only your own photo and follow
      your organization's attendance policy.
    </p>

    <iframe
      id="submitFrame"
      name="submitFrame"
      title="Attendance submission response"
      class="hidden">
    </iframe>

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

  </div>

  <script>
    // Your deployed Google Apps Script web app URL.
    const SCRIPT_URL =
      "https://script.google.com/macros/s/AKfycbyQSIir9sTPtzlncX0Thktz8lJcYWu5x1DlThpbAjbIodGXBXxx9_Ap168WXSw4O_tq/exec";

    // Replace these examples with your actual employee names and roles.
    const employees = [
      ["Aarav Reddy", "Mentor"],
      ["Priya Sharma", "Operations"],
      ["Rohit Verma", "Software Engineer"],
      ["Ananya Iyer", "Data Scientist"],
      ["Karthik Naidu", "Tech Lead"],
      ["Sneha Kapoor", "HR Executive"],
      ["Vikram Singh", "Admissions"],
      ["Meera Nair", "QA Engineer"],
      ["Arjun Patel", "Designer"],
      ["Divya Menon", "Accounts"]
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

    // Populate employee dropdown.
    employees.forEach((person, index) => {
      const option = document.createElement("option");
      option.value = String(index);
      option.textContent = person[0] + " — " + person[1];
      $("employee").appendChild(option);
    });

    $("employee").addEventListener("change", () => {
      capturedPhoto = null;

      $("submitBtn").disabled = true;
      $("retakeBtn").classList.add("hidden");

      canvas.style.display = "none";
      $("placeholder").classList.remove("hidden");

      if ($("employee").value === "") {
        $("cameraBtn").disabled = true;
        $("captureBtn").disabled = true;
        stopCamera();
      } else {
        $("cameraBtn").disabled = !locationData;
        $("status").textContent = "";
      }
    });

    function selectedEmployee() {
      const index = $("employee").value;

      return index === ""
        ? null
        : employees[Number(index)];
    }

    function showStatus(message, type) {
      $("status").textContent = message;
      $("status").className = "status " + (type || "");
    }

    // STEP 1: Get current GPS location.
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
                "GPS returned invalid coordinates or accuracy."
              );
            }

            locationData = {
              lat: c.latitude,
              lng: c.longitude,
              accuracy: c.accuracy,
              address: ""
            };

            showStatus("Getting your location address...");

            const url =
              "https://nominatim.openstreetmap.org/reverse?format=jsonv2&lat=" +
              encodeURIComponent(c.latitude) +
              "&lon=" +
              encodeURIComponent(c.longitude);

            try {
              const response = await fetch(url);

              if (response.ok) {
                const result = await response.json();
                locationData.address =
                  result.display_name || "";
              }
            } catch (error) {
              // Coordinates can still be submitted if address lookup fails.
            }

            updateLocationDetails();

            $("cameraBtn").disabled = !selectedEmployee();
            $("locationBtn").textContent = "Location Captured";

            showStatus(
              "Location captured. Next, open the camera.",
              "success"
            );

          } catch (error) {
            showStatus(
              error.message ||
              "Could not process your location. Please retry.",
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
            messages[error.code] ||
            "Could not get your location.",
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

    // STEP 2: Open camera.
    async function startCamera() {
      if (!selectedEmployee()) {
        showStatus("Please select your name first.", "error");
        return;
      }

      if (!locationData) {
        showStatus(
          "Get your location before opening the camera.",
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
        const detail =
          (error && error.name ? error.name + ": " : "") +
          (error && error.message
            ? error.message
            : "Unknown camera error");

        showStatus(
          "Unable to open camera. " + detail +
          "\nCheck browser camera permissions and close other apps using the camera.",
          "error"
        );

        $("cameraBtn").disabled = false;
        $("cameraBtn").textContent = "2. Open Camera";
      }
    }

    // STEP 3: Capture selfie and add a location stamp.
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

      ctx.drawImage(
        video,
        0,
        0,
        canvas.width,
        canvas.height
      );

      const scale = canvas.width / 640;
      const fontSize = Math.max(14, Math.round(16 * scale));
      const lineHeight = Math.round(fontSize * 1.5);
      const padding = Math.round(14 * scale);

      const lines = [
        "LEAPSTART ATTENDANCE",
        "Employee: " + person[0],
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
      ctx.fillRect(
        0,
        top,
        canvas.width,
        overlayHeight
      );

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
        "Photo captured with a visible location stamp. Review it before submitting.",
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

    // STEP 4: Submit attendance to Apps Script.
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
        "Submitting photo and attendance details. Please wait..."
      );

      const payload = {
        name: person[0],
        role: person[1],
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
        if (busy) {
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
            "No confirmation was received. Check the Attendance sheet before retrying to avoid a duplicate submission.",
            "error"
          );
        }
      }, 45000);
    }

    // UPDATED RESPONSE HANDLER
    function handleSubmissionMessage(event) {
      // Accept only the attendance response message.
      // Apps Script can redirect the form response across Google domains,
      // so checking event.source against the iframe may reject a valid reply.
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
          "Attendance could not be recorded. Please check the sheet before retrying.",
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

    // Stop camera when leaving the page.
    window.addEventListener("pagehide", stopCamera);
  </script>
</body>
</html>
