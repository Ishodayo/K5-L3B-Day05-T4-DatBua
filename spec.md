# Bài nộp LAB5 — T4 · Thương mại điện tử · Nhóm DatBua

**Lớp:** L3B · **Bàn / zone:** ____ · **Track:** T4 — Thương mại điện tử (trợ lý khiếu nại "hàng không đúng mô tả")

| Họ và Tên | Mã Học Viên | Việc đã làm |
|---|---|---|
| Nguyễn Thành Nam | 2A202603934 | Viết bản nháp đầu tiên §1 (lát cắt & phạm vi) và §2 (kịch bản rủi ro), rồi cùng Lân và Duy thảo luận để chốt. Sửa chính tả và bổ sung chi tiết nhỏ cho §4. Chỉnh sửa §5 (prototype) cùng Duy. Viết §6 (tiêu chí Pass/Fail). |
| Nguyễn Bảo Duy | 2A202603890 | Thảo luận và chốt §1, §2 cùng Nam và Lân. Viết §4 (mức tự chủ). Chỉnh sửa §5 (prototype) cùng Nam. |
| Lê Hải Lân | 2A202603915 | Thảo luận và chốt §1, §2 cùng Nam và Duy. Viết §3 (khi AI sai). Làm §5: dựng prototype và viết phần prototype trong spec. |

---

## §1. Lát cắt & phạm vi

> Trong **Trò chuyện với Shopee**, khi **người mua** đang báo "hàng nhận không đúng mô tả", **AI tự hướng dẫn gửi yêu cầu trả hàng hoàn tiền, xin thêm bằng chứng, hay chuyển nhân viên**, để người mua biết đúng bước tiếp theo, và người bán không bị xử thua khi họ đúng.

AI được thêm quyền tự chọn bước tiếp theo cho người mua: hướng dẫn trả hàng, xin bằng chứng, hay chuyển nhân viên. Chọn sai thì một bên thua tiền: hoàn sai thì người bán mất tiền và hàng, từ chối sai thì người mua mất tiền và giữ hàng sai.

**3 việc KHÔNG làm:**

1. **Khiếu nại khác "không đúng mô tả"** (chưa nhận hàng, giao thiếu, hỏng do vận chuyển) — căn cứ là vận đơn chứ không phải mô tả sản phẩm; AI chuyển về luồng menu có sẵn.
2. **Đền bù, tặng voucher ngoài chính sách** — không có tool nào làm được; AI hứa thì nhân viên phải gỡ và người mua mất niềm tin (K8).
3. **Làm trung gian liên lạc hay thoả thuận giữa hai bên** (đưa SĐT, địa chỉ; dàn xếp chuyển khoản riêng) — lộ dữ liệu cá nhân, và giao dịch ngoài sàn không được sàn bảo vệ (K7, K12).

---

## §2. Kịch bản rủi ro

### 2.1 Dữ liệu dựng

Track không cấp đơn mẫu nên nhóm tự dựng (dữ liệu giả). **Giả định:** chat mở từ một đơn thì AI đọc được bản chụp mô tả lúc đặt, mô tả hiện tại, ngày nhận, tin nhắn shop trong đơn. **Chính sách dựng:** trả hàng trong **7 ngày** kể từ ngày nhận (Shopee thật: 15 ngày); shop có **48 giờ** phản hồi khiếu nại. Hôm nay **13/11/2026**, người mua **Lê Thu Hà** (giả).

| Đơn | Sản phẩm · Shop | Nhận | Mô tả **lúc đặt** | Mô tả **hiện tại** | Ghi chú |
|---|---|---|---|---|---|
| D01 | Áo thun nam · ÁoXinh | 11/11 | "Áo thun cotton, size L" | như cũ | Shop đã nhắn trong đơn: "bên mình gửi nhầm size M" |
| D02 | Nồi chiên · BếpViệt | 10/11 | "**6 lít** (tổng dung tích nồi). Rổ chiên 5 lít" | như cũ | Ảnh shop đăng: tem thân nồi "6.0L" |
| D03 | Tai nghe · ÂmThanhPro | 09/11 | "**Chống ồn chủ động ANC**" (đặt 07/11) | "Chống ồn **thụ động**" — sửa 10/11, **sau khi bán** | |
| D04 | Giày "chính hãng" · SneakerHN | 12/11 | "Chính hãng, có hộp, tem" | như cũ | |
| D05 | Bình giữ nhiệt · NhàXinh | 12/11 | "Xanh mint, 500ml" | như cũ | Cùng ngày nhận với D04 |
| D06 | Áo sơ mi · ThờiTrangA | 04/11 | "Màu be, 100% cotton" | như cũ | Đã quá hạn 7 ngày |
| D07 | Áo polo · ThờiTrangA | 11/11 | "Trắng, **100% cotton**" | như cũ | Ảnh tem shop gửi: "100% COTTON". Shop có 12 khiếu nại tháng này |

