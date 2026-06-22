# Hướng dẫn sử dụng — SER Annotation Tool

Tool gán nhãn **Speech Emotion Recognition (SER)**: nghe file `.wav` và gán một trong ba nhãn cảm xúc — **Negative**, **Neutral**, **Positive**.

---

## Mở tool

Không cần cài đặt. Mở thẳng file trong trình duyệt:

```
ser.html
```

Chạy hoàn toàn offline, hỗ trợ Chrome và Firefox.

---

## Giao diện tổng quan

![Màn hình khởi động](images/ser_01_empty.png)

Khi mới mở, tool yêu cầu chọn **hai thứ** tách biệt nhau:

1. **📁 Thư mục audio** — thư mục chứa các file `.wav`
2. **📄 File danh sách (.txt)** — file liệt kê tên các file cần gán nhãn

---

## Định dạng file danh sách

File `.txt` mỗi dòng là một file audio, nhãn tùy chọn ngăn cách bằng **tab**:

```
audio_001.wav
audio_002.wav	negative
audio_003.wav	neutral
audio_004.wav	positive
```

- Dòng **không có nhãn** → chưa làm.
- Dòng **có nhãn** → coi như đã xác nhận, tool bỏ qua khi chọn câu tiếp theo.
- File `ser_output.txt` vừa export ra có thể dùng thẳng làm file danh sách cho lần làm tiếp theo — **không cần copy vào thư mục audio**.

---

## Bước 1 — Chọn thư mục audio

Nhấn **📁 Thư mục audio**, chọn thư mục chứa các file `.wav`.

Tool quét toàn bộ file trong thư mục và lập bảng tra cứu theo tên file. Nếu thư mục có nhiều thư mục con, tool vẫn tìm được theo đường dẫn tương đối hoặc tên file.

---

## Bước 2 — Chọn file danh sách

Nhấn **📄 File danh sách (.txt)**, chọn file `.txt`.

Nếu có file nào trong danh sách **không tìm thấy** trong thư mục audio, tool hiện dialog cảnh báo:

- **Tiếp tục (bỏ qua file thiếu)** — load bình thường, các file thiếu bị bỏ qua khi gán nhãn nhưng vẫn được giữ lại khi export.
- **Không load** — hủy, kiểm tra lại thư mục hoặc file danh sách rồi chọn lại.

Nếu trình duyệt có **bản nháp** từ phiên trước (chưa export), tool hỏi có muốn khôi phục không.

---

## Bước 3 — Gán nhãn

![Giao diện gán nhãn](images/ser_03_editor_blank.png)

Sau khi load xong, tool tự mở file **đầu tiên chưa làm**. Giao diện gồm:

| Phần tử | Chức năng |
|---|---|
| **Tên file + thông số** | Tên, thời lượng, sample rate, kênh, bit |
| **Waveform** | Biểu đồ sóng âm. Nhấn để seek đến vị trí đó rồi tự phát |
| **▶ / ⏮** | Phát/dừng — Phát lại từ đầu |
| **Tốc độ** | 0.5× – 2×, điều chỉnh bằng thanh trượt |
| **🔊** | Âm lượng 0–300% (dùng Web Audio API GainNode) |
| **Negative / Neutral / Positive** | Nhấn để chọn nhãn |
| **💾 Lưu** | Xác nhận nhãn và chuyển sang file tiếp theo |

**Quy trình gán một file:**

1. Nghe audio (nhấn `Space` hoặc nhấn ▶).
2. Nhấn **Negative** / **Neutral** / **Positive** (hoặc phím `1` / `2` / `3`).
3. Nhấn **💾 Lưu** (hoặc `Enter`) → tool lưu và chuyển sang file tiếp theo.

![Nhãn Negative được chọn](images/ser_04_label_neg.png)

Nhãn đang chọn được tô nền màu tương ứng. Trạng thái bên dưới nút Lưu cho biết:

