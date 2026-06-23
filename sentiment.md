# Hướng dẫn sử dụng — Sentiment Annotation Tool

Tool gán nhãn **cảm xúc cho văn bản (Sentiment)**: đọc từng câu và gán một trong ba nhãn — **Negative**, **Neutral**, **Positive**.

Đây là phiên bản "text" của tool SER: thay vì nghe `.wav`, bạn đọc trực tiếp câu hiển thị trên màn hình rồi chọn nhãn. Cách dùng, phím tắt, tự lưu nháp và chế độ Verify đều tương tự.

---

## Mở tool

Không cần cài đặt. Mở thẳng file trong trình duyệt:

```
sentiment.html
```

Chạy hoàn toàn offline, hỗ trợ Chrome và Firefox.

---

## Giao diện tổng quan

Khi mới mở, tool chỉ cần chọn **một thứ**:

- **📄 File text (.txt)** — file văn bản, **mỗi dòng là một câu** cần gán nhãn.

Không cần thư mục hay file phụ nào khác — toàn bộ nội dung nằm ngay trong file `.txt`.

---

## Định dạng file text

File `.txt` mỗi dòng là một câu, nhãn tùy chọn ngăn cách bằng **tab**:

```
Sản phẩm này dùng rất tốt, tôi rất hài lòng.
Giao hàng hơi chậm so với dự kiến.	negative
Chất lượng bình thường, không có gì đặc biệt.	neutral
Đóng gói đẹp, sẽ mua lại lần sau.	positive
```

- Dòng **không có nhãn** → chưa làm.
- Dòng **có nhãn** (cột 2) → coi như đã xác nhận, tool bỏ qua khi chọn câu tiếp theo.
- File `sentiment_output.txt` vừa export ra có thể dùng thẳng làm file đầu vào cho lần làm tiếp theo.

> Lưu ý: tab (`⇥`) là ký tự phân tách cột. Câu văn bản chỉ nên chứa khoảng trắng thường, không chứa tab.

---

## Bước 1 — Chọn file text

Nhấn **📄 File text (.txt)**, chọn file `.txt` của bạn.

Tool đọc từng dòng thành một câu cần gán nhãn. Nếu trình duyệt có **bản nháp** từ phiên trước (chưa export), tool hỏi có muốn khôi phục không.

---

## Bước 2 — Gán nhãn

Sau khi load xong, tool tự mở câu **đầu tiên chưa làm**. Giao diện gồm:

| Phần tử | Chức năng |
|---|---|
| **Số thứ tự câu** | Vị trí câu hiện tại / tổng số câu |
| **Khung văn bản** | Nội dung câu cần gán nhãn, hiển thị cỡ chữ lớn dễ đọc |
| **Negative / Neutral / Positive** | Nhấn để chọn nhãn |
| **💾 Lưu** | Xác nhận nhãn và chuyển sang câu tiếp theo |

**Quy trình gán một câu:**

1. Đọc câu trong khung văn bản.
2. Nhấn **Negative** / **Neutral** / **Positive** (hoặc phím `1` / `2` / `3`).
3. Nhấn **💾 Lưu** (hoặc `Enter`) → tool lưu và chuyển sang câu tiếp theo.

Nhãn đang chọn được tô nền màu tương ứng. Trạng thái bên dưới nút Lưu cho biết:

- *chưa làm* — chưa chọn nhãn
- *✏️ đã chọn, chưa lưu* — đã chọn nhãn nhưng chưa nhấn Lưu
- *✔ đã lưu* — đã xác nhận

---

## Phím tắt

| Phím | Chức năng |
|---|---|
| `1` | Gán nhãn Negative |
| `2` | Gán nhãn Neutral |
| `3` | Gán nhãn Positive |
| `Enter` | Lưu & chuyển câu tiếp |
| `J` | Câu tiếp theo |
| `K` | Câu trước |

---

## Bước 3 — Export

Nhấn **💾 Export sentiment_output.txt** để tải file kết quả về máy.

File xuất ra tên cố định `sentiment_output.txt`, định dạng:

```
Sản phẩm này dùng rất tốt, tôi rất hài lòng.	positive
Giao hàng hơi chậm so với dự kiến.	negative
Chất lượng bình thường, không có gì đặc biệt.	neutral
Đóng gói đẹp, sẽ mua lại lần sau.
```

Dòng chưa có nhãn được giữ nguyên (chỉ nội dung câu).

Sau khi export:
- Bản nháp trong `localStorage` bị xóa.
- Thanh trạng thái chuyển thành **✓ đã export**.
- Lần làm tiếp theo chỉ cần chọn lại `sentiment_output.txt` — tool tự nhận các nhãn đã có và bỏ qua những câu đó.

---

## Chế độ Verify task (kiểm định)

Tích ô **✅ Verify task** trên thanh công cụ để chuyển sang chế độ kiểm định: file chứa nhãn của **2 annotator trước**, người làm hiện tại chỉ cần **chọn 1 trong 2 nhãn** đó.

### Định dạng file (3 cột)

File `.txt` mỗi dòng có **3 cột** ngăn cách bằng **tab**: nội dung câu, nhãn của user 1, nhãn của user 2.

```
Sản phẩm này dùng rất tốt.	positive	neutral
Giao hàng quá chậm.	negative	neutral
Đóng gói đẹp, sẽ mua lại.	positive	positive
```

### Cách hoạt động

- Mọi thao tác (đọc câu, điều hướng, phím tắt) **giống hệt task thường**.
- Mỗi câu chỉ hiện **2 nút nhãn** đúng bằng nhãn của 2 annotator trước; bạn chọn 1 trong 2. Bấm nhãn khác sẽ bị từ chối. Dòng "Nhãn tham chiếu" hiển thị nhãn của cả 2 user.
- Dòng nào **2 annotator trùng nhau** (ví dụ câu thứ 3) được **tự động tính là đã làm** với nhãn đó — không cần thao tác.
- Khi export, file ra có **thêm cột thứ 4** là nhãn bạn vừa chọn:

```
Sản phẩm này dùng rất tốt.	positive	neutral	positive
Giao hàng quá chậm.	negative	neutral	neutral
Đóng gói đẹp, sẽ mua lại.	positive	positive	positive
```

- Mở lại file đã làm dở: dòng nào **đã có cột 4** thì coi như đã xong, tool bỏ qua khi chọn câu tiếp theo.

### Chọn nhầm chế độ / file

Tool tự đếm số cột của file và cảnh báo nếu không khớp với ô tích:

- **Chưa tích Verify mà chọn file 3–4 cột** → hỏi có muốn **bật Verify task** không.
- **Đã tích Verify mà chọn file 1–2 cột** → hỏi có muốn **tắt Verify** và load như task thường không.

Đồng ý → tool tự bật/tắt ô tích rồi đọc lại file đúng định dạng.

---

## Tự động lưu nháp

Mỗi lần nhấn Lưu, nhãn được ghi ngay vào `localStorage` (khóa theo tên file text). Nếu đóng tab đột ngột, lần sau mở lại và chọn đúng file, tool sẽ hỏi có muốn **Khôi phục bản nháp** không.

Export xong → nháp bị xóa hoàn toàn.

---

## Chỉ số màu trong sidebar

| Chấm | Ý nghĩa |
|---|---|
| ⚫ xám | Chưa làm |
| 🟡 vàng | Đã chọn nhãn, chưa nhấn Lưu |
| 🟢 xanh | Đã lưu (confirmed) |

Mỗi dòng trong sidebar có số thứ tự câu, chấm trạng thái và thẻ nhãn (`neg` / `neu` / `pos`) nếu đã gán.
