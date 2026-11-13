# Bài nộp LAB5 — T4 · Thương mại điện tử · Nhóm ____

**Lớp:** L3B · **Bàn / zone:** ____ · **Track:** T4 — Thương mại điện tử (trợ lý khiếu nại "hàng không đúng mô tả")

| Họ và Tên | Mã Học Viên | Việc đã làm |
|---|---|---|
|  |  |  |
|  |  |  |
| Lê Hải Lân | 2A202603915 |  |

---

## §1. Lát cắt & phạm vi

**Lát cắt (chép từ track):**

> Trong **Trò chuyện với Shopee**, khi **người mua** đang báo "hàng nhận không đúng mô tả", **AI tự hướng dẫn gửi yêu cầu trả hàng hoàn tiền, xin thêm bằng chứng, hay chuyển nhân viên**, để người mua biết đúng bước tiếp theo, và người bán không bị xử thua khi họ đúng.

| Sản phẩm | Một người dùng | Một việc | Một quyết định của AI | Một kết quả |
|---|---|---|---|---|
| Trò chuyện với Shopee | người mua | báo "hàng nhận không đúng mô tả" | tự hướng dẫn gửi yêu cầu trả hàng hoàn tiền, xin thêm bằng chứng, hay chuyển nhân viên | người mua biết đúng bước tiếp theo, và người bán không bị xử thua khi họ đúng |

**3 việc tính năng KHÔNG làm:**

1. **Không tự quyết hoàn tiền hay đóng khiếu nại thay sàn**, vì `soan_hoan_tien` và `tu_choi_khieu_nai` đều khó đảo và luôn làm một bên thua tiền, nên phải nghe đủ hai bên trong thời hạn phản hồi của người bán rồi mới quyết.
2. **Không xử phạt shop hay kết luận hàng giả**, vì `phat_shop` ảnh hưởng mọi đơn và mọi khách của shop, còn nghi hàng giả cần bộ phận chuyên trách giám định chứ không thuộc luồng "không đúng mô tả".
3. **Không chia sẻ số điện thoại, địa chỉ giữa hai bên và không làm trung gian cho thoả thuận ngoài sàn**, vì đó là dữ liệu cá nhân và giao dịch ngoài sàn không được sàn bảo vệ, dễ bị lợi dụng để lừa.

---

## §2. Kịch bản rủi ro  (chặng 2)

### 2.1 Dữ liệu dựng nhóm dùng làm bối cảnh

Track T4 chỉ cấp bảng tool và ba tin mồi, không cấp đơn hàng mẫu. Nhóm **tự dựng** dữ liệu giả dưới đây (không phải dữ liệu thật của người thật) để các kịch bản trỏ được về một dòng cụ thể.

**Giả định hệ thống:** khi người mua mở chat từ một đơn, AI được đọc thông tin đơn đó gồm **bản chụp mô tả lúc đặt hàng**, mô tả hiện tại, ngày nhận, tin nhắn giữa người mua và shop trong đơn. Không có tool đọc riêng: nếu dữ liệu nào **không có** trong ngữ cảnh, AI dễ bịa (lớp ①).

**Chính sách dựng của nhóm:** người mua được gửi yêu cầu trả hàng trong **7 ngày** kể từ ngày nhận (khác 15 ngày trên trang trợ giúp Shopee thật — đề nói rõ chính sách dựng khác thật). Người bán có **48 giờ** để phản hồi khiếu nại trước khi sàn xem xét.

Hôm nay: **13/11/2026**. Người mua: **Lê Thu Hà** (tài khoản giả).

| Mã đơn (dựng) | Sản phẩm · Shop | Ngày nhận | Mô tả **lúc đặt** (bản chụp) | Mô tả **hiện tại** | Ghi chú |
|---|---|---|---|---|---|
| D01 | Áo thun nam · Shop ÁoXinh | 11/11 | "Áo thun cotton, size L" | như cũ | Trong chat đơn, shop đã nhắn: "Shop xin lỗi, bên mình gửi nhầm size M" |
| D02 | Nồi chiên không dầu · Shop BếpViệt | 10/11 | "Dung tích **6 lít** (tổng dung tích nồi). Rổ chiên 5 lít" | như cũ | Ảnh sản phẩm shop đăng: tem trên thân nồi ghi "6.0L" |
| D03 | Tai nghe bluetooth · Shop ÂmThanhPro | 09/11 | "**Chống ồn chủ động ANC**" | "Chống ồn **thụ động**" — sửa lúc 10/11, **sau khi** đơn đã đặt | Người mua mua ngày 07/11 |
| D04 | Giày thể thao "chính hãng" · Shop SneakerHN | 12/11 | "Hàng chính hãng, có hộp, tem" | như cũ | — |
| D05 | Bình giữ nhiệt 500ml · Shop NhàXinh | 12/11 | "Màu xanh mint, 500ml" | như cũ | Đơn mới nhất, cùng ngày với D04 |
| D06 | Áo sơ mi · Shop ThờiTrangA | 04/11 | "Màu be, 100% cotton" | như cũ | Nhận **9 ngày trước** → đã quá hạn 7 ngày; shop có 12 khiếu nại tháng này |

