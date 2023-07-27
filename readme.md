<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Better Advert player</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #2b2b2b;
            background-size: cover;
        }

        #windowContainer {
            border: 2px solid #999;
            background-color: #fff;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            width: 600px;
            max-width: 95%;
            border-radius: 8px;
            overflow: hidden;
        }

        #windowTitleBar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background-color: #f1f1f1;
            padding: 8px;
            cursor: move;
            user-select: none;
        }

        #videoPlayer {
            padding: 16px;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 360px;
            background-color: #000; /* Set a background color to prevent flashing */
        }

        #playNextButton {
            margin: 16px;
            padding: 8px 16px;
            font-size: 16px;
            background-color: #0078D4;
            color: #fff;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }

        #reopenLink {
            position: absolute;
            top: 10px;
            left: 10px;
            color: #fff;
            text-decoration: underline;
            cursor: pointer;
        }
        a {
            position: absolute;
            top: 75px;
            left: 10px;
            color: #fff;
            text-decoration: underline;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div id="windowContainer">
        <div id="windowTitleBar">
            <span>Better Advert Player</span>
            <button onclick="closeApp()">-</button>
        </div>
        <div id="videoPlayer"></div>
        <button id="playNextButton" onclick="playNextVideo()">Play Next Video</button>
        <button class="buton" onclick="window.location.href = 'https://advert.randspoon.co.uk/update.html';">Updates!</button>
    </div>
    <img src="inv.png" id="reopenLink" onclick="reopenApp()" style="width:50px;height:42px;"></img>
    <a href="index.html">downgrade to older version</a>

    <script>
        const invidiousAPIEndpoint = 'https://vid.puffyan.us';
        const searchQuery = 'advert';
        let videoDataList = [];
        let currentVideoIndex = -1;

        function closeApp() {
            const appContainer = document.getElementById('windowContainer');
            appContainer.style.display = 'none';
        }

        function reopenApp() {
            const appContainer = document.getElementById('windowContainer');
            appContainer.style.display = 'block';
        }

        function getRandomVideoIndex() {
            return Math.floor(Math.random() * videoDataList.length);
        }

        function playNextVideo() {
            let randomIndex = getRandomVideoIndex();
            while (randomIndex === currentVideoIndex) {
                randomIndex = getRandomVideoIndex();
            }

            currentVideoIndex = randomIndex;
            const videoData = videoDataList[currentVideoIndex];
            const videoPlayerElement = document.getElementById('videoPlayer');
            videoPlayerElement.innerHTML = ''; // Clear previous video

            if (videoData.type === 'video') {
                const videoId = videoData.videoId;
                const videoUrl = `https://www.youtube.com/embed/${videoId}`;

                const iframe = document.createElement('iframe');
                iframe.src = videoUrl;
                iframe.width = '560';
                iframe.height = '315';
                iframe.allow = 'autoplay; encrypted-media';
                iframe.allowFullscreen = true;
                iframe.onload = () => {
                    iframe.style.display = 'block';
                    videoPlayerElement.style.backgroundColor = 'transparent';
                };
                iframe.style.display = 'none';

                videoPlayerElement.appendChild(iframe);
            }
        }

        fetch(`${invidiousAPIEndpoint}/api/v1/search?q=${searchQuery}`)
            .then(response => response.json())
            .then(data => {
                if (data && data.length > 0) {
                    videoDataList = data;
                    playNextVideo();
                } else {
                    const videoPlayerElement = document.getElementById('videoPlayer');
                    videoPlayerElement.innerHTML = 'No videos found for the search query.';
                }
            })
            .catch(error => {
                console.error('Error fetching data from the API:', error);
            });
        const windowTitleBar = document.getElementById('windowTitleBar');
        let offsetX, offsetY;
        let isDragging = false;

        windowTitleBar.addEventListener('mousedown', (e) => {
            isDragging = true;
            offsetX = e.clientX;
            offsetY = e.clientY;
        });

        window.addEventListener('mousemove', (e) => {
            if (isDragging) {
                const appContainer = document.getElementById('windowContainer');
                const newX = e.clientX - offsetX;
                const newY = e.clientY - offsetY;
                appContainer.style.transform = `translate(${newX}px, ${newY}px)`;
            }
        });

        window.addEventListener('mouseup', () => {
            isDragging = false;
        });
    </script>
</body>
</html>
