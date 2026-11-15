# UMKM Tumbuh — Aplikasi Android & iOS (Capacitor)

Pembungkus aplikasi "UMKM Tumbuh" supaya bisa dipasang di HP Android dan iPhone.
Aplikasi webnya **tidak diubah** — aplikasi ini hanya menampilkan aplikasi web yang
sudah live di https://aplikasi-introspeksi-umkm.vercel.app di dalam aplikasi HP.

- Nama aplikasi: **UMKM Tumbuh**
- ID aplikasi: `id.ac.unair.umkmtumbuh`
- Pemilik: Soegianto Soelistiono (soegianto@fst.unair.ac.id), Lab Sistem Cerdas Fisika, FST UNAIR
- Teknologi: Capacitor 7 (aplikasi web dibungkus menjadi aplikasi HP)

## Cara kerja

Aplikasi ini memuat URL live yang sama dengan aplikasi web, jadi isinya persis
seperti aplikasi web (asal yang sama, tidak perlu perubahan CORS, tidak ada
perubahan pada aplikasi web atau data cloud). Tautan ke enam aplikasi (utama,
pegawai, investor, Pemda, admin, marketing) tetap terbuka di dalam aplikasi.

Bila HP sedang tidak ada internet, tampil halaman sederhana "Muat ulang" dari
folder `www/` (halaman ini hanya cadangan; isi aplikasi tetap dari URL live).

## APK debug — untuk dibagikan langsung (sideload)

APK debug **belum dibuat** di mesin ini. Perangkat build (JDK 17, Android SDK,
Gradle 8.11.1) sudah terpasang di mesin ini, tetapi proses build Gradle tidak
bisa berjalan di mesin ini karena komunikasi program Gradle (daemon) selalu
gagal di mesin ini. Ini kendala mesin, bukan kendala proyek — proyeknya sudah
siap dan sudah diperiksa.

Cara mendapatkan APK (pilih salah satu):

1. **Lewat GitHub Actions (paling mudah, tidak butuh komputer sendiri):**
   unggah proyek ini ke GitHub, lalu buka tab "Actions" di repositori — alur
   "Build APK debug" akan berjalan dan APK bisa diunduh dari hasilnya.
   (Lihat "Build lewat GitHub Actions" di bawah.)
2. **Di komputer sendiri (Windows/Mac/Linux):** pasang JDK 17 + Android SDK,
   lalu jalankan perintah di "APK debug (uji coba / sideload)" di bawah.

Setelah APK didapat:

- Berkas: `android/app/build/outputs/apk/debug/app-debug.apk`
- Ditandatangani dengan kunci debug (kunci bawaan untuk uji coba).

Cara memasang di HP Android:

1. Salin berkas APK ke HP (misalnya lewat WhatsApp, Telegram, atau kabel USB).
2. Ketuk berkas APK di HP.
3. Bila HP bertanya, izinkan "Pasang aplikasi yang tidak dikenal" (Install unknown apps) untuk aplikasi yang membuka berkas itu.
4. Ketuk "Pasang". Ikon "UMKM Tumbuh" muncul di layar utama.

Catatan: APK debug hanya untuk uji coba. Untuk dibagikan ke banyak orang dan
untuk Play Store, pakai APK/AAB rilis (lihat di bawah) setelah APK ini stabil.

## Yang belum dilakukan (menyusul setelah APK stabil)

- **APK/AAB rilis:** butuh kunci rilis sendiri (keystore) yang dibuat sekali dan
  disimpan baik-baik. Bila kunci ini hilang, aplikasi tidak bisa diperbarui.
- **Pendaftaran toko:** Play Store dan App Store ditunda sampai APK stabil.

## Akun toko yang diperlukan (keduanya TIDAK gratis)

- **Google Play Console:** **US$25 sekali bayar** (selamanya) untuk mendaftarkan
  akun developer dan mengunggah aplikasi ke Play Store.
- **Apple Developer Program:** **US$99 per tahun** untuk mengunggah aplikasi ke
  App Store.

Tanpa kedua akun berbayar ini, aplikasi tidak bisa masuk toko. Pembagian langsung
(sideload APK) tidak butuh akun apa pun.

## Cara build

### Syarat

- Node.js 20 atau lebih baru (sudah ada di mesin ini).
- JDK 17.
- Android SDK (cmdline-tools, platform Android 35, build-tools 35.0.0).
- Untuk iOS: Mac dengan Xcode.

### Ambil proyek ini

