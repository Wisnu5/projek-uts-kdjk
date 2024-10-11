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
<img src="https://github.com/user-attachments/assets/7edf2cbd-be4b-41a4-ae52-8ee80192eb12">
2. Setelah berhasil mendaftar, kita akan diarahkan untuk melakukan sign in
<img src="https://github.com/user-attachments/assets/3bea3dda-fc01-45de-9ee3-188d2d123149">
3. Berikut tampilan dashboard setelah masuk (Bagian Explore)
<img src="https://github.com/user-attachments/assets/21162396-c85d-4e70-8171-48504ea5b935">
4. Ketika memilih satu menu, berikut tampilannya
<img src="https://github.com/user-attachments/assets/4ba8a3c7-53aa-4743-b05c-965e2095f087">
5. Resep dapat disimpan dan tampilan sebagai berikut
<img src="https://github.com/user-attachments/assets/8fcb4cdc-cc89-4abf-955f-88166ccdc77a">
6. Resep yang sudah disimpan pindah ke bagian My Recipes
<img src="https://github.com/user-attachments/assets/081b4e1c-9b69-40bc-95ef-f8ea4a9ad2c9">
7. User juga dapat menambahkan resep sendiri & mengisi format sebagai berikut
<img src="https://github.com/user-attachments/assets/19952934-e461-4826-ac1d-cc48fd30ddad">
8. User dapat menyertakan source link resep yang akan diupload dan website akan membantu otomatis mengisi form sesuai resep pada link tersebut
<img src="https://github.com/user-attachments/assets/ce0bdebb-ea9d-4513-bcdc-d35d5dc407b5">
9. Setelah mengisi form di atas, ditampilkan resep yang baru ditambahkan dan pindah ke bagian My Recipes
<img src="https://github.com/user-attachments/assets/2cfe4d21-430c-42f2-877c-b6c6fd628b9c">
10. Berikut tampilan resep yang sudah dibuat
<img src="https://github.com/user-attachments/assets/732fd9f7-9ccc-4dd7-a8e7-177b85cfeae0">
11. User dapat menambahkan resep favorit dan akan muncul di bagian My Recipes
<img src="https://github.com/user-attachments/assets/90ae1b98-db81-4989-9343-dac9784d6916">
12. User dapat menjadikan resep menjadi public dan membagikannya melalui link
<img src="https://github.com/user-attachments/assets/b18f58d9-34d5-4429-b856-04ab210348df">
Link: <a href="https://app.tamariapp.com/recipe/babaec82">Grilled Salmon - Tamari<a>
13. User dapat memasukkan menjadwalkan resep ke Meal Plannernya sesuai tanggal yang diinginkan
<img src="https://github.com/user-attachments/assets/b583402b-8b14-4365-b287-659a433562bd">
<img src="https://github.com/user-attachments/assets/4105c30e-2779-45a4-a6f8-f9ff368c49a2">
14. Pada Meal Planner, user juga dapat melihat resep yang sudah selesai sehingga dapat track Meal Plan yang sudah dimakan sebelumnya
<img src="https://github.com/user-attachments/assets/9c45d9d7-1815-492f-8410-4b294ed0b30c">
15. User dapat menambahkan bahan-bahan pada resep ke Shopping List sesuai kategorinya
<img src="https://github.com/user-attachments/assets/1b11c0f2-534c-467c-9e5e-7502f3267bb7">
16. User dapat melihat list belanjaan resep yang sudah dikategorikan pada bagian Shopping List (user juga dapat menambah atau menghapus juga list belanjaan yang sudah ada)
<img src="https://github.com/user-attachments/assets/e472f808-05d0-47d3-a0b6-e34afc4ad382">
17. Setelah menjelajahi bagian My Recipes, Explore, Shopping List & Meal Planner, berikut bagian yang tertera pada About
<img src="https://github.com/user-attachments/assets/1ec8d653-1cfc-42c9-9c85-bce921bf26d6">
18. Untuk bagian Profil (Preference), kita dapat melakukan edit seperti berikut
Before:
<img src="https://github.com/user-attachments/assets/d273942d-f11c-42da-baba-886d5c8ab424">
After:
<img src="https://github.com/user-attachments/assets/91ea4093-7d4f-4eec-9ab5-d2437c33e61d">
19. Sedangkan pada bagian History, kita dapat melihat seluruh aktivitas yang sudah dilakukan pada website
<img src="https://github.com/user-attachments/assets/d81e409d-b106-4771-a51e-81b310b9fc3b">
<img src="https://github.com/user-attachments/assets/c773b0ed-f7ab-497c-bcc1-3f0710944e00">
20. User juga dapat mendownload keseluruhan data yang sudah disimpan pada website
<img src="https://github.com/user-attachments/assets/4d02fb95-ad00-4d8c-9c92-bc9ed9fcde0f">
21. Hasil download data berisi berbagai resep yang sudah ditambahkan (dalam bentuk file Excel) dan gambar dari resep (dalam format JPEG)
<img src="https://github.com/user-attachments/assets/760843e2-57fa-431f-b977-401e36472117">
22. User juga dapat mengganti password & menghapus akun
<img src="https://github.com/user-attachments/assets/aeace88b-0980-4cf4-895e-9d89400cdb5c">
23. User dapat melakukan Sign Out (Logo Profile bagian kanan atas)
<img src="https://github.com/user-attachments/assets/1e0d68a6-0ab3-444d-b21e-23e6d2d90afa">
24. User berhasil melakukan logout
<img src="https://github.com/user-attachments/assets/b4200b3e-cdd0-40ef-9d58-8edfed6d05ae">
25. User dapat kembali melakukan login dan data sebelumnya pada website tetap ada
<img src="https://github.com/user-attachments/assets/babad38b-4f3f-413b-b7c0-23f9e682de84">

## Pembahasan 

### Kelebihan
### Kekurangan

### Perbandingan dengan Web App Sejenis

## Referensi


