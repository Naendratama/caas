# caas

![ss](https://github.com/Naendratama/caas/blob/main/Screenshot%202026-03-02%20160732.png)

Category: Web Exploitation

Difficulty: Medium

Description: Now presenting cowsay as a service

Challenge Endpoints ![ss](https://github.com/Naendratama/caas/blob/main/index.js)

Recoon:

Coba kita buka dulu websitenya
![ss](https://github.com/Naendratama/caas/blob/main/Screenshot%202026-03-02%20160800.png)

Ternyata kita disuruh mengikuti url yang dilingkari, ayo coba kita cek.

![ss](https://github.com/Naendratama/caas/blob/main/Screenshot%202026-03-02%20160827.png)

Seperti inilah tampilannya, ayo coba kita analisa index.js nya

Analisa:

File ini adalah web server sederhana menggunakan Node.js + Express yang menjalankan program cowsay dari terminal.

  Alur Logika
       
       Client request
             ↓
    GET /cowsay/:message
             ↓
    Server jalankan: /usr/games/cowsay <pesan>
             ↓
    Output dikembalikan sebagai plain text

Penjelasan perbagian

1. Setup Server

        const express = require('express');
        const app = express();
        const { exec } = require('child_process');

Menggunakan Express sebagai web framework
exec dari child_process digunakan untuk menjalankan perintah terminal

2. Static Files

       app.use(express.static('public'));

Menyajikan file statis (HTML, CSS, JS) dari folder public/

3. Route Utama

       app.get('/cowsay/:message', (req, res) => {
       exec(`/usr/games/cowsay ${req.params.message}`, ...);
        });

Ketika ada request ke /cowsay/hello, maka server menjalankan perintah shell: /usr/games/cowsay hello
Hasilnya dikirim balik ke client sebagai plain text

4. Menjalankan Server

       Ketika ada request ke /cowsay/hello, maka server menjalankan perintah shell: /usr/games/cowsay hello

Hasilnya dikirim balik ke client sebagai plain text

CELAH KEAMANAN KRITIS

Kode ini mengandung Command Injection Vulnerability yang sangat berbahaya:

    exec(`/usr/games/cowsay ${req.params.message}`
    //                        ^^^^^^^^^^^^^^^^^^^ INPUT TIDAK DISANITASI!
    ```

Parameter dari URL langsung dimasukkan ke perintah shell tanpa validasi apapun, sehingga attacker bisa menjalankan perintah arbitrary di server.

EXPLOITATION

dari hasil analisa diatas bisa dipastikan bahwa website tersebut memiliki vulnerability Command Injection

Ayo kita ces dulu, dengan mengganti input message dengan test

![ss](https://github.com/Naendratama/caas/blob/main/Screenshot%202026-03-02%20160855.png)

Dan yap benarr, message tersebut berubah menjadi test

Ayo kita coba menambahkan input test tersebut dengan coommand 

      test;echo%20"anjimmmm"

Ini mengetes apakah web ini menggunakan linux atau windows server, tanda ; adalah tanda untuk menjalankan 2 perintah secara bersamaan

![ss](https://github.com/Naendratama/caas/blob/main/Screenshot%202026-03-02%20161014.png)

Dan yapp ternyata bisa, sudah dipastikan bahwa ini adalah server linux

Ayo kita coba command lainn yang memungkinkan kita untuk melihat list file pada sebuah web

    test;ls%20/

![ss](https://github.com/Naendratama/caas/blob/main/Screenshot%202026-03-02%20161100.png)

Dan yap terdapat direktori falg.txt, mungkinn saja typo, aslinya flag.txt

Ayo kita coba command untuk melihat file dari falgg.txt tersebut

      test;cat%20falg.txt

![ss](https://github.com/Naendratama/caas/blob/main/Screenshot%202026-03-02%20161128.png)

Dan yapp flag telah ditemukannn

Flag : picoCTF{moooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo0o}