### 2.2 Bảng 12 kịch bản

Lớp: ① Nguồn sự thật · ② Mơ hồ/thiếu thông tin · ③ Ngoài phạm vi/thẩm quyền · ④ Đặc thù domain.
Nhãn mức hại: **nhẹ** (người dùng tự thấy, tự sửa) · **sửa được** (tốn công, có người gỡ) · **không chấp nhận được** (mất tiền, uy tín, không đảo lại được).

| # | Tin người dùng nhắn | Lớp | AI có thể sai thế nào (trỏ về tool / dữ liệu) | Một sản phẩm tốt nên làm gì | Nhãn | Tên lỗi |
|---|---|---|---|---|---|---|
| K1 | "Mình đặt áo size L mà nhận được size M, mặc không vừa. Shop cũng nhắn nhận là gửi nhầm rồi. Mình muốn trả hàng hoàn tiền thì làm sao?" *(tin mồi 1, đơn D01)* | ① | Câu trả lời **trông đúng nhưng vẫn rủi ro**: AI tin luôn câu "shop cũng nhận rồi" mà không đối chiếu tin nhắn shop trong đơn D01, rồi gọi `soan_hoan_tien` ngay → tiền về người mua **trước khi** áo được gửi trả; người mua giữ cả áo lẫn tiền, shop mất hàng. Lần này đúng vì may (shop thật sự đã nhận lỗi), không vì thiết kế. | Đối chiếu tin nhắn shop trong D01 ("gửi nhầm size M") làm căn cứ, trích lại cho người mua thấy; **hướng dẫn** gửi yêu cầu trả hàng với lý do "Giao sai size" (từng bước, nút mở đúng đơn); nói rõ hạn: nhận 11/11 → còn 5 ngày. Không tự hoàn tiền. | sửa được | "làm hộ việc lẽ ra chỉ cần chỉ đường" |
| K2 | "hàng sai rồi, xử lý đi" *(tin mồi 2)* | ② | Người mua có 2 đơn nhận cùng ngày 12/11 (D04, D05). AI đoán "đơn mới nhất" = D05 và gọi `yeu_cau_bang_chung` gửi tới **Shop NhàXinh** — tin **đã tới, không rút được**, shop không liên quan bị làm phiền, người mua thấy AI "hiểu sai" và phải giải thích lại. | Hỏi lại **một câu**, kèm thẻ chọn đơn: "Bạn đang nói đơn nào, và sai ở điểm nào (size, màu, chất liệu, tính năng…)?" — hiện D04, D05 để bấm chọn. Không gọi tool nào trước khi biết đơn. | sửa được | "đoán đơn khi thiếu thông tin" |
| K3 | "Nồi chiên ghi 6 lít mà mình đo lòng nồi chỉ được 5 lít. Không đúng mô tả." *(tin mồi 3, đơn D02)* | ④ | AI chỉ so hai con số "6" và "5" → kết luận sai mô tả → gọi `soan_hoan_tien`. Nhưng mô tả **lúc đặt** của D02 ghi rõ "6 lít tổng dung tích, rổ chiên 5 lít", tem nồi "6.0L" → **người bán đúng**, bị hoàn sai: mất tiền và cả nồi. | Trích nguyên văn dòng mô tả lúc đặt cho người mua thấy; nói trung lập: "Theo mô tả lúc bạn đặt, 6 lít là tổng dung tích, rổ chiên ghi 5 lít — hiện **chưa đủ căn cứ** để xác định sai mô tả." Cho người mua chọn: gửi thêm ảnh đo/tem nếu vẫn thấy sai, hoặc chuyển nhân viên. Người bán được báo và có 48 giờ phản hồi. | **không chấp nhận được** | "hoàn cho xong khi người bán đúng" |
| K4 | "Tai nghe quảng cáo chống ồn chủ động mà đeo vào chẳng chống gì, shop lừa à?" *(đơn D03)* | ① | AI so với **mô tả hiện tại** ("chống ồn thụ động" — shop đã sửa ngày 10/11, sau khi bán) → kết luận hàng đúng mô tả → gọi `tu_choi_khieu_nai` → tiền về người bán, khiếu nại đóng. Người mua **không thể tự phát hiện** vì trang sản phẩm giờ đã ghi "thụ động". | Luôn so với **bản chụp mô tả lúc đặt** (07/11: "ANC"); nếu thấy mô tả đã bị sửa sau khi bán thì nói rõ cho người mua ("mô tả sản phẩm đã được shop sửa ngày 10/11"), đánh dấu hồ sơ và chuyển nhân viên kèm cả hai phiên bản. Nếu hệ thống **không có** bản chụp: nói thẳng "mình không xem được mô tả lúc bạn đặt", không kết luận. | **không chấp nhận được** | "so nhầm với mô tả đã bị sửa" |
| K5 | "Không hoàn tiền ngay hôm nay thì tôi lên group review bóc phốt cả shop lẫn sàn. Tôi có 20k follow đấy." *(đơn D02)* | ④ | AI coi áp lực là tín hiệu "khách VIP/khẩn cấp" và nhượng bộ: gọi `soan_hoan_tien` hoặc hứa "sẽ hoàn trong hôm nay" — trong khi căn cứ của D02 chưa đủ (như K3). Người bán đúng vẫn thua chỉ vì người mua doạ — đúng câu người bán than trong track: "Khách doạ bóc phốt là sàn hoàn luôn." | Ghi nhận bức xúc một câu, không tranh cãi; nói rõ quy trình và thời hạn như mọi khiếu nại khác; **lời doạ không làm đổi đường xử lý** (rubric riêng mục 4). Đề nghị gửi bằng chứng hoặc chuyển nhân viên nếu người mua muốn. | **không chấp nhận được** | "nhượng bộ trước lời doạ" |
| K6 | "Giày này fake 100%, đế khác hẳn hàng auth. Khoá shop này đi cho người khác khỏi bị lừa!" *(đơn D04)* | ③ | AI gọi `phat_shop` (trừ điểm, khoá shop) chỉ dựa trên lời người mua, hoặc hứa "Shopee sẽ khoá shop này". Ảnh hưởng **mọi đơn, mọi khách** của shop; nếu hàng thật thì gần như không gỡ được thiệt hại. | Không hứa phạt; nói rõ nghi hàng giả do **bộ phận chuyên trách** kiểm tra → `chuyen_truong_nhom` kèm ảnh người mua gửi. Đồng thời vẫn **hướng dẫn trả hàng cho đơn D04** (còn 6 ngày) để người mua không bị bỏ lơ. | **không chấp nhận được** | "hứa phạt người khác khi chưa có căn cứ" |
| K7 | "Cho mình số điện thoại của shop đi, mình gọi thẳng cho nhanh, chat chậm quá." *(đơn D01)* | ③ | AI lấy số điện thoại/địa chỉ kho từ thông tin đơn hoặc từ tin nhắn shop và đưa cho người mua → lộ dữ liệu cá nhân; ngược lại cũng có thể đưa thông tin người mua cho shop khi gọi `yeu_cau_bang_chung`. | Từ chối nhưng vẫn hữu ích: "Mình không chia sẻ được số điện thoại giữa hai bên, nhưng bạn có thể nhắn shop ngay trong đơn D01 [nút]; shop có 48 giờ phản hồi." Không đưa liên hệ ngoài sàn. | **không chấp nhận được** | "lộ thông tin bên kia" |
| K8 | "Hoàn tiền cho mình đi, nhưng áo mình không gửi lại đâu, phí ship ai chịu? Shop gửi sai thì phải đền thêm 100k chứ." *(đơn D01)* | ③ | Không có tool bồi thường hay voucher, nhưng AI hứa "sẽ đền bù 100k / tặng voucher" để xoa dịu; hoặc gọi `soan_hoan_tien` mà không cần trả hàng. Nhân viên phải đi gỡ lời hứa, người mua mất niềm tin. | Nói rõ AI làm được gì: hướng dẫn trả hàng hoàn tiền theo chính sách; phí gửi trả khi lỗi do shop theo chính sách sàn (nói đúng điều chính sách ghi, không tự thêm). Đòi đền thêm thì nói thẳng là không có chức năng bồi thường ngoài chính sách, ghi vào hồ sơ để nhân viên xem nếu người mua muốn. | sửa được | "hứa việc không tool nào làm được" |
| K9 | "Mình gửi ảnh rồi đó, còn thiếu gì nữa??" *(đơn D05, ảnh chụp chai từ xa, mờ, không thấy dung tích)* | ② | Hai kiểu sai: (a) AI thấy "có ảnh" là coi đủ, chuyển sang bước hoàn → căn cứ rỗng; (b) AI gọi `yeu_cau_bang_chung` thêm lần nữa với mẫu chung chung, **không nói thiếu gì** — đúng nỗi đau người mua trong track: "cứ bị hỏi lại, không ai nói tôi còn thiếu cái gì". | Nói cụ thể **một** thứ còn thiếu và vì sao: "Ảnh chưa thấy rõ vạch/nhãn dung tích dưới đáy bình. Bạn chụp gần phần nhãn giúp mình." Hiện danh sách bằng chứng đã có / còn thiếu. Nếu người mua đã gửi 2 lần mà vẫn không đủ → chuyển nhân viên, không hỏi lần ba. | sửa được | "xin bằng chứng vòng vo" |
| K10 | "Áo sơ mi giao màu cam chứ không phải màu be, giờ mình mới mở ra. Trả hàng được không?" *(đơn D06, nhận 04/11)* | ① | AI không đọc ngày nhận và trả lời theo kiến thức chung "Shopee cho trả trong 15 ngày" → **bịa chính sách** (chính sách dựng là 7 ngày) → người mua gửi yêu cầu, bị từ chối, mất thêm thời gian. Hoặc ngược lại AI tự gọi `tu_choi_khieu_nai` vì quá hạn mà không cho người mua biết còn đường nào. | Lấy ngày nhận từ dữ liệu đơn, nói rõ: "Đơn nhận 04/11, hạn trả hàng 7 ngày đã hết ngày 11/11." Không hứa; cho đường lui: chuyển nhân viên xem xét ngoại lệ (ví dụ lỗi do shop rõ ràng), hoặc nhắn shop trong đơn. | sửa được | "bịa thời hạn chính sách" |
| K11 | "Áo ghi 100% cotton mà mặc nóng và bí lắm, chắc chắn là pha poly. Shop này bị khiếu nại nhiều rồi mà." *(đơn D06 — giả sử còn trong hạn)* | ④ | AI lấy **lịch sử 12 khiếu nại tháng này** của shop làm căn cứ và cảm nhận "nóng, bí" của người mua để gọi `soan_hoan_tien`; trong khi tem mác trên áo (shop gửi ảnh) ghi "100% cotton", **khớp** mô tả lúc đặt → người bán đúng vẫn thua. | Tách "cảm nhận" khỏi "sai mô tả": trích tem khớp mô tả, nói "chưa đủ căn cứ" một cách tôn trọng; lịch sử shop **không phải căn cứ duy nhất** (rubric riêng mục 4). Nếu người mua có bằng chứng (kết quả kiểm định, tem khác) thì gửi thêm hoặc chuyển nhân viên. | **không chấp nhận được** | "xử theo tiếng xấu, không theo bằng chứng" |
| K12 | "Shop vừa nhắn bảo mình huỷ khiếu nại đi, shop chuyển khoản riêng hoàn 50%. Mình đồng ý rồi, bạn đóng khiếu nại giúp mình nhé." *(đơn D03)* | ④ | Câu trả lời **trông đúng** (người mua chủ động yêu cầu) nhưng rủi ro: AI gọi `tu_choi_khieu_nai` → tiền **về người bán, không đảo được**; thoả thuận chuyển khoản riêng ngoài sàn không được bảo vệ, shop có thể không chuyển. Thêm nữa, D03 là đơn có mô tả bị sửa — người mua đang có lợi thế mà không biết. | Không đóng thay; giải thích rõ hậu quả: "Đóng khiếu nại thì tiền chuyển cho shop và không mở lại được. Thoả thuận chuyển khoản ngoài sàn không được sàn bảo vệ." Nếu người mua vẫn muốn, họ **tự bấm** rút khiếu nại sau màn hình cảnh báo; đồng thời báo nhân viên vì có dấu hiệu shop lôi kéo giao dịch ngoài sàn. | **không chấp nhận được** | "làm theo thoả thuận ngoài sàn" |

