# Web Enumeration
## Tóm tắt Công cụ Kiểm tra Web

| Công cụ | Mục đích | Cờ/Tùy chọn Quan trọng | Ví dụ Sử dụng |
|---|---|---|---|
| [**Gobuster**](#gobuster) | Tìm kiếm thư mục và tệp ẩn trên máy chủ web | `-u` (URL), `-w` (wordlist), `-t` (số luồng), `-x` (phần mở rộng tệp) | `gobuster dir -u http://example.com -w common.txt` |
| [**WPScan**](#wpscan) | Quét và kiểm tra các lỗ hổng trong trang web WordPress | `--url`, `--enumerate` (p: plugin, t: theme, u: user), `--passwords`, `--usernames` | `wpscan --url http://wordpress.com --enumerate u` |
| [**Nikto**](#nikto) | Quét máy chủ web để tìm các lỗ hổng và cấu hình sai | `-h` (host), `-p` (port), `-Tuning` (tùy chỉnh loại lỗ hổng), `-Plugins` | `nikto -h example.com -Tuning 2` (tìm kiếm tệp cấu hình sai) |

## Manual Enumeration
Các trình duyệt hiện đại bao gồm Chrome và Firefox có một bộ công cụ nằm trong "Công cụ dành cho nhà phát triển/Bảng điều khiển". Chúng ta sẽ thảo luận về Firefox, tuy nhiên, Chrome có một bộ công cụ rất tương tự. Bộ công cụ này bao gồm một loạt các công cụ bao gồm:

- Xem mã nguồn trang
- Tìm kiếm tài sản
- Gỡ lỗi và thực thi mã như javascript ở phía máy khách (Trình duyệt của chúng ta)

Sử dụng "F12" trên bàn phím của chúng ta là một phím tắt để khởi chạy bộ công cụ này.

## Gobuster
Như tên gọi của nó, Gobuster được viết bằng [Go](https://golang.org/). Go là một ngôn ngữ mã nguồn mở, cấp thấp (giống như C hoặc Rust) được phát triển bởi một nhóm tại Google và các cộng tác viên khác. Nếu bạn muốn tìm hiểu thêm về Go, hãy truy cập trang web được liên kết ở trên.

### **Các Cờ Toàn cục Hữu ích**

Ngoài ra còn có một số cờ Toàn cục hữu ích có thể được sử dụng. Tôi đã đưa chúng vào bảng dưới đây. Bạn cũng có thể xem lại chúng trong tài liệu chính - tại đây.

| **Cờ** | **Cờ Dài** | **Mô tả** |
| --- | --- | --- |
| -t | --threads | Số lượng luồng đồng thời (mặc định là 10) |
| -v | --verbose | Đầu ra chi tiết |
| -z | --no-progress | Không hiển thị tiến trình |
| -q | --quiet | Không in banner và các thông báo khác |
| -o | --output | Tệp đầu ra để ghi kết quả |

### **Chế độ "dir"**
Dirbuster có một chế độ "dir" cho phép người dùng liệt kê các thư mục của trang web.
```jsx
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
### **Các Cờ Hữu ích Khác**

Các cờ này hữu ích trong một số tình huống nhất định. Lưu ý rằng đây không phải là tất cả các tùy chọn cờ, mà là một số tùy chọn phổ biến hơn mà bạn sẽ sử dụng trong các bài kiểm tra thâm nhập và trong các sự kiện capture the flag. Nếu bạn muốn có danh sách đầy đủ, bạn có thể xem nó ở đây.

| **Cờ** | **Cờ Dài** | **Mô tả** |
| --- | --- | --- |
| -c | --cookies | Cookies để sử dụng cho các yêu cầu |
| -x | --extensions | Phần mở rộng tệp để tìm kiếm |
| -H | --headers | Chỉ định các tiêu đề HTTP, -H 'Header1: val1' -H 'Header2: val2' |
| -k | --no-tls-validation | Bỏ qua xác minh chứng chỉ TLS |
| -n | --no-status | Không in mã trạng thái |
| -P | --password | Mật khẩu cho Basic Auth |
| -s | --status-codes | Mã trạng thái dương |
| -b | --status-codes-blacklist | Mã trạng thái âm |
| -U | --username | Tên người dùng cho Basic Auth |

### Quy trình dùng gobuster
1. Chạy gobuster xem còn các thư mục khác trên máy chủ hay không
    
    ```jsx
    gobuster dir -u http://webenum.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 60
    ```
2. Chạy gobuster xem extensions đang tồn tại trong thư mục Changes
    
    ```jsx
    gobuster dir -u http://webenum.thm/Changes -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 60 -x php,txt,html,json,bak,old,zip,rar,tar,7z
    ```
3. Tìm flag trong thư mục Changes bằng gobuster và curl
    
    ```jsx
    gobuster dir -u http://webenum.thm/VIDEO -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100 -x html,php,txt,js 
    ```

4. Tìm các máy ảo khác trong máy chủ này bằng gobuster vhost
    
    ```jsx
    gobuster vhost -u  http://webenum.thm -w dlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
    ```

5. Tìm cờ trong các máy ảo này
    
    ```jsx
    gobuster dir -u  http://products.webenum.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt,html,json,bak,old,zip,rar,tar,7z -t 60
    ```

## WPScan
WPScan có khả năng liệt kê và nghiên cứu một vài loại lỗ hổng bảo mật hiện diện trong các trang web WordPress

**Tóm tắt - Bảng cheat sheet**

| Cờ | Mô tả | Ví dụ đầy đủ |
| --- | --- | --- |
| p | Liệt kê Plugin | --enumerate p |
| t | Liệt kê Giao diện | --enumerate t |
| u | Liệt kê Tên người dùng | --enumerate u |
| -v | Sử dụng WPVulnDB để đối chiếu các lỗ hổng. Ví dụ lệnh tìm kiếm các plugin dễ bị tấn công (p) | --enumerate vp |
| aggressive | Đây là một hồ sơ mức độ tấn công để WPScan sử dụng. | --plugins-detection aggressive |


**Cách dùng**

1. Liệt kê trang web, tên chủ đề phát hiện là gì

```jsx
wpscan --url [http://wpscan.thm](http://wpscan.thm/) --enumerate t
```

2. Liệt kê trang web, tên plugin mà WPScan tìm thấy

```jsx
wpscan --url [http://wpscan.thm](http://wpscan.thm/) --enumerate p
```

3. Liệt kê trang web, tên người dùng mà WPScan tìm thấy

```jsx
wpscan --url [http://wpscan.thm](http://wpscan.thm/) --enumerate u
```

4. Brute-force username và password

```jsx
wpscan –-url [http://wpscan.thm](http://wpscan.thm/) –-passwords /usr/share/wordlists/rockyou.txt –-usernames phreakazoid
```

## Nikto
Nikto có khả năng thực hiện đánh giá trên tất cả các loại máy chủ web (và không dành riêng cho ứng dụng cụ thể như WPScan.). 

**Basic Scanning:**
`nikto -h vulnerable_ip:port`

**Quét nhiều cổng:**`nikto -h 10.10.10.1 -p 80,8000,8080`

**plugin** 

Các plugin mở rộng thêm khả năng của Nikto. 

Một số plugin thú vị bao gồm:
| Tên plugin | Mô tả |
|---|---|
| apacheusers | Cố gắng liệt kê người dùng xác thực HTTP Apache |
| cgi | Tìm kiếm các tập lệnh CGI mà chúng ta có thể khai thác |
| robots | Phân tích tệp robots.txt quy định những tệp/thư mục mà chúng ta có thể điều hướng đến |
| dir_traversal | Cố gắng sử dụng một cuộc tấn công duyệt thư mục (tức là LFI) để tìm kiếm các tệp hệ thống như /etc/passwd trên Linux (http://ip_address/application.php?view=../../../../../../../etc/passwd) |

Ví dụ: để sử dụng plugin "apacheuser", quá trình quét Nikto của chúng ta sẽ trông như sau: `nikto -h 10.10.10.1 -Plugin apacheuser`

**Tăng tính chi tiết cho quá trình quét của chúng ta**
Chúng ta có thể tăng tính chi tiết của quá trình quét Nikto bằng cách cung cấp các đối số sau với cờ `-Display`. 

| Đối số | Mô tả | Lý do sử dụng |
|---|---|---|
| 1 | Hiển thị bất kỳ chuyển hướng nào được đưa ra bởi máy chủ web. | Máy chủ web có thể muốn chuyển chúng tôi đến một tệp hoặc thư mục cụ thể, vì vậy chúng tôi sẽ cần điều chỉnh quá trình quét của mình cho phù hợp với điều này. |
| 2 | Hiển thị bất kỳ cookie nào nhận được | Các ứng dụng thường sử dụng cookie như một phương tiện lưu trữ dữ liệu. Ví dụ: máy chủ web sử dụng phiên, trong đó các trang web thương mại điện tử có thể lưu trữ sản phẩm trong giỏ hàng của bạn dưới dạng các cookie này. Thông tin đăng nhập cũng có thể được lưu trữ trong cookie. |
| E | Xuất bất kỳ lỗi nào | Điều này sẽ hữu ích để gỡ lỗi nếu quá trình quét của bạn không trả về kết quả mà bạn mong đợi! |

**Điều chỉnh quá trình quét của bạn để tìm kiếm lỗ hổng**
Nikto có một số danh mục lỗ hổng mà chúng tôi có thể chỉ định quá trình quét của mình để liệt kê và kiểm tra. Chúng ta có thể sử dụng cờ `-Tuning` và cung cấp một giá trị trong quá trình quét Nikto

|Tên danh mục | Mô tả | Tùy chọn điều chỉnh |
---|---|---|
|Tải lên tệp | Tìm kiếm bất cứ thứ gì trên máy chủ web có thể cho phép chúng tôi tải lên một tệp. Điều này có thể được sử dụng để tải lên một reverse shell để một ứng dụng thực thi. | 0
|Cấu hình sai / Tệp mặc định | Tìm kiếm các tệp phổ biến nhạy cảm (và không nên truy cập được chẳng hạn như tệp cấu hình) trên máy chủ web. | 2
|Tiết lộ thông tin | Thu thập thông tin về máy chủ web hoặc ứng dụng (ví dụ: số phiên bản, tiêu đề HTTP hoặc bất kỳ thông tin nào có thể hữu ích để tận dụng trong cuộc tấn công của chúng tôi sau này) | 3
|injection | Tìm kiếm các vị trí có thể xảy ra trong đó chúng tôi có thể thực hiện một số loại tấn công tiêm như XSS hoặc HTML | 4
|excute | Tìm kiếm bất cứ thứ gì cho phép chúng tôi thực thi các lệnh OS (chẳng hạn như để tạo shell) | 8
| SQL injection | Tìm kiếm các ứng dụng có các tham số URL dễ bị tấn công SQL Injection | 9