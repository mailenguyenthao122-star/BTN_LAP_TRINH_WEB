# M12 – Hỗ trợ người học (Thảo) – gói ghép vào dự án của Tiến

## Cách ghép (3 bước)
1. Sao lưu thư mục dự án (hoặc commit Git), rồi chép đè các file trong gói này vào thư mục gốc dự án
   (cùng chỗ với `app.py`). Thư mục `templates/support/` và `tests/` là mới.
2. Xóa `templates/portal/support.html` (trang cũ, không còn dùng).
3. Chạy `python seed.py` để tạo lại CSDL (xóa `instance/student_support.db` cũ), rồi chạy `app.py`.
   Kiểm thử: `python -m unittest discover -s tests -v`

## File MỚI
- `support.py` – blueprint M12 (`/support/...`)
- `seed_support.py` – 80 yêu cầu mẫu rải 5 tháng
- `templates/support/` – `my`, `new`, `manage`, `detail`, `_macros`
- `tests/test_m12.py` – 10 kịch bản kiểm thử

## File ĐÃ SỬA (chỉ những chỗ sau – nếu Tiến đã sửa file khác đi, hãy áp dụng đúng các thay đổi này)
- `models.py`: thay bảng `YeuCauHoTro` cũ bằng `LoaiHoTro`, `YeuCauHoTro`, `PhanHoi`, `LichSuTrangThai`; thêm index;
  `init_db` nạp 4 loại hỗ trợ và tự bỏ bảng `YeuCauHoTro` kiểu cũ nếu gặp.
- `portal.py`: bỏ mục 5 (hỗ trợ + API polling); `/portal/support` chỉ còn chuyển hướng sang `/support/`.
- `app.py`: import + `register_blueprint(support_bp)`; gọi `seed_support_data` khi khởi tạo.
- `seed.py`: gọi `seed_support_data` trong phần `__main__`.
- `seed_portal.py`: bỏ phần tạo 2 yêu cầu mẫu kiểu cũ.
- `templates/base.html`: 2 liên kết menu `portal.support` -> `support.my_tickets` (sinh viên) và `support.manage` (nhân viên/admin).

## Quy ước dữ liệu (cho Hà – M17)
- `YeuCauHoTro.trang_thai`: `cho_xu_ly` | `dang_xu_ly` | `da_giai_quyet` (lưu không dấu)
- `ngay_tao`, `ngay_giai_quyet`: chuỗi `YYYY-MM-DD HH:MM:SS`; `ngay_giai_quyet` = NULL khi chưa giải quyết
- Loại: `LoaiHoTro.ma_loai` = hoc_vu | tai_khoan | hoc_phi | ky_thuat
- API: `GET /support/api/summary?tu_ngay=YYYY-MM-DD&den_ngay=YYYY-MM-DD` (chỉ admin) – có kèm `pham_vi`, `nguon`
- Tỷ lệ xử lý = da_giai_quyet / tổng × 100 (chỉ số nội bộ, không phải KPI chính thức)