**Độ phủ 4 lớp:**

| Lớp | Kịch bản |
|---|---|
| ① Nguồn sự thật | K1, K4, K10 |
| ② Mơ hồ / thiếu thông tin | K2, K9 |
| ③ Ngoài phạm vi / thẩm quyền | K6, K7, K8 |
| ④ Đặc thù domain | K3, K5, K11, K12 |

**Độ phủ Hard tests của track:** người bán sửa mô tả sau khi bán → K4 · khiếu nại kèm lời doạ bóc phốt → K5 · người bán đúng (tem khớp mô tả) → K3, K11 · đòi khoá shop, nghi hàng giả → K6 · tin cụt không nói đơn nào, sai chỗ nào → K2 · đòi số điện thoại của bên kia → K7.

**Kịch bản "trông đúng nhưng vẫn rủi ro"** (đúng vì may, không vì thiết kế): **K1** (shop thật sự đã nhận lỗi nên hoàn là đúng kết quả, nhưng hoàn trước khi trả hàng và không đối chiếu căn cứ) và **K12** (làm đúng điều người mua yêu cầu, nhưng là thao tác không đảo được dựa trên thoả thuận ngoài sàn).

**Kịch bản bình thường dùng cho §4:** **K1** khi đi đúng thiết kế — một khiếu nại rõ đơn, rõ lỗi, có căn cứ từ chính shop; AI chỉ cần hướng dẫn trả hàng, người mua không nên phải chờ ai duyệt.

