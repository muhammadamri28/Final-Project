# Website Monitoring dan Manajemen Data Sederhana

# Spesifikasi Server
  * Ubuntu 24.04:
  * RAM:4GB
  * Storage:50GB HDD

# Layanan Server yang Digunakan
1. Apache
2. MySQL
3. Docker
4. Firewall
5. Prometheus

# Persiapan awal
1. Update Sistem
* sudo apt update && sudo apt upgrade -y
2. Install Dependensi Dasar
* sudo apt install -y curl wget git unzip

#  Instalasi Apache Web Server
3. Install Apache
* sudo apt install -y apache2
4. Konfigurasi Firewall untuk Apache
* sudo ufw allow 'Apache Full'
* sudo ufw enable
5. Cek Status Apache
* systemctl status apache2

# Instalasi MySQL
1. Install MySQL Server
* sudo apt install -y mysql-server
2. Amankan Instalasi MySQL
* sudo mysql_secure_installation
3. sudo mysql -u root -p
* CREATE DATABASE project_db;
CREATE USER 'project_user'@'localhost' IDENTIFIED BY 'password123';
GRANT ALL PRIVILEGES ON project_db.* TO 'project_user'@'localhost';
FLUSH PRIVILEGES;
4. Cek Database
* SHOW DATABASES;

# Instalasi Docker dan Docker-Compose
1. Install Docker
* sudo apt install -y docker.io
* sudo systemctl enable docker
* sudo systemctl start docker
2. Install Docker-Compose
* sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
* sudo chmod +x /usr/local/bin/docker-compose
3. Verifikasi Instalasi
* docker --version
* docker-compose --version
# Instalasi Prometheus
* mkdir ~/prometheus && cd ~/prometheus
1. Download Prometheus

* wget https://github.com/prometheus/prometheus/releases/download/v2.47.0/prometheus-2.47.0.linux-amd64.tar.gz
tar -xvzf prometheus-2.47.0.linux-amd64.tar.gz
* mv prometheus-2.47.0.linux-amd64 prometheus

# Jalankan Prometheus
* cd prometheus
* ./prometheus
# Konfigurasi Firewall
* sudo ufw allow 9090  
* sudo ufw allow 3306
* sudo ufw status
# Pembuatan Aplikasi Web Sederhana
* mkdir ~/project-web && cd ~/project-web
1. Buat File PHP untuk Aplikasi
   * <?php
$conn = new mysqli('localhost', 'project_user', 'password123', 'project_db');
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $name = $_POST['name'];
    $message = $_POST['message'];
    $conn->query("INSERT INTO messages (name, message) VALUES ('$name', '$message')");
}
$result = $conn->query("SELECT * FROM messages");
?>
<!DOCTYPE html>
<html>
<head><title>Project Web</title></head>
<body>
    <form method="POST">
        <input type="text" name="name" placeholder="Your Name" required>
        <textarea name="message" placeholder="Your Message" required></textarea>
        <button type="submit">Submit</button>
    </form>
    <h2>Messages</h2>
    <ul>
        <?php while ($row = $result->fetch_assoc()) { ?>
            <li><b><?php echo $row['name']; ?></b>: <?php echo $row['message']; ?></li>
        <?php } ?>
    </ul>
</body>
</html>

# simpan
* sudo cp ~/project-web/index.php /var/www/html/
# Restart Apache
* sudo systemctl restart apache2
# Buat docker-compose.yml
* version: '3.8'
services:
  web:
    image: php:7.4-apache
    ports:
      - "80:80"
    volumes:
      - ./project-web:/var/www/html
  db:
    image: mysql:5.7
    environment:
      MYSQL_DATABASE: project_db
      MYSQL_USER: project_user
      MYSQL_PASSWORD: password123
      MYSQL_ROOT_PASSWORD: rootpass123
    ports:
      - "3306:3306"
# Jalankan Docker-Compose
* docker-compose up -d




