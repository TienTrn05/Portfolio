# Mô tả portfolio dùng trong CV

**Personal Portfolio — React, JavaScript, Vite, Tailwind CSS**

- Xây dựng portfolio responsive bằng React 19, JavaScript, Vite và Tailwind CSS 4.
- Tổ chức mã nguồn theo component, tách dữ liệu dự án, cấu hình và logic tương tác.
- Phát triển điều hướng theo vị trí cuộn, thanh tiến độ đọc và danh sách dự án mở rộng.
- Xây dựng hiệu ứng xuất hiện bằng IntersectionObserver, kết hợp requestAnimationFrame để gom các lần cập nhật khi cuộn.
- Triển khai giao diện sáng/tối và tùy chọn chuyển động, lưu thiết lập bằng localStorage và hỗ trợ reduced motion.
- Tích hợp form liên hệ qua FormSubmit với kiểm tra dữ liệu đầu vào, trạng thái gửi và thông báo kết quả.

Các gạch đầu dòng mô tả chức năng có trong repository portfolio. Phần kiểm tra đầu vào của form sử dụng thuộc tính HTML như `required`, `type="email"` và `maxLength`; phần gửi sử dụng Fetch API và xử lý phản hồi/lỗi. Khi ghi vào CV, không bổ sung số liệu hiệu năng, backend riêng, cơ sở dữ liệu hay CI/CD nếu chưa có bằng chứng triển khai tương ứng.

Xem [tài liệu kỹ thuật](PORTFOLIO_TECHNICAL_GUIDE_VI.md) để giải thích từng ý khi phỏng vấn.