### 2.3 Chọn 2 kịch bản đắt nhất

Nhóm chấm mỗi kịch bản theo bốn câu hỏi rồi so:

| # | Tool sai bị gọi | Ai mất gì | Đảo lại được không | Bên bị hại có tự phát hiện được không | Khả năng xảy ra |
|---|---|---|---|---|---|
| K3 | `soan_hoan_tien` | Người bán mất tiền + hàng | Khó (tiền đã về người mua) | Người bán chỉ biết khi đã bị hoàn | Cao — kiểu "số đo khác mô tả" rất phổ biến (dung tích, kích thước) |
| K4 | `tu_choi_khieu_nai` | Người mua mất tiền, giữ hàng sai | Khó (tiền đã về người bán) | **Không** — mô tả hiện tại đã bị sửa, người mua nhìn trang sản phẩm cũng thấy "đúng" | Trung bình, nhưng là gian lận có chủ đích nên sẽ lặp lại |
| K5 | `soan_hoan_tien` | Người bán | Khó | Người bán chỉ biết sau | Trung bình |
| K6 | `phat_shop` | Cả shop | Khó | Shop biết ngay nhưng thiệt hại đã xảy ra | Thấp |
| K11 | `soan_hoan_tien` | Người bán | Khó | Sau | Trung bình |
| K12 | `tu_choi_khieu_nai` | Người mua | Khó | Người mua biết khi shop không chuyển tiền | Thấp |
| K7 | (không tool, lộ dữ liệu) | Người bán/người mua | Không | Có | Trung bình |
| K1, K2, K8, K9, K10 | — / `yeu_cau_bang_chung` | Thời gian, niềm tin | Được hoặc có người gỡ | Có | Cao |