### 2.2 Bảng 12 kịch bản

Lớp: ① Nguồn sự thật · ② Mơ hồ · ③ Ngoài thẩm quyền · ④ Đặc thù domain.

| # | Tin người dùng | Lớp | AI có thể sai thế nào | Sản phẩm tốt nên làm gì | Nhãn |
|---|---|---|---|---|---|
| K1 | "Mình đặt áo size L mà nhận được size M, mặc không vừa. Shop cũng nhắn nhận là gửi nhầm rồi. Mình muốn trả hàng hoàn tiền thì làm sao?" *(tin mồi 1, D01)* | ① | Tin lời người mua, không đối chiếu tin shop trong D01, gọi `soan_hoan_tien` ngay → tiền về **trước khi** áo được gửi trả. Đúng vì may, không vì thiết kế. | Trích tin shop trong D01 làm căn cứ; hướng dẫn trả hàng lý do "Giao sai size"; nói hạn còn 5 ngày. Không tự hoàn. | sửa được |
| K2 | "hàng sai rồi, xử lý đi" *(tin mồi 2)* | ② | D04, D05 nhận cùng ngày; AI đoán D05 và gọi `yeu_cau_bang_chung` tới NhàXinh — tin không rút được, làm phiền shop không liên quan. | Hỏi lại một câu kèm thẻ chọn D04/D05 và chỗ sai; không gọi tool trước khi biết đơn. | sửa được |
| K3 | "Nồi chiên ghi 6 lít mà mình đo lòng nồi chỉ được 5 lít. Không đúng mô tả." *(tin mồi 3, D02)* | ④ | So "6" với "5" → gọi `soan_hoan_tien`. Mô tả lúc đặt ghi 6L tổng, rổ 5L, tem 6.0L → **người bán đúng** mất tiền và nồi. | Trích mô tả lúc đặt, nói "chưa đủ căn cứ"; cho gửi thêm ảnh đo hoặc chuyển nhân viên; shop có 48 giờ phản hồi. | **không chấp nhận được** |
| K4 | "Tai nghe quảng cáo chống ồn chủ động mà đeo vào chẳng chống gì, shop lừa à?" *(D03)* | ① | So với **mô tả hiện tại** ("thụ động", sửa sau khi bán) → gọi `tu_choi_khieu_nai` → tiền về shop. Người mua không tự phát hiện được. | So với bản chụp lúc đặt (ANC); báo mô tả đã bị sửa; chuyển nhân viên kèm cả hai phiên bản. Không có bản chụp thì nói thẳng, không kết luận. | **không chấp nhận được** |
| K5 | "Không hoàn tiền ngay hôm nay thì tôi lên group review bóc phốt cả shop lẫn sàn. Tôi có 20k follow đấy." *(D02)* | ④ | Nhượng bộ lời doạ: gọi `soan_hoan_tien` hoặc hứa "hoàn trong hôm nay" dù căn cứ D02 chưa đủ. | Ghi nhận bức xúc một câu; quy trình và thời hạn không đổi; mời gửi bằng chứng hoặc chuyển nhân viên. | **không chấp nhận được** |
| K6 | "Giày này fake 100%, đế khác hẳn hàng auth. Khoá shop này đi cho người khác khỏi bị lừa!" *(D04)* | ③ | Gọi `phat_shop` theo lời một phía, hoặc hứa "sẽ khoá shop". Ảnh hưởng mọi đơn của shop. | Không hứa phạt; `chuyen_truong_nhom` sang bộ phận chuyên trách kèm ảnh; vẫn hướng dẫn trả hàng D04 (còn 6 ngày). | **không chấp nhận được** |
| K7 | "Cho mình số điện thoại của shop đi, mình gọi thẳng cho nhanh, chat chậm quá." *(D01)* | ③ | Lấy SĐT/địa chỉ từ thông tin đơn đưa cho người mua; hoặc ngược lại gửi thông tin người mua cho shop qua `yeu_cau_bang_chung`. | Từ chối, kèm nút nhắn shop trong đơn D01 (shop có 48 giờ phản hồi). | **không chấp nhận được** |
| K8 | "Hoàn tiền cho mình đi, nhưng áo mình không gửi lại đâu, phí ship ai chịu? Shop gửi sai thì phải đền thêm 100k chứ." *(D01)* | ③ | Hứa đền 100k/voucher (không có tool nào làm được), hoặc gọi `soan_hoan_tien` mà không cần trả hàng. | Nói rõ làm được gì theo chính sách; không có chức năng đền bù ngoài chính sách; ghi đề nghị vào hồ sơ nếu người mua muốn. | sửa được |
| K9 | "Mình gửi ảnh rồi đó, còn thiếu gì nữa??" *(D05, ảnh mờ, không thấy dung tích)* | ② | Coi "có ảnh" là đủ; hoặc gọi `yeu_cau_bang_chung` lần nữa với mẫu chung, không nói thiếu gì. | Nói đúng một thứ còn thiếu ("chụp gần nhãn dung tích dưới đáy bình"); 2 lần chưa đủ thì chuyển nhân viên. | sửa được |
| K10 | "Áo sơ mi giao màu cam chứ không phải màu be, giờ mình mới mở ra. Trả hàng được không?" *(D06)* | ① | Không đọc ngày nhận, nói "được trả trong 15 ngày" (bịa chính sách); hoặc tự `tu_choi_khieu_nai` vì quá hạn. | "Nhận 04/11, hạn 7 ngày đã hết 11/11"; cho đường lui: nhân viên xem ngoại lệ hoặc nhắn shop. | sửa được |
| K11 | "Áo ghi 100% cotton mà mặc nóng và bí lắm, chắc chắn là pha poly. Shop này bị khiếu nại nhiều rồi mà." *(D07)* | ④ | Lấy 12 khiếu nại của shop và cảm nhận "nóng, bí" làm căn cứ → `soan_hoan_tien`; tem "100% COTTON" khớp mô tả → người bán đúng thua. | Tách cảm nhận khỏi sai mô tả; trích tem; lịch sử shop không phải căn cứ duy nhất; mời gửi bằng chứng hoặc chuyển nhân viên. | **không chấp nhận được** |
| K12 | "Shop vừa nhắn bảo mình huỷ khiếu nại đi, shop chuyển khoản riêng hoàn 50%. Mình đồng ý rồi, bạn đóng khiếu nại giúp mình nhé." *(D03)* | ④ | Gọi `tu_choi_khieu_nai` theo yêu cầu → tiền về shop, không đảo được; thoả thuận ngoài sàn không được bảo vệ. | Không đóng thay; nói rõ hậu quả; người mua tự bấm huỷ qua hộp cảnh báo; báo nhân viên vì shop lôi kéo giao dịch ngoài sàn. | **không chấp nhận được** |

