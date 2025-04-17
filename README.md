# Bloomer <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LuminEdit</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@700&family=Roboto:wght@400&display=swap');

        body {
            margin: 0;
            font-family: 'Roboto', sans-serif;
            background: linear-gradient(135deg, #1E90FF, #6A0DAD);
            color: #C0C0C0;
            height: 100vh;
            overflow: hidden;
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
        }

        /* Starfield Background */
        .starfield {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            z-index: 0;
        }
        .star {
            position: absolute;
            background: #FFF;
            border-radius: 50%;
            animation: twinkle 5s infinite;
        }
        @keyframes twinkle {
            0%, 100% { opacity: 0.3; }
            50% { opacity: 1; }
        }

        /* App Container */
        .app {
            background: rgba(255, 215, 0, 0.1);
            border-radius: 10px;
            padding: 20px;
            width: 90%;
            max-width: 1200px;
            height: 80%;
            display: flex;
            flex-direction: column;
            overflow-y: auto;
            position: relative;
            z-index: 1;
        }

        /* Upload Area */
        .upload-area {
            border: 2px dashed #C0C0C0;
            border-radius: 10px;
            height: 200px;
            display: flex;
            justify-content: center;
            align-items: center;
            margin-bottom: 20px;
            background: rgba(255, 255, 255, 0.1);
            transition: border-color 0.3s ease;
        }
        .upload-area.dragover {
            border-color: #FFD700;
        }
        .upload-area p {
            margin: 0;
            font-size: 1.2em;
        }

        /* Preview */
        .preview {
            width: 100%;
            max-width: 600px;
            margin: 0 auto;
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
            overflow: hidden;
        }
        #imagePreview {
            max-width: 100%;
            max-height: 400px;
            transition: filter 0.3s ease, transform 0.3s ease;
        }

        /* Tools */
        .tools {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            margin-top: 20px;
        }
        .tool-panel {
            background: rgba(30, 144, 255, 0.2);
            border-radius: 10px;
            padding: 10px;
            width: 100%;
            max-width: 300px;
        }
        .tool-panel h3 {
            font-family: 'Orbitron', sans-serif;
            font-size: 1.5em;
            color: #FFFFFF;
            text-shadow: 0 0 10px #1E90FF;
            margin: 0 0 10px;
        }
        .filter-btn, .adjust-slider, .edit-btn {
            width: 100%;
            padding: 5px;
            margin: 5px 0;
            background: #6A0DAD;
            color: #FFF;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background 0.3s ease;
        }
        .filter-btn:hover, .edit-btn:hover {
            background: #FFD700;
        }
        .adjust-slider {
            width: 90%;
            cursor: pointer;
        }
        .crop-controls, .rotate-controls {
            display: none;
            margin-top: 10px;
        }
        .crop-controls.active, .rotate-controls.active {
            display: block;
        }
        #cropCanvas {
            display: none;
            max-width: 100%;
            max-height: 400px;
        }

        /* Responsive Design */
        @media (max-width: 768px) {
            .app {
                width: 95%;
                height: 90%;
            }
            .upload-area {
                height: 150px;
            }
            .preview {
                max-width: 100%;
            }
            #imagePreview, #cropCanvas {
                max-height: 300px;
            }
            .tools {
                flex-direction: column;
            }
            .tool-panel {
                max-width: 100%;
            }
        }
    </style>
