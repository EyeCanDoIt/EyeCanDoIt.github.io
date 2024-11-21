---
title: Acupuncture Project
layout: template
filename: Acupuncture
---

**Acupuncture Project**

Acupuncture Project

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Interactive Acupuncture Map</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background-color: #f0f0f0;
    }

    .container {
      position: relative;
      width: 300px; /* Adjust as per the image dimensions */
      height: 600px; /* Adjust as per the image dimensions */
    }

    #human-body {
      width: 100%;
      height: 100%;
    }

    .acupuncture-point {
      position: absolute;
      width: 15px;
      height: 15px;
      background-color: red;
      border-radius: 50%;
      cursor: pointer;
      border: 2px solid white;
      transform: translate(-50%, -50%);
    }

    .acupuncture-point:hover {
      background-color: darkred;
    }

    .popup {
      position: absolute;
      padding: 10px;
      background-color: white;
      border: 1px solid black;
      border-radius: 5px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
      display: none;
      z-index: 10;
    }
  </style>
</head>
<body>
  <div class="container">
    <img id="human-body" src="https://EyeCanDoIt.github.io/Images/humanimage.png" alt="Human Body">
    <div class="acupuncture-point" data-info="This is research for Point A" style="top: 40%; left: 50%;"></div>
    <div class="acupuncture-point" data-info="This is research for Point B" style="top: 60%; left: 55%;"></div>
    <div class="popup" id="popup"></div>
  </div>

  <script>
    document.addEventListener('DOMContentLoaded', () => {
      const popup = document.getElementById('popup');
      const points = document.querySelectorAll('.acupuncture-point');

      points.forEach(point => {
        // Show popup on hover
        point.addEventListener('mouseover', (e) => {
          const info = e.target.getAttribute('data-info');
          popup.textContent = info;
          popup.style.display = 'block';
          popup.style.top = `${e.target.offsetTop - 50}px`;
          popup.style.left = `${e.target.offsetLeft + 20}px`;
        });

        // Hide popup when the mouse leaves the point
        point.addEventListener('mouseout', () => {
          popup.style.display = 'none';
        });

        // Show popup on click (optional, for mobile or additional behavior)
        point.addEventListener('click', (e) => {
          const info = e.target.getAttribute('data-info');
          popup.textContent = info;
          popup.style.display = 'block';
          popup.style.top = `${e.target.offsetTop - 50}px`;
          popup.style.left = `${e.target.offsetLeft + 20}px`;
        });
      });

      // Hide popup on body click (optional, for a better UX)
      document.body.addEventListener('click', (e) => {
        if (!e.target.classList.contains('acupuncture-point')) {
          popup.style.display = 'none';
        }
      });
    });
  </script>
</body>
</html>




<br>
**ACUPUNCTURE AS AN EFFECTIVE MODALITY FOR POST-AMPUTATION PAIN: A SYSTEMATIC REVIEW**
<br>
ACUPUNCTURE AS AN EFFECTIVE MODALITY FOR POST-AMPUTATION PAIN: A SYSTEMATIC REVIEW

><center><img src="https://EyeCanDoIt.github.io/Images/RCTCASPimg.png" alt=" " width="50%" height="50%"></center>

><center><img src="https://EyeCanDoIt.github.io/Images/systematicreviewCASPimage.png" alt=" " width="50%" height="50%"></center>