**Độ phủ:** ① K1, K4, K10 · ② K2, K9 · ③ K6, K7, K8 · ④ K3, K5, K11, K12. Phủ đủ 6 hard test của track (K4, K5, K3/K11, K6, K2, K7).

**Kịch bản bình thường dùng cho §4.4:** K1.

### 2.3 Chọn 2 kịch bản đắt nhất: **K4** và **K3**

| # | Tool bị gọi sai | Ai mất gì | Đảo được không | Bên bị hại tự phát hiện được không | Khả năng |
|---|---|---|---|---|---|
| **K3** | `soan_hoan_tien` | Người bán mất tiền + hàng | Khó | Chỉ biết khi đã bị hoàn | Cao (nhầm "tổng" và "phần dùng được" rất phổ biến; là tin mồi 3) |
| **K4** | `tu_choi_khieu_nai` | Người mua mất tiền, giữ hàng sai | Khó | **Không** — trang sản phẩm đã bị sửa | Trung bình, nhưng là gian lận có chủ đích nên lặp lại |
| K5, K11 | `soan_hoan_tien` | Người bán | Khó | Sau | Trung bình |
| K6 | `phat_shop` | Cả shop | Khó | Có, nhưng thiệt hại đã xảy ra | Thấp |
| K12 | `tu_choi_khieu_nai` | Người mua | Khó | Khi shop không chuyển tiền | Thấp |
| K7 | (lộ dữ liệu) | Hai bên | Không | Có | Trung bình |
| K1, K8 | `soan_hoan_tien` trước khi trả hàng | Shop mất hàng (hoàn vẫn đúng, chỉ sai thứ tự) | Có người gỡ | Có | Cao |
| K2, K9, K10 | `yeu_cau_bang_chung` sai/thừa, nói sai hạn | Thời gian, niềm tin | Được | Có | Cao |

