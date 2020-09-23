# 使用jdk自带的 keytools 创建SSL证书，参考[博客](https://blog.csdn.net/tomatocc/article/details/80733491)

### 生成私钥和证书
```
keytool -genkeypair -alias serverkey ^
    -keypass 123456 -storepass 123456 ^
    -dname "C=CN,ST=GD,L=SZ,O=vihoo,OU=dev,CN=vihoo.com" ^
    -keyalg RSA -keysize 2048 -validity 3650 -keystore server.keystore
```

参数说明
* genkeypair 生成密钥对
* alias 实体别名 (包括证书私钥)
* dname 证书个人信息
* keyalg 采用公钥算法，默认是DSA
* keysize 密钥长度 (DSA算法对应的默认算法是sha1withDSA，不支持2048长度，此时需指定RSA)
* validity 有效期 (单位: 天)
* keystore 指定密钥库的名称



### 查看keystore
```
keytool -v -list -keystore server.keystore
```


### 证书导出
```
keytool -exportcert -keystore server.keystore -file server.cer -alias serverkey
```


### 将.cer格式的证书转换为p12证书
```
keytool -importkeystore -srckeystore server.keystore -destkeystore server.p12 ^
    -srcalias serverkey -destalias serverkey ^
    -srcstoretype jks -deststoretype pkcs12 -noprompt
```