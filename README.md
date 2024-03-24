
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Film Editor</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            text-align: center;
            margin: 50px;
        }

        #drop-zone {
            border: 2px dashed #ccc;
            padding: 20px;
            cursor: pointer;
        }

        video {
            width: 100%;
            max-width: 600px;
            margin-top: 20px;
        }

        button {
            margin-top: 20px;
            padding: 10px;
            font-size: 16px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>Film Editor</h1>

    <div id="drop-zone">
        <p>Przeciągnij i upuść plik video tutaj<br />lub kliknij, aby wybrać plik</p>
        <input type="file" id="file-input" accept="video/*" />
    </div>

    <video controls id="output-video"></video>

    <button id="save-button" style="display:none;">Zapisz film</button>

    <script src="https://cdn.jsdelivr.net/npm/@ffmpeg/ffmpeg/dist/ffmpeg.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@ffmpeg/ffmpeg/dist/ffprobe.min.js"></script>
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const dropZone = document.getElementById('drop-zone');
            const fileInput = document.getElementById('file-input');
            const outputVideo = document.getElementById('output-video');
            const saveButton = document.getElementById('save-button');

            dropZone.addEventListener('click', () => fileInput.click());
            dropZone.addEventListener('dragover', (e) => {
                e.preventDefault();
                dropZone.style.border = '2px dashed #39f';
            });
            dropZone.addEventListener('dragleave', () => {
                dropZone.style.border = '2px dashed #ccc';
            });
            dropZone.addEventListener('drop', (e) => {
                e.preventDefault();
                dropZone.style.border = '2px dashed #ccc';
                const file = e.dataTransfer.files[0];
                handleFile(file);
            });

            fileInput.addEventListener('change', () => {
                const file = fileInput.files[0];
                handleFile(file);
            });

            saveButton.addEventListener('click', async () => {
                const result = await convertVideo();
                downloadVideo(result);
            });

            async function handleFile(file) {
                if (file && file.type.startsWith('video/')) {
                    const videoUrl = URL.createObjectURL(file);
                    outputVideo.src = videoUrl;
                    saveButton.style.display = 'block';
                } else {
                    alert('Wybierz plik wideo.');
                }
            }

            async function convertVideo() {
                const ffmpeg = createFFmpeg({ log: true });
                await ffmpeg.load();
                const inputPath = 'input.mp4';
                const outputPath = 'output.mp4';

                ffmpeg.FS('writeFile', inputPath, await fetchFile(fileInput.files[0]));
                await ffmpeg.run('-i', inputPath, outputPath);
                const data = ffmpeg.FS('readFile', outputPath);

                return new Blob([data.buffer], { type: 'video/mp4' });
            }

            function downloadVideo(blob) {
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = 'edited_video.mp4';
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
            }

            function fetchFile(file) {
                return new Promise((resolve) => {
                    const reader = new FileReader();
                    reader.onload = (event) => {
                        resolve(new Uint8Array(event.target.result));
                    };
                    reader.readAsArrayBuffer(file);
                });
            }
        });
    </script>
</body>
</html>
