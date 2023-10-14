
# lời dẫn cho Werkzeug

# Werkzeug là gì và cách dùng của werkzeug
https://github.com/ThanhTuan1695/boot2root3/blob/minh/Minh's%20Report/KeyGen%20Werkzeug%20Debugger%20Console%20Pin%20%2B%20LFI%20(Part%201%20-%20Rewrite).md

# Cơ chế bảo vệ của Werkzeug?

https://github.com/ThanhTuan1695/boot2root3/blob/minh/Minh's%20Report/KeyGen%20Werkzeug%20Debugger%20Console%20Pin%20(Part%202).md#1-werkzeug-debugger-ta%CC%A3o-ra-ma%CC%83-pin-nh%C6%B0-th%C3%AA%CC%81-na%CC%80o-

Với chỉ mỗi pin thì có thực sự bảo vệ khỏi các kẻ tấn công được hay không? 
Ở đây mình sẽ hiển nhiên không bàn đến việc đã tắt debug, Nhưng trong thực tế thì có rất nhiều chương trình chạy trên môi trường production mà quên tắt tính năng debug. 
Vậy thì trường hợp đó xảy ra thì attack có thể làm gì để lấy được pin.

file:///C:/Users/Bounty%20Startup/Downloads/v2Werkzeug_PIN_exploit.pdf

# Sự khác nhau của cơ chế tạo pin werkzeug giữa Flask và Django

với sự phổ biến của 2 framework này thì việc dùng thêm werkzeug là hết sức tiện lợi. 
Vậy thì cơ chế để tạo pin của Django và flask thì có khác nhau gì không? 
Ở đây mình sẽ thực hiện  so sánh  thông qua so sánh việc lấy các tham số trong quá trình cấu hình và tạo pin để mọi người có cái nhìn tổng quát hơn và dễ dàng nhận thấy là cái nào sẽ bảo mật hơn. 

| Parameters  | Flask  |  Django |  Security noted |
|---|---|---|---|
|  username |   |   |   |
|  modname (module name) |   |   |   |
| getattr(app, '__name__', getattr (app .__ class__, '__name__'))  |   |   |   |
| getattr(mod, '__file__', None)  |   |   |   |
| str(uuid.getnode())  |   |   |   |
| get_machine_id()  |   |   |   |
| getattr(app, '__name__', getattr (app .__ class__, '__name__'))  |   |   |   |
