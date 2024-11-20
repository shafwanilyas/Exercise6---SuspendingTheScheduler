## Kontrol LED STM32 dengan RTOS (FreeRTOS)

Proyek ini menunjukkan aplikasi dasar STM32 yang menggunakan FreeRTOS untuk mengontrol beberapa LED (hijau, merah, dan biru) dalam lingkungan multitasking kooperatif. LED hijau dan merah dikendalikan oleh tugas terpisah dengan penundaan waktu tertentu, sementara bagian kritis digunakan untuk mengelola data bersama (variabel flag) dengan aman di antara tugas-tugas.

## Fitur yang Digunakan
1. Multitasking FreeRTOS
FreeRTOS digunakan untuk penjadwalan tugas. Dalam proyek ini, tiga tugas dibuat:

- defaultTask: Tugas dasar yang tidak melakukan apa-apa kecuali menjaga penjadwal berjalan.
- green_led: Mengontrol LED hijau, menyalakan dan mematikannya dengan penundaan 0,5 detik.
- red_led: Mengontrol LED merah, menyalakan dan mematikannya dengan penundaan 0,1 detik.
### Prioritas Tugas:

- defaultTask diberikan prioritas normal.
- green_led dijalankan dengan prioritas idle.
- red_led dijalankan dengan prioritas normal.
### Penundaan Tugas (osDelay()):
- Setiap tugas menggunakan osDelay() untuk memberikan kontrol kembali ke penjadwal setelah melakukan operasinya (misalnya menyalakan atau mematikan LED).
##  Critical Section Management
- taskENTER_CRITICAL() dan taskEXIT_CRITICAL() digunakan untuk melindungi data bersama (variabel startFlag).
 - Tugas green_led dan red_led keduanya mengakses variabel startFlag, sehingga akses ke variabel ini disinkronkan untuk mencegah kondisi balapan (race condition).
 - Ketika flag diset ke 1, operasi LED dilakukan. Jika flag sudah 0, LED biru dinyalakan untuk menunjukkan adanya interferensi.
 - Flag disetel ulang setelah setiap bagian kritis untuk memungkinkan tugas lain mengaksesnya.
## Kontrol GPIO
Pin GPIO STM32 (terhubung ke LED) diinisialisasi dan digunakan untuk mengontrol LED.
- LED1 terhubung ke LED hijau.
- LED2 terhubung ke LED merah.
- LED3 digunakan untuk menunjukkan interferensi (LED biru).
- LED dinyalakan dan dimatikan menggunakan HAL_GPIO_WritePin().
## Inisialisasi Sistem Jam dan GPIO
- sistem clock dikonfigurasi menggunakan fungsi SystemClock_Config().
- GPIO pin diinisialisasi dengan MX_GPIO_Init() untuk mengatur mode dan kecepatan yang sesuai untuk pin-pin LED.
## Penanganan Kesalahan
- Fungsi Error_Handler() dipanggil jika terjadi masalah selama inisialisasi atau operasi, dan program masuk ke dalam loop tak terhingga untuk menunjukkan status kesalahan.
## Cara Kerja
### Inisialisasi Sistem:
- Sistem clock dan GPIO diinisialisasi, dan tugas FreeRTOS dibuat untuk mengontrol LED.
### Kontrol LED:
- Tugas green_led menyalakan dan mematikan LED hijau setiap 0,5 detik, sementara tugas red_led menyalakan dan mematikan LED merah setiap 0,1 detik.
- Kedua tugas menggunakan bagian kritis untuk memeriksa dan memodifikasi startFlag. Jika flag adalah 1, operasi LED dilanjutkan; jika tidak, LED biru dinyalakan sebagai indikator adanya interferensi.
###  Critical Section Management:
- Tugas-tugas berjalan secara bersamaan, dan FreeRTOS mengelola pergantian tugas berdasarkan prioritas dan periode penundaan mereka.
### Critical Section:
- Variabel startFlag diperiksa dan dimodifikasi dalam bagian kritis untuk menghindari masalah dari akses bersamaan, memastikan flag tidak diubah saat sebuah tugas sedang menggunakannya.

- ## Demo

https://github.com/user-attachments/assets/f1d2cea7-fc98-4e35-b9e7-a36d14cec1f1


- 
