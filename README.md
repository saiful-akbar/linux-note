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

### 1.1.3. Edit file `sudo nano /etc/fstab` lalu tambahkan kode berikut

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

### 1.1.4. Buka file manager lalu tambahkan bookmark pada folder sharing `/mnt/*`
