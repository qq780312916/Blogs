---
title: OpenSSL
tags:
  - OpenSSL
  - 证书
categories: 安全
cover: img/fengmian/openssl.jpg
abbrlink: 2e203ce7
date: 2022-08-29 05:50:11
---
# 概念

## 加密算法

- 对称加密(使用相同的密码进行加密解密)

对称加密是指双方对约定一个相同的密码，通过这个密码对文件或信息进行加密解密

常见加密算法：DES、3DES、IDEA等

- 非对称加密(使用不相同的密码进行加密解密)

非对称加密是通过生成密钥对(公钥/私钥)，通过私钥对文件进行加密，可以使用公钥进行解密；通过使用公钥进行加密，只能使用私钥进行解密。一般私钥是私有的，公钥是公开的，一旦私钥泄露对安全的影响极大

常见加密算法：RSA、ECC等

## 摘要算法

> MD5是摘要算法,不是加密算法

消息摘要算法是指对信息、文件等数据进行计算，得到一定长度的摘要；比如我在网上下载了某个文件，我想确认文件是不是完整的，或者有没有被人修改的，只需要将文件下载到本地使用支持的相关算法进行摘要运算得出摘要信息，与发布者公示的摘要信息进行比较确认是否一致，如果一致则文件没有被修改也未损坏，如果不一样说明文件并不是原来的文件

消息摘要算法有如下几类：

- 消息摘要

  生成的消息摘要都是128位的包括：MD5、MD4、MD2，MD5相对4、2安全性最高，至今未被破解；通过MD5算法是不可逆的，无法通过加密后的信息破解出原始数据。但是MD5不足之处就是可以通过穷举进行推举出明文，因为相同的信息用MD5进行摘要运算后的信息都是一样的。在使用MD5摘要算法对密码进行加密时，应使用复杂密码

- 安全散列

  SHA1、SHA256、SHA384、SHA512

## 数字签名

数字签名的三个特征：

- 不可否认
- 报文的完整性
- 报文鉴别

数字签名是指对文件进行摘要运算得出摘要信息，通过使用私钥对摘要进行加密的过程称之为数字签名的过程，而通过加密摘要信息称之为该文件的数字签名

## 证书颁发机构

- CA机构

  CA机构全称为Certificate Authority证书认证中心，CA是否则签发证书、认证证书、管理已颁发的证书的机关。先前说的私钥公钥，每个人都可以去生成一对密钥对，而我们怎么确定这个密钥对一定是某某的呢？这时候我们就需要把我们的公钥以及相关信息发送给权威的CA机构，通过CA机构来鉴别

  而CA机构的证书在安装好操作系统时就已经被内置在系统中

  ![image-20220829020633073](openssl.png)

# 生成密钥对