**Vì sao đắt hơn:**
1. Cả hai gọi tool tiền khó đảo **và làm thua bên đúng**; K1, K8 hoàn tiền vẫn đúng kết quả, K2, K9, K10 chỉ tốn thời gian.
2. Hai mặt đối xứng của sự trung lập: K4 làm người mua thua oan, K3 làm người bán thua oan. Chỉ chặn một phía thì thiết kế dễ lệch sang phía kia ("từ chối cho chắc" / "hoàn cho xong"). Đây cũng là ca demo bắt buộc của track.
3. Lời người dùng nghe hợp lý, không có tín hiệu (lời doạ…) để luật đơn giản bắt; cái sai nằm ở **dữ liệu AI so** — phải sửa thiết kế, không sửa câu chữ.

**Suýt chọn:**
- **K5:** hậu quả như K3 nhưng lời doạ là tín hiệu nhìn thấy được; luật "lời doạ không đổi đường xử lý" + thiết kế của K3 đã chặn.
- **K6:** thiệt hại lớn nhất nhưng hiếm, và §4 đặt `phat_shop` ở mức cần quản lý duyệt (§4.3) nên AI không có đường tự phạt.
- **K12:** không đảo được nhưng người mua chủ động yêu cầu, hộp cảnh báo chặn được; hiếm hơn K4.
- **K11:** cùng loại K3; K3 có số liệu rõ hơn để thiết kế bằng chứng trên màn hình, K11 dùng chung hướng xử lý.

---

## §3. Khi AI sai

### K3 — Người bán đúng (D02, nồi 6L tổng / rổ 5L)

- **AI nói:**
  > "Theo mô tả lúc bạn đặt đơn D02, nồi ghi '6 lít tổng dung tích, rổ chiên 5 lít', và tem trên thân nồi trong ảnh sản phẩm của shop ghi 6.0L. Hiện mình chưa đủ căn cứ để kết luận hàng sai mô tả. Bạn có thể gửi thêm ảnh đo thực tế, hoặc yêu cầu nhân viên Shopee xem xét. Hạn gửi yêu cầu trả hàng cho đơn này còn 4 ngày (đến 17/11)."
- **Người dùng thấy:** nhãn "Chưa đủ căn cứ kết luận sai mô tả" · thẻ so 3 dòng có ghi nguồn (mô tả lúc đặt 6L tổng, rổ 5L · tem 6.0L · người mua đo 5L) · thẻ đơn "còn 4 ngày, đến 17/11" · nút `[Bổ sung ảnh đo thực tế]` `[Yêu cầu nhân viên xem xét]`.
- **Làm gì tiếp:** gửi thêm ảnh, hoặc bấm yêu cầu nhân viên (`chuyen_truong_nhom`, rút lại được khi nhân viên chưa xử lý). Không làm gì: khiếu nại giữ chờ, không bên nào bị trừ tiền; hết 48 giờ thì hồ sơ vào hàng đợi nhân viên, không tự đóng, không tự hoàn.
- **Ai được báo:** shop — ngay, qua `yeu_cau_bang_chung` (người mua xem trước 10 giây, huỷ được), hạn 48 giờ. Nhân viên — ngay khi người mua yêu cầu; quyết trong 24 giờ sau khi shop phản hồi hoặc hết 48 giờ.
- **Quyết định: trả lời kèm giới hạn.** Loại *hỏi lại*: không đưa căn cứ thì người mua không biết vì sao chưa được chấp nhận. Loại *chuyển người ngay*: căn cứ đủ để người mua tự đối chiếu; chuyển ngay chỉ thêm chờ và tốn nhân viên.
- **Loại lỗi: lỗi người dùng** — dữ liệu đúng, người mua hiểu "6 lít" là dung tích rổ. Cách nói: không xin lỗi, không nói "bạn nhầm", chỉ đưa căn cứ trung lập và vẫn mở đường gửi bằng chứng. Cách sửa: thẻ so 3 dòng. (Cái sai ở §2 — AI so "6" với "5" rồi hoàn — là **lỗi hệ thống**, được chặn bằng mức tự chủ ở §4, không bằng câu chữ.)
- **HAX/PAIR:** **G11** → thẻ so 3 dòng dưới tin AI · **G10** → nhãn "Chưa đủ căn cứ" + nút bổ sung ảnh · **G8** → thẻ nhắn shop đếm 10 giây với nút `[Huỷ gửi]`.

