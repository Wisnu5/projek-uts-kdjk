# projek-uts-kdjk

## Sekilas Tentang
Tamari adalah aplikasi web gratis dan open source yang membantu user mengelola resep masakan. Fitur-fitur utamanya mencakup pembuatan akun user dengan login menggunakan email, pengelompokan resep berdasarkan kategori dan favorit, serta fitur meal planner yang memungkinkan user menjadwalkan masakan hingga 30 hari ke depan. User juga dapat membuat daftar belanja berdasarkan bahan resep dan menandai item yang telah dibeli. Aplikasi ini mendukung kustomisasi seperti tema, gambar profil, dan gaya tampilan resep, serta memungkinkan impor/ekspor data. Selain itu, Tamari menawarkan lebih dari 100.000 resep yang dapat dicari dan disimpan langsung tanpa harus mengunjungi situs asli.

## Automation scripts

### Installing Docker

Untuk menginstall docker kita dapat melihat langsung dokumentasi dari docker sendiri [disini](https://docs.docker.com/engine/install/), namun disini saya membuat script bantuan untuk machine dengan sistem operasi ubuntu agar mempermudah proses download docker (tidak dengan docker desktop) dan post instalasinya.

jalankan dengan priviledge root / sudo, berikut scriptnya : 

```bash
#!/bin/bash

if [ "$EUID" -ne 0 ]; then
  echo "This script must be run as root. Please re-run with sudo."
  exit 1
fi

for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
apt-get update
apt-get install -y ca-certificates curl
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update

apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin 

groupadd docker

usermod -aG docker $SUDO_USER

echo "Docker installed successfully. Please log out and log back in to apply the group changes."
```

setelah selesai docker akan terinstall dalam machine anda tetapi perlu dilakukan refresh terhadap group di machine tersebut, hal ini bisa digapai dengan melakukan relog user atau menjalankan command :
```
newgrp docker
```
hal ini dikarenakan kita menambahkan user kita ke group docker agar bisa menjalankan command docker tanpa prefix sudo sehingga perlu refresh group, hal ini adalah step tambahan dalam proses instalasi docker.


### Build docker image

Aplikasi yang kami hosting sudah terdapat Dockerfile tetapi belum memiliki docker compose file, sehingga kami memutuskan untuk membuat compose filenya, dalam versi docker terbaru compose file diberi nama compose.yml (sebelumnya docker-compose.yml) dan tidak perlu menambahkan version pada yaml filenya.

compose.yml
```yaml
services:
  tamari-app:
    build: .
    ports:
      - 4888:4888
    restart: always
    environment:
      - MAIL_SERVER=mail.example.com
      - MAIL_PORT=587
      - MAIL_USE_TLS=1
      - MAIL_USERNAME=test@gmail.com
      - MAIL_PASSWORD=password
    volumes: 
      - ./TamariData:/app/appdata
```

lalu untuk menjalankannya kita dapat mengetikkan command sebagai berikut : 

```
docker compose up -d
```

flag -d ditambahkan untuk menjalankan aplikasi dalam mode detach. setelah berhasil di build aplikasi akan berjalan dalam port 4888.

## Cara Pemakaian
1. Sebelum dapat mengakses Tamari Web App, kita diharuskan membuat akun terlebih dahulu
![image](https://github.com/user-attachments/assets/7edf2cbd-be4b-41a4-ae52-8ee80192eb12)
