# FinalProject2 (MyGram Golang API)

REST API berbasis Go untuk aplikasi mini media sosial dengan fitur autentikasi (JWT) dan CRUD untuk Photos, Comments, dan Social Medias.

## Fitur Utama
- Registrasi dan Login pengguna (JWT)
- CRUD Photo
- CRUD Comment (terkait Photo)
- CRUD Social Media
- Proteksi endpoint menggunakan middleware Authentication
- Pembuatan tabel database otomatis saat startup

## Teknologi
- Go (Golang)
- Gin Web Framework
- PostgreSQL (database/sql, lib/pq)
- JWT (golang-jwt)
- Godotenv untuk konfigurasi .env

## Struktur Proyek
- Entry point: `main.go` memanggil `StartApp()`
- Routing & inisialisasi service: `handler/app.go`
- Handler HTTP: `handler/*.go`
- Service (logika bisnis): `service/*.go`
- Repository (data access): `repository/*` dengan implementasi Postgres di `*_pg`
- DTO & Entity: `dto/*`, `entity/*`
- Infrastruktur: `infrastructure/config`, `infrastructure/database`
- Middleware: `pkg/middleware/authentication.go`
- Helpers: `pkg/helpers/*`
- Error wrapper: `pkg/errs/err.go`
- Dokumentasi: `docs/openapi.json` (Swagger/OpenAPI), `docs/swagger.html` (Swagger UI), `docs/postman_collection.json`

## Konfigurasi Environment
Buat file `.env` (atau isi yang sudah ada) sesuai contoh `./.env-example`:
```
DB_HOST=
DB_PORT=
DB_USER=
DB_PASSWORD=
DB_NAME=
DB_DIALECT=postgres
JWT_SECRET_KEY=
PORT=8080
```
Catatan: PORT default di contoh adalah 8080.

## Menjalankan Aplikasi
1. Pastikan PostgreSQL aktif dan kredensial sesuai dengan `.env`.
2. Jalankan:
   ```bash
   go run main.go
   ```
3. Server berjalan di `http://localhost:<PORT>` (default `http://localhost:8080`).

## Dokumentasi API
- Swagger UI: buka `http://localhost:8080/docs/swagger.html`
- Spesifikasi OpenAPI: `http://localhost:8080/docs/openapi.json`
- Postman collection: import file `./docs/postman_collection.json` ke Postman

## Autentikasi
- Gunakan header `Authorization: Bearer <token>` untuk endpoint yang dilindungi.
- Token didapat dari endpoint `POST /users/login`.

## Endpoint Ringkas
- Users
  - POST `/users/register` — register pengguna baru
  - POST `/users/login` — login dan mendapatkan JWT token
  - PUT `/users` — update data user (Auth)
  - DELETE `/users` — hapus akun user (Auth)
- Photos (Auth)
  - POST `/photos` — buat foto baru
  - GET `/photos` — ambil daftar foto (dengan info user)
  - PUT `/photos/:photoId` — update foto
  - DELETE `/photos/:photoId` — hapus foto
- Comments (Auth)
  - POST `/comments` — buat komentar (terkait photo)
  - GET `/comments` — ambil komentar milik user (dengan info user & photo)
  - PUT `/comments/:commentId` — update komentar
  - DELETE `/comments/:commentId` — hapus komentar
- Social Medias (Auth)
  - POST `/socialmedias` — buat social media
  - GET `/socialmedias` — ambil social medias milik user
  - PUT `/socialmedias/:socialMediaId` — update social media
  - DELETE `/socialmedias/:socialMediaId` — hapus social media

## Skema Error
Semua error dibungkus dengan format:
```json
{
  "message": "<deskripsi>",
  "status": <kode_status_http>,
  "error": "<kode_error>"
}
```

## Database
Saat startup, tabel berikut dibuat otomatis:
- `users` — data pengguna
- `photos` — foto milik user (FK ke `users`)
- `social_medias` — akun medsos milik user (FK ke `users`)
- `comments` — komentar milik user pada photo (FK ke `users` & `photos`)

Relasi menggunakan ON DELETE CASCADE untuk menjaga integritas data.

## Catatan
- Untuk endpoint yang dilindungi, `user_id` diambil dari klaim token JWT, bukan dari body request.
- Pastikan `JWT_SECRET_KEY` di `.env` terisi untuk generate/verify token.
