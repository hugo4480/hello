<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Portfolio</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>

    <header>
        <h1>Bienvenue sur Mon Portfolio</h1>
        <p>Découvrez mes compétences et projets</p>
    </header>

    <section class="skills">
        <h2>Compétences</h2>
        <div class="skill-box" data-skill="HTML">
            <span>HTML</span>
        </div>
        <div class="skill-box" data-skill="CSS">
            <span>CSS</span>
        </div>
        <div class="skill-box" data-skill="JavaScript">
            <span>JavaScript</span>
        </div>
        <div class="skill-box" data-skill="PHP">
            <span>PHP</span>
        </div>
    </section>

    <section class="contact">
        <h2>Me contacter</h2>
        <form action="contact.php" method="POST" id="contactForm">
            <label for="name">Nom</label>
            <input type="text" id="name" name="name" required>

            <label for="email">Email</label>
            <input type="email" id="email" name="email" required>

            <label for="message">Message</label>
            <textarea id="message" name="message" required></textarea>

            <button type="submit">Envoyer</button>
        </form>
        <p id="feedback"></p>
    </section>

    <footer>
        <p>© 2024 Mon Portfolio - Tous droits réservés.</p>
    </footer>

    <script src="script.js"></script>
</body>
</html>
