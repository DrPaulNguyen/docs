# Tạo WLF 

Do đặc thù riêng về việc các chuyến bay WLF chưa chắc chắc sẽ bay, nên cần quản lý chặt chẽ trạng thái này. Vì vậy, bổ sung thêm field riêng để quản lý: **Flight Status - FS**
|Trạng thái - Status|Diễn giải|
|-------------------|---------|
|NEW|Mới khởi tạo|
|PUBLISHED|Đã cho phép bán|
|CONFIRMED|Đã có xác nhận chuyến bay được cấp phép|
|CANCEL|Đã huỷ (vì bất kỳ lý do nào)|
|COMPLETED|Hoàn tất thành công|

> Lưu ý cho nhà phát triển: **FS** được ghi nhận trong tất cả các Data Entities (SeriesBlock, SeriesSeat, SeriesBooking), tuy nhiên việc thay đổi các giá trị này không nên tuỳ tiện mà cần thông qua các Action (function) chuyên biệt để đảm bảo tính toàn vẹn của dữ liệu

# Fare Rules (FR)

Sau khi xem xét một vài phương án, thống nhất quản lý chính sách bán hàng thông qua Fare-rule (FR). FR cũng là một cách thức quản lý chính sách bán hàng khá phổ biến trong hàng không & ngành du lịch khách sạn nói chung (hospitality).

### Single seat - Single Fare {#singlefare}

Mỗi seat khi bán ra sẽ được tương ứng **FR** duy nhất. Trên cùng một chuyến bay, có thể bán nhiều seat với các Fare (hạng ghế khác nhau). Để tối ưu doanh thu, khi mở bán, số lượng chỗ được phân bổ theo các Fare khác nhau, nhưng vẫn tuân thủ nguyên tắc *single seat - single fare*.

