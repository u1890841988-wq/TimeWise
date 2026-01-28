<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Subway Surfers Theme Layout</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background-color: #121212;
            color: white;
            font-family: 'Arial Black', sans-serif;
            padding: 40px 20px;
        }

        .container {
            max-width: 900px;
            margin: 0 auto;
            display: flex;
            flex-direction: column;
            gap: 30px;
        }

        h1 {
            text-align: center;
            font-size: 3.5rem;
            margin-bottom: 50px;
            color: #fff;
            text-shadow: 4px 4px #ff00ff;
        }

        /* Das massive Themen-Feld */
        .theme-card {
            position: relative;
            width: 100%;
            height: 250px; /* Massive Höhe für das Video */
            border-radius: 30px;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            border: 6px solid #fff;
            box-shadow: 0 15px 35px rgba(0,0,0,0.8);
            cursor: pointer;
            transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }

        .theme-card:hover {
            transform: scale(1.03);
            border-color: #00ff00;
        }

        /* Das unruhige Video im Hintergrund des Feldes */
        .card-bg-video {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            object-fit: cover;
            z-index: 1;
        }

        /* Dunkler Filter ÜBER dem Video, damit der Text knallt */
        .card-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.3); /* Nur leicht abdunkeln */
            z-index: 2;
        }

        /* Der Text im Feld */
        .theme-title {
            position: relative;
            z-index: 3;
            font-size: 3rem;
            text-transform: uppercase;
            text-align: center;
            letter-spacing: -2px;
            text-shadow: 4px 4px 0px rgba(0,0,0,1);
            pointer-events: none;
        }

        /* Checkbox-Stil rechts im Feld */
        .theme-status {
            position: absolute;
            right: 30px;
            bottom: 30px;
            z-index: 4;
        }

        input[type="checkbox"] {
            width: 50px;
            height: 50px;
            cursor: pointer;
            accent-color: #00ff00;
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>LEVEL UP TASKS</h1>

        <div class="theme-card">
            <video class="card-bg-video" autoplay muted loop playsinline>
                <source src="DEIN_VIDEO_LINK_1.mp4" type="video/mp4">
            </video>
            <div class="card-overlay"></div>
            <div class="theme-title">MATHE TASKS</div>
            <div class="theme-status">
                <input type="checkbox">
            </div>
        </div>

        <div class="theme-card">
            <video class="card-bg-video" autoplay muted loop playsinline>
                <source src="DEIN_VIDEO_LINK_2.mp4" type="video/mp4">
            </video>
            <div class="card-overlay"></div>
            <div class="theme-title">GAMING GOALS</div>
            <div class="theme-status">
                <input type="checkbox">
            </div>
        </div>

        <div class="theme-card">
            <video class="card-bg-video" autoplay muted loop playsinline>
                <source src="DEIN_VIDEO_LINK_3.mp4" type="video/mp4">
            </video>
            <div class="card-overlay"></div>
            <div class="theme-title">GYM TIME</div>
            <div class="theme-status">
                <input type="checkbox">
            </div>
        </div>

    </div>

</body>
</html>