- 生成密钥对(公私钥)

  ```shell
  [root@xiaowangc media]# openssl genrsa -out ca-key.pem 2048	  # 生成2048位的私钥
  Generating RSA private key, 2048 bit long modulus (2 primes)
  ......................................+++++
  ....................................................................................................................+++++
  e is 65537 (0x010001)
  [root@xiaowangc media]# cat ca-key.pem
  -----BEGIN RSA PRIVATE KEY-----
  MIIEpAIBAAKCAQEA2CjcI+aU1cwHczvbMJglT/QstPJzKwwRqftmUTm4UjWJCBap
  MSjWS+9EfkoqqkwUodjy6d1KyOqlXKnsa6JuinkbL0S2iFnh0RN19EIoqne+a1Y5
  3KhrV0J765bl71ugdU87G5WxgnXrQeZcHINeEUkkMTfAL//FWnZlZdJ2JMUrc6Og
  CX+NzX4ITLNZagILWIC3IofGkxLGLRestN4Lveh4S7bLZHQekqsxLgRG0tWIGa3b
  V50gFbRdeFWYP82Z59AK6t62/fcSefSqwCRBnHOkTKkGom13umV/vIkW8rdexVZE
  tCYvZmbN4qNJ5GGSjiBWHj8qtDqnw8Bjz3zF4QIDAQABAoIBAF68kbb+UQ7ezAka
  G7fRhtDi8FEhzY35TSiVsUM6K+mD4xnzbJXKExnWtMsw0EAw9f31KomK3kLubCkP
  pDmMSCxSZbKyx9k8o3bRs6mo8U+9CWzbrqJiAiGNVuhrCz17h/jCD+LIGbNW4RPR
  1V79yFWFG+KiT4356FH8f/Y/Zl44ad6OVVGr66tgpAv2fOUNiInOcKpi+AmSl4lj
  3Us/Sc+ZqgaLkdWFWvQM+RtjFvDt9E7GHrHieh+CnTmKj9CRzHBDTut/36WdTqlS
  JnyBQsTUD4T2yTgTlr0ewhRcITXrUY2od0cm+dpr33UCE8nDMMGAWvGCL6ZD0CHr
  w4Rk9hECgYEA6/yGJBrt8TTB9RTQTaflD1YB6nF1NMwJ1M2SQz5w6uhOnfvigpIj
  lBAp3JfpgnYcjxUoXPanrSk04UwtM5llmDMj8wg66XtSmm8iT0uL4PbrZUxONjKo
  4BYud0rIWFMrZilc0EIm0nywsfK3L+lhtM+/+1uVCGgDfW8YIgT+wg0CgYEA6n3g
  zGwxAPN/wAD4i2/hc3tQ+Z9LgAcFy7MAxEzOv62Gcbge3NohHwTPTm7wuQr53TZg
  GcNV+T0UiOhKEjcSccqiq8IHsfl+dCaWJQdPSnG5XrHd2zRSYuzihVQLgiHWSkJG
  06U8BdmXN/7Yp99TX60O2ADNivSrr8rHRzw5IiUCgYEArbTvRMpx1bhhATd18YOh
  z70eoeUsQlXi8rrza/4dfjzMCeysmjJacBXJyrAj2b15XjVTxcJmQMdxPlold7L1
  nqgeUToAq3b0oesmVTol183KDoGxnKGDv5d0UqlAeguWiZfu0vmuvAe+xO4FvAXN
  vxuhlLOgK1TtJLrPB9Ond00CgYEA4xTn389eXVdxfZTzHMVKBTWEo1g6G0+xsyQ0
  N+VRypnWusXdTW8H6CwWPhR9lhUlB66ivhBGb8lQ24xoPt+KQxxDECYkoZvFc+Hy
  QQWlKaicJTIGcUNoDVjtvMQ5KNpv1RX91PQM/nVLVfS8B0XkTaEf4NpWMpzirqim
  9ztA8OkCgYBr/MLkp/6EvoeUhH1SwCal2DThXhbIuTOoVjl1lQhLqdyJlKnKPAN2
  mTcwzk779JQhIvbFS8iZ4iYu6mBJGStnMzl+ZHWhRgHohT4M960ToikTsNOwbMon
  CCg+8ntFycNc0ml0BBQ5B37AN72iX0Fwair93PdDyH46PRIuiIZ8zg==
  -----END RSA PRIVATE KEY-----
  [root@xiaowangc media]#
  ```

- 通过私钥提取出公钥

  ```shell
  [root@xiaowangc media]# openssl rsa -in ca-key.pem -pubout -out ca-pub.pem
  writing RSA key
  [root@xiaowangc media]# ls
  ca-key.pem  ca-pub.pem
  [root@xiaowangc media]# cat ca-pub.pem
  -----BEGIN PUBLIC KEY-----
  MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2CjcI+aU1cwHczvbMJgl
  T/QstPJzKwwRqftmUTm4UjWJCBapMSjWS+9EfkoqqkwUodjy6d1KyOqlXKnsa6Ju
  inkbL0S2iFnh0RN19EIoqne+a1Y53KhrV0J765bl71ugdU87G5WxgnXrQeZcHINe
  EUkkMTfAL//FWnZlZdJ2JMUrc6OgCX+NzX4ITLNZagILWIC3IofGkxLGLRestN4L
  veh4S7bLZHQekqsxLgRG0tWIGa3bV50gFbRdeFWYP82Z59AK6t62/fcSefSqwCRB
  nHOkTKkGom13umV/vIkW8rdexVZEtCYvZmbN4qNJ5GGSjiBWHj8qtDqnw8Bjz3zF
  4QIDAQAB
  -----END PUBLIC KEY-----
  ```

