  # TodoApp React Native + Expo + Firebase

Aplikasi TodoApp CRUD dengan data Cloud Firestore secara real-time.

## Menjalankan proyek

```powershell
cd TodoApp
npx expo start
```

Pada iPhone, buka Expo Go dan scan QR Code. Pastikan iPhone dan Windows berada pada jaringan Wi-Fi yang sama. Jika LAN bermasalah, jalankan `npx expo start --tunnel`.

## Konfigurasi Firebase

1. Buka https://console.firebase.google.com/.
2. Buat project Firebase baru.
3. Pilih **Add app** lalu pilih aplikasi Web (`</>`).
4. Daftarkan aplikasi, copy konfigurasi `firebaseConfig`.
5. Isi konfigurasi pada `config/firebase.js`.
6. Di Firebase Console, buka **Build > Firestore Database**.
7. Pilih **Create database** dan pilih lokasi yang tersedia.
8. Untuk pengujian awal, gunakan mode dengan autentikasi yang sesuai proyek Anda. Contoh rules sementara:

```text
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /todos/{todoId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

Aturan di atas hanya mengizinkan pengguna terautentikasi. Aplikasi contoh ini belum memiliki login, sehingga aturan tersebut dapat mencegah data digunakan sampai autentikasi ditambahkan. Jangan gunakan aturan publik permanen. Untuk tugas lokal tanpa login, gunakan hanya sementara dengan pembatasan jaringan/release, misalnya:

```text
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /todos/{todoId} {
      allow read, write: if true;
    }
  }
}
```

Rules publik `true` bukan solusi aman dan tidak boleh dipertahankan saat aplikasi dipublikasikan. Implementasikan Firebase Authentication untuk penggunaan permanen.

## Struktur file

- `App.js`: NavigationContainer dan Stack Navigator.
- `config/firebase.js`: inisialisasi Firebase dan Firestore.
- `Screens/Home.js`: form tambah, real-time list, dan hapus.
- `Screens/Detail.js`: penerimaan parameter tugas dan update.
- `components/TodoItem.js`: kartu tugas dan tombol hapus.
- `components/EmptyState.js`: tampilan saat daftar kosong.
- `app.json`: konfigurasi Expo.
- `package.json`: dependency dan script proyek.

## Penjelasan untuk presentasi

### TodoApp dan CRUD
TodoApp adalah aplikasi pencatat tugas. CRUD adalah Create (tambah), Read (baca), Update (ubah), dan Delete (hapus).

### React Native dan Expo
React Native membuat antarmuka mobile menggunakan komponen JavaScript. Expo menyediakan template, tool berjalan, dan modul yang kompatibel dengan Expo Go tanpa konfigurasi native Xcode.

### Firebase dan Cloud Firestore
Firebase adalah platform backend. Cloud Firestore adalah basis data dokumen yang menyimpan `todos` sebagai collection, sedangkan setiap tugas adalah dokumen dengan field `heading` dan `createdAt`.

### useState()
`useState()` menyimpan data yang dapat berubah pada komponen, misalnya teks input, daftar tugas, dan status loading.

### useEffect()
`useEffect()` menjalankan proses saat komponen dibuka atau dilepas. Pada Home, effect membuat listener Firestore dan mengembalikan fungsi `unsubscribe` agar listener dihentikan saat layar tidak digunakan.

### onSnapshot()
`onSnapshot()` membaca collection dan memperbarui aplikasi otomatis ketika data berubah. Fungsi ini dipakai untuk real-time Read.

### addDoc()
`addDoc()` menambahkan dokumen baru ke collection `todos` dengan ID otomatis.

### updateDoc()
`updateDoc()` mengubah field dokumen berdasarkan ID. Detail menggunakan `updateDoc()` untuk mengubah `heading`.

### deleteDoc()
`deleteDoc()` menghapus satu dokumen berdasarkan ID setelah konfirmasi Alert.

### FlatList
`FlatList` menampilkan daftar secara efisien. Ia menerima `data`, `keyExtractor`, dan `renderItem`.

### TextInput
`TextInput` menerima nama tugas dari pengguna.

### React Navigation dan Stack Navigator
React Navigation mengatur perpindahan layar. Stack Navigator menyimpan layar dalam tumpukan. Home membuka Detail dengan `navigation.navigate('Detail', { todo: item })`.

### Cara komunikasi dengan Firebase

1. Aplikasi menginisialisasi Firebase pada `config/firebase.js`.
2. Home mendengarkan collection `todos` dengan `onSnapshot()`.
3. Add memakai `addDoc()`.
4. Update memakai `updateDoc()`.
5. Delete memakai `deleteDoc()`.
6. Perubahan Firestore dikirim kembali ke listener dan memperbarui `FlatList` otomatis.

## Pemeriksaan fitur

- Create: validasi teks kosong, tambah dokumen dengan `heading` dan `serverTimestamp()`.
- Read: listener real-time dan pengurutan `createdAt` descending.
- Update: menerima todo melalui route params, validasi, lalu update dokumen.
- Delete: konfirmasi batal/hapus, lalu hapus dokumen.
- Navigation: Home dan Detail menggunakan Stack Navigator.
- iOS: Safe Area, KeyboardAvoidingView, tombol besar, dan layout flex/touchscreen.
