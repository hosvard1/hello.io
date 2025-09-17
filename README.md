 <!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Регистрация на стиле Instagram</title>
    <link rel="stylesheet" href="style.css">
    <style>
        * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: Arial, sans-serif;
    background-color: #fafafa;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.form-container {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    width: 320px;
    padding: 20px;
    text-align: center;
}

h2 {
    font-size: 22px;
    margin-bottom: 20px;
}

input {
    width: 100%;
    padding: 10px;
    margin: 10px 0;
    border: 1px solid #ddd;
    border-radius: 5px;
    font-size: 14px;
}

button {
    width: 100%;
    padding: 10px;
    background-color: #3897f0;
    color: white;
    border: none;
    border-radius: 5px;
    font-size: 16px;
    cursor: pointer;
}

button:hover {
    background-color: #3488d4;
}

.login-link {
    margin-top: 15px;
    font-size: 14px;
}

.login-link a {
    color: #3897f0;
    text-decoration: none;
}

.login-link a:hover {
    text-decoration: underline;
}
    </style>
</head>
<body>
    <div class="form-container">
        <h2>Создать аккаунт</h2>
        <form action="#" method="POST">
            <input type="text" placeholder="Имя" required>
            <input type="email" placeholder="Электронная почта" required>
            <input type="password" placeholder="Пароль" required>
            <button type="submit">Зарегистрироваться</button>
        </form>
        <div class="login-link">
            <p>Уже есть аккаунт? <a href="#">Войти</a></p>
        </div>
    </div>
</body>
</html>
