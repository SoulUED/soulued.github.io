最近所负责的网站需要上HTTPS，需要在开发环境部署证书，一般CA的认证的证书都非常昂贵。如果只是自己开发使用，那么我们自己生成一个自己的证书以供开发时使用，对外业务仍需要使用大型CA机构认证的证书，这样才不会被浏览器厂商将浏览器标记为不安全

### HTTPS与OpenSSL

这里使用了OpenSSL库，OpenSSL是一个强大的加密库，可以帮助我们生成证书。当然你也可以使用其它工具生成证书，只要符合HTTPS所规定的TSL/SSL协议即可。

###  生成证书和私钥

```shell
openssl genrsa -des3 -out test.key 1024
```

```shell
openssl req -new -subj "/CN=test" -key test.key -out test.csr
```

```shell
//移除第一步中所设置的密码，避免每次重启服务器的时候输入密码
mv test.key test.origin.key
openssl rsa -in test.origin.key -out test.key
```

```shell
openssl x509 -req -days 3650 -in test.csr -signkey test.key -out test.crt
```

以上步骤完成，会生成

​	私钥: test.key

​	证书: test.crt

### 配置nginx

```nginx
server {
	listen 443 ssl;
	ssl_certificate     test.crt;
 	ssl_certificate_key test.key;
}
```