# 生成摘要

## 对字符串生成摘要

- MD5

  ```shell
  [root@xiaowangc media]# echo 123456 | openssl md5
  (stdin)= f447b20a7fcbf53a5d5be013ea0b15af
  [root@xiaowangc media]#
  ```

- Sha

  ```shell
  [root@xiaowangc media]# echo 123456 | openssl sha1
  (stdin)= c4f9375f9834b4e7f0a528cc65c055702bf5f24a
  [root@xiaowangc media]# echo 123456 | openssl sha256
  (stdin)= e150a1ec81e8e93e1eae2c3a77e66ec6dbd6a3b460f89c1d08aecf422ee401a0
  [root@xiaowangc media]#
  ```

## 对文件生成摘要

- MD5

  > 更改文件名MD5值不变，当内容发生改变后MD5值才会产生变化

  ```shell
  [root@xiaowangc media]# echo xiaowangc > 1.txt
  [root@xiaowangc media]# openssl dgst -md5 1.txt
  MD5(1.txt)= 117d8e3d36b77fd390ee6f0304257ec1
  ```

  **验证更改文件数据后的MD5值**

  ```shell
  root@xiaowangc media]# echo 12345678 > 1.txt
  [root@xiaowangc media]# openssl dgst -md5 1.txt
  MD5(1.txt)= 23cdc18507b52418db7740cbb5543e54
  ```

- Sha256

  ```shell
  [root@xiaowangc media]# openssl dgst -sha256 1.txt
  SHA256(1.txt)= e7e213f38f47bbaec6aef3307831c1ce60a932e2c380a19e00d191927122a9f4
  ```

# 生成数字签名

通过上面的命令已经了解到了如何生成密钥对以及提取摘要信息，下面我们开始试着对文件进行数字签名

```shell
# 对文件进行摘要运算
[root@xiaowangc media]# ls
ca-key.pem  ca-pub.pem
[root@xiaowangc media]# echo xiaowangc.com > 1.txt
[root@xiaowangc media]# openssl dgst -md5 1.txt
MD5(1.txt)= b02eb36de574cd721a3dda12d1693ff3
[root@xiaowangc media]# openssl dgst -sha256 1.txt
SHA256(1.txt)= a84c0b84e7e54dcb092f289b174b506580e675682b0c79756d90947e07fe4fea
[root@xiaowangc media]# openssl dgst -sha512 1.txt
SHA512(1.txt)= cb7e7a147ed14a0479a792fb4d6f3c83d63da9627d8e5badfb2f98f4e180bec1813f211d639357af3044757a3946af35827be29bb0c2320e7cdfcf8a9de5eb9e
# 使用私钥对文件进行签名
# 通过-hex在命令行显示数字签名信息
[root@xiaowangc media]# openssl dgst -md5 -hex -sign ca-key.pem 1.txt
RSA-MD5(1.txt)= 00f943f79704d775c6b4112a1ebf7ff1c6cc314bf809f4482de2f2f159c83b297291c4159d307358d3bdb11214be8b5c6d1f2ec0c983910de0a1c2ba617de52eb609b55c140c34ef3cc5b65d29f7734e57dce29032e49ebf333d86ee2314aba65901a977e1426e17b7eb919cd9c74b87e4da025fa1428f494695c08349185a693b80a010596ce4985422766d1c38ed7328b35dbd4aa471e1b08292833038f2b2df30a8c89667c5036bf608fbb1b8f1eb2d16cd8c81ee583d2c2b3c23da744786f105643abd131a706a761f7ada75e4546c1349666d0fed88f86a529d04e83e98ddeb031500a0d8a2c678cbd56aa8d51d25a643a282bd876a39066f55a1fc9db4
# 通过将数字签名保存到文件中，保存不能使用-hex参数
[root@xiaowangc media]# openssl dgst -md5 -out 1.txt.sign -sign ca-key.pem 1.txt
# 因为是二进制所以显示乱码
[root@xiaowangc media]# cat 1.txt.sign
ºa}.*\4<Ŷ])sNW2䞿3=#YwBn둜K_BIF��IZi;YlT"vm8s(]Jqᰂ080Ȗg-͌X=,+
[root@xiaowangc media]#
#####################################
#   使用公钥来对文件进行验证数字签名
#####################################
[root@xiaowangc media]# ls
1.txt  1.txt.sign  ca-key.pem  ca-pub.pem
[root@xiaowangc media]# openssl dgst -md5 -verify ca-pub.pem -signature 1.txt.sign 1.txt
Verified OK 

# 生成数字签名的时候使用的是MD5算法，所有验证的时候也要指定相同算法
```

