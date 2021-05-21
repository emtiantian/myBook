# oepnresty安装

## 安装步骤

```shell
brew tap openresty/brew
brew install openresty
```

## 遇到错误

```shell
Failed to connect to raw.githubusercontent.com port 443: Connection refused Error: Failed to download resource "openresty-openssl--patch" Download failed: https://raw.githubusercontent.com/openresty/openresty/master/patches/openssl-1.1.0d-sess\_set\_get\_cb\_yield.patch
```

- 解决：

- ```shell
  # 修改host 在本地hosts文件加上：199.232.4.133 raw.githubusercontent.com
  sudo vim /etc/hosts
  # 添加 199.232.4.133 raw.githubusercontent.com
  ```
