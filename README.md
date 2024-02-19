#Untuk sebagian besar proyek, file alur kerja ini tidak perlu diubah; kamu hanya perlu
#untuk mengkomitnya ke repositori Anda.
#
#Anda mungkin ingin mengubah file ini untuk mengganti kumpulan bahasa yang dianalisis,
#atau untuk memberikan kueri khusus atau membangun logika.
#
#******** CATATAN ********
#Kami telah mencoba mendeteksi bahasa di repositori Anda. Silakan periksa
#matriks `bahasa` yang ditentukan di bawah untuk mengonfirmasi bahwa Anda memiliki kumpulan yang benar
#bahasa CodeQL yang didukung.
#
nama:"KodeQL"

pada:
  dorongan:
    ranting:[ "utama" ]
  tarik_permintaan:
    #Cabang-cabang di bawahnya harus merupakan subset dari cabang-cabang di atasnya
    ranting:[ "utama" ]
  jadwal:
    -cron:'45 2* *0'

pekerjaan:
  menganalisa:
    nama:Menganalisa
    #Ukuran pelari memengaruhi waktu analisis CodeQL. Untuk mempelajari lebih lanjut, silakan lihat:
    #   - https://gh.io/recommended-hardware-resources-for-running-codeql
    #   - https://gh.io/supported-runners-and-hardware-resources
    #   - https://gh.io/using-larger-runners
    #Pertimbangkan untuk menggunakan runner yang lebih besar untuk kemungkinan peningkatan waktu analisis.
    berjalan terus:${{ (matrix.bahasa == 'cepat' && 'macos-terbaru') || 'ubuntu-terbaru' }}
    batas waktu-menit:${{ (matrix.bahasa == 'cepat' && 120) || 360 }}
    izin:
      tindakan:membaca
      isi:membaca
      peristiwa keamanan:menulis

    strategi:
      gagal-cepat:PALSU
      matriks:
        bahasa:[ 'javascript', 'python' ]
        #CodeQL mendukung [ 'cpp', 'csharp', 'go', 'java', 'javascript', 'python', 'ruby', 'swift' ]
        #Gunakan hanya 'java' untuk menganalisis kode yang ditulis dalam Java, Kotlin, atau keduanya
        #Gunakan hanya 'javascript' untuk menganalisis kode yang ditulis dalam JavaScript, TypeScript, atau keduanya
        #Pelajari lebih lanjut tentang dukungan bahasa CodeQL di https://aka.ms/codeql-docs/bahasa-support

    Langkah:
    -nama:Repositori pembayaran
      kegunaan:tindakan/checkout@v3

    #Menginisialisasi alat CodeQL untuk pemindaian.
    -nama:Inisialisasi CodeQL
      kegunaan:github/codeql-action/init@v2
      dengan:
        bahasa:${{ matriks.bahasa }}
        #Jika Anda ingin menentukan kueri khusus, Anda dapat melakukannya di sini atau di file konfigurasi.
        #Secara default, kueri yang tercantum di sini akan menggantikan kueri yang ditentukan dalam file konfigurasi.
        #Awali daftar di sini dengan "+" untuk menggunakan kueri ini dan kueri yang ada di file konfigurasi.

        #Untuk detail selengkapnya tentang paket kueri CodeQL, lihat: https://docs.github.com/en/code-security/code-scanning/automatically-scanning-your-code-for-vulnerabilities-and-errors/configuring-code -memindai#menggunakan-kueri-dalam-ql-paket
        #pertanyaan: keamanan diperluas, keamanan dan kualitas


    #Autobuild mencoba membangun bahasa apa pun yang dikompilasi (C/C++, C#, Go, Java, atau Swift).
    #Jika langkah ini gagal, Anda harus menghapusnya dan menjalankan build secara manual (lihat di bawah)
    -nama:Pembuatan otomatis
      kegunaan:github/codeql-action/autobuild@v2

    #ℹ️ Program baris perintah untuk dijalankan menggunakan shell OS.
    #📚 Lihat https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstepsrun

    #   Jika Autobuild gagal di atas, hapus dan hapus komentar pada tiga baris berikut.
    #   modifikasi (atau tambahkan lebih banyak) untuk membuat kode jika proyek Anda, silakan lihat CONTOH di bawah untuk panduan.

    #- jalankan: |
    #     echo "Jalankan, Bangun Aplikasi menggunakan script"
    #     ./location_of_script_within_repo/buildscript.sh

    -nama:Lakukan Analisis CodeQL
      kegunaan:github/codeql-action/analyze@v2
      dengan:
        kategori:"/bahasa:${{matrix.bahasa}}"
