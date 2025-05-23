<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Class Management Dashboard</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 0; display: flex; }
    .sidebar { width: 250px; background: #f4f4f4; padding: 20px; border-right: 1px solid #ccc; }
    .main { flex-grow: 1; padding: 20px; }
    table { width: 100%; border-collapse: collapse; margin-top: 20px; }
    th, td { border: 1px solid #ccc; padding: 10px; text-align: left; }
    #searchBox { padding: 10px; width: 300px; margin-bottom: 20px; }
    button { padding: 10px 20px; margin-left: 10px; }
    #notification { margin-top: 20px; padding: 10px; background: #e0ffe0; border: 1px solid #b2ffb2; }
    #roomFilter { padding: 10px; width: 150px; margin-left: 10px; }
  </style>
</head>
<body>
  <div class="sidebar">
    <h3>Class Alerts</h3>
    <div id="notification"></div>
  </div>
  <div class="main">
    <input type="text" id="searchBox" placeholder="Search for a course..." onkeyup="searchCourse()" />
    <select id="roomFilter" onchange="filterByRoom()">
      <option value="">All Rooms</option>
      <option value="101">Room 101</option>
      <option value="102">Room 102</option>
      <option value="103">Room 103</option>
    </select>
    <button onclick="addNewRow()">Add New Row</button>
    <button onclick="exportToExcel()">Export to Excel</button>
    <button onclick="exportToPDF()">Export to PDF</button>

    <table id="courseTable">
      <thead>
        <tr>
          <th>Course</th>
          <th>Instructor</th>
          <th>Days</th>
          <th>Time</th>
          <th>Fee</th>
          <th>Room No.</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr><td contenteditable="true">Bharatanatyam</td><td contenteditable="true">Meenakshi Rao</td><td contenteditable="true">Sat. & Sun.</td><td contenteditable="true">8.30 to 11.30am</td><td contenteditable="true">₹2800</td><td contenteditable="true">101</td><td><button onclick="alert('Changes saved!')">Save</button></td></tr>
        <tr><td contenteditable="true">Bharatanatyam</td><td contenteditable="true">Arupa Lahiry</td><td contenteditable="true">Sat. & Sun.</td><td contenteditable="true">4.30 to 5.30pm & 10.30 to 11.30am</td><td contenteditable="true">₹2500</td><td contenteditable="true">102</td><td><button onclick="alert('Changes saved!')">Save</button></td></tr>
        <tr><td contenteditable="true">Kathak (Advance)</td><td contenteditable="true">Deepti Gupta</td><td contenteditable="true">Tue. & Wed. Sat.</td><td contenteditable="true">5 to 6pm</td><td contenteditable="true">₹2800</td><td contenteditable="true">101</td><td><button onclick="alert('Changes saved!')">Save</button></td></tr>
        <tr><td contenteditable="true">Kathak (Beginners)</td><td contenteditable="true">Anjali Chauhan</td><td contenteditable="true">Tue. & Sat.</td><td contenteditable="true">4 to 5pm & 12 to 1pm</td><td contenteditable="true">₹2000</td><td contenteditable="true">103</td><td><button onclick="alert('Changes saved!')">Save</button></td></tr>
        <tr><td contenteditable="true">Odissi</td><td contenteditable="true">Subrata Panda</td><td contenteditable="true">Thur.</td><td contenteditable="true">3.30 to 5.30pm</td><td contenteditable="true">₹2000</td><td contenteditable="true">102</td><td><button onclick="alert('Changes saved!')">Save</button></td></tr>
        <tr><td contenteditable="true">Kathak (Beginner)</td><td contenteditable="true">Priyanka Shukla</td><td contenteditable="true">Sat. & Sun.</td><td contenteditable="true">11am to 12pm</td><td contenteditable="true">₹2000</td><td contenteditable="true">103</td><td><button onclick="alert('Changes saved!')">Save</button></td></tr>
      </tbody>
    </table>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.0/xlsx.full.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <script>
    function searchCourse() {
      let input = document.getElementById("searchBox").value.toLowerCase();
      let rows = document.querySelectorAll("#courseTable tbody tr");
      for (let i = 0; i < rows.length; i++) {
        let rowText = rows[i].innerText.toLowerCase();
        rows[i].style.display = rowText.includes(input) ? "" : "none";
      }
    }

    function filterByRoom() {
      const selectedRoom = document.getElementById("roomFilter").value;
      const rows = document.querySelectorAll("#courseTable tbody tr");
      rows.forEach(row => {
        const roomCell = row.cells[5].innerText.trim();
        if (!selectedRoom || roomCell === selectedRoom) {
          row.style.display = "";
        } else {
          row.style.display = "none";
        }
      });
    }

    function addNewRow() {
      const table = document.querySelector("#courseTable tbody");
      const newRow = document.createElement("tr");
      newRow.innerHTML = `
        <td contenteditable="true">New Course</td>
        <td contenteditable="true">Instructor</td>
        <td contenteditable="true">Days</td>
        <td contenteditable="true">Time</td>
        <td contenteditable="true">Fee</td>
        <td contenteditable="true">Room No.</td>
        <td><button onclick="alert('Changes saved!')">Save</button></td>
      `;
      table.appendChild(newRow);
    }

    function exportToExcel() {
      let table = document.getElementById("courseTable");
      let workbook = XLSX.utils.table_to_book(table, {sheet: "Courses"});
      XLSX.writeFile(workbook, "ClassSchedule.xlsx");
    }

    async function exportToPDF() {
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF();
      const table = document.getElementById("courseTable");
      let rows = Array.from(table.rows).map(row =>
        Array.from(row.cells).map(cell => cell.innerText.trim())
      );

      let startY = 10;
      rows.forEach((row, i) => {
        row.forEach((cell, j) => {
          doc.text(cell, 10 + j * 30, startY);
        });
        startY += 10;
      });

      doc.save("ClassSchedule.pdf");
    }

    function checkClassNotifications() {
      const now = new Date();
      const currentHour = now.getHours();
      const currentMinutes = now.getMinutes();
      const notification = document.getElementById("notification");
      let activeClasses = [];

      const rows = document.querySelectorAll("#courseTable tbody tr");
      rows.forEach(row => {
        const timeText = row.cells[3]?.innerText;
        if (timeText) {
          const ranges = timeText.split(/&|,/);
          ranges.forEach(range => {
            const [start, end] = range.trim().split(' to ');
            const startTime = parseTime(start);
            const endTime = parseTime(end);
            if (startTime && endTime) {
              const nowTime = currentHour * 60 + currentMinutes;
              if (nowTime >= startTime && nowTime <= endTime) {
                activeClasses.push(row.cells[0].innerText);
              }
            }
          });
        }
      });

      if (activeClasses.length > 0) {
        notification.innerText = 'Ongoing Classes: ' + [...new Set(activeClasses)].join(', ');
      } else {
        notification.innerText = 'No class is currently ongoing.';
      }
    }

    function parseTime(str) {
      const match = str.trim().match(/(\d+)(?:\.(\d+))?\s*(am|pm)/i);
      if (!match) return null;
      let hour = parseInt(match[1]);
      let minute = parseInt(match[2] || '0') * (match[2]?.length === 1 ? 10 : 1);
      const ampm = match[3].toLowerCase();
      if (ampm === 'pm' && hour !== 12) hour += 12;
      if (ampm === 'am' && hour === 12) hour = 0;
      return hour * 60 + minute;
    }

    setInterval(checkClassNotifications, 60000);
    window.onload = checkClassNotifications;
  </script>
</body>
</html>
