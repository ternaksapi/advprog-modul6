Muhammad Yusuf Haikal </br>
Advanced Programming A </br>
NPM 2206081490 </br>

# Reflection

## Milestone 1: Single Threaded Server
Pada milestone 1, kita telah membuat sebuah Single Threaded Server yang mendengarkan pada alamat 127.0.0.1 di port 7878. Ketika terdapat koneksi, maka koneksi tersebut akan dihandle oleh fungsi `handle_connection`. Fungsi tersebut pertama menerima objek `TcpStream` yang kemudian dibhuatkan object `BufReader` untuk me_wrap_ `TcpStream` tadi. Object tersebut kemudian akan membaca masing - masing HTTP request dari koneksi lalu kemudian nmencetak hasilnya.

## Milestone 2: Returning HTML
Sebelumnya, ketika kita mengakses web kita, webnya tidak akan mengembalikan apa - apa, hanya menerima HTTP request dan mencetaknya di terminal. Sekarang, kita ingin agar web mengembalikan suatu tampilan HTML. Yang pertama kita lakukan adalah menambahkan modul `fs` agar Rust kode kita dapat membaca file dari sistem, dalam hal ini file HTML yang ingin kita tampilkan nantinya. Kita kemudian membuat file HTML kita yang bernama `hello.html` dan kemudian akan membaca isinya dengan menggunakan line `fs:::read_to_string`, yang kemudian di simpan dalam variabel `contents`. 

HTML tersebut kemudian akan dikirim sebagai HTTP response pada variable `response` yang juga berisikan status response `200 OK` dan header berupa panjang dari konten. 
![Commit 2 Screen Capture](https://github.com/ternaksapi/advprog-modul6/assets/116947973/b7a3a37f-e9ce-4e83-919d-4ac500eb315d)

## Milestone 3: Validating Request and Selectively Responding
Saat ini, web kita hanya mengembalikan satu respon untuk apapun requestnya, yaitu mengembalikan halaman `hello.html`. Kita ingin membuat dua respon yang berbeda bergantung dengan request dari user, yaitu apabila status line request merupakan `HTTP/1.1 200 OK` dan apabila status linenya selain itu. Hal ini dilakukan dengan cara pertama membaca baris pertama dari request dengan `buf_reader.lines().next().unwrap().unwrap()`. Apabila baris pertamanya adalah `GET / HTTP/1.1` maka web akan memberikan response berupa HTML `hello.html` dan responses status `200 OK`, dan apabila baris pertamanya selain itu makan responsenya berupa HTML `404.html` dan response status `404 NOT FOUND`

Refactoring juga dapat dilakukan pada block if else tersebut. Karena keduanya sama - sama mengembalikan sebuah response yang memiliki format sebuah status line dan content yang merupakan file HTML yang akan dikembalikan, kita dapat menyederhanakan block if else hanya untuk mengatur `status_line` dan `filename` yang akan diread oleh `fs`. Refactoring tersebut penting dilakukan untuk mengurangi _redundancy_ pada kode kita.
![Commit 3 Screen Capture](https://github.com/ternaksapi/advprog-modul6/assets/116947973/f9605dcd-c526-4967-b01b-ca51c7881417)

## Milestone 4: Simulation Slow Response
Karena kita menjalankan server pada single thread, kita ingin melihat bagaimana ketika banyak user menjalankan web. Kita menambahkahkan `/sleep` pada request, yang memberikan penundaan waktu sebanyak 10 detik untuk mengakses situs website. Ini bisa dibayangkan seperti apabila dalam sitausi nyata, serwer sedang melakukan operasi yang memakan waktu. Jika banyak pengguna yang mengakses dengan `/sleep` secara bersamaan, server harus menghandle request secara satu per satu, dan akan terjadi antrian yang memperlambat response dari web. 

## Milestone 5: Multithreaded Server
Sekarang, kita telah melihat bagaimana kemungkinan kondisi yang terjadi bila banyak user mencoba untuk mengakses web kita secara bersamaan. Salah satu cara untuk menangani banyaknya response secara bersamaan adalah dengan menggunakan server multithreaded. Kita dapat mengimplemetasikan multithreading dengan thread pool. Sebuah thread pool adalah sekelompok thread yang sudah dibuat sebelumnya dan siap untuk mengerjakan tugas (task). Jumlah thread dalam pool ini dibatasi untuk mencegah serangan Denial of Service (DoS). Ketika permintaan baru masuk, permintaan tersebut dimasukkan ke dalam sebuah antrian, lalu alah satu thread dari pool yang sedang menganggur akan mengambil permintaan dari antrian. Thread yang mengambil permintaan akan mengerjakan permintaan tersebut. Setelah selesai mengerjakan permintaan, thread akan dikembalikan ke pool agar dapat digunakan kembali untuk mengerjakan permintaan berikutnya. Dengan adanya thread pool, server dapat memproses hingga N permintaan secara konkuren, di mana N adalah jumlah thread dalam pool.
