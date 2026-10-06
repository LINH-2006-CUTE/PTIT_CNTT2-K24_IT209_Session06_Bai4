 # Session 06 - Exercise 4

## Quản lý tiến trình nền với nohup và tín hiệu Kill

## 1. Mục tiêu

* Chạy một tiến trình nền độc lập với terminal bằng `nohup` và `&`.
* Theo dõi tiến trình bằng các lệnh `ps`, `pgrep`.
* Kiểm tra dữ liệu được ghi vào file log.
* Dừng tiến trình bằng tín hiệu `SIGTERM` với lệnh `kill -15`.

---

## 2. Tạo file loop-monitor.sh

File `loop-monitor.sh` có nội dung:

```bash
#!/bin/bash

while true; do
    echo "System time: $(date)" >> /tmp/monitor.log
    sleep 5
done
```

Kiểm tra nội dung file:

```bash
cat loop-monitor.sh
```

---

## 3. Cấp quyền thực thi

Sử dụng lệnh:

```bash
chmod +x loop-monitor.sh
```

Kiểm tra quyền:

```bash
ls -l loop-monitor.sh
```

Kết quả:

```text
-rwxrwxrwx 1 nlinhh nlinhh 97 Oct 6 23:44 loop-monitor.sh
```

---

## 4. Chạy tiến trình nền với nohup

Xóa file log cũ trước khi chạy:

```bash
rm -f /tmp/monitor.log
```

Chạy script ở chế độ nền:

```bash
nohup ./loop-monitor.sh > /dev/null 2>&1 &
```

Kết quả:

```text
[1] 558
```

Trong đó `558` là PID của tiến trình.

### Giải thích

* `nohup`: giúp tiến trình tiếp tục chạy độc lập với terminal.
* `&`: đưa tiến trình chạy ở background.
* `> /dev/null`: bỏ qua standard output.
* `2>&1`: chuyển standard error vào cùng nơi với standard output.

---

## 5. Kiểm tra tiến trình

Sử dụng:

```bash
pgrep -af loop-monitor.sh
```

Kết quả:

```text
558 /bin/bash ./loop-monitor.sh
```

Có thể kiểm tra thêm bằng:

```bash
ps aux | grep loop-monitor.sh
```

Kết quả:

```text
nlinhh  558  0.0  0.0  4940  3456 pts/0  S  23:44  0:00 /bin/bash ./loop-monitor.sh
nlinhh  599  0.0  0.0  4256  2304 pts/0  S+ 23:45  0:00 grep --color=auto loop-monitor.sh
```

Dòng có `/bin/bash ./loop-monitor.sh` là tiến trình đang chạy.

Dòng `grep loop-monitor.sh` chỉ là tiến trình tìm kiếm và không phải tiến trình của script.

---

## 6. Kiểm tra file log

Sử dụng:

```bash
tail -n 10 /tmp/monitor.log
```

Kết quả:

```text
System time: Tue Oct  6 23:44:48 +07 2026
System time: Tue Oct  6 23:44:53 +07 2026
System time: Tue Oct  6 23:44:58 +07 2026
System time: Tue Oct  6 23:45:03 +07 2026
System time: Tue Oct  6 23:45:08 +07 2026
System time: Tue Oct  6 23:45:13 +07 2026
System time: Tue Oct  6 23:45:18 +07 2026
System time: Tue Oct  6 23:45:23 +07 2026
System time: Tue Oct  6 23:45:28 +07 2026
```

Các dòng log được ghi cách nhau khoảng 5 giây, đúng với lệnh `sleep 5` trong script.

Có thể theo dõi log trực tiếp bằng:

```bash
tail -f /tmp/monitor.log
```

Nhấn `Ctrl + C` để thoát chế độ theo dõi.

---

## 7. Dừng tiến trình bằng SIGTERM

Tiến trình có PID là `558`.

Sử dụng:

```bash
kill -15 558
```

Kết quả:

```text
[1]+  Terminated                 nohup ./loop-monitor.sh > /dev/null 2>&1
```

Điều này cho biết tiến trình nền đã được dừng thành công bằng tín hiệu `SIGTERM`.

Không cần sử dụng `kill -9` vì tiến trình đã xử lý và kết thúc bình thường với `SIGTERM`.

---

## 8. Kiểm tra tiến trình sau khi dừng

Kiểm tra bằng:

```bash
pgrep -af loop-monitor.sh
```

Kết quả: không có output.

Kiểm tra thêm:

```bash
ps aux | grep loop-monitor.sh
```

Kết quả:

```text
nlinhh  691  0.0  0.0  4256  2304 pts/0  S+  23:47  0:00 grep --color=auto loop-monitor.sh
```

Chỉ còn tiến trình `grep`, không còn tiến trình:

```text
/bin/bash ./loop-monitor.sh
```

=> Tiến trình `loop-monitor.sh` đã được dừng thành công.

---

## 9. Kiểm tra log sau khi dừng

Sử dụng:

```bash
tail -n 10 /tmp/monitor.log
```

Kết quả cuối cùng:

```text
System time: Tue Oct  6 23:45:58 +07 2026
System time: Tue Oct  6 23:46:03 +07 2026
System time: Tue Oct  6 23:46:08 +07 2026
System time: Tue Oct  6 23:46:13 +07 2026
System time: Tue Oct  6 23:46:18 +07 2026
System time: Tue Oct  6 23:46:23 +07 2026
System time: Tue Oct  6 23:46:28 +07 2026
System time: Tue Oct  6 23:46:33 +07 2026
System time: Tue Oct  6 23:46:38 +07 2026
System time: Tue Oct  6 23:46:43 +07 2026
```

File log vẫn tồn tại sau khi tiến trình bị dừng và chứa các thời điểm cuối cùng mà script hoạt động.

---

## 10. Kết luận

Exercise 4 đã hoàn thành các yêu cầu:

* Tạo script `loop-monitor.sh`.
* Cấp quyền thực thi cho script.
* Chạy script dưới dạng background process bằng `nohup` và `&`.
* Xác định PID của tiến trình là `558`.
* Kiểm tra tiến trình bằng `pgrep` và `ps`.
* Kiểm tra log bằng `tail`.
* Theo dõi log bằng `tail -f`.
* Dừng tiến trình bằng `kill -15 558`.
* Xác nhận tiến trình đã kết thúc thành công.

### Files trong Exercise 4

```text
ex4/
├── README.md
└── loop-monitor.sh
```