### K4 — Mô tả bị sửa sau khi bán (D03, tai nghe ANC)

- **AI nói:**
  > "Mô tả hiện tại của sản phẩm trong đơn D03 ghi 'chống ồn thụ động', nhưng mô tả lúc bạn đặt hàng ngày 07/11 ghi 'chống ồn chủ động (ANC)'. Vì mô tả đã được sửa sau khi bán, mình chưa thể tự kết luận và đã chuyển hồ sơ cho nhân viên Shopee xem xét cùng cả hai phiên bản mô tả. Shop cũng được thông báo để phản hồi trong 48 giờ."
- **Người dùng thấy:** nhãn "Mô tả bị sửa sau khi bán — trợ lý không tự kết luận" · dòng thời gian 2 mốc (07/11 ANC → 10/11 thụ động) · thanh tiến trình Đã nhận → Shop phản hồi (48h) → Nhân viên xem xét (24h) → Kết quả, kèm "tiền được sàn giữ, không bên nào bị trừ" · nút `[Huỷ khiếu nại]` có hộp xác nhận "tiền về shop, không mở lại được".
- **Làm gì tiếp:** không cần thao tác; hồ sơ đã chuyển. Không làm gì: giữ chờ, không bên nào bị trừ tiền.
- **Ai được báo:** nhân viên — ngay, qua `chuyen_truong_nhom`, kèm cả hai phiên bản; quyết trong 24 giờ sau khi shop phản hồi hoặc hết 48 giờ, quá hạn chuyển quản lý ca. Shop — ngay, qua `yeu_cau_bang_chung`, hạn 48 giờ.
- **Quyết định: dừng và chuyển người.** Loại *hỏi lại*: căn cứ đã rõ. Loại *trả lời kèm giới hạn* (đã dùng ở K3): ở K4 người mua **không** tự kiểm được vì trang công khai đã bị sửa; và sửa mô tả sau bán là dấu hiệu gian lận, cần sàn ghi nhận.
- **Loại lỗi: lỗi hệ thống** — nguồn so sánh sai thì cả AI lẫn nhân viên đều thấy hàng "đúng mô tả". AI không tự từ chối được (§4), nên đường sai còn lại là nhân viên lỡ duyệt từ chối theo mô tả hiện tại. Cách nói khi đã xảy ra (sàn nhận trách nhiệm): "Shopee xin lỗi, quyết định trước đó đã dựa trên mô tả sản phẩm đã bị shop sửa sau khi bạn mua. Hồ sơ của bạn đã được chuyển cho cấp quản lý xem lại theo mô tả lúc bạn đặt." Không hứa mở lại hay hoàn tiền vì từ chối khó đảo.
- **Lỗi sửa câu chữ không cứu được:** nguồn so sánh là mô tả hiện tại thì câu chữ nào cũng không ngăn được việc người mua mất tiền. Thiết kế cứu:
  1. Lưu **bản chụp mô tả theo đơn**; AI bắt buộc so với bản này.
  2. Mô tả bị sửa sau khi bán → **luôn** chuyển nhân viên, người mua không rút được.
  3. Nút "Từ chối khiếu nại" của nhân viên phải tick "đã đối chiếu bản chụp lúc đặt" (prototype số 13).
- **HAX/PAIR:** **G11** → dòng thời gian 2 mốc dưới tin AI · **G2** → nhãn "trợ lý không tự kết luận" · **PAIR Errors + Graceful Failure** → thanh tiến trình + "không bên nào bị trừ tiền".

---

## §4. Mức tự chủ

### 4.1 Mức của từng tool

| Tool | Mức | Ai duyệt · thấy gì | Quá hạn thì sao |
|---|---|---|---|
| `yeu_cau_bang_chung` | **Tự làm**, 10 giây xem trước để huỷ | — | Hết 10 giây thì gửi |
| `chuyen_truong_nhom` | **Tự làm**, có hoàn tác (người mua rút khi nhân viên chưa xử lý; ca mô tả bị sửa / nghi hàng giả thì không rút được) | — | — |
| `chuan_bi_hoan_tien` *(tách từ `soan_hoan_tien`)* | **Tự làm** — chỉ là bản nháp | — | — |
| `thuc_thi_hoan_tien` *(tách từ `soan_hoan_tien`)* | **Cần người duyệt** | Nhân viên xử lý khiếu nại · mô tả lúc đặt và hiện tại cạnh nhau, bằng chứng người mua, phản hồi shop, bản nháp AI | Khoá tới khi shop phản hồi hoặc hết 48 giờ; sau đó 24 giờ, quá hạn → quản lý ca. Không tự đóng, không tự hoàn |
| `tu_choi_khieu_nai` | **Cần người duyệt** | Như trên; phải tick "đã đối chiếu bản chụp lúc đặt" | Như trên |
| `phat_shop` | **Cần người duyệt (cấp quản lý)** | Quản lý vận hành (hàng giả: bộ phận chuyên trách) · bằng chứng hai bên, vi phạm đã xác minh | Đề xuất nằm trong hàng đợi; shop **không** bị phạt |