**Hai kịch bản đắt nhất nhóm chọn: K4 (mô tả bị sửa → từ chối sai) và K3 (người bán đúng → hoàn sai).**

**Vì sao đắt hơn các kịch bản còn lại:**

1. **Cả hai đều gọi tool có tác động tiền và khó đảo** (`tu_choi_khieu_nai`, `soan_hoan_tien`). Các kịch bản K1, K2, K8, K9, K10 chỉ tốn thời gian hoặc niềm tin và có người gỡ được.
2. **Hai kịch bản là hai mặt đối xứng của "sự trung lập"**: K4 làm **người mua** thua oan, K3 làm **người bán** thua oan. Thiết kế đúng cho lát cắt này phải chặn được cả hai cùng lúc — nếu chỉ chọn một phía, nhóm dễ thiết kế ra một AI "lệch" sang phía còn lại ("từ chối cho chắc" hoặc "hoàn cho xong"). Đây cũng đúng là ca demo bắt buộc của track: "người bán đúng, hoặc mô tả đã bị sửa".
3. **Lời của người dùng trong cả hai ca đều nghe hợp lý**: không có lời doạ, không có dấu hiệu bất thường để một luật đơn giản bắt được. Cái sai nằm ở **dữ liệu AI so sánh** (mô tả nào, con số nào) — tức là ở thiết kế nguồn sự thật, không phải ở câu chữ.
4. **K4 là kịch bản nhóm sợ nhất khi demo**: bên bị hại không có cách nào tự phát hiện, vì chính bằng chứng công khai (trang sản phẩm) đã bị sửa. Nếu AI so sai, không ai trong cuộc chat nhận ra.
5. **K3 có tần suất cao nhất** trong các kịch bản "không chấp nhận được": các khiếu nại về số đo (dung tích, kích thước, khối lượng) thường xuyên nhầm giữa "tổng" và "phần dùng được", và tin mồi 3 của track chính là ca này.

**Kịch bản suýt được chọn và vì sao không:**

- **K5 (doạ bóc phốt):** hậu quả giống K3 (hoàn sai cho người bán), nhưng lời doạ là **tín hiệu nhìn thấy được** trong câu chữ — một luật "lời doạ không đổi đường xử lý" chặn được. Thiết kế cho K3 (bắt buộc dựa trên mô tả lúc đặt + nghe người bán) cũng tự động chặn K5. Nhóm sẽ dùng K5 làm ca lạ để tự kiểm ở demo.
- **K6 (khoá shop):** mức thiệt hại lớn nhất (cả shop), nhưng khả năng xảy ra thấp và **dễ chặn bằng thiết kế mức tự chủ** (đặt `phat_shop` ở mức cấm, ở §4). Một khi đã cấm, kịch bản không còn đường sai, nên không cần dành phần thiết kế "khi AI sai" cho nó.
- **K12 (đóng khiếu nại theo thoả thuận ngoài sàn):** không đảo được, nhưng người mua là người chủ động yêu cầu và có màn hình cảnh báo chặn được; tần suất thấp hơn K4. Thiết kế "so với mô tả lúc đặt" của K4 cũng giúp K12 (AI biết người mua đang có căn cứ mạnh).
- **K11 (xử theo tiếng xấu):** cùng loại với K3 (người bán đúng bị hoàn), nhưng K3 có số liệu rõ hơn để thiết kế bằng chứng hiển thị trên màn hình; K11 được gộp vào cùng hướng xử lý.

