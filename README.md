# 安装GraalVM
Java JDK选择了GraalVM，你也可以选择自己喜欢的JDK发行版。

需要先安装GraalVM：
```shell
bash <(curl -sL https://get.graalvm.org/jdk)
```
或者 [下载](https://github.com/graalvm/graalvm-ce-builds/releases) 之后 [安装](https://www.graalvm.org/latest/docs/getting-started/linux/)

设置环境变量，在/etc/profile文件新增：
```shell
export JAVA_HOME=/usr/local/graalvm/graalvm-ce-java17-22.3.2
export PATH=$JAVA_HOME/bin:$PATH

export WX_MP_APPID=xxx
export WX_MP_SECRET=xxx
```
`WX_MP_APPID`和`WX_MP_SECRET`分别是微信公众号的AppID和Secret。如果不设置，可以在Java应用启动时指定（使用`-D WX_MP_APPID=xxx`）。

启动服务：
```shell
chmod +x mvnw
nohup ./mvnw clean package spring-boot:run >> app.log 2>> app.log &

// or
./mvnw clean package
nohup java -jar target/wxshare-backend-xx.jar >> app.log 2>> app.log &
```

### 使用Docker && Native Image
安装Docker：
```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

systemctl daemon-reload
systemctl restart docker.service
```

构建镜像：
```shell
./mvnw -Pnative spring-boot:build-image
```

启动：
```shell
docker run -p 8080:8080 -e WX_MP_APPID=xxx -e WX_MP_SECRET=xxx --rm --name wxshare-backend wxshare-backend
···