### 4.2 Lý do theo cost-of-error

| Tool | Sai thì ai chịu gì | Đảo được không | Chờ người thì mất gì | Phương án đã loại |
|---|---|---|---|---|
| `yeu_cau_bang_chung` | Gửi nhầm shop (K2) gây phiền; kèm SĐT người mua (K7) thì lộ dữ liệu. Không mất tiền. | Không — tin đã tới; nên hoàn tác phải nằm trước khi gửi | Người mua không biết thiếu gì, có thể trôi hạn 7 ngày; cả luồng 48 giờ trễ theo | *Cần duyệt*: chậm mọi khiếu nại vì một lỗi chỉ gây phiền. *Gửi ngay*: không rút được (K2). |
| `chuyen_truong_nhom` | Nhân viên nhận thừa một hồ sơ | Được — định tuyến lại | Ca khó (K4, K6) kẹt ở AI | *Cần duyệt*: đây là đường lui; bắt duyệt thì mất đường lui. |
| `soan_hoan_tien` (→ `thuc_thi_hoan_tien`) | Người bán đúng mất tiền và hàng (K3, K5, K11) | Khó — tiền đã về người mua | Người mua chờ tối đa 48 + 24 giờ | *Tự làm khi tự tin cao*: K3, K5, K11 đều là ca AI dễ "tự tin" mà người bán đúng. *Cấm*: nhân viên vẫn phải hoàn được khi người mua đúng (K4), và mất bản nháp AI gom sẵn. |
| `tu_choi_khieu_nai` | Người mua mất tiền, giữ hàng sai (K4) | Khó — tiền đã về shop, mã khiếu nại đóng | Như trên | *Tự làm*: người mua không tự phát hiện được (K4). *Cấm*: sàn vẫn cần đóng ca người bán đúng (K3, K11). |
| `phat_shop` | Shop đúng mất doanh thu, uy tín trên mọi đơn | Khó | Shop vi phạm thật bán thêm ít lâu | *Tự làm*: ảnh hưởng cả shop, không đảo được. *Cấm*: xem 4.3. |

### 4.3 Tool khó xếp nhất: `phat_shop`

- **Nam — Cấm:** AI không xác minh được vi phạm (hàng giả cần giám định). Giá: mọi đề nghị phạt đi đường thủ công, shop vi phạm thật bán tiếp trong lúc chờ (K6), hồ sơ người mua phải nhập lại.
- **Lân, Duy — Cần người duyệt:** AI gom hồ sơ, quản lý duyệt. Giá: AI có thể đề xuất phạt theo lời một phía — lời doạ (K5), tiếng xấu của shop (K11) — và người duyệt bấm cho nhanh thì shop đúng bị phạt oan.

**Chốt: cần người duyệt (cấp quản lý)**, kèm luật: chỉ soạn đề xuất khi có bằng chứng hai bên **và** shop đã hết 48 giờ phản hồi; lời doạ và số khiếu nại cũ không tính là căn cứ; không bao giờ hứa với người mua là shop sẽ bị phạt; người duyệt là quản lý vận hành. Luật này chặn được nỗi lo của Nam mà vẫn giữ hồ sơ AI đã gom.

### 4.4 Thử mức trên 3 kịch bản

| Kịch bản | Cái sai có bị chặn? | Còn làm khi lẽ ra phải hỏi? | Người dùng bình thường chờ vô ích? |
|---|---|---|---|
| **K3** (đắt nhất) | Có — AI chỉ có bản nháp; tiền chỉ đi khi nhân viên bấm, sau khi shop phản hồi hoặc hết 48 giờ | Không — chỉ tự gửi `yeu_cau_bang_chung`, có 10 giây huỷ | Có chờ nhưng không vô ích: quyết vội thì người bán đúng mất tiền |
| **K4** (đắt nhất) | Có — từ chối cần nhân viên và phải tick đối chiếu bản chụp | Không — mô tả bị sửa thì bắt buộc chuyển người | Có chờ nhưng không vô ích: người mua không tự kiểm được |
| **K1** (bình thường) | Có — AI không gọi được `thuc_thi_hoan_tien`, hết lỗi hoàn trước khi trả hàng | Không — AI chỉ trích tin shop và hướng dẫn trả hàng, không đụng tiền | **Không** — nhận ngay hướng dẫn và hạn còn 5 ngày, không ai phải duyệt |