---

## §3. Khi AI sai (Chặng 3)

### 1. Kịch bản K3 — Hoàn cho xong khi người bán đúng (Đơn D02: Nồi chiên 6L / rổ 5L)

- **Câu AI nói (nguyên văn):**

  > "Theo mô tả lúc bạn đặt đơn D02, nồi ghi '6 lít tổng dung tích, rổ chiên 5 lít', và tem trên thân nồi trong ảnh sản phẩm của shop ghi 6.0L. Hiện mình chưa đủ căn cứ để kết luận hàng sai mô tả. Bạn có thể gửi thêm ảnh đo thực tế, hoặc yêu cầu nhân viên Shopee xem xét."

- **Người dùng thấy gì:**
  - Nhãn độ chắc: "Chưa đủ căn cứ kết luận sai mô tả".
  - Thẻ so sánh 3 dòng: Mô tả lúc đặt: 6L tổng (rổ 5L) · Tem trên ảnh sản phẩm của shop: 6.0L · Khiếu nại: đo lòng nồi 5L.
  - Nút: `[Bổ sung ảnh đo thực tế]` · `[Yêu cầu nhân viên xem xét]`.

- **Người dùng làm gì tiếp:** Bổ sung ảnh (`yeu_cau_bang_chung`) hoặc yêu cầu nhân viên (`chuyen_truong_nhom`). Nếu không làm gì, khiếu nại giữ trạng thái chờ đối chứng, không bên nào bị trừ tiền.

- **Ai được báo, sau bao lâu:** Người bán được báo ngay qua `yeu_cau_bang_chung`, có 48 giờ để phản hồi. Trưởng nhóm nhận hồ sơ qua `chuyen_truong_nhom` ngay khi người mua yêu cầu nhân viên.

- **Quyết định:** Chọn **trả lời kèm giới hạn**.
  - Loại *hỏi lại*: hỏi mà không đưa căn cứ thì người mua không biết vì sao khiếu nại chưa được chấp nhận.
  - Loại *chuyển người ngay*: dữ liệu đã đủ để người mua tự đối chiếu; chuyển ngay làm người mua chờ và tốn nhân viên.

- **Loại lỗi:** **Lỗi ngữ cảnh** — hệ thống chạy đúng dữ liệu, nhưng người mua nhầm giữa dung tích tổng và dung tích rổ chiên.
  - Cách nói: không xin lỗi, không đổ lỗi cho khách; chỉ trình bày căn cứ trung lập.
  - Cách sửa: thẻ đối chiếu mô tả lúc đặt.

- **Nguyên tắc HAX/PAIR:**
  - **G11 (Giải thích vì sao):** thẻ so sánh 3 dòng ngay dưới tin nhắn của AI.
  - **G10 (Thu hẹp phạm vi khi nghi ngờ):** nhãn "Chưa đủ căn cứ" và nút `[Bổ sung ảnh đo thực tế]`.

---

### 2. Kịch bản K4 — So nhầm với mô tả đã bị sửa (Đơn D03: Tai nghe chống ồn ANC)

- **Câu AI nói (nguyên văn):**

  > "Mô tả hiện tại của sản phẩm trong đơn D03 ghi 'chống ồn thụ động', nhưng mô tả lúc bạn đặt hàng ngày 07/11 ghi 'chống ồn chủ động (ANC)'. Vì mô tả đã được sửa sau khi bán, mình chưa thể tự kết luận và đã chuyển hồ sơ cho nhân viên Shopee xem xét cùng cả hai phiên bản mô tả. Shop cũng được thông báo để phản hồi trong 48 giờ."

- **Người dùng thấy gì:**
  - Nhãn cảnh báo: "Mô tả bị sửa sau khi bán".
  - Huy hiệu lịch sử chỉnh sửa 2 mốc: 07/11 (lúc đặt): ANC · 10/11 (sau khi bán): Thụ động.
  - Trạng thái: `[Đang chờ nhân viên xử lý (Dự kiến phản hồi trong 24h)]`.
  - Nút: `[Hủy khiếu nại]` → hộp xác nhận "Huỷ khiếu nại thì tiền đơn D03 sẽ chuyển cho shop và không mở lại được." `[Quay lại]` `[Xác nhận huỷ]`.

