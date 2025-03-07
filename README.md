# Khung-Film-Oke
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Finndei Frame</title>
    <style>
        body {
            background-color: #121212;
            color: #ffffff;
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
        }

        h1 {
            text-align: center;
            font-size: 2rem;
            margin: 20px 0;
            color: #e0e0e0;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #1e1e1e;
            border-radius: 10px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.5);
        }

        label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
            color: #e0e0e0;
        }

        input[type="file"] {
            width: 100%;
            padding: 10px;
            margin-bottom: 20px;
            border: 1px solid #444;
            border-radius: 5px;
            background-color: #333;
            color: #fff;
        }

        button {
            display: block;
            width: 100%;
            padding: 10px;
            background-color: #007acc;
            color: #fff;
            border: none;
            border-radius: 5px;
            font-size: 1rem;
            cursor: pointer;
            transition: background-color 0.3s;
            margin-bottom: 10px;
        }

        button:hover {
            background-color: #005f99;
        }

        .results img {
            max-width: 100%;
            margin-top: 20px;
            border-radius: 10px;
        }

        .results {
            text-align: center;
        }

        #download-btn {
            background-color: #4CAF50;
            display: none; /* Ẩn nút tải xuống ban đầu */
        }

        #download-btn:hover {
            background-color: #3e8e41;
        }
    </style>
</head>
<body>
    <h1>Finndei Frame</h1>
    <div class="container">
        <label for="upload-images">Tải ảnh lên:</label>
        <input type="file" id="upload-images" accept="image/*" multiple>

        <label for="upload-frames">Tải khung lên:</label>
        <input type="file" id="upload-frames" accept="image/*" multiple>

        <button id="render-btn">Xử lý ảnh</button>
        <button id="download-btn">Tải xuống</button>

        <div class="results" id="results"></div>
    </div>

    <script>
        document.getElementById('render-btn').addEventListener('click', () => {
            const imagesInput = document.getElementById('upload-images').files;
            const framesInput = document.getElementById('upload-frames').files;

            if (imagesInput.length === 0 || framesInput.length === 0) {
                alert('Vui lòng tải ảnh và ít nhất một khung lên.');
                return;
            }

            const frames = Array.from(framesInput).map(frameFile => {
                const frameImage = new Image();
                frameImage.src = URL.createObjectURL(frameFile);
                return frameImage;
            });

            const resultsDiv = document.getElementById('results');
            resultsDiv.innerHTML = '';

            let loadedFrames = 0;

            frames.forEach(frameImage => {
                frameImage.onload = () => {
                    loadedFrames++;
                    if (loadedFrames === frames.length) {
                        processImages(imagesInput, frames);
                    }
                };
            });
        });

        function processImages(imagesInput, frames) {
            Array.from(imagesInput).forEach(imageFile => {
                const img = new Image();
                const imgURL = URL.createObjectURL(imageFile);
                img.src = imgURL;

                img.onload = () => {
                    const canvas = document.createElement('canvas');
                    const ctx = canvas.getContext('2d');

                    const isPortrait = img.height > img.width;

                    const frameImage = frames.length > 1
                        ? frames[Math.floor(Math.random() * frames.length)]
                        : frames[0];

                    let frameWidth = frameImage.width;
                    let frameHeight = frameImage.height;
                    const tempCanvas = document.createElement('canvas');
                    const tempCtx = tempCanvas.getContext('2d');

                    if (isPortrait && frameWidth > frameHeight) {
                        tempCanvas.width = frameHeight;
                        tempCanvas.height = frameWidth;
                        tempCtx.translate(frameHeight / 2, frameWidth / 2);
                        tempCtx.rotate(Math.PI / 2);
                        tempCtx.drawImage(frameImage, -frameWidth / 2, -frameHeight / 2);
                        [frameWidth, frameHeight] = [frameHeight, frameWidth];
                    } else if (!isPortrait && frameHeight > frameWidth) {
                        tempCanvas.width = frameHeight;
                        tempCanvas.height = frameWidth;
                        tempCtx.translate(frameHeight / 2, frameWidth / 2);
                        tempCtx.rotate(-Math.PI / 2);
                        tempCtx.drawImage(frameImage, -frameWidth / 2, -frameHeight / 2);
                        [frameWidth, frameHeight] = [frameHeight, frameWidth];
                    } else {
                        tempCanvas.width = frameWidth;
                        tempCanvas.height = frameHeight;
                        tempCtx.drawImage(frameImage, 0, 0);
                    }

                    const scale = Math.max(frameWidth / img.width, frameHeight / img.height);
                    const scaledWidth = img.width * scale;
                    const scaledHeight = img.height * scale;
                    const cropX = (scaledWidth - frameWidth) / 2;
                    const cropY = (scaledHeight - frameHeight) / 2;

                    canvas.width = frameWidth;
                    canvas.height = frameHeight;

                    ctx.drawImage(
                        img,
                        -cropX,
                        -cropY,
                        scaledWidth,
                        scaledHeight
                    );

                    ctx.globalCompositeOperation = 'lighten';
                    ctx.drawImage(tempCanvas, 0, 0, frameWidth, frameHeight);

                    const resultImg = document.createElement('img');
                    resultImg.src = canvas.toDataURL('image/png');
                    document.getElementById('results').appendChild(resultImg);

                    // Hiển thị nút tải xuống sau khi xử lý ảnh
                    document.getElementById('download-btn').style.display = 'block';
                    document.getElementById('download-btn').href = resultImg.src;
                    document.getElementById('download-btn').download = 'framed_image.png';
                };
            });
        }
    </script>
</body>
</html># Khung-Film-Oke
