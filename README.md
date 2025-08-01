# Blockblaster video game 



<!DOCTYPE html> <html lang="en"> <head>
<meta charset="UTF-8" />

<title>Finger Mouse V5.5 (MediaPipe)</title>

<style>

    body {

        margin: 0;

        overflow: hidden;

        background-color: #000011;

        touch-action: none;

    }

    #info {

        position: absolute;

        top: 10px;

        left: 10px;

        color: #00ffcc;

        font-family: sans-serif;

        background: rgba(0,0,0,0.5);

        padding: 8px;

        border-radius: 8px;

        z-index: 10;

    }

    #cursor {

        position: absolute;

        width: 40px;

        height: 40px;

        border-radius: 50%;

        border: 2px solid #00ffcc;

        background-color: rgba(0,255,255,0.2);

        pointer-events: none;

        z-index: 100;

        transform: translate(-20px, -20px);

    }

    video {

        display: none;

    }

</style>
</head> <body>
<div id="info">V5.5: MediaPipe - Finger as Mouse Cursor</div>

<div id="cursor"></div>

<video id="video" autoplay playsinline muted></video>

<script src="https://cdn.jsdelivr.net/npm/@mediapipe/hands/hands.min.js"></script>

<script src="https://cdn.jsdelivr.net/npm/@mediapipe/camera_utils/camera_utils.min.js"></script>

<script src="https://cdn.jsdelivr.net/npm/@mediapipe/drawing_utils/drawing_utils.min.js"></script>

<script>

    const cursor = document.getElementById('cursor');

    let cursorX = window.innerWidth / 2;

    let cursorY = window.innerHeight / 2;

    function lerp(a, b, t) {

        return a + (b - a) * t;

    }

    const hands = new Hands({

        locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${file}`

    });

    hands.setOptions({

        maxNumHands: 1,

        modelComplexity: 1,

        minDetectionConfidence: 0.7,

        minTrackingConfidence: 0.7

    });

    hands.onResults(onResults);

    const videoElement = document.getElementById('video');

    const camera = new Camera(videoElement, {

        onFrame: async () => {

            await hands.send({image: videoElement});

        },

        width: 320,

        height: 240

    });

    camera.start();

    function onResults(results) {

        if (results.multiHandLandmarks && results.multiHandLandmarks.length > 0) {

            const landmarks = results.multiHandLandmarks[0];

            const indexTip = landmarks[8]; // index finger tip

            let nx = indexTip.x; // normalized 0-1

            let ny = indexTip.y;

            // Flip X to match camera facing user

            nx = 1 - nx;

            // Smooth follow

            cursorX = lerp(cursorX, nx * window.innerWidth, 0.4);

            cursorY = lerp(cursorY, ny * window.innerHeight, 0.4);

            cursor.style.left = `${cursorX}px`;

            cursor.style.top = `${cursorY}px`;

        }

    }

</script>
