# Báo cáo Day 14 — Trịnh Đức Hưng — 2A202603177

- Mã học viên dùng với `--student`: `2A202603177`
- `seed_tag` trong `frames_A.txt`: `82ac3100fd`

## 1. Dự đoán trước sweep (bước 5b)

Ghi **trước** khi chạy `scripts/pp_sweep.py`, rồi commit và push ngay (lịch sử commit là bằng chứng thời điểm viết). Không sửa phần này sau khi có kết quả.

- Tham số tôi nghĩ giảm công sửa nhiều nhất: **`score_thr`**, tăng từ 0.1 lên khoảng **0.4** (giữ `min_points=0`). Tôi nghĩ `min_points` gần như không đổi kết quả.
- Vì sao (nhìn ảnh BEV tập A: box thừa nhiều hay box sót nhiều?):
  - Tập A thừa box nhiều hơn sót box rất nhiều. Cấu hình gốc dự đoán 83 box, nhãn sửa tay chỉ có 44. Số đo: **xóa 54, thêm 15, sửa 14 → chi phí 38.2**. Riêng s03f40 phải xóa 33 box. Ảnh `png_A/s03f40.jpg` (BEV và 7 camera) cho thấy khoảng 16 `Cyclist` và nhiều `Car` đặt dọc hai mép đường. Trên camera, các box này nằm trên bụi cây, dải cây xanh và lan can, không phải xe thật.
  - Các box tôi xóa có `score` thấp hơn hẳn các box tôi giữ: trung vị khoảng 0.31, so với 0.86 ở box giữ. Gần như toàn bộ `Cyclist` thừa ở s03f40 có `score` từ 0.21 đến 0.48. Đặt ngưỡng 0.4 sẽ bỏ khoảng 37 box thừa (−11.1 chi phí, mỗi box 0.3), nhưng làm mất khoảng 3 box đúng (+3.0, mỗi box sót 1.0). Như vậy chi phí giảm khoảng 8, từ 38.2 xuống khoảng 30. Ngưỡng 0.5 bỏ thêm box thừa nhưng cũng mất thêm box đúng, nên tôi đoán mức lợi gần bằng 0.4.
  - `min_points` không giúp được: chỉ 1 box thừa có dưới 10 điểm LiDAR. Box thừa vẫn có trung vị khoảng 40 điểm, vì chúng nằm trên vật thật, chỉ bị gán sai lớp. Vì vậy lọc theo số điểm ở mức 5 hoặc 10 gần như không bỏ được box nào.
  - Rủi ro: 15 box cần thêm (14 ở s01f25) là vật mô hình bỏ sót hẳn. Tăng `score_thr` không sửa được nhóm này, và nếu đặt ngưỡng quá cao thì sẽ có thêm box bị sót.

## 2. Tập A: sửa tay và tune

| Cấu hình | Xóa | Thêm | Sửa | Chi phí |
|---|---:|---:|---:|---:|
| Gốc | | | | |
| `best_config.json` | | | | |

- Tham số đã đổi trong `best_config.json`: ...
- Kết quả leave-one-out (dán các dòng `scripts/pp_sweep.py` in ra): ...
- Cấu hình có ổn định trên từng frame không? Vì sao tôi vẫn chọn (hoặc không chọn) nó: ...

## 3. Trước/sau (bước 8)

![so sánh](../../d14_<ID>/compare/<frame>.jpg)

- `scripts/pp_compare.py` báo giảm (hoặc tăng) ...% công sửa trên tập A.
- Dự đoán ở mục 1 đúng hay sai? Giải thích bằng số box xóa/thêm/sửa: ...

## 4. Tập B

Số đo từ `scripts/pp_prelabel.py --frames d14_<ID>/frames_B.txt --config d14_<ID>/best_config.json --gt d14_<ID>/ref_B.json`:

| Xóa | Thêm | Sửa | Chi phí |
|---:|---:|---:|---:|
| | | | |

- Loại box tốn công sửa nhất ở tập B: ...

## 5. Giới hạn

- Nêu ít nhất 2 giới hạn (ví dụ: chỉ 3 frame tune; model KITTI không có Truck/Bus; chi phí là công thức, chưa phải thời gian sửa thật): ...