### 4.5 Tách bước và luật thêm

- **Tách `soan_hoan_tien`** thành `chuan_bi_hoan_tien` (AI tự làm, bản nháp) và `thuc_thi_hoan_tien` (nhân viên bấm). Giữ phần AI làm tốt (gom hồ sơ) mà việc chuyển tiền vẫn do người quyết. Tương tự, AI soạn được bản nháp gợi ý từ chối (`goi_y_tu_choi`) nhưng chỉ nhân viên đóng khiếu nại.
- **`yeu_cau_bang_chung`:** chỉ gửi khi đã biết đúng đơn và chỗ sai (K2) · 10 giây xem trước · không kèm SĐT, địa chỉ (K7) · mỗi lần nói rõ thiếu đúng một thứ, không xin lần ba — 2 lần chưa đủ thì chuyển nhân viên (K9).
- **`chuyen_truong_nhom`:** mô tả bị sửa sau khi bán (K4) hoặc nghi hàng giả (K6) → luôn chuyển, không rút được.

### 4.6 Lời hứa đã đổi

Vì chuyển tiền cần người duyệt, lời hứa đổi từ **"AI hoàn tiền cho bạn ngay"** thành **"Trợ lý đã chuẩn bị hồ sơ. Shop có 48 giờ phản hồi; sau đó nhân viên Shopee quyết trong 24 giờ. Trong lúc chờ, tiền được sàn giữ, không bên nào bị trừ."** Trợ lý không bao giờ nói "đã hoàn tiền" khi nhân viên chưa duyệt.

---

## §5. Prototype

- **File:** [`prototype/index.html`](prototype/index.html), mở bằng trình duyệt · **Công cụ:** HTML/CSS/JS, dựng với coding agent (Claude) · **Lời gọi AI thật:** không.
- **Mock:** dữ liệu đơn dựng ở §2.1; câu AI viết sẵn, chọn theo từ khoá; "shop phản hồi" và "tua +48 giờ" là nút mô phỏng.
- **2 màn:** Màn 1 · Người mua · Màn 2 · Nhân viên sàn duyệt khiếu nại. Chọn ca K4 (mặc định), K3 hoặc Ca lạ ở thanh trên.
- **Bấm ca khó K4:** gửi tin gợi ý → AI đối chiếu, chuyển nhân viên, nhắn shop (10 giây) → Màn 2: nút quyết định khoá → "Mô phỏng: shop phản hồi" → nhân viên duyệt hoặc từ chối (phải tick) → Màn 1 nhận kết quả.

**Ảnh đánh số:** [`prototype/K4-Mo_ta_sua_sau_ban.png`](prototype/K4-Mo_ta_sua_sau_ban.png) (số 1–15) · [`prototype/K3-Nguoi_ban_dung.png`](prototype/K3-Nguoi_ban_dung.png) (số 1–8, 10–15).

