---
layout: post
title: Binary to Hexadecimal
type: hacks
courses: { compsci: {week: 31} }
---

<style>
    body { 
        font-family: Arial, sans-serif; 
        margin: 20px; 
    }
    input, button { 
        margin: 5px; 
        padding: 8px; 
        font-size: 16px; 
    }
    #history { 
        margin-top: 20px; 
    }
  </style>

  <h1>Binary to Hexadecimal Converter</h1>

  <input type="text" id="binaryInput" placeholder="Enter binary number">
  <button onclick="convertBinaryToHex()">Convert</button>
  <button onclick="showHistory()">Show History</button>

  <h2 id="result"></h2>

  <div id="history"></div>

  <script>
    function convertBinaryToHex() {
      const binaryStr = document.getElementById('binaryInput').value.trim();
      if (!/^[01]+$/.test(binaryStr)) {
        document.getElementById('result').textContent = "Invalid binary number.";
        return;
      }

      const decimal = parseInt(binaryStr, 2);
      const hexStr = decimal.toString(16).toUpperCase();

      document.getElementById('result').textContent = `Hexadecimal: ${hexStr}`;

      let history = JSON.parse(localStorage.getItem('conversionHistory')) || [];
      history.push({ binary: binaryStr, hexadecimal: hexStr });
      localStorage.setItem('conversionHistory', JSON.stringify(history));
    }

    function showHistory() {
      const historyDiv = document.getElementById('history');
      const history = JSON.parse(localStorage.getItem('conversionHistory')) || [];

      if (history.length === 0) {
        historyDiv.innerHTML = "<p>No history found.</p>";
        return;
      }

      let html = "<h3>Conversion History:</h3><ul>";
      history.forEach(item => {
        html += `<li>Binary: ${item.binary} ➔ Hexadecimal: ${item.hexadecimal}</li>`;
      });
      html += "</ul>";

      historyDiv.innerHTML = html;
    }
  </script>