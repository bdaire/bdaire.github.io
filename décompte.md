<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Décompte ❤️</title>
  <style>
    body {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
      background: linear-gradient(135deg, #ff9a9e, #fad0c4);
      font-family: 'Arial', sans-serif;
      color: #fff;
      text-align: center;
    }
    h2 {
      font-size: 1.2rem;
      font-weight: normal;
      margin-bottom: 20px;
    }
    #countdown {
      font-size: 2rem;
      font-weight: bold;
      background: rgba(255,255,255,0.2);
      padding: 20px 30px;
      border-radius: 20px;
      box-shadow: 0 4px 15px rgba(0,0,0,0.2);
    }
    .expired {
      font-size: 2rem;
      font-weight: bold;
      color: #fff;
    }
  </style>
</head>
<body>
  <h2>On se revoit dans :</h2>
  <div id="countdown">-- jours, -- heures, -- minutes, -- secondes</div>

  <script>
    // 🗓️ Date cible : mercredi 10 septembre 2025 à 19h00
    const targetDate = new Date("2025-09-10T19:00:00");

    function updateCountdown() {
      const now = new Date();
      const diff = targetDate - now;

      if (diff <= 0) {
        document.getElementById("countdown").innerHTML = "<span class='expired'>C'est maintenant ❤️</span>";
        clearInterval(timer);
        return;
      }

      const days = Math.floor(diff / (1000 * 60 * 60 * 24));
      const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
      const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
      const seconds = Math.floor((diff % (1000 * 60)) / 1000);

      // Format lisible en français
      const format = 
        days + " jours, " +
        String(hours).padStart(2, '0') + " heures, " +
        String(minutes).padStart(2, '0') + " minutes, " +
        String(seconds).padStart(2, '0') + " secondes";

      document.getElementById("countdown").innerText = format;
    }

    // Mise à jour chaque seconde
    updateCountdown();
    const timer = setInterval(updateCountdown, 1000);
  </script>
</body>
</html>