</head>
<body>
    <!-- Starfield Background -->
    <div class="starfield">
        <div class="star" style="width: 2px; height: 2px; top: 10%; left: 20%; animation-delay: 0s;"></div>
        <div class="star" style="width: 3px; height: 3px; top: 30%; left: 40%; animation-delay: 1s;"></div>
        <div class="star" style="width: 1px; height: 1px; top: 50%; left: 70%; animation-delay: 2s;"></div>
    </div>

    <!-- App Container -->
    <div class="app">
        <div class="upload-area" id="uploadArea">
            <p>Drag and drop an image here or click to upload</p>
            <input type="file" id="fileInput" accept="image/*" style="display: none;">
        </div>
        <div class="preview">
            <img id="imagePreview" src="" alt="Preview">
            <canvas id="cropCanvas"></canvas>
        </div>
        <div class="tools">
            <div class="tool-panel">
                <h3>Filters</h3>
                <button class="filter-btn" onclick="applyFilter('sepia')">Sepia</button>
                <button class="filter-btn" onclick="applyFilter('noir')">Noir</button>
                <button class="filter-btn" onclick="applyFilter('cosmic')">Cosmic Fusion</button>
            </div>
            <div class="tool-panel">
                <h3>Adjustments</h3>
                <label>Brightness</label>
                <input type="range" class="adjust-slider" min="-100" max="100" value="0" oninput="adjustImage('brightness', this.value)">
                <label>Contrast</label>
                <input type="range" class="adjust-slider" min="-100" max="100" value="0" oninput="adjustImage('contrast', this.value)">
                <label>Saturation</label>
                <input type="range" class="adjust-slider" min="-100" max="100" value="0" oninput="adjustImage('saturate', this.value)">
            </div>
            <div class="tool-panel">
                <h3>Edit</h3>
                <button class="edit-btn" onclick="toggleCrop()">Crop</button>
                <div class="crop-controls">
                    <button onclick="cropImage()">Apply Crop</button>
                    <button onclick="cancelCrop()">Cancel</button>
                </div>
                <button class="edit-btn" onclick="toggleRotate()">Rotate</button>
                <div class="rotate-controls">
                    <button onclick="rotateImage(90)">Rotate 90°</button>
                    <button onclick="rotateImage(-90)">Rotate -90°</button>
                </div>
                <button class="edit-btn" onclick="downloadImage()">Download</button>
            </div>
        </div>
    </div>

    <script>
        const uploadArea = document.getElementById('uploadArea');
        const fileInput = document.getElementById('fileInput');
        const imagePreview = document.getElementById('imagePreview');
        const cropCanvas = document.getElementById('cropCanvas');
        const ctx = cropCanvas.getContext('2d');
        let originalImage = new Image();
        let isCropping = false;
        let cropStart = { x: 0, y: 0 };
        let cropEnd = { x: 0, y: 0 };

        // Upload Handling
        uploadArea.addEventListener('dragover', (e) => {
            e.preventDefault();
            uploadArea.classList.add('dragover');
        });
        uploadArea.addEventListener('dragleave', () => {
            uploadArea.classList.remove('dragover');
        });
        uploadArea.addEventListener('drop', (e) => {
            e.preventDefault();
            uploadArea.classList.remove('dragover');
            const file = e.dataTransfer.files[0];
            if (file && file.type.startsWith('image/')) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    originalImage.src = e.target.result;
                    imagePreview.src = e.target.result;
                };
                reader.readAsDataURL(file);
            }
        });
        uploadArea.addEventListener('click', () => fileInput.click());
        fileInput.addEventListener('change', (e) => {
            const file = e.target.files[0];
            if (file && file.type.startsWith('image/')) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    originalImage.src = e.target.result;
                    imagePreview.src = e.target.result;
                };
                reader.readAsDataURL(file);
            }
        });

        // Filters
        function applyFilter(filterType) {
            let filter = '';
            switch (filterType) {
                case 'sepia':
                    filter = 'sepia(1)';
                    break;
                case 'noir':
                    filter = 'grayscale(1) contrast(1.5)';
                    break;
                case 'cosmic':
                    filter = 'hue-rotate(90deg) brightness(1.2) blur(1px) drop-shadow(0 0 10px #FFD700)';
                    break;
            }
            imagePreview.style.filter = filter;
        }

        // Adjustments
        function adjustImage(type, value) {
            const brightness = parseInt(document.querySelector('[oninput="adjustImage(\'brightness\', this.value)"]').value) / 100;
            const contrast = parseInt(document.querySelector('[oninput="adjustImage(\'contrast\', this.value)"]').value) / 100 + 1;
            const saturation = parseInt(document.querySelector('[oninput="adjustImage(\'saturate\', this.value)"]').value) / 100 + 1;
            imagePreview.style.filter = `brightness(${brightness}) contrast(${contrast}) saturate(${saturation})`;
        }

        // Crop
        function toggleCrop() {
            isCropping = !isCropping;
            document.querySelector('.crop-controls').classList.toggle('active');
            if (isCropping) {
                cropCanvas.style.display = 'block';
                imagePreview.style.display = 'none';
                cropCanvas.width = imagePreview.width;
                cropCanvas.height = imagePreview.height;
                ctx.drawImage(imagePreview, 0, 0);
                cropCanvas.addEventListener('mousedown', startCrop);
                cropCanvas.addEventListener('mousemove', doCrop);
                cropCanvas.addEventListener('mouseup', endCrop);
            } else {
                cropCanvas.style.display = 'none';
                imagePreview.style.display = 'block';
                cropCanvas.removeEventListener('mousedown', startCrop);
                cropCanvas.removeEventListener('mousemove', doCrop);
                cropCanvas.removeEventListener('mouseup', endCrop);
            }
        }

        function startCrop(e) {
            cropStart.x = e.offsetX;
            cropStart.y = e.offsetY;
        }

        function doCrop(e) {
            if (isCropping) {
                cropEnd.x = e.offsetX;
                cropEnd.y = e.offsetY;
                ctx.clearRect(0, 0, cropCanvas.width, cropCanvas.height);
                ctx.drawImage(imagePreview, 0, 0);
                ctx.strokeStyle = '#FFD700';
                ctx.lineWidth = 2;
                ctx.strokeRect(cropStart.x, cropStart.y, cropEnd.x - cropStart.x, cropEnd.y - cropStart.y);
            }
        }

        function endCrop() {
            // Handled in applyCrop
        }

        function cropImage() {
            const width = cropEnd.x - cropStart.x;
            const height = cropEnd.y - cropStart.y;
            const cropCanvasTemp = document.createElement('canvas');
            cropCanvasTemp.width = width;
            cropCanvasTemp.height = height;
            const ctxTemp = cropCanvasTemp.getContext('2d');
            ctxTemp.drawImage(imagePreview, cropStart.x, cropStart.y, width, height, 0, 0, width, height);
            imagePreview.src = cropCanvasTemp.toDataURL();
            originalImage.src = cropCanvasTemp.toDataURL();
            toggleCrop();
        }

        function cancelCrop() {
            toggleCrop();
            ctx.clearRect(0, 0, cropCanvas.width, cropCanvas.height);
            ctx.drawImage(imagePreview, 0, 0);
        }

        // Rotate
        function toggleRotate() {
            document.querySelector('.rotate-controls').classList.toggle('active');
        }

        function rotateImage(degrees) {
            const tempImg = new Image();
            tempImg.src = imagePreview.src;
            tempImg.onload = () => {
                const tempCanvas = document.createElement('canvas');
                const ctxTemp = tempCanvas.getContext('2d');
                tempCanvas.width = tempImg.height;
                tempCanvas.height = tempImg.width;
                ctxTemp.translate(tempCanvas.width / 2, tempCanvas.height / 2);
                ctxTemp.rotate(degrees * Math.PI / 180);
                ctxTemp.drawImage(tempImg, -tempImg.width / 2, -tempImg.height / 2);
                imagePreview.src = tempCanvas.toDataURL();
                originalImage.src = tempCanvas.toDataURL();
            };
        }

        // Download
        function downloadImage() {
            const link = document.createElement('a');
            link.href = imagePreview.src;
            link.download = 'edited_image.png';
            link.click();
        }

        // Initialize Starfield
        function createStars(count) {
            for (let i = 0; i < count; i++) {
                const star = document.createElement('div');
                star.className = 'star';
                star.style.width = `${Math.random() * 3 + 1}px`;
                star.style.height = `${Math.random() * 3 + 1}px`;
                star.style.top = `${Math.random() * 100}%`;
                star.style.left = `${Math.random() * 100}%`;
                star.style.animationDelay = `${Math.random() * 5}s`;
                document.querySelector('.starfield').appendChild(star);
            }
        }
        createStars(50);
    </script>
</body>
</html>