# 生成证书

```shell
# 生成私钥
[root@xiaowangc media]# openssl genrsa -out ca-key.pem
Generating RSA private key, 2048 bit long modulus (2 primes)
..............................................................+++++
............................+++++
e is 65537 (0x010001)
# 提取公钥
[root@xiaowangc media]# openssl rsa -in ca-key.pem -pubout -out ca-pub.pem
writing RSA key
# 生成证书请求文件
[root@xiaowangc media]# openssl rsa -in ca-key.pem -pubout -out ca-pub.pem
writing RSA key
[root@xiaowangc media]# ls
ca-key.pem  ca-pub.pem
[root@xiaowangc media]# openssl req -new -key ca-key.pem -out ca.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN					# 国家
State or Province Name (full name) []:GD				# 省
Locality Name (eg, city) [Default City]:SZ				# 市
Organization Name (eg, company) [Default Company Ltd]:XX		# 公司/单位
Organizational Unit Name (eg, section) []:System				# 组织
Common Name (eg, your name or your server's hostname) []:ca			# 主机名
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:						# 密码
An optional company name []:	
[root@xiaowangc media]# openssl req -in ca.csr -text				# 使用文本方式查看证书请求文件
Certificate Request:
    Data:
        Version: 1 (0x0)
        Subject: C = CN, ST = GD, L = SZ, O = XX, CN = ca
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:e7:69:75:37:6f:c9:3d:56:cc:3e:83:8e:5d:86:
                    3d:54:4c:fa:95:d1:66:42:60:96:4f:1a:45:20:0d:
                    7a:bb:69:08:41:c6:41:48:7d:f7:e4:30:54:4f:48:
                    88:fc:d2:3f:bb:a3:34:3a:74:a3:ed:83:f3:30:aa:
                    01:a8:d5:ee:ad:37:65:ad:e8:dd:a0:ef:a4:7f:4f:
                    3d:12:aa:df:7c:5c:df:f5:e6:d7:56:65:7c:db:fe:
                    57:e2:6a:77:31:72:18:40:12:ad:24:fe:ed:d3:8a:
                    75:ed:23:7c:97:41:40:27:ea:29:f8:27:4a:30:2e:
                    3b:18:a8:c0:52:5a:23:5b:0f:1e:2d:54:c0:ab:fd:
                    b5:42:85:9e:6a:78:fd:9d:cd:b3:f7:0c:d2:cb:f1:
                    d8:28:ac:55:aa:93:a4:5e:6e:fd:0c:53:64:b6:17:
                    2a:1b:32:b9:f9:03:62:27:8b:ce:59:5d:05:63:39:
                    f4:48:3b:81:a0:1f:0f:2d:d9:a9:54:65:2e:6b:b0:
                    34:0a:b4:22:d1:76:27:82:11:11:a4:a0:4c:1a:9a:
                    b7:57:bd:76:b9:e4:34:51:c7:2f:82:f8:14:23:82:
                    6e:c7:63:18:37:3e:b9:83:a1:84:1e:7c:f9:d8:8d:
                    ab:48:1a:96:5a:db:99:ef:5f:fb:05:46:e7:32:53:
                    15:3d
                Exponent: 65537 (0x10001)
        Attributes:
            a0:00
    Signature Algorithm: sha256WithRSAEncryption
         de:f0:6d:f8:5f:0f:59:3c:5a:b5:f4:9a:07:c3:15:d6:a7:dd:
         18:d9:87:b6:7d:83:99:6e:b3:82:b6:1f:2d:93:c0:85:fc:a8:
         71:17:0f:11:be:34:d5:32:44:3a:a9:a3:05:75:98:25:7a:a6:
         0c:90:4b:2c:99:fa:7c:42:70:c9:84:6a:a3:c0:6f:c8:c3:87:
         d1:4b:7f:b4:1f:6c:d4:ff:0b:13:6d:21:57:57:8e:ce:b9:b0:
         96:8a:28:47:84:77:6b:2b:9b:e6:50:fb:14:8c:26:ac:35:46:
         b1:6f:38:aa:c8:7b:ee:f8:05:9f:81:c4:ca:91:91:d9:54:ce:
         ef:fa:dd:ff:8e:a9:50:e0:3c:db:8c:2f:25:19:1f:7e:e7:a2:
         08:78:02:1a:02:b3:2b:d0:63:6c:c7:97:64:c8:4c:29:0f:f6:
         33:67:c1:2b:51:46:c8:5e:1c:cb:ca:32:bc:05:93:39:0a:0d:
         25:97:4d:98:e5:f4:b5:37:1d:30:e3:d5:6e:ba:a0:c1:37:9b:
         7b:65:2d:43:82:22:63:73:e0:17:df:b7:75:63:bc:40:5e:c4:
         34:df:08:6e:0d:04:ab:f4:36:e4:52:6f:cf:7c:5a:31:4b:fc:
         eb:97:30:e8:8c:be:b4:67:b2:1f:66:aa:46:19:af:93:f9:3a:
         7e:f6:ca:dd
-----BEGIN CERTIFICATE REQUEST-----
MIIChjCCAW4CAQAwQTELMAkGA1UEBhMCQ04xCzAJBgNVBAgMAkdEMQswCQYDVQQH
DAJTWjELMAkGA1UECgwCWFgxCzAJBgNVBAMMAmNhMIIBIjANBgkqhkiG9w0BAQEF
AAOCAQ8AMIIBCgKCAQEA52l1N2/JPVbMPoOOXYY9VEz6ldFmQmCWTxpFIA16u2kI
QcZBSH335DBUT0iI/NI/u6M0OnSj7YPzMKoBqNXurTdlrejdoO+kf089EqrffFzf
9ebXVmV82/5X4mp3MXIYQBKtJP7t04p17SN8l0FAJ+op+CdKMC47GKjAUlojWw8e
LVTAq/21QoWeanj9nc2z9wzSy/HYKKxVqpOkXm79DFNkthcqGzK5+QNiJ4vOWV0F
Yzn0SDuBoB8PLdmpVGUua7A0CrQi0XYnghERpKBMGpq3V712ueQ0UccvgvgUI4Ju
x2MYNz65g6GEHnz52I2rSBqWWtuZ71/7BUbnMlMVPQIDAQABoAAwDQYJKoZIhvcN
AQELBQADggEBAN7wbfhfD1k8WrX0mgfDFdan3RjZh7Z9g5lus4K2Hy2TwIX8qHEX
DxG+NNUyRDqpowV1mCV6pgyQSyyZ+nxCcMmEaqPAb8jDh9FLf7QfbNT/CxNtIVdX
js65sJaKKEeEd2srm+ZQ+xSMJqw1RrFvOKrIe+74BZ+BxMqRkdlUzu/63f+OqVDg
PNuMLyUZH37nogh4AhoCsyvQY2zHl2TITCkP9jNnwStRRsheHMvKMrwFkzkKDSWX
TZjl9LU3HTDj1W66oME3m3tlLUOCImNz4Bfft3VjvEBexDTfCG4NBKv0NuRSb898
WjFL/OuXMOiMvrRnsh9mqkYZr5P5On72yt0=
-----END CERTIFICATE REQUEST-----
# 签发证书
[root@xiaowangc media]# openssl x509 req -key ca-key.pem -in ca.csr -out ca.crt -days 36500
# 查看证书
[root@xiaowangc media]# openssl x509 -in ca.crt -noout -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            26:9f:17:62:72:7d:f1:88:68:a7:b7:49:ad:c8:df:9a:fb:78:03:cc
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C = CN, ST = GD, L = SZ, O = XX, CN = ca
        Validity
            Not Before: Aug 28 21:39:13 2022 GMT
            Not After : Aug  4 21:39:13 2122 GMT
        Subject: C = CN, ST = GD, L = SZ, O = XX, CN = ca
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:e7:69:75:37:6f:c9:3d:56:cc:3e:83:8e:5d:86:
                    3d:54:4c:fa:95:d1:66:42:60:96:4f:1a:45:20:0d:
                    7a:bb:69:08:41:c6:41:48:7d:f7:e4:30:54:4f:48:
                    88:fc:d2:3f:bb:a3:34:3a:74:a3:ed:83:f3:30:aa:
                    01:a8:d5:ee:ad:37:65:ad:e8:dd:a0:ef:a4:7f:4f:
                    3d:12:aa:df:7c:5c:df:f5:e6:d7:56:65:7c:db:fe:
                    57:e2:6a:77:31:72:18:40:12:ad:24:fe:ed:d3:8a:
                    75:ed:23:7c:97:41:40:27:ea:29:f8:27:4a:30:2e:
                    3b:18:a8:c0:52:5a:23:5b:0f:1e:2d:54:c0:ab:fd:
                    b5:42:85:9e:6a:78:fd:9d:cd:b3:f7:0c:d2:cb:f1:
                    d8:28:ac:55:aa:93:a4:5e:6e:fd:0c:53:64:b6:17:
                    2a:1b:32:b9:f9:03:62:27:8b:ce:59:5d:05:63:39:
                    f4:48:3b:81:a0:1f:0f:2d:d9:a9:54:65:2e:6b:b0:
                    34:0a:b4:22:d1:76:27:82:11:11:a4:a0:4c:1a:9a:
                    b7:57:bd:76:b9:e4:34:51:c7:2f:82:f8:14:23:82:
                    6e:c7:63:18:37:3e:b9:83:a1:84:1e:7c:f9:d8:8d:
                    ab:48:1a:96:5a:db:99:ef:5f:fb:05:46:e7:32:53:
                    15:3d
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                52:A5:C9:FF:70:11:AD:37:A5:1C:4F:4E:83:35:80:B7:A1:93:09:C7
            X509v3 Authority Key Identifier:
                keyid:52:A5:C9:FF:70:11:AD:37:A5:1C:4F:4E:83:35:80:B7:A1:93:09:C7

            X509v3 Basic Constraints: critical
                CA:TRUE
    Signature Algorithm: sha256WithRSAEncryption
         2d:b6:74:58:33:4a:5d:5a:dd:46:d3:84:12:21:80:b9:68:48:
         fb:3b:30:9a:00:a6:7b:9d:bb:05:3b:f3:5d:a7:c0:33:da:7e:
         5c:7d:5c:c1:25:85:1c:87:48:7b:6f:1f:ac:49:0e:71:67:0e:
         25:50:cc:52:09:7a:b6:86:07:50:9c:32:e8:cc:a8:6b:ad:07:
         46:59:03:c2:d2:69:5e:b2:c4:74:7a:c1:da:1b:3a:cc:21:dd:
         fd:43:96:08:a6:33:2e:98:f4:a6:c3:e1:e5:f1:1d:53:03:37:
         64:5d:4f:7d:40:db:c8:e3:1a:91:e1:59:51:e9:ca:b4:b4:a1:
         6e:7a:bd:2e:29:57:19:1b:e2:ff:32:d9:e4:72:8d:9d:80:79:
         9f:1e:b7:4b:5e:4b:0f:f3:50:b7:3d:ce:f1:c4:a9:06:af:d7:
         15:d9:5f:2a:c7:bd:27:d4:60:2e:7b:27:68:1a:a3:fd:15:46:
         15:42:fe:a7:af:a5:cf:91:f9:e5:9c:55:21:54:db:c7:12:2a:
         9f:0d:b0:6b:15:b7:74:cf:aa:34:a4:16:6c:1b:c0:b9:eb:ac:
         96:25:68:be:c3:f7:99:2a:b3:b0:e5:7a:11:45:65:bc:cf:b2:
         81:33:1a:5a:41:42:27:48:71:46:53:0b:f1:23:67:bb:ff:38:
         eb:9d:fd:96
```

**签发他人证书**

```shell
[root@xiaowangc media]# openssl x509 -req -in a.csr -CA ca.crt -CAkey ca.key -out host01.crt -CAcreateserial
```

