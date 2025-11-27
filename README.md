<?php
/* ------------------------------------------
   DATABASE SQL (Run this first in phpMyAdmin)
---------------------------------------------
CREATE DATABASE event_management;

USE event_management;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE,
    password VARCHAR(100)
);
-------------------------------------------*/

// Start session
session_start();

// Database connection
$conn = mysqli_connect("localhost", "root", "", "event_management");

// Handle registration
if (isset($_POST['register'])) {
    $name     = $_POST['name'];
    $email    = $_POST['email'];
    $password = $_POST['password'];

    $query = "INSERT INTO users(name, email, password) VALUES('$name','$email','$password')";
    mysqli_query($conn, $query);

    echo "<script>alert('Registration Successful');</script>";
}

// Handle login
if (isset($_POST['login'])) {
    $email    = $_POST['email'];
    $password = $_POST['password'];

    $check = mysqli_query($conn, "SELECT * FROM users WHERE email='$email' AND password='$password'");

    if (mysqli_num_rows($check)) {
        $_SESSION['email'] = $email;
        header("Location: event_system.php?page=dashboard");
        exit();
    } else {
        echo "<script>alert('Invalid Email or Password');</script>";
    }
}

// Handle logout
if (isset($_GET['logout'])) {
    session_destroy();
    header("Location: event_system.php");
    exit();
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Event Management System</title>

    <!-- Inline CSS -->
    <style>
        body { font-family: Arial; background: #f1f1f1; margin:0; padding:0;}
        .container {
            width: 350px; background:#fff; padding:25px;
            margin: 80px auto; border-radius:6px;
            box-shadow:0 0 10px rgba(0,0,0,0.1);
        }
        h2 { text-align:center; }
        .input-group{ margin-bottom:15px; }
        .input-group label { font-weight:bold; display:block; }
        .input-group input{
            width:100%; padding:10px; border:1px solid #aaa;
            border-radius:4px;
        }
        .btn{
            width:100%; padding:10px; background:#007bff;
            color:white; border:none; border-radius:4px;
            cursor:pointer; font-size:16px;
        }
        .btn:hover{ background:#0056b3; }
        p{ text-align:center; }
        a{ color:#007bff; text-decoration:none; }
    </style>
</head>

<body>

<?php
/* --------------------------------------------------
   PAGE ROUTING
--------------------------------------------------*/
$page = isset($_GET['page']) ? $_GET['page'] : 'login';

/* --------------------------------------------------
   LOGIN PAGE
--------------------------------------------------*/
if ($page == "login") {
?>
<div class="container">
    <h2>Login</h2>

    <form method="POST">
        <div class="input-group">
            <label>Email</label>
            <input type="email" name="email" required>
        </div>

        <div class="input-group">
            <label>Password</label>
            <input type="password" name="password" required>
        </div>

        <button class="btn" name="login">Login</button>

        <p>Don't have an account? <a href="event_system.php?page=register">Register</a></p>
    </form>
</div>

<?php
}

/* --------------------------------------------------
   REGISTRATION PAGE
--------------------------------------------------*/
if ($page == "register") {
?>
<div class="container">
    <h2>Register</h2>

    <form method="POST">

        <div class="input-group">
            <label>Full Name</label>
            <input type="text" name="name" required>
        </div>

        <div class="input-group">
            <label>Email</label>
            <input type="email" name="email" required>
        </div>

        <div class="input-group">
            <label>Password</label>
            <input type="password" name="password" required>
        </div>

        <button class="btn" name="register">Register</button>

        <p>Already have an account? <a href="event_system.php">Login</a></p>
    </form>
</div>

<?php
}

/* --------------------------------------------------
   DASHBOARD PAGE
--------------------------------------------------*/
if ($page == "dashboard") {

    if (!isset($_SESSION['email'])) {
        header("Location: event_system.php");
        exit();
    }
?>
<div class="container">
    <h2>Dashboard</h2>

    <p>Welcome, <strong><?php echo $_SESSION['email']; ?></strong></p>

    <p><a href="event_system.php?logout=1" class="btn" style="background:red;">Logout</a></p>
</div>

<?php } ?>

</body>
</html>
