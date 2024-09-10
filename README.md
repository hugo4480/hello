<?php
session_start();
$mysqli = new mysqli("localhost", "root", "", "agenda");

// Vérification de la connexion à la base de données
if ($mysqli->connect_error) {
    die("Erreur de connexion à la base de données : " . $mysqli->connect_error);
}

// Gestion de l'inscription
if (isset($_POST['register'])) {
    $username = htmlspecialchars($_POST['username']);
    $password = password_hash($_POST['password'], PASSWORD_BCRYPT);
    $stmt = $mysqli->prepare("INSERT INTO users (username, password) VALUES (?, ?)");
    $stmt->bind_param("ss", $username, $password);
    if ($stmt->execute()) {
        echo "Inscription réussie !";
    } else {
        echo "Erreur d'inscription.";
    }
    $stmt->close();
}

// Gestion de la connexion
if (isset($_POST['login'])) {
    $username = htmlspecialchars($_POST['username']);
    $password = $_POST['password'];
    $stmt = $mysqli->prepare("SELECT id, password FROM users WHERE username = ?");
    $stmt->bind_param("s", $username);
    $stmt->execute();
    $stmt->store_result();
    if ($stmt->num_rows > 0) {
        $stmt->bind_result($id, $hashed_password);
        $stmt->fetch();
        if (password_verify($password, $hashed_password)) {
            $_SESSION['user_id'] = $id;
            $_SESSION['username'] = $username;
        } else {
            echo "Mot de passe incorrect.";
        }
    } else {
        echo "Utilisateur non trouvé.";
    }
    $stmt->close();
}

// Gestion de l'ajout d'événements
if (isset($_POST['add_event']) && isset($_SESSION['user_id'])) {
    $event_date = $_POST['event_date'];
    $event_description = htmlspecialchars($_POST['event_description']);
    $user_id = $_SESSION['user_id'];
    $stmt = $mysqli->prepare("INSERT INTO events (user_id, event_date, event_description) VALUES (?, ?, ?)");
    $stmt->bind_param("iss", $user_id, $event_date, $event_description);
    $stmt->execute();
    $stmt->close();
}

// Récupération des événements de l'utilisateur
$events = [];
if (isset($_SESSION['user_id'])) {
    $user_id = $_SESSION['user_id'];
    $result = $mysqli->prepare("SELECT event_date, event_description FROM events WHERE user_id = ? ORDER BY event_date");
    $result->bind_param("i", $user_id);
    $result->execute();
    $result->bind_result($event_date, $event_description);
    while ($result->fetch()) {
        $events[] = ['date' => $event_date, 'description' => $event_description];
    }
    $result->close();
}

// Déconnexion
if (isset($_POST['logout'])) {
    session_destroy();
    header("Location: index.php");
    exit();
}
?>

<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agenda Personnel</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f5f5f5;
        }

        header {
            text-align: center;
            background-color: #4CAF50;
            padding: 20px;
            color: white;
        }

        .container {
            max-width: 900px;
            margin: 0 auto;
            padding: 20px;
        }

        .agenda {
            display: flex;
            justify-content: space-between;
            flex-wrap: wrap;
            margin-top: 30px;
        }

        .event-box {
            background-color: #ffffff;
            padding: 20px;
            margin: 10px;
            border-radius: 8px;
            width: calc(50% - 20px);
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }

        .event-box h4 {
            margin-bottom: 10px;
            font-size: 18px;
        }

        form {
            margin-bottom: 20px;
        }

        input, textarea {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 5px;
            border: 1px solid #ccc;
        }

        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        button:hover {
            background-color: #45a049;
        }

        @media screen and (max-width: 600px) {
            .event-box {
                width: 100%;
            }
        }
    </style>
</head>
<body>
    <header>
        <h1>Agenda Personnel</h1>
    </header>

    <div class="container">
        <?php if (!isset($_SESSION['user_id'])): ?>
            <h2>Connexion / Inscription</h2>
            <form method="post" action="">
                <label>Nom d'utilisateur :</label>
                <input type="text" name="username" required>
                
                <label>Mot de passe :</label>
                <input type="password" name="password" required>
                
                <button type="submit" name="login">Connexion</button>
                <button type="submit" name="register">Inscription</button>
            </form>
        <?php else: ?>
            <h2>Bonjour, <?php echo $_SESSION['username']; ?> !</h2>
            <form method="post" action="">
                <label>Date de l'événement :</label>
                <input type="date" name="event_date" required>

                <label>Description :</label>
                <textarea name="event_description" required></textarea>

                <button type="submit" name="add_event">Ajouter un événement</button>
            </form>

            <div class="agenda">
                <?php foreach ($events as $event): ?>
                    <div class="event-box">
                        <h4><?php echo $event['date']; ?></h4>
                        <p><?php echo $event['description']; ?></p>
                    </div>
                <?php endforeach; ?>
            </div>

            <form method="post" action="">
                <button type="submit" name="logout">Déconnexion</button>
            </form>
        <?php endif; ?>
    </div>

    <script>
        // Vous pouvez ajouter des interactions supplémentaires avec JavaScript ici.
    </script>
</body>
</html>