### Time Limit (TL) Types {#tl}
*Trong hầu hết các trường hợp, 1 rule có thể kết hợp nhiều kiểu TL với nhau, khi đó nguyên tắc TL nào đến sớm nhất sẽ được áp dụng.*
|Kiểu (Type) | Diễn giải|
|-----------|------------|
|Fixed time| Một giá trị thời gian cụ thể. Sau mốc thời gian này, nghiệp vụ sẽ không còn hiệu lực|
|Duration from Booking (Reservation)| Khoảng thời gian tính từ thời điểm Booking (Reservation) được khởi tạo (tính theo phút/minutes)|
|Duration to Departure|Khoảng thời gian trước thời điểm khởi hành (tính theo phút/minutes)|
## Payment Types (PT) {#pt}
*Trong hầu hết các trường hợp, 1 rule có thể kết hợp nhiều kiểu thanh toán với nhau, khi đó nguyên tắc thanh toán nào cao hơn sẽ được áp dụng, ngoại trừ các kiểu thanh toán đặc biệt (ví dụ: kiểu cộng dồn, kiểu trừ dần)*
|Kiểu (Type) | Diễn giải|
|-----------|------------|
|Fixed per Booking (Reservation) - **FDB**|Thanh toán một giá trị tiền cụ thể theo từng booking, không phụ thuộc vào số lượng khách (seat) trong booking|
|Fixed per Pax (Adult, CHD, INF, Couple, Triple, Room-Night) - **FDP**|Thanh toán một giá trị tiền cụ thể theo từng khách. Phạm vi áp dụng điều kiện có thể được chỉ định khi khai báo số tiền (_ví dụ: **500ADT;200CHD**_)|
|Percent per Booking (Reservation) - **PCB**|Thanh toán một giá trị tiền theo tỷ lệ phần trăm dựa theo giá bán cơ sở của Booking (_**Lưu ý: để làm được điều này, giá bán cơ sở của Booking cần phải được quản lý cụ thể, tránh sai sót**_).|
|Percent per Pax (Adult, CHD, INF, Couple, Triple, Room-Night)-  **PCP**|Thanh toán một giá trị tiền theo tỷ lệ phần trăm dựa theo giá cơ sở của từng khách (_**Lưu ý: để làm được điều này, giá bán cơ sở theo từng khách cần phải được quản lý cụ thể, tránh sai sót**_). Phạm vi áp dụng điều kiện có thể được chỉ định khi khai báo số tiền (_ví dụ: **10ADT;5CHD**_)|
|Max per Booking (Reservation) - **MXB**|Tương tự **FDB**, nhưng thay nguyên tắc lựa chọn giá trị cao nhất khi dùng kết hợp với các kiểu, MXB lại giới hạn trần của số tiền thanh toán|
|Max per Pax (Adult, CHD, INF, Couple, Triple, Room-Night) - **MXP**|Tương tự **FDP** & **MXB**|
|Adding per Booking (Reservation) - **ADB**|Tương tự **FDB**, nhưng thay nguyên tắc lựa chọn giá trị cao nhất khi dùng kết hợp với các kiểu, ADB sẽ được cộng thêm vào số tiền thanh toán|
|Adding per Pax (Adult, CHD, INF, Couple, Triple, Room-Night) - **ADP**|Tương tự **FDP** & **ADB**|
## Reservation Amount - RSA
Trong một số trường hợp, khách hàng cần phải trả một số tiền để được phép khởi tạo một Booking (Reservation - Đặt chỗ) trên hệ thống (hay còn gọi là đặt cọc). Điều này cũng là hợp lý để tránh tình trạng giữ chỗ khống. Trong trường hợp này **RSA** được thiết lập để tự động trừ số tiền tương ứng từ tài khoản của khách hàng.
>**DEFAULT**: 0 VND
>**Values**: kết hợp các giá trị của [**PT**](#pt)

## Fee for Cancel Reservation by Customer - FCRA
Trong một số trường hợp, khách hàng cần phải trả một số tiền để được huỷ một Booking chưa xuất vé. Điều này cũng là hợp lý để tránh tình trạng giữ chỗ khống. Trong trường hợp này **FCRA** được thiết lập để tự động trừ số tiền tương ứng từ tài khoản của khách hàng.
>**DEFAULT**: 0 VND
>**Values**: kết hợp các giá trị của [**PT**](#pt)
## TL for Cancel Reservation by Customer - TCRA
Trong một số trường hợp, khách hàng bị giới hạn về thời gian để được phép huỷ chỗ chưa xuất vé. Sau mốc thời gian này, khoản tiền đã chi trả khi đặt chỗ sẽ không được hoàn lại. Điều này cũng là hợp lý để tránh tình trạng giữ chỗ khống. Trong trường hợp này **TCRA** được thiết lập để tự động trừ số tiền tương ứng từ tài khoản của khách hàng.
>**DEFAULT**: trùng thời gian khởi hành
>**Values**: kết hợp các giá trị của [**TL**](#tl)
## Fee for Cancel Reservation by Carrier - FCRC
Trong một số trường hợp, khi nhà vận chuyển (Carrier) huỷ đặt chỗ, carrier được phép thu một khoản phí. Trong trường hợp này **FCRC** được thiết lập để tự động trừ số tiền tương ứng từ tài khoản của khách hàng.
>**DEFAULT**: 0 VND.
>**Values**: kết hợp các giá trị của [**PT**](#pt).  _Nếu giá trị được thiết lập số âm (< 0), khách hàng sẽ được đền bù giá trị tương ứng_
## TL for Issueing Ticket
Mốc thời gian tối đa khách hàng được phép trì hoãn việc xuất vé (hay còn gọi là Vào tên). Sau mốc thời gian này, Booking (đặt chỗ) sẽ bị huỷ, toàn bộ chỗ sẽ bị huỷ và trả lại trên hệ thống với trạng thái **Sẵn sàng để bán**.
>_Lưu ý về hạng vé: Để đơn giản cho quá trình vận hành, chỗ được trả về với hạng vé gốc trước khi được giữ chỗ_
>_Lưu ý về số tiền thanh toán: Để đơn giản cho quá trình vận hành, khách hàng bắt buộc phải thanh toán **toàn bộ số tiền còn thiếu** của đặt chỗ (bằng số tiền giá vé trừ đi khoản tiền đặt cọc - **RSA**)_
## TL for Pax Name Changing
Trong một số trường hợp, khách hàng bị giới hạn về thời gian để được phép thay đổi tên trong Booking.
>**DEFAULT**: trùng thời gian khởi hành
>**Values**: kết hợp các giá trị của [**TL**](#tl)
## Fee for Pax Changing
Trong một số trường hợp, khách hàng cần trả một khoản phí cho việc thay đổi tên hành khách.
>**DEFAULT**: 0 VND.
>**Values**: kết hợp các giá trị của [**PT**](#pt).
