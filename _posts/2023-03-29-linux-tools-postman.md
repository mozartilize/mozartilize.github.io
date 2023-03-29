---
layout: post
title: "Linux tools for replacing Postman"
---

Trong những năm phát triển web, mình vẫn hay dùng đến Postman.  Nhưng có một số
điểm mình thấy không thoải mái:

- Nặng.  Sau khi cài trên máy nó ngốn đến hơn 800Mb.
- Workspace lộn xộn.  Khi làm với nhiều dự án, workspace của mình mở lên đến năm
    mươi mấy tab, và nó không auto save. Nhiều lúc close rất phiền phức.
- Không tương thích với Wayland.  Trừ khi bạn thêm flag `--ozone-platform-hint=auto`,
    như vậy mình phải mở terminal để chạy nó.
- Tốn tiền.

<!-- more -->

# Meet `curlie`

`curlie` là một shell script đơn giản wrapping `curl`, kèm theo một số thủ thuật
Linux để xử lý curl config file từ env variable.

Cấu trúc thư mục dưới đây giúp chúng ta dễ dàng quản lý các request, cũng như
đưa nó lên version control để chia sẻ với mọi người.

```
your-repository
|--httpbin
   |--.env.stg
   |--.env.local
   |--data
   |  `--user_data.json
   |--post_user.txt
   `--get_json.txt
```

- Select environment tương tự như Postman

```
httpbin$ export ENV_FILE=.env.stg
```

Ngoài ra `curlie` sẽ tự động đọc env variables từ `.env.local`. Ví dụ bạn
hay làm việc với một user nào đó, thay vì liên tục đặt `USER_GUID` ở
command, chúng ta có thể bỏ nó vào `.env.local`.

```bash
set -a; source $ENV_FILE; source .env.local; set +a;
```

Một tính năng của nó có thể kể đến việc bạn get authentication
access_token về, append vào file và sử dụng lại ở tất cả các request.

- Thực hiện request `get_json.txt`

```
curlie get_json.txt

```

`get_json.txt` là một file config của `curl`. Thay vì set env variable trên
command, chúng ta có thể lưu vào file và gọi từ đó.

```
$ cat get_json.txt
url = https://httpbin.org/json
```

Với `curl`, nó chỉ trả về response body, đôi khi chúng ta muốn xem những
headers nào từ server thì phải thêm flag `-i/--include`. Tuy nhiên việc này
khiến việc parse json data bằng `jq` bị lỗi.

Với trường hợp này, mình có một giải pháp đó là in ra stdout tất cả headers.
Sau đó output body sang một file và `jq` sẽ xử lý từ file đó.

```bash
touch /tmp/curlie_body_$input_file
moveBody=0
while IFS= read -r CMD || [[ -n "$CMD" ]]; do
    # if we pass the linebreak, redirect body to file
    if [ $moveBody -eq "1" ]; then
        printf '%s\n' "$CMD" >> /tmp/curlie_body_$input_file
    else
        # check the linebreak between headers and body
        if [[ $CMD == *[^$'\r\n']* ]]; then
            printf '%s\n' "$CMD"
        else
            moveBody=1
        fi
    fi
done < /tmp/curlie_out_$input_file
mv /tmp/curlie_body_$input_file /tmp/curlie_out_$input_file
```

- Thực hiện request `post_user.txt`

```
$ cat post_user.txt
url = https://httpbin.org/anything
data = @$DATA_FILE
header = "Authorization: Bearer $ACCESS_TOKEN"
```

```
$ cat .env.local
ACCESS_TOKEN=an-access-token
```

```
$ cat data/user_data.json
{
  "username": "foobar",
  "email": "foo.bar@example.com"
}
```

```
$ DATA_FILE=data/user_data.json curlie post_user.txt --curl-opts='-i'
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   620  100   559  100    61    415     45  0:00:01  0:00:01 --:--:--   460
HTTP/2 200
date: Wed, 29 Mar 2023 16:05:23 GMT
content-type: application/json
content-length: 559
server: gunicorn/19.9.0
access-control-allow-origin: *
access-control-allow-credentials: true
{
  "args": {},
  "data": "",
  "files": {},
  "form": {
    "{    \"username\": \"foobar\",    \"email\": \"foo.bar@example.com\"}": ""
  },
  "headers": {
    "Accept": "*/*",
    "Authorization": "Bearer an-access-token",
    "Content-Length": "61",
    "Content-Type": "application/x-www-form-urlencoded",
    "Host": "httpbin.org",
    "User-Agent": "curl/8.0.1",
    "X-Amzn-Trace-Id": "Root=1-642461c3-5c4b5ab210f16f8f1275e499"
  },
  "json": null,
  "method": "POST",
  "origin": "1.53.152.12",
  "url": "https://httpbin.org/anything"
}
```

Chính xác hơn `post_user.txt` là một file config template của curl. Việc
parse env variables được xử lý bằng:

```bash
cat $1 | envsubst | curl -K - $curlOpts -o /tmp/curlie_out_$input_file
```

`curlie` còn hỗ trợ `--jq-opts` cho phép xử lý thêm request body.

- More...

Sẽ có thêm một số usecase phức tạp hơn như tính toán hash từ post data và đẩy
lên server với một header nào đó.  Tuy nhiên tất cả đều có thể thực hiện được
với thêm một số bước đơn giản nữa.  Với Linux, không gì là không thể!

# Future enhancements

- Cho phép support các processor khác tùy vào response content type, hiện tại
    chỉ support json.

- Sử dụng lại một số config của curl thay vì đặt vào từng file như url, authorization header...

- Unique curl output file để có thể gọi 1 request nhiều lần song song.

- Xử lý `curl` `-i/--include` hiệu quả hơn.  Hiện tại dùng với `--curl-opts` `-i` phải
    tách biệt với các flag khác.
