# 1. Debian (Ubuntu & Linuxmint)

## 1.1. Mengkoneksikan file sharing samba (Client)

### 1.1.1. Buat folder pada `/mnt`

```bash
sudo mkdir /mnt/general
sudo mkdir /mnt/it
sudo mkdir /mnt/kam
sudo mkdir /mnt/warehouse
```

### 1.1.2. Buat file credentials

- Buat file:

  ```bash
  sudo nano ~/.smbcredentials
  ```

- Isikan username dan password:

  ```txt
  username=
  password=
  ```

- Lalu amankan:
  ```bash
  sudo chmod 600 ~/.smbcredentials
  ```

### 1.1.3. Edit file `/etc/fstab`

- Buka dieditor

```bash
sudo nano /etc/fstab
```

- Lalu tambahkan kode berikut. Pastikan ubah `{USERNAME}` dengan username pc anda.

```bash
//10.10.2.77/general /mnt/general cifs credentials=/home/{USERNAME}/.smbcredentials,iocharset=utf8,vers=3.0,_netdev,nofail,uid=1000,gid=1000,file_mode=0777,dir_mode=0777 0 0
//10.10.2.77/it /mnt/it cifs credentials=/home/{USERNAME}/.smbcredentials,iocharset=utf8,vers=3.0,_netdev,nofail,uid=1000,gid=1000,file_mode=0777,dir_mode=0777 0 0
//10.10.2.77/kam /mnt/kam cifs credentials=/home/{USERNAME}/.smbcredentials,iocharset=utf8,vers=3.0,_netdev,nofail,uid=1000,gid=1000,file_mode=0777,dir_mode=0777 0 0
//10.10.2.77/warehouse /mnt/warehouse cifs credentials=/home/{USERNAME}/.smbcredentials,iocharset=utf8,vers=3.0,_netdev,nofail,uid=1000,gid=1000,file_mode=0777,dir_mode=0777 0 0
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

### 1.3.3. Cek versi php

```bash
php -v
```

### 1.3.4. Install composer

- Install paket
  ```bash
  sudo apt install composer -y
  ```
- Cek versi composer
  ```bash
  composer -V
  ```

### 1.3.5. Install Laravel Installer

- Install Laravel Installer (Global)

  ```bash
  composer global require laravel/installer
  ```

- Tambahkan ke PATH

  ```bash
  nano ~/.bashrc
  ```

  Tambahkan kode ini dan simpan:

  ```bash
  export PATH="$HOME/.config/composer/vendor/bin:$PATH"
  ```

- Apply PATH

  ```bash
  source ~/.bashrc
  ```

- Verifikasi
  ```bash
  laravel --version
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

### 1.4.3. Membuat site available baru

- Buat file konfigurasi

  ```bash
  sudo nano /etc/nginx/sites-available/{NAMA_APLIKASI}
  ```

  Isikan dengan kode berikut:

  ```nginx
  server {
    listen 8000;
    server_name 127.0.0.1;

    root /path/ke/project/public;
    index index.php index.html;

    location / {
      try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
      include snippets/fastcgi-php.conf;
      fastcgi_pass unix:/var/run/php/php8.3-fpm.sock; # sesuaikan versi PHP
    }

    location ~ /\.ht {
      deny all;
    }
  }
  ```

- Aktifkan konfigurasi

  ```bash
  sudo ln -s /etc/nginx/sites-available/{NAMA_APLIKASI} /etc/nginx/sites-enabled/
  ```

- Tes konfigurasi

  ```bash
  sudo nginx -t
  ```

- Restart nginx

  ```bash
  sudo systemctl restart nginx
  ```

