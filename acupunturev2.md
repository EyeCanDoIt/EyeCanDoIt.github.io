---
title: Acupuncture Project
layout: template
filename: acupuncturev2
---

<center>**Acupuncture Project**</center>

<center>Acupuncture Map Version 0.001</center>

<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Interactive Acupuncture Map</title>
  <style>
    .container {
      position: relative;
      width: 60%; /* Adjust based on your image size */
      margin: 0 auto;
    }
    
    #human-body {
      width: 100%; /* Make image responsive */
      height: auto;
    }

    .acupuncture-point {
      position: absolute;
      width: 15px;
      height: 15px;
      background-color: red;
      border-radius: 50%;
      cursor: pointer;
    }

    .popup {
      position: absolute;
      padding: 10px;
      background-color: white;
      border: 1px solid black;
      border-radius: 5px;
      display: none;
      z-index: 10;
    }
  </style>
</head>
<body>
  <div class="container">
    <img id="human-body" src="https://EyeCanDoIt.github.io/Images/humanimage.png" alt="Human Body">
    
    <!-- Existing Acupuncture Points -->
    <div class="acupuncture-point" data-info="Peng et al. 2020 - 12 right handed males treated with electroacupuncture at TE5 acupunture point (posterior forearm) which demonstrated increased neuroplasticity on transcranial magnetic stimulation." style="top: 50%; left: 80%;"></div>
    
    <!-- Additional Acupuncture Points -->
    <!-- Hand -->
    
    <!-- Foot -->
    <div class="acupuncture-point" data-info="This is research for Foot Point" style="top: 92%; left: 60%;"></div>
    
    <!-- Ear -->
    <div class="acupuncture-point" data-info="Yurasek et al. - Bethesda Naval Academy Hospital successful treatment of Marine amputees suffering from phantom limb pain using gold ASP needles at ear kidney point." style="top: 10%; left: 40%;"></div>
    
    <div class="popup" id="popup"></div>
  </div>

  <script>
    document.addEventListener('DOMContentLoaded', () => {
      const popup = document.querySelector('.popup');
      const points = document.querySelectorAll('.acupuncture-point');

      points.forEach(point => {
        point.addEventListener('mouseover', (e) => {
          const info = e.target.getAttribute('data-info');
          popup.textContent = info;
          popup.style.display = 'block';
          popup.style.top = `${e.target.offsetTop - 50}px`;
          popup.style.left = `${e.target.offsetLeft + 20}px`;
        });

        point.addEventListener('mouseout', () => {
          popup.style.display = 'none';
        });

        point.addEventListener('click', (e) => {
          const info = e.target.getAttribute('data-info');
          popup.textContent = info;
          popup.style.display = 'block';
          popup.style.top = `${e.target.offsetTop - 50}px`;
          popup.style.left = `${e.target.offsetLeft + 20}px`;
        });
      });

      document.body.addEventListener('click', (e) => {
        if (!e.target.classList.contains('acupuncture-point')) {
          popup.style.display = 'none';
        }
      });
    });
  </script>
</body>
</html>





<center><br>
**ACUPUNCTURE AS AN EFFECTIVE MODALITY FOR POST-AMPUTATION PAIN: A SYSTEMATIC REVIEW**
<br></center>


><center><img src="https://EyeCanDoIt.github.io/Images/RCTCASPimg.png" alt=" " width="50%" height="50%"></center>

><center><img src="https://EyeCanDoIt.github.io/Images/systematicreviewCASPimage.png" alt=" " width="50%" height="50%"></center>

