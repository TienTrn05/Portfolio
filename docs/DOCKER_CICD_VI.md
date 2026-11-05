# Docker và GitHub Actions

Dự án được build bằng Node.js 24 và phục vụ bằng Nginx. Image runtime chỉ chứa Nginx và thư mục `dist`, không chứa Node.js, source code hoặc `node_modules`. Bản Docker sử dụng base URL `/` và chạy tại gốc domain.

## Chạy trên máy local

Cài và mở Docker Desktop với Linux containers, sau đó chạy tại thư mục dự án:

```bash
docker compose up --build -d
docker compose ps
```

Truy cập <http://localhost:8080>. Kiểm tra health endpoint tại <http://localhost:8080/healthz>.

```bash
docker compose logs -f
docker compose down
```

Hoặc build và chạy trực tiếp. File hiện có trong repo tên là `dockerfile` viết thường, nên lệnh build chỉ định rõ `-f`:

```bash
docker build -f dockerfile -t tien-portfolio:local .
docker run --rm --name tien-portfolio -p 8080:80 tien-portfolio:local
```

Compose hỗ trợ biến `PORTFOLIO_PORT` để đổi cổng host, mặc định `8080`. Có thể đặt biến này trong shell hoặc file `.env` tại thư mục gốc; file `.env` bị loại khỏi Docker build context.

## Cấu hình Docker Hub và GitHub

1. Tạo repository `tien-portfolio` trên Docker Hub. Chọn Public nếu muốn máy khác pull mà không cần đăng nhập.
2. Tạo Docker Hub Personal Access Token có quyền **Read & Write**. Không dùng mật khẩu tài khoản làm token.
3. Mở GitHub repository → **Settings → Secrets and variables → Actions** và thêm các giá trị sau:

| Loại                          | Tên                    | Giá trị                                                                  |
| ----------------------------- | ---------------------- | ------------------------------------------------------------------------ |
| Repository variable           | `DOCKERHUB_USERNAME`   | Username Docker Hub của bạn, không phải email hoặc username GitHub       |
| Repository variable, tùy chọn | `DOCKERHUB_REPOSITORY` | Tên repository Docker Hub, mặc định `tien-portfolio`; không kèm username |
| Repository secret             | `DOCKERHUB_TOKEN`      | Personal Access Token có quyền push vào repository trên                  |

Image sẽ có dạng `docker.io/<DOCKERHUB_USERNAME>/<DOCKERHUB_REPOSITORY>:<tag>`.

Không đưa token vào source code, Dockerfile hoặc biến bắt đầu bằng `VITE_`. Các biến `VITE_` được nhúng vào JavaScript công khai khi build. Không gửi token qua chat.

## Pipeline hoạt động thế nào

Workflow nằm tại [docker-publish.yml](../.github/workflows/docker-publish.yml).

| Sự kiện                                                                    | CI  | Push Docker Hub                              |
| -------------------------------------------------------------------------- | --- | -------------------------------------------- |
| Pull request vào `main`                                                    | Có  | Không; không cần credentials Docker Hub      |
| Push vào `main`                                                            | Có  | Có, sau khi CI thành công                    |
| Push tag bắt đầu bằng `v`, ví dụ `v1.0.0`                                  | Có  | Có, sau khi CI thành công                    |
| Chạy thủ công bằng **Actions → CI and Docker Hub delivery → Run workflow** | Có  | Có nếu chọn `main` hoặc tag bắt đầu bằng `v` |

Job `ci` chạy `npm ci`, lint, kiểm tra Prettier, Vite build, kiểm tra cấu hình Compose và build image AMD64. Sau đó job khởi chạy container để kiểm tra health endpoint, HTML, JavaScript đã build, SPA fallback, file CV và phản hồi 404 cho asset không tồn tại.

Job `publish` chỉ chạy khi `ci` thành công. Job đăng nhập Docker Hub, build và push image đa kiến trúc `linux/amd64` và `linux/arm64`. Build cache được lưu bằng GitHub Actions cache. Các action Docker sử dụng workflow theo [tài liệu chính thức](https://docs.docker.com/build/ci/github-actions/).

Các tag được xuất bản:

- Push `main`: `latest`, `main`, `sha-<full-commit-sha>`.
- Push `v1.0.0`: `v1.0.0`, `sha-<full-commit-sha>`; giữ nguyên `latest` của `main`.

Tag `latest` thay đổi theo mỗi bản build `main`. Khi cần chạy lại đúng một bản, sử dụng tag commit hoặc image digest. Không ghi đè Git tag release đã phát hành.

Sau khi commit các file cấu hình và push lên `main`, mở tab **Actions** để theo dõi. Khi workflow thành công, mở tab **Tags** của repository Docker Hub để xem image.

## Chạy image đã xuất bản

Thay `YOUR_DOCKERHUB_USERNAME` bằng username thật:

```bash
docker pull YOUR_DOCKERHUB_USERNAME/tien-portfolio:latest
docker run -d --name tien-portfolio --restart unless-stopped -p 8080:80 YOUR_DOCKERHUB_USERNAME/tien-portfolio:latest
```

Với Compose, đặt `PORTFOLIO_IMAGE=YOUR_DOCKERHUB_USERNAME/tien-portfolio:latest` trong `.env`, rồi chạy:

```bash
docker compose pull
docker compose up -d --no-build
```

Nếu repository Docker Hub là private, chạy `docker login` trước khi pull.

Pipeline hiện thực hiện continuous integration và giao image lên Docker Hub. Triển khai tự động lên VPS hoặc nền tảng hosting cần bổ sung đích triển khai và credentials của môi trường đó.