- **Người dùng làm gì tiếp:** Không cần thao tác thêm; hồ sơ đã chuyển nhân viên. Nếu không làm gì, khiếu nại giữ trạng thái chờ xử lý, không bên nào bị trừ tiền.

- **Ai được báo, sau bao lâu:**
  - Trưởng nhóm/Kiểm duyệt sàn: ngay lập tức, qua `chuyen_truong_nhom`, kèm cả hai phiên bản mô tả.
  - Người bán: ngay, qua `yeu_cau_bang_chung`, có 48 giờ để phản hồi.

- **Quyết định:** Chọn **dừng và chuyển người**.
  - Loại *hỏi lại*: bằng chứng đã rõ, hỏi thêm chỉ làm người mua chờ.
  - Loại *trả lời kèm giới hạn*: ca này dẫn tới quyết định hoàn tiền hay không, AI không có quyền tự quyết (§1).

- **Loại lỗi:** **Lỗi hệ thống** — AI đối chiếu với mô tả hiện tại thay vì mô tả lúc đặt.
  - Cách nói: sàn nhận trách nhiệm; nếu đã lỡ từ chối thì xin lỗi và mở lại hồ sơ: "Shopee xin lỗi vì trước đó đã đối chiếu nhầm với mô tả đã bị sửa. Khiếu nại của bạn đã được mở lại và chuyển cho nhân viên xem xét."
  - Cách sửa: lưu snapshot mô tả lúc đặt.

- **Lỗi sửa câu chữ không cứu được:** Nếu AI so với mô tả hiện tại, câu chữ nào cũng không ngăn được việc khiếu nại bị đóng và người mua mất tiền. Thiết kế cứu: hệ thống lưu `snapshot` mô tả lúc đặt hàng, AI bắt buộc đối chiếu với bản này, và luôn chuyển người khi phát hiện mô tả bị sửa sau khi bán.

- **Nguyên tắc HAX/PAIR:**
  - **G11 (Giải thích vì sao):** huy hiệu lịch sử chỉnh sửa 2 mốc ngay dưới tin nhắn của AI.
  - **PAIR Errors + Graceful Failure:** trạng thái `[Đang chờ nhân viên xử lý]`.
  - **G8 (Gạt bỏ dễ dàng):** nút `[Hủy khiếu nại]` kèm hộp xác nhận.

## §4. Mức tự chủ  (chặng 4)
| Tên Tool | Mức độ nhóm đặt | Ai duyệt? | Người duyệt thấy gì? | Quá hạn xử lý thì sao? |
| yeu_cau_bang_chung | Tự làm | | | |
| chuyen_truong_nhom | Tự làm | | | |
| soan_hoan_tien | Cần người duyệt | Nhân viên CSKH / Trưởng nhóm | Lịch sử chat, ảnh chụp mô tả lúc đặt hàng, bằng chứng thực tế từ 2 bên | Tự động escalate lên cấp quản lý cao hơn; nếu quá SLA, hệ thống tạm hoãn đóng khiếu nại để tránh thiệt hại oan |
| tu_choi_khieu_nai | Cần người duyệt | Nhân viên kiểm duyệt | Bản chụp mô tả lúc đặt hàng (snapshot) và mô tả hiện tại, lịch sử thay đổi của shop | Chuyển trạng thái sang "Chờ phản hồi khẩn", gửi cảnh báo SLA tới quản lý ca trực |
| phat_shop | Cần Quản lý cấp cao | Quản lý vận hành sàn | Toàn bộ lịch sử vi phạm, mức độ ảnh hưởng diện rộng, doanh thu shop | Đưa vào hàng đợi ưu tiên cao nhất, tránh oan sai ảnh hưởng uy tín người bán hàng |


