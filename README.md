<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Beautiful Calendar</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(to right, #6dd5ed, #2193b0);
      color: #fff;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      min-height: 100vh;
      padding: 20px;
    }

    .calendar {
      background: rgba(255, 255, 255, 0.1);
      padding: 25px;
      border-radius: 15px;
      box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
      backdrop-filter: blur(10px);
      -webkit-backdrop-filter: blur(10px);
      border: 1px solid rgba(255, 255, 255, 0.18);
      width: 90%;
      max-width: 700px;
    }

    .calendar h2 {
      text-align: center;
      margin-bottom: 20px;
    }

    .controls {
      text-align: center;
      margin-bottom: 10px;
    }

    .controls button {
      background-color: #ffffff20;
      color: white;
      border: none;
      padding: 10px 15px;
      margin: 0 5px;
      border-radius: 10px;
      cursor: pointer;
      font-weight: bold;
      transition: background-color 0.3s;
    }

    .controls button:hover {
      background-color: #ffffff40;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
    }

    th, td {
      width: 14.2%;
      height: 60px;
      text-align: center;
      border: 1px solid rgba(255, 255, 255, 0.2);
      position: relative;
      color: white;
    }

    th {
      background-color: #ffffff20;
    }

    td {
      background-color: rgba(255, 255, 255, 0.05);
    }

    td.today {
      background-color: rgba(255, 255, 255, 0.3);
      font-weight: bold;
    }

    .event {
      font-size: 0.7rem;
      color: #ffe100;
      position: absolute;
      bottom: 2px;
      left: 4px;
    }

    .weather {
      margin-top: 20px;
      font-size: 1.1rem;
      color: #f0f0f0;
    }
  </style>
</head>
<body>
  <div class="calendar">
    <h2 id="monthYear"></h2>
    <div class="controls">
      <button onclick="changeMonth(-1)">&lt; Prev</button>
      <button onclick="changeMonth(1)">Next &gt;</button>
    </div>
    <table>
      <thead>
        <tr>
          <th>Sun</th>
          <th>Mon</th>
          <th>Tue</th>
          <th>Wed</th>
          <th>Thu</th>
          <th>Fri</th>
          <th>Sat</th>
        </tr>
      </thead>
      <tbody id="calendarBody"></tbody>
    </table>
  </div>
  <div class="weather" id="weather">Loading temperature...</div>

  <script>
    const calendarBody = document.getElementById("calendarBody");
    const monthYear = document.getElementById("monthYear");
    const weatherDiv = document.getElementById("weather");

    let today = new Date();
    let currentMonth = today.getMonth();
    let currentYear = today.getFullYear();

    const monthNames = [
      "January", "February", "March", "April", "May", "June",
      "July", "August", "September", "October", "November", "December"
    ];

    const events = {
      '2025-06-06': 'Eid-ul-Adha',
      '2025-06-20': 'Exam Day'
    };

    function generateCalendar(month, year) {
      calendarBody.innerHTML = "";
      monthYear.textContent = `${monthNames[month]} ${year}`;

      const firstDay = new Date(year, month, 1).getDay();
      const daysInMonth = new Date(year, month + 1, 0).getDate();

      let date = 1;
      for (let i = 0; i < 6; i++) {
        let row = document.createElement("tr");

        for (let j = 0; j < 7; j++) {
          let cell = document.createElement("td");
          if (i === 0 && j < firstDay) {
            cell.textContent = "";
          } else if (date > daysInMonth) {
            break;
          } else {
            cell.textContent = date;
            let eventDate = `${year}-${(month+1).toString().padStart(2, '0')}-${date.toString().padStart(2, '0')}`;
            if (events[eventDate]) {
              let event = document.createElement("div");
              event.className = "event";
              event.textContent = events[eventDate];
              cell.appendChild(event);
            }
            if (
              date === today.getDate() &&
              month === today.getMonth() &&
              year === today.getFullYear()
            ) {
              cell.classList.add("today");
            }
            date++;
          }
          row.appendChild(cell);
        }
        calendarBody.appendChild(row);
      }
    }

    function changeMonth(offset) {
      currentMonth += offset;
      if (currentMonth < 0) {
        currentMonth = 11;
        currentYear--;
      } else if (currentMonth > 11) {
        currentMonth = 0;
        currentYear++;
      }
      generateCalendar(currentMonth, currentYear);
    }

    function getTemperature() {
      weatherDiv.textContent = "Fetching temperature...";
      fetch("https://api.open-meteo.com/v1/forecast?latitude=23.81&longitude=90.41&current_weather=true")
        .then(res => res.json())
        .then(data => {
          const temp = data.current_weather.temperature;
          weatherDiv.textContent = `Current Temperature: ${temp}°C`;
        })
        .catch(err => {
          weatherDiv.textContent = "Could not load temperature.";
        });
    }

    generateCalendar(currentMonth, currentYear);
    getTemperature();
  </script>
</body>
</html>
