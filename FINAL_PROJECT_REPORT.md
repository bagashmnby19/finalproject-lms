# FINAL PROJECT REPORT

## 1. Identitas

* **Nama** : Bagas Humanabiyu
* **NIM** : A11.2023.15392
* **Kelas** : A11.4602

---

## 2. Gambaran Project

**Simple LMS Extended Backend** merupakan aplikasi backend Learning Management System (LMS) yang dibangun menggunakan **Django** dan **Django Ninja API**. Aplikasi ini menyediakan layanan untuk mengelola course, lesson, enrollment, serta progress pembelajaran dengan sistem autentikasi berbasis JWT.

Selain fitur utama tersebut, aplikasi juga mengimplementasikan beberapa teknologi pendukung seperti **Redis** untuk caching, **MongoDB** sebagai penyimpanan log aktivitas dan analytics, serta **Celery** yang dipadukan dengan **RabbitMQ** untuk menjalankan proses asynchronous. Seluruh layanan dijalankan menggunakan **Docker Compose** sehingga proses deployment menjadi lebih mudah dan terstruktur.

---

## 3. Fitur Utama

| No | Fitur                                    | Status |
| -- | ---------------------------------------- | :----: |
| 1  | JWT Authentication                       |    ✅   |
| 2  | Custom User (Admin, Instructor, Student) |    ✅   |
| 3  | Course Management API                    |    ✅   |
| 4  | Lesson Management API                    |    ✅   |
| 5  | Enrollment Management                    |    ✅   |
| 6  | Learning Progress Tracking               |    ✅   |
| 7  | PostgreSQL Database                      |    ✅   |
| 8  | Swagger API Documentation                |    ✅   |
| 9  | Docker Compose Deployment                |    ✅   |

---

## 4. Implementasi Teknologi Tambahan

| Teknologi | Implementasi                                                                  |
| --------- | ----------------------------------------------------------------------------- |
| Redis     | Cache course list, cache detail course, cache invalidation, dan rate limiting |
| MongoDB   | Activity logging dan learning analytics                                       |
| Celery    | Menjalankan background task secara asynchronous                               |
| RabbitMQ  | Message broker untuk Celery                                                   |
| Flower    | Monitoring task Celery secara real-time                                       |

---

## 5. Detail Implementasi

### Redis

Redis digunakan untuk meningkatkan performa aplikasi melalui mekanisme caching. Data daftar course maupun detail course akan disimpan sementara sehingga permintaan berikutnya tidak perlu mengambil data kembali dari PostgreSQL.

Implementasi Redis meliputi:

* Cache daftar course
* Cache detail course
* Cache invalidation saat instructor membuat course baru
* Rate limiting sebanyak **60 request per menit** untuk setiap alamat IP

Endpoint terkait:

* `GET /api/courses`
* `GET /api/courses/{course_id}`

---

### MongoDB

MongoDB digunakan sebagai database NoSQL untuk menyimpan data yang bersifat non-relasional, seperti log aktivitas pengguna dan data analitik pembelajaran.

Data yang disimpan meliputi:

* Activity Logs
* Learning Analytics
* Statistik jumlah enrollment setiap course

Endpoint:

* `GET /api/activity-logs`
* `GET /api/reports/course-statistics`

---

### Celery & RabbitMQ

Celery menjalankan proses yang membutuhkan waktu lebih lama secara asynchronous, sedangkan RabbitMQ bertindak sebagai message broker yang mengirimkan task dari aplikasi menuju Celery Worker.

Task yang tersedia antara lain:

* Enrollment Email
* Certificate Generation
* Export CSV Report
* Update Course Statistics

Endpoint:

* `POST /api/reports/courses/export`
* `POST /api/tasks/update-course-statistics`
* `GET /api/tasks/{task_id}/status`

---

### Progress Tracking

Fitur ini memungkinkan student menandai lesson yang telah diselesaikan sehingga progres belajar dapat dipantau secara otomatis.

Endpoint:

* `POST /api/progress/complete`
* `GET /api/my-progress`

---

## 6. Menjalankan Project

Clone repository

```bash
git clone <repository-url>
cd simple-lms
```

Menjalankan seluruh service

```bash
docker compose up -d
```

Migrasi database

```bash
docker exec -it lms_web python manage.py migrate
```

Swagger Documentation

```text
http://127.0.0.1:8000/api/docs
```

Flower Dashboard

```text
http://127.0.0.1:5555
```

---

## 7. Akun Pengujian

| Role       | Username    | Password      |
| ---------- | ----------- | ------------- |
| Admin      | admin       | admin123      |
| Instructor | instructor1 | instructor123 |
| Student    | student1    | student123    |

---

## 8. Endpoint Utama

### Authentication

* `POST /api/auth/register`
* `POST /api/token/pair`
* `GET /api/auth/me`

### Course

* `GET /api/courses`
* `POST /api/courses`
* `GET /api/courses/{course_id}`
* `GET /api/courses/{course_id}/lessons`

### Enrollment

* `POST /api/enrollments/{course_id}`
* `GET /api/my-enrollments`
* `POST /api/enrollments/{enrollment_id}/complete`

### Progress

* `POST /api/progress/complete`
* `GET /api/my-progress`

### Analytics

* `GET /api/activity-logs`
* `GET /api/reports/course-statistics`

### Background Task

* `POST /api/tasks/update-course-statistics`
* `POST /api/reports/courses/export`
* `GET /api/tasks/{task_id}/status`

---

## 9. Kendala dan Solusi

| Kendala                           | Solusi                                                                         |
| --------------------------------- | ------------------------------------------------------------------------------ |
| Port 8000 digunakan aplikasi lain | Menghentikan service yang menggunakan port tersebut sebelum menjalankan Docker |
| Celery task berstatus **PENDING** | Memastikan Celery Worker dan RabbitMQ berjalan dengan baik                     |
| Token JWT kedaluwarsa             | Melakukan login kembali untuk memperoleh token baru                            |
| Username sudah digunakan          | Menambahkan validasi username dan email sebelum registrasi                     |
| Certificate gagal dibuat          | Menggunakan akun student yang telah terdaftar pada course                      |

---

## 10. Kesimpulan

Pengembangan **Simple LMS Extended Backend** berhasil mengintegrasikan berbagai teknologi backend modern, mulai dari REST API berbasis Django Ninja, autentikasi JWT, PostgreSQL, Redis, MongoDB, RabbitMQ, Celery, Flower, hingga Docker Compose.

Implementasi tersebut menghasilkan aplikasi yang tidak hanya mampu mengelola proses pembelajaran, tetapi juga memiliki performa yang lebih baik melalui caching, mendukung proses asynchronous, menyediakan monitoring task, serta menyimpan data analitik untuk kebutuhan evaluasi sistem.