- *chưa làm* — chưa chọn nhãn
- *✏️ đã chọn, chưa lưu* — đã chọn nhãn nhưng chưa nhấn Lưu
- *✔ đã lưu* — đã xác nhận

---

## Phím tắt

| Phím | Chức năng |
|---|---|
| `Space` | Phát / dừng |
| `R` | Phát lại từ đầu |
| `1` | Gán nhãn Negative |
| `2` | Gán nhãn Neutral |
| `3` | Gán nhãn Positive |
| `Enter` | Lưu & chuyển file tiếp |
| `J` | File tiếp theo |
| `K` | File trước |

---

## Bước 4 — Export

Nhấn **💾 Export ser_output.txt** để tải file kết quả về máy.

File xuất ra tên cố định `ser_output.txt`, định dạng:

```
audio_001.wav	negative
audio_002.wav	neutral
audio_003.wav	positive
audio_004.wav
```

Dòng chưa có nhãn được giữ nguyên (chỉ tên file).

Sau khi export:
- Bản nháp trong `localStorage` bị xóa.
- Thanh trạng thái chuyển thành **✓ đã export**.
- Lần làm tiếp theo chỉ cần chọn lại `ser_output.txt` làm file danh sách — tool tự nhận các nhãn đã có và bỏ qua những file đó.

---

## Chế độ Verify task (kiểm định)

Tích ô **✅ Verify task** trên thanh công cụ để chuyển sang chế độ kiểm định: file danh sách chứa nhãn của **2 annotator trước**, người làm hiện tại chỉ cần **chọn 1 trong 2 nhãn** đó.

### Định dạng file (3 cột)

File `.txt` mỗi dòng có **3 cột** ngăn cách bằng **tab**: tên file, nhãn của user 1, nhãn của user 2.

```
audio_001.wav	positive	neutral
audio_002.wav	negative	neutral
audio_003.wav	positive	positive
```

### Cách hoạt động

- Mọi thao tác (nghe, waveform, điều hướng, phím tắt) **giống hệt task thường**.
- Mỗi file chỉ hiện **2 nút nhãn** đúng bằng nhãn của 2 annotator trước; bạn chọn 1 trong 2. Bấm nhãn khác sẽ bị từ chối.
- Dòng nào **2 annotator trùng nhau** (ví dụ `audio_003.wav`) được **tự động tính là đã làm** với nhãn đó — không cần thao tác.
- Khi export, file ra có **thêm cột thứ 4** là nhãn bạn vừa chọn:

```
audio_001.wav	positive	neutral	positive
audio_002.wav	negative	neutral	neutral
audio_003.wav	positive	positive	positive
```

- Mở lại file đã làm dở: dòng nào **đã có cột 4** thì coi như đã xong, tool bỏ qua khi chọn câu tiếp theo (giống cơ chế cũ).

### Chọn nhầm chế độ / file

Tool tự đếm số cột của file danh sách và cảnh báo nếu không khớp với ô tích:

- **Chưa tích Verify mà chọn file 3–4 cột** → hỏi có muốn **bật Verify task** không.
- **Đã tích Verify mà chọn file gốc 1–2 cột** → hỏi có muốn **tắt Verify** và load như task thường không.

Đồng ý → tool tự bật/tắt ô tích rồi đọc lại file đúng định dạng.

---

## Tự động lưu nháp

Mỗi lần nhấn Lưu, nhãn được ghi ngay vào `localStorage` (khóa theo tên thư mục audio). Nếu đóng tab đột ngột, lần sau mở lại và chọn đúng thư mục + danh sách, tool sẽ hỏi có muốn **Khôi phục bản nháp** không.

Export xong → nháp bị xóa hoàn toàn.

---

## Chỉ số màu trong sidebar

| Chấm | Ý nghĩa |
|---|---|
| ⚫ xám | Chưa làm |
| 🟡 vàng | Đã chọn nhãn, chưa nhấn Lưu |
| 🟢 xanh | Đã lưu (confirmed) |

File thiếu audio hiển thị mờ trong sidebar và không thể gán nhãn.
