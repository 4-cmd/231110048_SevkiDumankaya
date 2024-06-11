# 231110048_SevkiDumankaya
Wep Programla Final Ödevi 
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Kayıt Formu</title>
<style>
    form {
        max-width: 400px;
        margin: 0 auto;
    }
    input[type="text"],
    input[type="email"],
    input[type="password"],
    select {
        width: 100%;
        padding: 10px;
        margin-bottom: 10px;
        box-sizing: border-box;
    }
    input[type="submit"] {
        background-color: #4CAF50;
        color: white;
        padding: 15px 20px;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        width: 100%;
    }
    input[type="submit"]:hover {
        background-color: #45a049;
    }
    table {
        width: 100%;
        border-collapse: collapse;
    }
    th, td {
        border: 1px solid #ddd;
        padding: 8px;
        text-align: left;
    }
    th {
        background-color: #f2f2f2;
    }
</style>
</head>
<body>

<?php
$errors = array();

// Veritabanına bağlanma bilgileri
$servername = "localhost";
$username = "kullanici_adi";
$password = "sifre";
$dbname = "veritabani_adi";

try {
    // Veritabanı bağlantısı
    $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
    // Hata modunu ayarla
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    // Formdan gelen verileri işle
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        // Form verilerini al
        $fname = $_POST['fname'];
        $lname = $_POST['lname'];
        $email = $_POST['email'];
        $password = $_POST['password'];
        $password_confirm = $_POST['password_confirm'];
        $birthdate = $_POST['birthdate'];
        $gender = $_POST['gender'];

        // Detaylı validasyonlar
        // (Burası kontrol edilecek)

        // Şifreleri şifrele
        $hashed_password = password_hash($password, PASSWORD_DEFAULT);

        // Veritabanına ekleme işlemi
        $stmt = $conn->prepare("INSERT INTO users (first_name, last_name, email, password, birthdate, gender) 
                                VALUES (:fname, :lname, :email, :password, :birthdate, :gender)");
        $stmt->bindParam(':fname', $fname);
        $stmt->bindParam(':lname', $lname);
        $stmt->bindParam(':email', $email);
        $stmt->bindParam(':password', $hashed_password);
        $stmt->bindParam(':birthdate', $birthdate);
        $stmt->bindParam(':gender', $gender);
        $stmt->execute();

        echo "Kayıt işlemi başarılı!";
    }

    // Kayıtlı kullanıcıları sorgula
    $stmt = $conn->query("SELECT * FROM users");
    $users = $stmt->fetchAll(PDO::FETCH_ASSOC);

    if (count($users) > 0) {
        echo "<table>";
        echo "<tr><th>İsim</th><th>Soyisim</th><th>E-posta</th><th>Doğum Tarihi</th><th>Cinsiyet</th></tr>";
        foreach ($users as $user) {
            echo "<tr>";
            echo "<td>" . $user['first_name'] . "</td>";
            echo "<td>" . $user['last_name'] . "</td>";
            echo "<td>" . $user['email'] . "</td>";
            echo "<td>" . $user['birthdate'] . "</td>";
            echo "<td>" . $user['gender'] . "</td>";
            echo "</tr>";
        }
        echo "</table>";
    } else {
        echo "Kayıtlı kullanıcı bulunamadı.";
    }
} catch(PDOException $e) {
    echo "Hata: " . $e->getMessage();
}

$conn = null; // Bağlantıyı kapat
?>

<form method="post">
    <label for="fname">İsim:</label>
    <input type="text" id="fname" name="fname" required>

    <label for="lname">Soyisim:</label>
    <input type="text" id="lname" name="lname" required>

    <label for="email">E-posta:</label>
    <input type="email" id="email" name="email" required>

    <label for="password">Şifre:</label>
    <input type="password" id="password" name="password" required>

    <label for="password_confirm">Şifre (Tekrar):</label>
    <input type="password" id="password_confirm" name="password_confirm" required>

    <label for="birthdate">Doğum Tarihi:</label>
    <input type="date" id="birthdate" name="birthdate" required>

    <label for="gender">Cinsiyet:</label>
    <select id="gender" name="gender" required>
        <option value="">Seçiniz</option>
        <option value="male">Erkek</option>
        <option value="female">Kadın</option>
        <option value="other">Diğer</option>
    </select>

    <input type="submit" value="Kaydol">
</form>

</body>
</html>