- Memastikan PHP-FPM aktif

  ```bash
  sudo systemctl status php8.3-fpm # sesuaikan versi PHP
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
SELECT user, host, plugin FROM mysql.user WHERE user = 'root';
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

### 1.6.3. Set PATH Flutter

- Tambahkan ke PATH:

  ```bash
  nano ~/.bashrc
  ```

- Tambahkan dibagian bawah:

  ```bash
  export PATH="$PATH:$HOME/flutter/bin"
  ```

- Simpan, lalu reload:
  ```bash
  source ~/.bashrc
  ```

### 1.6.4. Verifikasi instalasi Flutter

```bash
flutter doctor
```

Cek versi:

```bash
flutter --version
```

### 1.6.5. Install android studio

- Download android studio [Link download](https://developer.android.com/studio?hl=id), lalu extract
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

- Menambahkan android studio ke menu (Linuxmint)
  - Cek path:
    ```bash
    ls /opt/android-studio/bin/studio.sh
    ```
  - Buat file launcher
    ```bash
    nano ~/.local/share/applications/android-studio.desktop
    ```
    Isikan:
    ```bash
    [Desktop Entry]
    Version=1.0
    Type=Application
    Name=Android Studio
    Icon=/opt/android-studio/bin/studio.png
    Exec=/opt/android-studio/bin/studio.sh
    Comment=Android IDE
    Categories=Development;IDE;
    Terminal=false
    StartupNotify=true
    ```
  - Aktifkan file:
    ```bash
    chmod +x ~/.local/share/applications/android-studio.desktop
    ```
  - Trust file:
    ```bash
    gio set ~/.local/share/applications/android-studio.desktop metadata::trusted true
    ```
  - Refresh menu:
    ```bash
    update-desktop-database ~/.local/share/applications
    ```

### 1.6.6. Periksa instalasi

```bash
flutter doctor
```

### 1.6.7. Install ADB

```bash
sudo apt install adb
```

### 1.6.8. Buat Proyek Baru

```bash
flutter create my_app && cd my_app
```

Jalankan aplikasi:

```bash
flutter run
```

## 1.7. Install Postman

### 1.7.1. Download file

[Link download](https://www.postman.com/downloads/), atau bisa langsung:

```bash
wget https://dl.pstmn.io/download/latest/linux64 -O postman.tar.gz
```

### 1.7.2. Extract

```bash
tar -xvzf postman.tar.gz
```

### 1.7.3. Pindahkan ke /opt

```bash
sudo mv Postman /opt/postman
```

### 1.7.4. Buat symlink

```bash
sudo ln -s /opt/postman/Postman /usr/bin/postman
```

### 1.7.5. Jalankan

```bash
postman
```

### 1.7.6. Tambahkan ke Menu (Linux Mint)

- Buat file desktop:

  ```bash
  nano ~/.local/share/applications/postman.desktop
  ```

- Isikan dengan kode berikut:

  ```INI
  [Desktop Entry]
  Name=Postman
  Exec=/opt/postman/Postman
  Icon=/opt/postman/app/resources/app/assets/icon.png
  Type=Application
  Categories=Development;
  ```

- Simpan, lalu

  ```bash
  chmod +x ~/.local/share/applications/postman.desktop
  ```

## 1.8. Install VPN L2TP

### 1.8.1. Install paket yang dibutuhkan

```bash
sudo apt update
sudo apt install network-manager-l2tp network-manager-l2tp-gnome -y
sudo apt install strongswan -y
```

### 1.8.2. Restart Network Manager

```bash
sudo systemctl restart NetworkManager
```

### 1.8.3. Tambahkan koneksi VPV

- Buka Menu → Settings → Network
- Pilih VPN
- Klik tombol +
- Pilih:
  - Layer 2 Tunneling Protocol (L2TP)

## 1.9. Remote SFTP Tanpa Password

Membuat koneksi SFTP ke linux server tanpa harus selalu memasukan password (Untuk backup Feelbuy Shop)

### 1.9.1. Generate SSH Key

```bash
ssh-keygen -t ed25519 -C "linuxmint"
```

Tekan Enter terus untuk lokasi default:

```bash
~/.ssh/id_ed25519
```

### 1.9.2. Copy Public Key ke Ubuntu Server

Ubah username dan ip-server

```bash
ssh-copy-id username@ip-server
```

### 1.9.3. Test Login

```bash
ssh username@ip-server
```

## 1.10. Menambah Swap RAM dan ZRAM

### 1.10.1. Menambahkan swap ram

- Periksa kondisi saat ini:

  ```bash
  free -h
  swapon --show
  ```

- Jika swap sudah ada. nonaktifkan terlebih dahulu.

  ```bash
  sudo swapoff /swapfile
  ```

  Verifikasi:

  ```
  swapon --show
  ```

  Seharusnya tidak ada output untuk /swapfile.

- Hapus swap lama

  ```bash
  sudo rm /swapfile
  ```

- Buat Swap File Baru 8 GB

  ```bash
  sudo fallocate -l 8G /swapfile
  ```

  Jika fallocate gagal:

  ```bash
  sudo dd if=/dev/zero of=/swapfile bs=1M count=8192 status=progress
  ```

- Atur Permission

  ```bash
  sudo chmod 600 /swapfile
  ```

  Periksa:

  ```bash
  ls -lh /swapfile
  ```

- Format Sebagai Swap

  ```bash
  sudo mkswap /swapfile
  ```

- Aktifkan Swap

  ```bash
  sudo swapon /swapfile
  ```

  Verifikasi:

  ```bash
  swapon --show
  free -h
  ```

  Contoh output:

  ```bash
  NAME      TYPE SIZE USED PRIO
  /swapfile file   8G   0B   -2
  ```

- Pastikan Aktif Saat Boot

  Cek isi /etc/fstab:

  ```bash
  cat /etc/fstab
  ```

  Pastikan terdapat baris:

  ```bash
  /swapfile none swap sw 0 0
  ```

  Jika belum ada:

  ```bash
  echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
  ```

- Verifikasi Setelah Reboot

  ```bash
  sudo reboot
  ```

  Setelah masuk kembali:

  ```bash
  swapon --show
  ```

### 1.10.2. Install ZRAM

Dengan RAM 8 GB, konfigurasi yang umum dan cukup efektif adalah:

- ZRAM: 4 GB (50% RAM)
- Swapfile: 8 GB (yang baru saja Anda buat)

Prioritasnya dibuat sehingga Linux akan menggunakan ZRAM terlebih dahulu karena jauh lebih cepat dibanding swapfile di SSD/HDD.

- Cek Apakah ZRAM Sudah Aktif

  ```bash
  swapon --show
  ```

  Jika hanya muncul /swapfile, berarti ZRAM belum aktif.

- Install ZRAM

  ```bash
  sudo apt update && sudo apt install systemd-zram-generator
  ```

- Buat Konfigurasi

  Buat file konfigurasi:

  ```bash
  sudo nano /etc/systemd/zram-generator.conf
  ```

  Isi dengan:

  ```ini
  [zram0]
  zram-size = ram / 2
  compression-algorithm = zstd
  swap-priority = 100

  ```

  Keterangan:

  - ram / 2 → 4 GB ZRAM untuk RAM 8 GB.
  - zstd → algoritma kompresi yang cepat dan efisien.
  - swap-priority = 100 → ZRAM digunakan sebelum swapfile.

  Simpan lalu keluar (Ctrl+O, Enter, Ctrl+X).

- Aktifkan ZRAM

  Reload konfigurasi:

  ```bash
  sudo systemctl daemon-reload && sudo systemctl start systemd-zram-setup@zram0
  ```

  Jika muncul error, reboot:

  ```bash
  sudo reboot
  ```

- Verifikasi

  Setelah reboot:

  ```bash
  swapon --show
  ```

  Hasil yang diharapkan:

  ```
  NAME        TYPE      SIZE USED PRIO
  /dev/zram0  partition 4G   0B   100
  /swapfile   file      8G   0B   -2
  ```

  Atau:

  ```bash
  zramctl
  ```

  Contoh output:

  ```bash
  NAME       ALGORITHM DISKSIZE DATA COMPR TOTAL STREAMS
  /dev/zram0 zstd          4G
  ```

- Optimasi Swappiness

  Agar Linux tidak terlalu agresif menggunakan swapfile:

  ```bash
  echo 'vm.swappiness=10' | sudo tee /etc/sysctl.d/99-swappiness.conf
  ```

  ```bash
  sudo sysctl --system
  ```

  Verifikasi:

  ```bash
  cat /proc/sys/vm/swappiness
  ```

  Hasil:

  ```
  10
  ```