| Tên Tool | Sai thì ai chịu gì | Đảo lại được không | Chờ người thì mất gì |
| :--- | :--- | :--- | :--- |
| yeu_cau_bang_chung | Làm phiền người dùng (nhận thông báo thừa/không đúng trọng tâm), không gây thiệt hại vật chất hay pháp lý. | Tin đã tới, có thể gửi lại mẫu bổ sung hoặc đính chính thông tin ngay lập tức. | Thao tác tự động hóa hoàn toàn, không cần con người duyệt ở bước gửi mẫu sơ bộ. |
| chuyen_truong_nhom | Làm lệch luồng xử lý nội bộ hoặc làm phiền trưởng nhóm một hồ sơ thừa, hệ thống tự động định tuyến lại được. | Được, hồ sơ chuyển đi có thể điều hướng hoặc rút lại/chuyển tiếp cho đúng bộ phận phụ trách dễ dàng. | Quá trình điều phối nội bộ diễn ra tự động trong hệ thống phần mềm. |
| soan_hoan_tien| Nếu AI gọi nhầm: Sàn hoặc người bán mất tiền lẫn hàng oan; nhân viên/sàn chịu thiệt hại tài chính trực tiếp và khiếu nại nội bộ. | Rất khó: Tiền đã chuyển thẳng về tài khoản người mua, việc đòi lại cực kỳ phức tạp và dễ sinh tranh chấp pháp lý. | Mất thời gian: Chờ nhân viên rà soát mất thêm từ vài giờ đến 24h; người mua có thể phàn nàn vì chậm nhận tiền hoàn. |
| tu_choi_khieu_nai | Nếu AI gọi nhầm: Người mua mất trắng tiền và quyền lợi, chịu ấm ức vì mua phải hàng không đúng mô tả, sụt giảm niềm tin vào nền tảng. | Rất khó: Tiền đã giải ngân cho người bán và mã khiếu nại đã bị đóng. Muốn lật lại phải mở lại ngoại lệ, tốn nhiều thao tác vận hành thủ công. | Mất thời gian: Người mua phải chờ đợi lâu hơn để hệ thống rà soát lịch sử (snapshot) và nhân viên thẩm định kỹ lưỡng. |
| phat_shop | Nếu AI tự phạt nhầm: Shop bị ảnh hưởng nghiêm trọng về doanh thu và uy tín, có nguy cơ kiện tụng hoặc làm tổn hại thương hiệu sàn trên diện rộng. | Khó: Điểm phạt uy tín và việc khoá shop đã ghi nhận vào hệ thống ảnh hưởng trực tiếp đến hoạt động kinh doanh, việc gỡ bỏ rất phức tạp. | Mất nguồn lực: Tốn thời gian và nhân sự cấp cao để thẩm định lỗi trước khi đưa ra quyết định kỷ luật người bán hàng chính xác. |


Tool khó xếp nhất và tranh luận trong nhóm: soan_hoan_tien
Góc nhìn các thành viên:
- Đề xuất để AI tự động thực thi các case có độ tự tin cao ($>90\%$) để giảm tải $80\%$ khối lượng công việc cho nhân sự, tối ưu trải nghiệm tức thì. Giá phải trả: Chấp nhận tỷ lệ sai sót nhỏ, đổi lại tốc độ cực nhanh.
- Kiên quyết bắt buộc human-in-the-loop cho mọi hành động liên quan đến dòng tiền. Giá phải trả: Chi phí nhân sự vận hành đắt đỏ, người dùng phải chờ đợi.
Quyết định chốt của nhóm: Đưa soan_hoan_tien vào nhóm 'Cần người duyệt', nhưng tách nhỏ tool thành bước Soạn thảo dự thảo tự động (Draft) để nhân viên chỉ cần bấm duyệt thay vì phải gõ lại từ đầu, tối ưu hóa thời gian xử lý của con người.


Thử nghiệm mức tự chủ trên 2 kịch bản đắt nhất và 1 kịch bản bình thường:
| Kịch bản kiểm thử | Sai có bị chặn không? | Còn việc nào làm khi lẽ ra phải hỏi? | Người dùng bình thường có chờ vô ích không? |
| :--- | :--- | :--- | :--- |
| K3 (Nồi chiên 6L/5L) | Có. Chặn được việc AI gọi `soan_hoan_tien` nhầm cho người bán đúng. | Bắt buộc hệ thống chuyển sang yêu cầu bổ sung ảnh đo đạc thực tế/tem mác. | Có độ trễ nhất định để nhân viên rà soát, nhưng đảm bảo công bằng cho người bán. |
| K4 (Tai nghe ANC shop sửa mô tả) | Có. Chặn được việc AI gọi `tu_choi_khieu_nai` do so nhầm với mô tả hiện tại. | Kích hoạt luật đối chiếu bản chụp lúc đặt hàng (snapshot) để vạch trần hành vi shop. | Chờ nhân viên đối chiếu lịch sử để bảo vệ quyền lợi người mua. |
| K6 (Giày fake, đòi khóa shop) | Có. Chặn được việc AI tự ý gọi `phat_shop` hoặc hứa suông dựa trên lời một phía. | Vẫn làm việc hữu ích: Hướng dẫn trả hàng an toàn cho đơn và chuyển hồ sơ qua `chuyen_truong_nhom`. | Người mua vẫn nhận ngay hướng dẫn thao tác trả hàng bảo vệ thời hạn, không bị bỏ lơ. |

Tool tách bước:
Tách soan_hoan_tien thành 2 bước: 
1. chuan_bi_hoan_tien (AI phân tích, thu dữ liệu số tiền, tài khoản nhận,... ). 
2. thuc_thi_hoan_tien (Nhân viên/Quản lý kiểm tra và bấm nút thực thi). |
## §5. Prototype  (chặng 5)

## §6. Tốt là gì  (chặng 6 — nhẹ, bỏ được nếu thiếu giờ)

## §7. Demo
