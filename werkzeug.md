# Werkzeug là gì và cách dùng của werkzeug

https://github.com/williamdunbar/Dunbar-Magazine/blob/master/KeyGen%20Werkzeug%20Debugger%20Console%20Pin%20(Part%201).md

# Cơ chế bảo vệ của Werkzeug tránh khỏi ?

https://github.com/williamdunbar/Dunbar-Magazine/blob/master/KeyGen%20Werkzeug%20Debugger%20Console%20Pin%20(Part%202).md
Với chỉ mỗi pin thì có thực sự bảo vệ khỏi các kẻ tấn công được hay không? 
Ở đây hiển nhiên không bàn đến việc đã tắt debug, Nhưng trong thực tế thì có rất nhiều chương trình chạy trên môi trường production mà quên tắt tính năng debug. 
Vậy thì trường hợp đó xảy ra thì attack có thể làm gì để lấy được pin.

https://github.com/thng01/CyberResearch/blob/main/Web/Werkzeug%20PIN%20Exploit/(v2)Werkzeug%20PIN%20exploit.pdf

# Sự khác nhau của cơ chế tạo pin werkzeug giữa Flask và Django

với sự phổ biến của 2 framework này thì việc dùng thêm werkzeug là hết sức tiện lợi. 
Vậy thì cơ chế để tạo pin của Django và flask thì có khác nhau gì không? 
Ở đây mình sẽ thực hiện  so sánh  thông qua so sánh việc lấy các tham số trong quá trình cấu hình và tạo pin để mọi người có cái nhìn tổng quát hơn và dễ dàng nhận thấy là cái nào sẽ bảo mật hơn. 

| Parameters                                   | Flask                                                | Django                                               | Security noted                                               |
| -------------------------------------------- | ---------------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------ |
| username                                     | user chạy server                                     | user chạy server                                     | /proc/self/environ<br />/etc/passwd<br />/proc/self/status   |
| modname (module name)                        | "flask.app", "werkzeug.debug"                        | django.contrib.staticfiles.handlers                  | Default value                                                |
| getattr(app, "__name__", type(app).__name__) | "wsgi_app", "DebuggedApplication", "Flask"           | StaticFilesHandler                                   | Default value                                                |
| getattr(mod, '__file__', None)               | /path/to/app.py                                      | /path/to/handlers.py                                 | Available on debugger browser                                |
| str(uuid.getnode())                          | MAC address của interface host server                | MAC address của interface host server                | /proc/net/arp<br />/sys/class/net/[interface]/address        |
| get_machine_id()                             | /etc/machine-id hoặc /proc/sys/kernel/random/boot_id | /etc/machine-id hoặc /proc/sys/kernel/random/boot_id | /etc/machine-id<br />/proc/sys/kernel/random/boot_id<br />/proc/self/cgroup |
