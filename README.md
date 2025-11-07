<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Weather App</title>
  <style>
    body {
      font-family: "Poppins", sans-serif;
      background: linear-gradient(135deg, #4f8ef7, #3540a1);
      color: white;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
      overflow: hidden;
    }

    .container {
      background: rgba(255, 255, 255, 0.1);
      backdrop-filter: blur(12px);
      border-radius: 15px;
      padding: 20px;
      width: 380px;
      height: 85vh;
      max-height: 600px;
      box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
      text-align: center;
      overflow: hidden;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }

    h1 {
      margin-bottom: 10px;
      font-size: 1.6em;
    }

    input {
      width: 80%;
      padding: 8px;
      border: none;
      border-radius: 5px;
      outline: none;
      font-size: 14px;
    }

    button {
      padding: 8px 16px;
      border: none;
      border-radius: 5px;
      background-color: #2636a4;
      color: white;
      cursor: pointer;
      font-size: 14px;
      transition: 0.3s;
      margin-top: 8px;
    }

    button:hover {
      background-color: #17226b;
    }

    .result {
      margin-top: 15px;
      flex-grow: 1;
      overflow: hidden;
    }

    .temp {
      font-size: 1.8em;
      font-weight: bold;
      margin: 5px 0;
    }

    .condition {
      font-size: 1em;
      margin: 4px 0;
    }

    .forecast {
      display: flex;
      overflow-x: auto;
      justify-content: flex-start;
      gap: 8px;
      padding-bottom: 10px;
      margin-top: 10px;
    }

    .forecast::-webkit-scrollbar {
      display: none;
    }

    .day {
      background: rgba(255, 255, 255, 0.15);
      padding: 8px;
      border-radius: 10px;
      width: 70px;
      flex-shrink: 0;
    }

    .day img {
      width: 40px;
      height: 40px;
    }

    .error {
      color: #ffcccc;
      font-size: 0.9em;
    }

    h2 {
      font-size: 1.2em;
      margin-bottom: 5px;
    }

    h3 {
      font-size: 1em;
      margin: 10px 0 5px 0;
    }

    @media (max-width: 400px) {
      .container {
        width: 90%;
        height: 90vh;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <div>
      <h1>🌤️ Weather App</h1>
      <input type="text" id="cityInput" placeholder="Enter city name" />
      <br />
      <button onclick="getWeather()">Get Weather</button>
    </div>
    <div id="result" class="result"></div>
  </div>

  <script>
    async function getWeather() {
      const city = document.getElementById("cityInput").value.trim();
      const resultDiv = document.getElementById("result");
      const apiKey = "b33151c3b26c43e98df191235250711";

      if (!city) {
        resultDiv.innerHTML = "<p class='error'>Please enter a city name.</p>";
        return;
      }

      const url = `http://api.weatherapi.com/v1/forecast.json?key=${apiKey}&q=${city}&days=5&aqi=yes&alerts=no`;

      try {
        resultDiv.innerHTML = "<p>Loading...</p>";
        const response = await fetch(url);
        if (!response.ok) throw new Error("City not found");

        const data = await response.json();
        const current = data.current;
        const forecast = data.forecast.forecastday;

        let html = `
          <h2>${data.location.name}, ${data.location.country}</h2>
          <img src="https:${current.condition.icon}" alt="Weather icon">
          <p class="temp">${current.temp_c}°C</p>
          <p class="condition">${current.condition.text}</p>
          <p>💧 ${current.humidity}% | 🌬️ ${current.wind_kph} km/h</p>
          <h3>Next 5 Days</h3>
          <div class="forecast">
        `;

        forecast.forEach(day => {
          const date = new Date(day.date);
          const dayName = date.toLocaleDateString(undefined, { weekday: "short" });
          html += `
            <div class="day">
              <p><strong>${dayName}</strong></p>
              <img src="https:${day.day.condition.icon}" alt="icon">
              <p>${day.day.avgtemp_c}°C</p>
            </div>
          `;
        });

        html += `</div>`;
        resultDiv.innerHTML = html;

      } catch (error) {
        resultDiv.innerHTML = `<p class="error">${error.message}</p>`;
      }
    }
  </script>
</body>
</html>
