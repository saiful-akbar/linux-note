# 1. Debian (Ubuntu & Linuxmint)

## 1.1. Cara mengkoneksikan file sharing samba (Client)

### 1.1.1. Buat folder pada `/mnt`

```bash
sudo mkdir /mnt/general
sudo mkdir /mnt/it
```

### 1.1.2. Buat file credentials

```bash
sudo mkdir -p ~/.smbcredentials
```

### 1.1.3. Edit file `/etc/fstab`

- Buka dieditor

```bash
sudo nano /etc/fstab
```

- Lalu tambahkan kode berikut

```bash
//10.10.2.77/general /mnt/general cifs credentials=/home/{USERNAME}/.smbcredentials,iocharset=utf8,uid=1000,gid=1000,nofail,_x-systemd.automount 0 0
//10.10.2.77/it /mnt/it cifs credentials=/home/{USERNAME}/.smbcredentials,iocharset=utf8,uid=1000,gid=1000,nofail,_x-systemd.automount 0 0
```

### 1.1.3. Jalankan mount berikut

```bash
sudo mount -a
```

Jika ingin mengapus mount (unmount)

```bash
sudo umount /mnt/{folder yang ingin di-unmount}
```

### 1.1.4. Tambahkan bookmark pada folder sharing

- Buka file manager
- arahkan ke `/mnt`
- Tambahkan folder sharing ke bookmark

## 1.2. Install GIT

### 1.2.1. Install paket

```bash
sudo apt update && sudo apt install git -y
```

### 1.2.2. Periksa instalasi

```bash
git --version
```

### 1.2.3. Buat global konfigurasi

```bash
git config --global init.defaultBranch main
git config --global user.name {nama}
git config --global user.email {email}
```

## 1.3. Install PHP

### 1.3.1. Install paket

```bash
sudo apt update && sudo apt install php -y
```

### 1.3.2. Install extension

Extension yang umum digunakan

```bash
sudo apt install php-fpm php-gd php-cli php-mbstring php-xml php-curl php-mysql php-zip unzip -y
```

### 1.3.3. Cek versi

```bash
php -v
```

### 1.3.4. Install composer

```bash
sudo apt install composer -y
```

### 1.3.4. Cek versi composer

```bash
composer -V
```

## 1.4. Install NGINX

### 1.4.1. Install paket

```bash
sudo apt install nginx -y
```

### 1.4.2. Start service

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
```

> [!CAUTION]
> Jika terjadi error kemungkinan port :80 terpakai apache2 yang ikut terinstal saat instal php

- Periksa apakah ada package apache2

```bash
dpkg -l | grep apache2
```

- Jika muncul hapus semua packagenya

```bash
sudo apt remove {daftar package apache2} -y
sudo apt autoremove
sudo apt autoclean
```

## 1.5. Install Database MySQL

### 1.5.1 Install paket

```bash
sudo apt update && sudo apt install mysql-server
```

Cek versi:

```bash
mysql --version
```

### 1.5.2. Periksa & Jalankan service

- Cek status service:

```bash
sudo systemctl status mysql
```

- Kalau belum aktif:

```bash
sudo systemctl start mysql
```

- Agar MySQL auto start saat booting:

```bash
sudo systemctl enable mysql
```

- Disable auto start:

```bash
sudo systemctl disable mysql
```

### 1.5.3. Buat password untuk user root

- Masuk MySQL:

```bash
sudo mysql
```

- Lihat daftar user:

```sql
SELECT user, host, plugin FROM mysql.user WHERE user = 'root'
```

- Kalau hasilnya:
  - auth_socket → perlu diubah ke password
  - mysql_native_password → tinggal set password saja

- Set password root

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password_baru';
```

```sql
FLUSH PRIVILEGES;
```

- Keluar MySQL dan tes login

```sql
EXIT;
```

```bash
sudo mysql -u root -p
```

### 1.5.4. Amankan instalasi

```bash
sudo mysql_secure_installation
```

Ikuti langkah-langkah:

- Set password root
- Hapus user anonymous → YES
- Disable remote root login → YES
- Remove test database → YES
- Reload privilege → YES

## 1.6. Install Dart & Flutter

### 1.6.1. Install Dependency Dasar

```bash
sudo apt update && sudo apt install -y curl git unzip xz-utils zip libglu1-mesa
```

### 1.6.2. Install Flutter SDK

```bash
cd ~ && git clone https://github.com/flutter/flutter.git -b stable
```

Cek:

```bash
flutter --version
```

### 1.6.3. Download android studio

- Download android studio [Link download](https://developer.android.com/studio?hl=id)
- Extract
  ```bash
  cd ~/Downloads && tar -xvf android-studio-*.tar.gz
  ```
- Pindahkan ke `/opt`
  ```bash
  sudo mv android-studio /opt/
  ```
- Jalankan
  ```bash
  /opt/android-studio/bin/studio.sh
  ```
- Install Android SDK
  - Android SDK Build-Tools
  - Android SDK Command-line Tools
  - Android Emulator
  - Android SDK Platform-Tools

- Hubungkan Flutter dengan Android

  ```bash
  flutter doctor --android-licenses
  ```

  Tekan:

  ```
  y (accept semua)
  ```

### 1.6.4. Periksa instalasi

```bash
flutter doctor
```

### 1.6.5. Install ADB

```bash
sudo apt install adb
```

### 1.6.6. Buat Proyek Baru

```bash
flutter create my_app && cd my_app
```

Jalankan aplikasi:

```bash
flutter run
```
