# This was produced with the use of AI
<!DOCTYPE html>
<html>
<head>
    <title>Animal Classifier AI</title>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest/dist/tf.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@latest/dist/teachablemachine-image.min.js"></script>
</head>

<body>
    <h1>Animal Classifier AI</h1>

    <button onclick="init()">Start AI</button>

    <div id="webcam-container"></div>
    <div id="label-container"></div>

    <script>
        const URL = "./model/";

        let model, webcam, labelContainer, maxPredictions;

        async function init() {
            model = await tmImage.load(
                URL + "model.json",
                URL + "metadata.json"
            );

            maxPredictions = model.getTotalClasses();

            webcam = new tmImage.Webcam(300, 300, true);
            await webcam.setup();
            await webcam.play();

            window.requestAnimationFrame(loop);

            document.getElementById("webcam-container")
                .appendChild(webcam.canvas);

            labelContainer = document.getElementById("label-container");

            for (let i = 0; i < maxPredictions; i++) {
                labelContainer.appendChild(document.createElement("div"));
            }
        }

        async function loop() {
            webcam.update();
            await predict();
            window.requestAnimationFrame(loop);
        }

        async function predict() {
            const predictions = await model.predict(webcam.canvas);

            predictions.sort((a, b) =>
                b.probability - a.probability
            );

            for (let i = 0; i < maxPredictions; i++) {
                labelContainer.childNodes[i].innerHTML =
                    predictions[i].className + ": " +
                    (predictions[i].probability * 100).toFixed(2) + "%";
            }
        }
    </script>
</body>
</html>
