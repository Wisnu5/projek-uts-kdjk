# projek-uts-kdjk
Tugas Projek sesi UTS mata kuliah Komdat kelompok 6, Dapat diakses di http://103.139.193.140:4888/

Kelompok 6 KDJK P3:
1. Wisnu Al Hussaeni (G6401221008) ‎  ‎ ‎ 
2. Numero Uno Arroefy (G6401221014)‎ ‎ ‎ 
3. Aisha Fitria Salsabila (G6401221019)‎ ‎ ‎
4. Ahmad Qaulan Sadida‎ (G6401221032) 
5. T. Mochamad Rafly‎ (G6401221121)

# Aplikasi Web Tamari

![image](https://github.com/user-attachments/assets/ec139aff-4e5d-40aa-8591-f66b2906570f)

[Sekilas Tentang](#sekilas-tentang) | [Instalasi](#automation-scripts) | [Otomisasi](#build-docker-image) |[Cara Pemakaian](#cara-pemakaian) | [Pembahasan](#pembahasan) | [Referensi](#referensi)
:---:|:---:|:---:|:---:|:---:|:---:

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

## VPS hosting

setelah dapat menjalankan di local agar bisa diakses melalui internet, kita memerlukan ip publik. Kami memutuskan untuk menggunakan VPS dari IDCloud. Kita bisa membuka lama compute dan klik tombol new untuk membuat resource baru

![{CB471819-DF4A-4198-8494-FC7701A3BFE7}](https://github.com/user-attachments/assets/86b71cfe-f85e-45d8-855c-7ea54bd9e112)

disini kami memutuskan menggunakan VPS dengan spesifikasi terendah yaitu 2 GB ram dan 20 GB memory dengan operasi sistem ubuntu.

![{0C42A5EA-C98E-4A01-B882-128DB4969038}](https://github.com/user-attachments/assets/e5152a58-f5fb-460a-b3a4-2ca91e68d7da)

setelah VPS berhasil dibuild kita akan mendapat kredensial untuk ssh, setelah masuk ke sesi ssh kita bisa mengikuti step automasi diatas untuk mendownload docker, ketika docker sudah terisntall maka kita hanya perlu untuk menjalankan applikasi dengan perintah

```
docker compose up -d
```
dan aplikasi berjalan pada port 4888, dapat kita cek dengan perintah

```
docker ps
```

![image](https://github.com/user-attachments/assets/c0efb157-fe2b-4aca-9628-cd0ccc68d415)


## Cara Pemakaian
1. Sebelum dapat mengakses Tamari Web App, kita diharuskan membuat akun terlebih dahulu <br>
<img src="https://github.com/user-attachments/assets/7edf2cbd-be4b-41a4-ae52-8ee80192eb12"> <br>

2. Setelah berhasil mendaftar, kita akan diarahkan untuk melakukan sign in <br>
<img src="https://github.com/user-attachments/assets/3bea3dda-fc01-45de-9ee3-188d2d123149"> <br>

3. Berikut tampilan dashboard setelah masuk (Bagian Explore) <br>
<img src="https://github.com/user-attachments/assets/21162396-c85d-4e70-8171-48504ea5b935"> <br>

4. Ketika memilih satu menu, berikut tampilannya <br>
<img src="https://github.com/user-attachments/assets/4ba8a3c7-53aa-4743-b05c-965e2095f087"> <br>

5. Resep dapat disimpan dan tampilan sebagai berikut <br>
<img src="https://github.com/user-attachments/assets/8fcb4cdc-cc89-4abf-955f-88166ccdc77a"> <br>

6. Resep yang sudah disimpan pindah ke bagian My Recipes <br>
<img src="https://github.com/user-attachments/assets/081b4e1c-9b69-40bc-95ef-f8ea4a9ad2c9"> <br>

7. User juga dapat menambahkan resep sendiri & mengisi format sebagai berikut <br>
<img src="https://github.com/user-attachments/assets/19952934-e461-4826-ac1d-cc48fd30ddad"> <br>

8. User dapat menyertakan source link resep yang akan diupload dan website akan membantu otomatis mengisi form sesuai resep pada link tersebut <br>
<img src="https://github.com/user-attachments/assets/ce0bdebb-ea9d-4513-bcdc-d35d5dc407b5"> <br>

9. Setelah mengisi form di atas, ditampilkan resep yang baru ditambahkan dan pindah ke bagian My Recipes <br>
<img src="https://github.com/user-attachments/assets/2cfe4d21-430c-42f2-877c-b6c6fd628b9c"> <br>

10. Berikut tampilan resep yang sudah dibuat <br>
<img src="https://github.com/user-attachments/assets/732fd9f7-9ccc-4dd7-a8e7-177b85cfeae0"> <br>

11. User dapat menambahkan resep favorit dan akan muncul di bagian My Recipes <br>
<img src="https://github.com/user-attachments/assets/90ae1b98-db81-4989-9343-dac9784d6916"> <br>

12. User dapat menjadikan resep menjadi public dan membagikannya melalui link <br>
<img src="https://github.com/user-attachments/assets/b18f58d9-34d5-4429-b856-04ab210348df"> <br>
Link: <a href="https://app.tamariapp.com/recipe/babaec82">Grilled Salmon - Tamari<a> <br>

13. User dapat memasukkan menjadwalkan resep ke Meal Plannernya sesuai tanggal yang diinginkan <br>
<img src="https://github.com/user-attachments/assets/b583402b-8b14-4365-b287-659a433562bd"> <br>
<img src="https://github.com/user-attachments/assets/4105c30e-2779-45a4-a6f8-f9ff368c49a2"> <br>

14. Pada Meal Planner, user juga dapat melihat resep yang sudah selesai sehingga dapat track Meal Plan yang sudah dimakan sebelumnya <br>
<img src="https://github.com/user-attachments/assets/9c45d9d7-1815-492f-8410-4b294ed0b30c"> <br>

15. User dapat menambahkan bahan-bahan pada resep ke Shopping List sesuai kategorinya <br>
<img src="https://github.com/user-attachments/assets/1b11c0f2-534c-467c-9e5e-7502f3267bb7"> <br>

16. User dapat melihat list belanjaan resep yang sudah dikategorikan pada bagian Shopping List (user juga dapat menambah atau menghapus juga list belanjaan yang sudah ada) <br>
<img src="https://github.com/user-attachments/assets/e472f808-05d0-47d3-a0b6-e34afc4ad382"> <br>

17. Setelah menjelajahi bagian My Recipes, Explore, Shopping List & Meal Planner, berikut bagian yang tertera pada About <br>
<img src="https://github.com/user-attachments/assets/1ec8d653-1cfc-42c9-9c85-bce921bf26d6"> <br>

18. Untuk bagian Profil (Preference), kita dapat melakukan edit seperti berikut <br>
Before: <br>
<img src="https://github.com/user-attachments/assets/d273942d-f11c-42da-baba-886d5c8ab424"> <br>
After: <br>
<img src="https://github.com/user-attachments/assets/91ea4093-7d4f-4eec-9ab5-d2437c33e61d"> <br>

19. Sedangkan pada bagian History, kita dapat melihat seluruh aktivitas yang sudah dilakukan pada website <br>
<img src="https://github.com/user-attachments/assets/d81e409d-b106-4771-a51e-81b310b9fc3b"> <br>
<img src="https://github.com/user-attachments/assets/c773b0ed-f7ab-497c-bcc1-3f0710944e00"> <br>

20. User juga dapat mendownload keseluruhan data yang sudah disimpan pada website <br>
<img src="https://github.com/user-attachments/assets/4d02fb95-ad00-4d8c-9c92-bc9ed9fcde0f"> <br>

21. Hasil download data berisi berbagai resep yang sudah ditambahkan (dalam bentuk file Excel) dan gambar dari resep (dalam format JPEG) <br>
<img src="https://github.com/user-attachments/assets/760843e2-57fa-431f-b977-401e36472117"> <br>

22. User juga dapat mengganti password & menghapus akun <br>
<img src="https://github.com/user-attachments/assets/aeace88b-0980-4cf4-895e-9d89400cdb5c"> <br>

23. User dapat melakukan Sign Out (Logo Profile bagian kanan atas) <br>
<img src="https://github.com/user-attachments/assets/1e0d68a6-0ab3-444d-b21e-23e6d2d90afa"> <br>

24. User berhasil melakukan logout <br>
<img src="https://github.com/user-attachments/assets/b4200b3e-cdd0-40ef-9d58-8edfed6d05ae"> <br>

25. User dapat kembali melakukan login dan data sebelumnya pada website tetap ada <br>
<img src="https://github.com/user-attachments/assets/babad38b-4f3f-413b-b7c0-23f9e682de84"> <br>

## Pembahasan 

### Kelebihan dan Kekurangan
1. Tidak hanya dapat explore berbagai resep yang sudah dikategorikan dalam berbagai jenis <br>
   <img src="https://github.com/user-attachments/assets/9c1d4e1d-079c-41a0-9bb9-f696fd258987"> <br>
   
2. User juga dapat menambahkan resep pribadi dengan form yang dapat mengisi otomatis mengikuti source link yang diupload <br>
   <img src="https://github.com/user-attachments/assets/8b0bae0f-1c11-4071-8534-17cf8078f4f4"> <br>
   Namun terdapat kekurangan pada website bagian autofill form dimana terdapat beberapa ketidaksesuaian input data sehingga user perlu mengecek ulang secara manual untuk menyesuaikan data sesuai dengan source   link <br>
   
3. User juga dapat memfavoritkan, menjadikan public/private, menambahkan jadwal ke Meal Planner, mengedit dan menghapus resep pribadi yang sudah dibuat <br>
   <img src="https://github.com/user-attachments/assets/7e41c99b-eb82-4b7e-ad5e-f6d771965b80"> <br>
   
4. User juga dapat memasukkan list bahan pada resep ke Shopping List agar lebih mudah saat berbelanja sehingga meminimalkan kemungkinan lupa membeli kebutuhan dalam membuat resep. <br>
   <img src="https://github.com/user-attachments/assets/9c448df9-d913-4029-b56d-83ca26c0842d"> <br>
   <img src="https://github.com/user-attachments/assets/a4974f22-6ca0-4ab5-a002-5ab0f005a8f9"> <br>
   User juga dapat menambahkan item list secara manual namun terdapat kekurangan yaitu ketika item baru ditambahkan (5 lemon) tidak tertera form input untuk resep bagian mana (tidak seperti list atas-atasnya). <br>
   
5. Selain memasukkan list bahan resep pada Shopping List, user dapat menambahkan resep ke Meal Plannernya sehingga memudahkan user saat ingin memasak pada waktu makan yang sudah ditentukan. <br>
   <img src="https://github.com/user-attachments/assets/ff4fa4e8-0588-4108-b734-d2943d08d363"> <br>
   User dapat menghapus resep dari Meal Planner jika berubah pikiran untuk tidak memasukkan resep pada Meal Plannernya (Logo Hapus bagian kanan bawah) <br>
   
6. User juga dapat mengedit tampilan website maupun bagian Profile sehingga website dapat diubah sedemikian rupa menyesuaikan selera user dan membuat website atraktif & tidak membosankan. <br>

### Perbandingan dengan Web App Sejenis
- Tampilan home website Recipya <br>
<img src="https://github.com/user-attachments/assets/78827056-71de-48c1-94a6-72aa761849e0"> <br>

- Tampilan home website Tamari <br>
<img src="https://github.com/user-attachments/assets/766256ae-1f41-4e07-b89b-3dd7733676b3"> <br>

Pada kedua gambar tersebut, Website recipya menampilkan menu yang terdapat pada website dan terdapat fitur sort untuk mengurutkan resep. Sedangkan pada website tamari menampilkan bagian explore dimana user dapat langsung memilih Category resep yang diinginkan. Hal ini dapat menjadi nilai tambah pada website Tamari karena dapat lebih mudah menemukan jenis makanan yang diinginkan.
Tampilan pada website Tamari juga lebih tertata rapi dimana jika kita memilih kategori Dessert maka yang ditampilkan pada website hanya resep yang merupakan jenis makanan penutup, sedangkan pada website Recipya jenis makanan yang ditampilkan akan tersusun secara acak sehingga sulit untuk menemukan kategori makanan yang diinginkan. 

## Referensi
1. [Tamari](https://tamariapp.com/) - Recipe manager web app with a built-in collection of recipes. Organize by favorites and categories, create shopping lists, and plan meals.
2. [Eat this much](https://www.eatthismuch.com/) - An automated meal planning app tailored to your dietary preferences and goals. 
3. [Recipya](https://recipes.musicavis.ca/) - A clean, simple and powerful recipe manager your whole family will enjoy.


