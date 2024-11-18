<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Emotion Detector with Camera</title>
  <script defer src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"></script>
  <script defer src="https://cdn.jsdelivr.net/npm/face-api.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin: 50px;
    }
    video {
      width: 80%;
      margin-top: 20px;
      border: 1px solid #ccc;
    }
    button {
      padding: 10px 20px;
      background-color: #007BFF;
      color: white;
      border: none;
      cursor: pointer;
    }
    button:hover {
      background-color: #0056b3;
    }
    .result {
      margin-top: 20px;
      font-size: 1.5em;
      color: green;
    }
  </style>
</head>
<body>
  <h1>Emotion Detector with Camera</h1>
  <video id="video" autoplay muted></video>
  <br>
  <button onclick="detectCameraEmotion()">Detect Emotion</button>
  <div class="result" id="result"></div>

  <script>
    const video = document.getElementById('video');

    // Load the face-api models
    Promise.all([
      faceapi.nets.tinyFaceDetector.loadFromUri('https://cdn.jsdelivr.net/npm/face-api.js/models'),
      faceapi.nets.faceExpressionNet.loadFromUri('https://cdn.jsdelivr.net/npm/face-api.js/models')
    ]).then(startVideo);

    function startVideo() {
      navigator.mediaDevices.getUserMedia({ video: {} })
        .then(stream => {
          video.srcObject = stream;
        })
        .catch(err => console.error("Camera access denied:", err));
    }

    async function detectCameraEmotion() {
      const detections = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions())
        .withFaceExpressions();

      if (detections.length > 0) {
        const emotions = detections[0].expressions;
        const highestEmotion = Object.keys(emotions).reduce((a, b) => emotions[a] > emotions[b] ? a : b);
        document.getElementById('result').textContent = `Detected Emotion: ${highestEmotion}`;
      } else {
        document.getElementById('result').textContent = "No face detected!";
      }
    }
  </script>
</body>
</html>
