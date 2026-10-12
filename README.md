
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>LeapStart | Employee Attendance</title>

  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">

  <style>
    :root {
      --teal: #0f4d68;
      --blue: #3bb3e3;
      --orange: #f5b95a;
      --text: #5b6470;
      --line: #e5eaf0;
      --success: #15803d;
      --danger: #dc2626;
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      font-family: "Inter", system-ui, sans-serif;
      color: var(--text);
      background: linear-gradient(180deg, #e9f6fc 0, #fff 380px) no-repeat #fff;
    }

    nav {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 16px;
      padding: 14px 4vw;
      background: #fff;
      box-shadow: 0 2px 14px rgba(15,77,104,.05);
    }

    .logo {
      display: flex;
      align-items: center;
      gap: 8px;
      text-decoration: none;
      flex-shrink: 0;
    }

    .logo b {
      display: block;
      font-size: 27px;
      font-weight: 800;
      color: var(--teal);
    }

    .logo small {
      display: block;
      font-size: 8px;
      letter-spacing: .25em;
      color: var(--teal);
      margin-top: 3px;
    }

    .links {
      display: flex;
      gap: 20px;
      font-size: 14px;
    }

    .links a {
      color: var(--teal);
      text-decoration: none;
    }

    .btn {
      font: inherit;
      font-weight: 600;
      border: 0;
      cursor: pointer;
      color: #fff;
      background: var(--teal);
      padding: 12px 22px;
      border-radius: 999px;
      text-decoration: none;
      font-size: 14px;
      box-shadow: 0 8px 18px rgba(15,77,104,.18);
    }

    .btn:disabled {
      opacity: .65;
      cursor: wait;
    }

    header {
      text-align: center;
      padding: 48px 18px 24px;
    }

    .badge {
      display: inline-flex;
      gap: 8px;
      align-items: center;
      border: 1.5px solid #8fd3ee;
      background: #dff3fb;
      color: #111;
      border-radius: 999px;
      padding: 9px 20px;
      font-size: 14px;
    }

    h1 {
      margin: 26px 0 12px;
      color: var(--teal);
      font-weight: 700;
      font-size: clamp(34px, 7vw, 68px);
      line-height: 1.12;
    }

    h1 .gradient {
      background: linear-gradient(90deg, var(--blue), #b9bf9c 50%, var(--orange));
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
    }

    .sub {
      font-size: clamp(16px, 2.2vw, 20px);
      line-height: 1.6;
      max-width: 760px;
      margin: 0 auto;
    }

    .sub em {
      font-style: normal;
      color: #bd821d;
      font-weight: 600;
    }

    .clock {
      margin-top: 22px;
      color: var(--teal);
    }

    .clock strong {
      display: block;
      font-size: 32px;
    }

    main {
      max-width: 1200px;
      margin: 0 auto;
      padding: 10px 16px 50px;
    }

    .card {
      background: #fff;
      border: 1px solid var(--line);
      border-radius: 20px;
      padding: 22px;
      box-shadow: 0 14px 40px rgba(15,77,104,.08);
    }

    .bar {
      display: flex;
      gap: 12px;
      flex-wrap: wrap;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 18px;
    }

    input {
      font: inherit;
      padding: 13px 18px;
      border: 1px solid var(--line);
      border-radius: 999px;
      width: 100%;
      max-width: 480px;
      color: var(--teal);
      outline: none;
    }

    input:focus { border-color: var(--blue); }

    .hint { font-size: 13px; }

    .wrap { overflow-x: auto; }

    table {
      width: 100%;
      border-collapse: collapse;
      min-width: 760px;
    }

    th, td {
      text-align: left;
      padding: 14px 12px;
      border: 1px solid #dce2e8;
      font-size: 14px;
      vertical-align: middle;
    }

    th {
      font-size: 12px;
      letter-spacing: .06em;
      color: var(--teal);
      background: #f8fafc;
    }

    tbody tr:nth-child(even) { background: #f5f7f9; }
    td strong { color: var(--teal); }

    .ok {
      display: inline-block;
      background: #e6f7ee;
      color: var(--success);
      border-radius: 999px;
      padding: 7px 12px;
      font-weight: 600;
      font-size: 12px;
    }

    .loc {
      font-size: 12px;
      max-width: 280px;
      overflow-wrap: anywhere;
    }

    .loc a { color: #087ca7; }

    .err {
      color: var(--danger);
      font-size: 12px;
      line-height: 1.5;
    }

    #toast {
      position: fixed;
      left: 50%;
      bottom: 24px;
      transform: translateX(-50%) translateY(30px);
      background: var(--teal);
      color: #fff;
      padding: 16px 22px;
      border-radius: 16px;
      box-shadow: 0 14px 40px rgba(0,0,0,.25);
      opacity: 0;
      pointer-events: none;
      transition: .3s;
      max-width: 92vw;
      font-size: 14px;
      line-height: 1.5;
      z-index: 9999;
    }

    #toast.show {
      opacity: 1;
      transform: translateX(-50%) translateY(0);
    }

    #toast b {
      display: block;
      font-size: 16px;
      color: var(--orange);
    }

    footer {
      text-align: center;
      font-size: 12px;
      padding: 18px;
    }

    @media (max-width: 860px) {
      .links { display: none; }
      nav { padding: 12px 18px; }
    }

    @media (max-width: 600px) {
      header { padding-top: 32px; }
      .card { padding: 12px; }
      .bar { align-items: stretch; }
      input { max-width: 100%; }
      .hint { text-align: center; }
    }
  </style>
</head>

<body>

  <nav>
    <a class="logo" href="https://leapstart.in" aria-label="LeapStart School of Technology">
      <svg width="44" height="48" viewBox="0 0 44 48" aria-hidden="true">
        <path d="M4 6l18-4 18 4v20c0 10-8 17-18 20C12 43 4 36 4 26z"
          fill="#fff" stroke="#0f4d68" stroke-width="3"/>
        <path d="M14 30l12-16M26 14h-8M26 14v8"
          fill="none" stroke="#0f4d68" stroke-width="3.2"
          stroke-linecap="round" stroke-linejoin="round"/>
        <path d="M10 12l8-2" stroke="#f5b95a" stroke-width="3" stroke-linecap="round"/>
      </svg>
      <span>
        <b>LEAPSTART</b>
        <small>SCHOOL OF TECHNOLOGY</small>
      </span>
    </a>

    <div class="links">
      <a href="https://leapstart.in/about">About</a>
      <a href="https://leapstart.in/programs">Programs</a>
      <a href="https://leapstart.in/school-of-skills">Skills</a>
      <a href="https://leapstart.in/admissions">Admissions</a>
      <a href="https://leapstart.in/faq">FAQ</a>
    </div>

    <a class="btn" href="https://leapstart.in/contact">Contact us</a>
  </nav>

  <header>
    <span class="badge">
      ✨ Employee Attendance · <span id="badgeDate"></span>
    </span>

    <h1>
      Mark Your<br>
      <span class="gradient">Daily</span> Attendance
    </h1>

    <p class="sub">
      Find your name, tap <em>Check-in</em> and allow location access.
      Your <em>date, time and location</em> are submitted for attendance.
    </p>

    <div class="clock">
      <strong id="time">--:--:--</strong>
      <span id="date"></span>
    </div>
  </header>

  <main>
    <div class="card">
      <div class="bar">
        <input id="q" type="search" placeholder="Search your name..."
          autocomplete="off" aria-label="Search employee name">
        <span class="hint">Location permission is required to check in.</span>
      </div>

      <div class="wrap">
        <table>
          <thead>
            <tr>
              <th>Employee</th>
              <th>Role</th>
              <th>Date</th>
              <th>Time</th>
              <th>Location</th>
              <th>Attendance</th>
            </tr>
          </thead>
          <tbody id="rows"></tbody>
        </table>
      </div>
    </div>
  </main>

  <footer>© 2026 LeapStart School of Technology. All rights reserved.</footer>
  <div id="toast" role="status" aria-live="polite"></div>

  <script>
    // YOUR GOOGLE APPS SCRIPT WEB APP URL
    var SCRIPT_URL =
      "https://script.google.com/macros/s/AKfycbyGA87BMSgqzQwsqaih70npcB4uCXA0cMqqtlan_fWxsvA51CUL5ZBKGVm0mPa0RdVp/exec";

    // Replace these sample names with your actual employee list.
    var people = [
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

    var tableBody = document.getElementById("rows");
    var searchBox = document.getElementById("q");
    var toast = document.getElementById("toast");

    function esc(value) {
      return String(value).replace(/[&<>"']/g, function (c) {
        return {
          "&": "&amp;",
          "<": "&lt;",
          ">": "&gt;",
          '"': "&quot;",
          "'": "&#39;"
        }[c];
      });
    }

    function today() {
      return new Date().toLocaleDateString("en-CA", {
        timeZone: "Asia/Kolkata"
      });
    }

    var storageKey = "ls_att_" + today();
    var records = {};

    try {
      records = JSON.parse(localStorage.getItem(storageKey) || "{}");
    } catch (error) {
      records = {};
    }

    function saveRecords() {
      try {
        localStorage.setItem(storageKey, JSON.stringify(records));
      } catch (error) {
        console.warn("Could not save local attendance cache.", error);
      }
    }

    function updateClock() {
      var now = new Date();
      var opts = { timeZone: "Asia/Kolkata" };

      document.getElementById("time").textContent =
        now.toLocaleTimeString("en-IN", Object.assign({
          hour: "2-digit",
          minute: "2-digit",
          second: "2-digit",
          hour12: true
        }, opts));

      document.getElementById("date").textContent =
        now.toLocaleDateString("en-IN", Object.assign({
          weekday: "long",
          day: "numeric",
          month: "long",
          year: "numeric"
        }, opts));

      document.getElementById("badgeDate").textContent =
        now.toLocaleDateString("en-IN", Object.assign({
          day: "2-digit",
          month: "short",
          year: "numeric"
        }, opts));
    }

    setInterval(updateClock, 1000);
    updateClock();

    function locationHTML(record) {
      var address = esc(record.address || "");
      var coordinates =
        Number(record.lat).toFixed(5) + ", " +
        Number(record.lng).toFixed(5);

      var mapURL =
        "https://maps.google.com/?q=" +
        encodeURIComponent(record.lat + "," + record.lng);

      return (
        (address ? address + "<br>" : "") +
        "<a target='_blank' rel='noopener noreferrer' href='" +
        mapURL + "'>" + coordinates + "</a>"
      );
    }

    function render() {
      var query = searchBox.value.trim().toLowerCase();
      tableBody.innerHTML = "";

      people.forEach(function (person, index) {
        var name = person[0];
        var role = person[1];

        if (query && name.toLowerCase().indexOf(query) === -1) {
          return;
        }

        var record = records[name];
        var row = document.createElement("tr");

        var locationContent = record ? locationHTML(record) : "—";

        var attendanceContent = record
          ? "<span class='ok'>✓ Logged in</span>"
          : "<button class='btn' data-index='" + index + "'>Check-in</button>";

        row.innerHTML =
          "<td><strong>" + esc(name) + "</strong></td>" +
          "<td>" + esc(role) + "</td>" +
          "<td>" + (record ? esc(record.date) : "—") + "</td>" +
          "<td>" + (record ? esc(record.time) : "—") + "</td>" +
          "<td class='loc' id='location-" + index + "'>" +
          locationContent + "</td>" +
          "<td>" + attendanceContent + "</td>";

        tableBody.appendChild(row);
      });
    }

    function showMessage(title, message) {
      toast.replaceChildren();

      var heading = document.createElement("b");
      heading.textContent = title;
      toast.appendChild(heading);

      var content = document.createElement("div");
      content.textContent = message;
      toast.appendChild(content);

      toast.className = "show";

      clearTimeout(showMessage.timer);
      showMessage.timer = setTimeout(function () {
        toast.className = "";
      }, 7000);
    }

    function getAddress(latitude, longitude) {
      var controller = new AbortController();
      var timer = setTimeout(function () {
        controller.abort();
      }, 8000);

      var url =
        "https://nominatim.openstreetmap.org/reverse" +
        "?format=jsonv2&lat=" +
        encodeURIComponent(latitude) +
        "&lon=" +
        encodeURIComponent(longitude);

      return fetch(url, { signal: controller.signal })
        .then(function (response) {
          if (!response.ok) {
            throw new Error("Address lookup failed.");
          }
          return response.json();
        })
        .then(function (data) {
          return data.display_name || "";
        })
        .catch(function () {
          // Attendance can still be submitted with coordinates.
          return "";
        })
        .finally(function () {
          clearTimeout(timer);
        });
    }

    function submitAttendance(person, position, button, locationCell) {
      var name = person[0];
      var role = person[1];
      var coords = position.coords;

      var latitude = coords.latitude;
      var longitude = coords.longitude;
      var accuracy = Math.round(coords.accuracy);

      button.textContent = "Getting address...";

      getAddress(latitude, longitude)
        .then(function (address) {
          button.textContent = "Recording...";

          var payload = {
            name: name,
            role: role,
            lat: latitude,
            lng: longitude,
            accuracy: accuracy,
            address: address
          };

          return fetch(SCRIPT_URL, {
            method: "POST",
            headers: {
              "Content-Type": "text/plain;charset=utf-8"
            },
            body: JSON.stringify(payload)
          }).then(function (response) {
            if (!response.ok) {
              throw new Error("Server returned HTTP " + response.status);
            }
            return response.json();
          }).then(function (result) {
            if (!result.ok) {
              throw new Error(result.error || "Attendance was not saved.");
            }

            // CORRECTED: save the actual address with the attendance record.
            records[name] = {
              date: result.date,
              time: result.time,
              lat: latitude,
              lng: longitude,
              accuracy: accuracy,
              address: address
            };

            saveRecords();
            render();

            showMessage(
              result.duplicate ? "Already checked in today" : "Attendance recorded",
              name + " · " + result.date + " · " + result.time
            );
          });
        })
        .catch(function (error) {
          console.error("Attendance submission failed:", error);

          button.disabled = false;
          button.textContent = "Check-in";

          locationCell.textContent =
            "Attendance could not be confirmed. Check Apps Script deployment and Executions, then retry.";
          locationCell.className = "loc err";
        });
    }

    tableBody.addEventListener("click", function (event) {
      var button = event.target.closest("button");
      if (!button) return;

      var index = Number(button.dataset.index);
      var person = people[index];
      var locationCell = document.getElementById("location-" + index);

      if (!SCRIPT_URL || SCRIPT_URL.indexOf("https://script.google.com/") !== 0) {
        locationCell.textContent = "Google Apps Script URL is not configured.";
        locationCell.className = "loc err";
        return;
      }

      if (!navigator.geolocation) {
        locationCell.textContent = "Geolocation is not supported by this browser.";
        locationCell.className = "loc err";
        return;
      }

      button.disabled = true;
      button.textContent = "Requesting location...";
      locationCell.textContent = "Waiting for location permission...";

      navigator.geolocation.getCurrentPosition(
        function (position) {
          submitAttendance(person, position, button, locationCell);
        },
        function (error) {
          button.disabled = false;
          button.textContent = "Check-in";

          var message = "Could not get your location. Please try again.";
          if (error.code === 1) {
            message = "Location access denied. Allow location access in your browser settings.";
          } else if (error.code === 2) {
            message = "Your location could not be determined. Check device location settings.";
          } else if (error.code === 3) {
            message = "Location request timed out. Please try again.";
          }

          locationCell.textContent = message;
          locationCell.className = "loc err";
        },
        {
          enableHighAccuracy: true,
          timeout: 20000,
          maximumAge: 0
        }
      );
    });

    searchBox.addEventListener("input", render);
    render();
  </script>

</body>
</html>
