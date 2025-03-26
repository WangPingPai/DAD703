<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>全螢幕播放影片的拼圖遊戲</title>
    <style>
        #puzzle-container {
            width: 300px;
            height: 300px;
            display: flex;
            flex-wrap: wrap;
            border: 2px solid #000;
        }
        .tile {
            width: 100px;
            height: 100px;
            background-size: 300px 300px;
            border: 1px solid #ccc;
            cursor: grab;
            box-sizing: border-box;
        }
        video {
            width: 100%;
            height: 100%;
            display: none;
        }
    </style>
</head>
<body>
    <audio id="bg-music" src="您的音樂檔案路徑.mp3" loop></audio>
    <button onclick="startGame()">開始遊戲</button>
    <div id="puzzle-container"></div>
    <video id="video-player" controls>
        <source src="您的影片檔案路徑.mp4" type="video/mp4">
        您的瀏覽器不支援影片播放。
    </video>
    <p id="message"></p>

    <script>
        const imageSrc = '112445.gif'; // 替換為您的圖片路徑
        const puzzleContainer = document.getElementById('puzzle-container');
        const bgMusic = document.getElementById('bg-music');
        const message = document.getElementById('message');
        const videoPlayer = document.getElementById('video-player');
        let tiles = [];
        let originalOrder = [];

        function startGame() {
            bgMusic.play(); // 播放背景音樂
            generatePuzzle();
            shuffleTiles();
            renderPuzzle();
        }

        function generatePuzzle() {
            tiles = [];
            originalOrder = [];
            for (let i = 0; i < 9; i++) {
                tiles.push(i); // 建立拼圖編號陣列
                originalOrder.push(i); // 保存初始正確排列
            }
        }

        function shuffleTiles() {
            for (let i = tiles.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [tiles[i], tiles[j]] = [tiles[j], tiles[i]];
            }
        }

        function renderPuzzle() {
            puzzleContainer.innerHTML = '';
            tiles.forEach((tile, index) => {
                const tileElement = document.createElement('div');
                tileElement.classList.add('tile');
                tileElement.draggable = true;

                if (tile !== null) {
                    tileElement.style.backgroundImage = `url(${imageSrc})`;
                    tileElement.style.backgroundPosition = `${-(tile % 3) * 100}px ${-Math.floor(tile / 3) * 100}px`;
                }

                tileElement.dataset.index = index;

                tileElement.ondragstart = (e) => dragStart(e, index);
                tileElement.ondragover = (e) => e.preventDefault();
                tileElement.ondrop = (e) => drop(e, index);

                puzzleContainer.appendChild(tileElement);
            });
        }

        function dragStart(e, index) {
            e.dataTransfer.setData('draggedIndex', index);
        }

        function drop(e, index) {
            const draggedIndex = parseInt(e.dataTransfer.getData('draggedIndex'), 10);
            [tiles[index], tiles[draggedIndex]] = [tiles[draggedIndex], tiles[index]];
            renderPuzzle();
            checkCompletion();
        }

        function checkCompletion() {
            if (tiles.every((tile, index) => tile === originalOrder[index])) {
                message.textContent = '恭喜你完成拼圖！即將播放影片。';
                bgMusic.pause(); // 完成時停止背景音樂
                setTimeout(() => {
                    playFullScreenVideo(); // 播放全螢幕影片
                }, 2000); // 2 秒後播放影片
            }
        }

        function playFullScreenVideo() {
            videoPlayer.style.display = 'block';
            const requestFullScreen = videoPlayer.requestFullscreen || videoPlayer.webkitRequestFullscreen || videoPlayer.mozRequestFullScreen || videoPlayer.msRequestFullscreen;
            if (requestFullScreen) {
                requestFullScreen.call(videoPlayer); // 進入全螢幕模式
            }
            videoPlayer.play(); // 播放影片
        }
    </script>
</body>
</html>