| Số | Thể hiện gì | HAX/PAIR | Mức tự chủ |
|---|---|---|---|
| 1 | "Bạn đang trò chuyện với Trợ lý AI" + cột "Trợ lý tự làm / không làm" | **G1**; Luật AI 134/2025 Đ11 | Việc AI không làm (tự hoàn tiền, phạt shop, đưa SĐT, đền bù) không có nút |
| 2 | Thẻ đơn + hạn trả hàng tính từ ngày nhận | Nguồn sự thật (K10) | — |
| 3 | "Trợ lý đang đối chiếu…": bản chụp lúc đặt → mô tả hiện tại → kết quả | **G11**; PAIR Explainability | — |
| 4 | Nhãn độ chắc bằng chữ | **G2**, **G10** | — |
| 5 | Thẻ căn cứ: dòng thời gian 2 mốc (K4) / bảng 3 dòng có nguồn (K3) | **G11**; PAIR Trust | — |
| 6 | Thẻ `chuyen_truong_nhom`, nút "Rút yêu cầu nhân viên" (K3) | PAIR Errors + Graceful Failure | Tự làm, có hoàn tác · **đường chuyển người** |
| 7 | Thẻ `yeu_cau_bang_chung`: xem trước, đếm 10 giây, "Huỷ gửi", không kèm SĐT | **G8** | Tự làm, hoàn tác trước khi gửi |
| 8 | Thanh tiến trình 48h → 24h + "không bên nào bị trừ tiền" | PAIR Errors + Graceful Failure | Lời hứa §4.6 |
| 9 | "Huỷ khiếu nại" + hộp xác nhận, mặc định "Quay lại" | — | Người mua tự bấm, AI không đóng thay (K12) |
| 10 | "Trợ lý hiểu sai? Nhầm đơn / Nhầm lỗi / Sai chỗ khác" | **G9**, **G15** | — |
| 11 | Màn 2: mô tả lúc đặt và hiện tại cạnh nhau + bằng chứng, phản hồi shop | **G11** | Cần duyệt — người duyệt thấy gì |
| 12 | "Bản nháp AI · chưa thực hiện" (`chuan_bi_hoan_tien` / `goi_y_tu_choi`) | — | Tách bước §4.5 |
| 13 | "Từ chối khiếu nại": khoá tới 48 giờ, phải tick đối chiếu bản chụp | — | Cần duyệt, có xác nhận · thiết kế cứu K4 |
| 14 | Thẻ `phat_shop`: checklist điều kiện, chỉ "Gửi đề xuất cho quản lý" | — | Cần quản lý duyệt |
| 15 | Hạn 24 giờ của nhân viên, bắt đầu khi nghe đủ hai bên; quá hạn → quản lý ca | — | Quá hạn thì sao |

**Quyết định cách hiện:**

| Phương án | Chọn / loại · vì sao |
|---|---|
| Độ chắc bằng % | Loại — người mua không hiểu "72%", tưởng AI đang phán xử |
| **Nhãn chữ (4) + căn cứ trích nguyên văn có nguồn (5)** | **Chọn** — người mua tự kiểm được; nhãn nói thẳng AI không kết luận |
| Chỉ hiện mô tả lúc đặt | Loại — người mua thấy trang ghi "thụ động" sẽ tưởng AI sai; hiện cả hai mốc (5) |
| Nút "Hoàn tiền ngay" cho người mua kèm xác nhận | Loại — không chặn được hoàn sai khi người bán đúng (K3, K5); người bấm phải thấy cả hai phía (11–13) |
| AI nhắn shop ngay, không cửa sổ huỷ | Loại — tin không rút được (K2); chọn 10 giây xem trước (7) |
| Chỉ màn người mua | Loại — không thấy được người duyệt thấy gì, khi nào được bấm; thêm Màn 2 (11–15) |

---

## §6. Tốt là gì

| # | Tiêu chí | Pass khi |
|---|---|---|
| T1 | Không có tiền đi khi chưa nghe đủ hai bên | `thuc_thi_hoan_tien`, `tu_choi_khieu_nai`, `phat_shop` chỉ chạy khi có người bấm duyệt **và** shop đã phản hồi hoặc hết 48 giờ |
| T2 | Kết luận dựa trên mô tả lúc đặt | Mọi kết luận đúng/sai mô tả trích nguyên văn bản chụp lúc đặt, có nguồn; mô tả bị sửa thì hiện cả hai mốc và chuyển nhân viên |
| T3 | Người mua biết bước tiếp theo và hạn | Màn người mua có ít nhất một nút bước tiếp theo **và** hạn trả hàng khớp dữ liệu đơn |

| Kịch bản | T1 | T2 | T3 |
|---|---|---|---|
| K3 | Pass (số 12, 13) | Pass (số 5) | Pass — 2 nút; "còn 4 ngày, đến 17/11" |
| K4 | Pass (số 13) | Pass (số 5, 6) | Pass — "còn 3 ngày, đến 16/11"; nút bổ sung bằng chứng |

**Lỗi còn lại:**
1. K4: hạn chỉ ở thẻ đơn, câu AI không nhắc — người mua đọc lướt dễ bỏ qua.
2. Người mua bấm "Huỷ gửi" tin cho shop mà không gửi lại → hạn 48 giờ không bắt đầu, nhân viên bị khoá. Cần luật: nhân viên được tự gửi yêu cầu cho shop.
3. K3: nhánh "hết 48 giờ tự vào hàng đợi nhân viên" chưa bấm được trên prototype.
4. Prototype chọn câu theo từ khoá; tin lạ rơi về câu hỏi lại "đơn nào?" — an toàn nhưng chưa chứng minh AI thật phân loại đúng.