```bash
git clone https://github.com/soegiantos/capacitor-umkm-tumbuh.git
cd capacitor-umkm-tumbuh
npm install
```

(Bila repositori belum dibuat, lihat "Mengunggah proyek ke GitHub" di bawah.)

### APK debug (uji coba / sideload)

```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export ANDROID_HOME=$HOME/Android/Sdk
cd android
./gradlew assembleDebug
```

Hasil: `android/app/build/outputs/apk/debug/app-debug.apk`

### Build lewat GitHub Actions (cloud, tanpa komputer sendiri)

Proyek ini sudah punya alur build di `.github/workflows/build-apk.yml`.
Caranya:

1. Unggah proyek ini ke GitHub (lihat "Mengunggah proyek ke GitHub" di bawah).
2. Buka repositori di GitHub → tab **Actions** → alur **Build APK debug**.
3. Alur berjalan otomatis setiap ada perubahan di cabang `main`, atau jalankan
   manual lewat tombol **Run workflow**.
4. Setelah selesai, unduh APK dari bagian **Artifacts** (nama
   `umkm-tumbuh-debug-apk`).

Alur ini memakai mesin cloud GitHub (ubuntu-latest) yang memasang JDK 17 dan
Android SDK sendiri, jadi tidak butuh komputer dengan Android Studio.

### AAB rilis (untuk Play Store — menyusul)

1. Buat kunci rilis sekali saja (simpan berkas dan kata sandinya baik-baik):
   ```bash
   keytool -genkeypair -v -keystore umkm-tumbuh.keystore -alias umkm -keyalg RSA -keysize 2048 -validity 10000
   ```
2. Buat berkas `android/keystore.properties` (jangan diunggah ke GitHub):
   ```
   storeFile=../umkm-tumbuh.keystore
   storePassword=ISI_KATA_SANDI_KEYSTORE
   keyAlias=umkm
   keyPassword=ISI_KATA_SANDI_KUNCI
   ```
3. Build:
   ```bash
   cd android
   ./gradlew bundleRelease
   ```
   Hasil: `android/app/build/outputs/bundle/release/app-release.aab`

### .ipa (untuk App Store — menyusul)

Butuh Mac dengan Xcode. Langkah singkat:

1. Di Mac, buka `ios/App/App.xcworkspace` di Xcode.
2. Masuk dengan Apple ID yang sudah terdaftar di Apple Developer Program (US$99/tahun).
3. Pilih tim (Signing & Capabilities), lalu Product → Archive.
4. Dari Organizer, pilih "Distribute App" → App Store Connect.

Alternatif tanpa Mac: layanan build cloud seperti **Codemagic** — hubungkan repositori
GitHub ini, pilih target iOS, dan unduh `.ipa` dari hasil build-nya. (Layanan ini
punya paket gratis terbatas; paket berbayar bila butuh lebih.)

## Mengunggah proyek ke GitHub

```bash
cd capacitor-umkm-tumbuh
git init
git add .
git commit -m "Pembungkus Capacitor UMKM Tumbuh (Android + iOS)"
git branch -M main
git remote add origin https://github.com/soegiantos/capacitor-umkm-tumbuh.git
git push -u origin main
```

Catatan: jangan unggah `node_modules/`, folder `android/app/build/`, folder
`ios/App/build/`, berkas `android/keystore.properties`, dan berkas `.keystore`
apa pun. (Berkas `.gitignore` di proyek ini sudah mengaturnya.)

## Struktur proyek

```
capacitor-umkm-tumbuh/
├── .github/workflows/build-apk.yml  # build APK otomatis di cloud (GitHub Actions)
├── .gitignore
├── capacitor.config.json   # appId, nama, URL live, tautan antar-aplikasi
├── package.json            # Capacitor 7 + @capacitor/assets
├── www/
│   └── index.html          # halaman cadangan saat offline
├── resources/
│   ├── icon.png            # ikon 1024x1024 (tunas emas, latar gelap)
│   └── splash.png          # splash 2732x2732
├── android/                # proyek Android (Gradle)
├── ios/                    # proyek iOS (Xcode) — build butuh Mac
└── README.md               # berkas ini
```

## Penegasan

- Aplikasi web "UMKM Tumbuh" di Vercel **tidak diubah** oleh proyek ini.
- Data cloud (Supabase) dan SQL **tidak diubah**.
- Tidak ada kunci API atau kata sandi di dalam proyek ini — semua kunci tetap di
  server. Aplikasi hanya memuat URL live.
