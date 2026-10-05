# Bai 3: Cau hinh tuong lua UFW va chuan doan cong mang

## 1. Thong tin
- Repo: `session06-homework-ex3` | Duong dan: `homework/session_06/ex3/README.md`
- Boi canh: deploy web nghe 8080 tren VPS, chi mo 22 + 8080
- Moi truong: Ubuntu 22.04, UFW 0.36

## 2. Cac lenh da thuc hien

```bash
sudo ufw --version
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow 8080/tcp
# Tuong duong day du:
# sudo ufw allow 22/tcp comment 'SSH admin'
# sudo ufw allow 8080/tcp comment 'Web app'

sudo ufw enable
# Goi y: Command may disrupt existing ssh connections. Proceed with operation (y|n)? -> go y
sudo ufw status verbose
ss -tlnp
# Neu chua co app 8080 de test:
# python3 -m http.server 8080 &
# ss -tlnp | grep 8080
# curl -I http://localhost:8080
```

## 3. Bang chung ket qua

```bash
$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
8080/tcp                   ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
8080/tcp (v6)              ALLOW IN    Anywhere (v6)

$ ss -tlnp
State  Recv-Q Send-Q Local Address:Port  Peer Address:Port Process
LISTEN 0      4096   0.0.0.0:22         0.0.0.0:*          users:(("sshd",pid=720,fd=3))
LISTEN 0      4096   0.0.0.0:8080       0.0.0.0:*          users:(("python3",pid=1234,fd=3))
LISTEN 0      4096   [::]:22           [::]:*             users:(("sshd",pid=720,fd=4))
LISTEN 0      4096   [::]:8080         [::]:*             users:(("python3",pid=1234,fd=4))

$ curl -I http://localhost:8080
HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.10.12
```

> Dat: `Status: active`, co 22 ALLOW IN va 8080/tcp ALLOW IN tu Anywhere.

## 4. Giai thich
- `default deny incoming`: zero-trust, dong het truoc roi mo co chon loc. tranh quen dong port nguy hiem.
- `default allow outgoing`: cho phep apt update, curl ra ngoai. Neu set deny outgoing se gay loi kho debug.
- Thu tu quan trong: phai `allow 22` truoc khi `enable`, neu khong se bi khoa SSH khoi VPS.
- `ss -tlnp`: `-t` tcp, `-l` listening, `-n` numeric, `-p` process. Thay the netstat da deprecated.
- Khoi phuc khi sai: dung console cua nha cung cap VPS (DigitalOcean/AWS console) de `ufw disable` hoac `ufw reset`.

## 5. Rollback / mo rong
```bash
sudo ufw delete allow 8080/tcp
sudo ufw deny 8080/tcp
sudo ufw reload
sudo ufw disable
```
