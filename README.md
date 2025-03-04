<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Reproductor Plyr</title>

  <!-- Estilos de Plyr -->
  <link rel="stylesheet" href="https://cdn.plyr.io/3.7.8/plyr.css">

  <style>
    body {
      margin: 0;
      padding: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background-color: #151515;
    }
    .plyr__video-wrapper {
      max-width: 800px;
      width: 100%;
    }
  </style>
</head>
<body>

  <!-- Reproductor Plyr -->
  <div id="videoContainer">
    <video id="videoPlayer" controls></video>
  </div>

  <!-- Plyr JS -->
  <script src="https://cdn.plyr.io/3.7.8/plyr.polyfilled.js"></script>

  <script>
    const player = new Plyr('#videoPlayer');

    // OBTENER LA URL DEL VIDEO DESDE LA BARRA DEL NAVEGADOR
    const urlParams = new URLSearchParams(window.location.search);
    const videoPageUrl = urlParams.get("video_url");

    if (videoPageUrl) {
      fetchVideo(videoPageUrl);
    } else {
      console.error("No se proporcionó un enlace de video.");
    }

    async function fetchVideo(pageUrl) {
      try {
        // Proxy para evitar problemas de CORS
        const proxyUrl = `https://api.allorigins.win/raw?url=${encodeURIComponent(pageUrl)}`;
        const response = await fetch(proxyUrl);
        const html = await response.text();

        // Buscar enlaces directos de video (MP4, M3U8, HLS)
        const videoMatch = html.match(/https?:\/\/[^\s'"<>]+(?:\.mp4|\.m3u8|\.hls)[^\s'"<>]*/);
        if (videoMatch) {
          const videoUrl = videoMatch[0]; // Extraemos el enlace del video

          // REEMPLAZAR EL VIDEO ORIGINAL POR EL NUEVO EXTRAÍDO
          player.source = {
            type: 'video',
            sources: [{
              src: videoUrl,
              type: videoUrl.includes('.m3u8') ? 'application/x-mpegURL' : 'video/mp4'
            }]
          };
        } else {
          console.error("No se encontró un enlace de video válido.");
        }
      } catch (error) {
        console.error("Error al obtener el video:", error);
      }
    }
  </script>

</body>
</html>
