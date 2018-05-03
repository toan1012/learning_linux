# Cài đặt mailx.

```
yum -y update
yum install -y mailx
```

Tạo link

`ln -s /bin/mailx /bin/email`

###Set an External SMTP Server to Relay E-Mails

```
vi /etc/mail.rc

edit

set smtp=smtps://smtp.gmail.com:465
set smtp-auth=login
set smtp-auth-user=zabbix.discovery@gmail.com
set smtp-auth-password=*****
set ssl-verify=ignore
set nss-config-dir=/etc/pki/nssdb/
```

Chú ý là gmail không cài bảo mật nhiều lớp. Nếu không sẽ thông báo tài khoản hoặc mật khẩu
không đúng.
Kết quả

```
#echo "Hello world! " | mail -v -s "Test send mail" zabbix.discovery@gmail.com
Resolving host smtp.gmail.com . . . done.
Connecting to 108.177.97.108:465 . . . connected.
Error in certificate: Peer's certificate issuer is not recognized.
Comparing DNS name: "smtp.gmail.com"
SSL parameters: cipher=AES-128-GCM, keysize=128, secretkeysize=128,
issuer=CN=Google Internet Authority G3,O=Google Trust Services,C=US
subject=CN=smtp.gmail.com,O=Google Inc,L=Mountain View,ST=California,C=US
220 smtp.gmail.com ESMTP y29sm23729583pff.42 - gsmtp
>>> EHLO zabbixSV-141.lab.domain
250-smtp.gmail.com at your service, [123.30.232.174]
250-SIZE 35882577
250-8BITMIME
250-AUTH LOGIN PLAIN XOAUTH2 PLAIN-CLIENTTOKEN OAUTHBEARER XOAUTH
250-ENHANCEDSTATUSCODES
250-PIPELINING
250-CHUNKING
250 SMTPUTF8
>>> AUTH LOGIN
334 VXNlcm5hbWU6
>>> emFiYml4LmRpc2NvdmVyeUBnbWFpbC5jb20=
334 UGFzc3dvcmQ6
>>> SFA4NDcwcEBAMjIz
235 2.7.0 Accepted
>>> MAIL FROM:<root@zabbixSV-141.lab.domain>
250 2.1.0 OK y29sm23729583pff.42 - gsmtp
>>> RCPT TO:<zabbix.discovery@gmail.com
250 2.1.5 OK y29sm23729583pff.42 - gsmtp
>>> DATA
354  Go ahead y29sm23729583pff.42 - gsmtp
>>> .
250 2.0.0 OK 1525244357 y29sm23729583pff.42 - gsmtp
>>> QUIT
221 2.0.0 closing connection y29sm23729583pff.42 - gsmtp
```

Nếu muốn gửi qua port 587 (TLS) thì 

```
set smtp=smtps://smtp.gmail.com:465 # change protocol here
set smtp-use-starttls        # add this
./zabbix-alert-smtp.sh zabbix.discovery@gmail.com "Test email 11111111111" "Hello Zabbix"
```

# Install SSMTP on Centos 7

Thêm repo nếu chưa có

`yum install epel-release -y`

Cài đặt

`yum -y install ssmtp`

Cấu hình

`alternatives --config mta`

Có 2 chương trình chạy MTA

```
Selection    Command
-----------------------------------------------
*+ 1         /usr/sbin/sendmail.postfix
2            /usr/sbin/sendmail.ssmtp
```

Chọn cái thứ 2

Disable postfix (or sendmail):

```
systemctl stop postfix
systemctl disable postfix
```

Edit SSMTP config file: (/etc/ssmtp/ssmtp.conf)

```
    root=postmaster
    mailhub=mail
    TLS_CA_File=/etc/pki/tls/certs/ca-bundle.crt
    mailhub=smtp.gmail.com:587   # SMTP server for Gmail
    Hostname=localhost
    UseTLS=YES
    UseSTARTTLS=Yes
    FromLineOverride=YES #TO CHANGE FROM EMAIL
    Root=ROOT@EMAIL # Redirect root email
    AuthUser=EMAIL@gmail.com
    AuthPass=PASSWORD
    AuthMethod=LOGIN
    RewriteDomain=gmail.com
```
 
Test:

`echo "TEST EMAIL" | mail -s "SSMTP IS WORKING" TEST@gmail.com`

## Linh tham khảo

[http://howtoconfigure.blogspot.com/2013/04/how-to-install-and-configure-ssmtp-on.html](http://howtoconfigure.blogspot.com/2013/04/how-to-install-and-configure-ssmtp-on.html)

[https://gist.github.com/ilkereroglu/aa6c868153d1c5d57cd8](https://gist.github.com/ilkereroglu/aa6c868153d1c5d57cd8)








