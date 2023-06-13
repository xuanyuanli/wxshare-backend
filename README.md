# 安装GraalVM
Java JDK选择了GraalVM。

需要先安装GraalVM：
```shell
bash <(curl -sL https://get.graalvm.org/jdk)
```
或者 [下载](https://github.com/graalvm/graalvm-ce-builds/releases) 之后 [安装](https://www.graalvm.org/latest/docs/getting-started/linux/)

设置环境变量，在/etc/profile文件新增：
```shell
export JAVA_HOME=/usr/local/graalvm/graalvm-ce-java17-22.3.2
export PATH=$JAVA_HOME/bin:$PATH
```

启动服务：
```shell
chmod +x mvnw
nohup ./mvnw clean package spring-boot:run >> app.log 2>> app.log &
```

### 使用Docker && Native Image（实验）

安装Docker：
```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

systemctl daemon-reload
systemctl restart docker.service
```

启动Docker Daemon：
```shell
mkdir -p /etc/systemd/system/docker.service.d/
vim /etc/systemd/system/docker.service.d/override.conf
```
写入：
```text
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2376
```

构建镜像：
```shell
mkdir /mvn_repo
docker run --rm -v $(pwd):/app -w /app -v /mvn_repo:/root/.m2/repository -e DOCKER_HOST=tcp://172.25.227.88:2376  ghcr.io/graalvm/jdk:22.3.2  sh -c "./mvnw spring-boot:build-image -Pnative "
```

出现以下错误：
- `org.springframework.boot.buildpack.platform.build.BuilderException: Builder lifecycle 'creator' failed with status code 51`
- `/META-INF/native-image/ returned non-zero result`

[TODO]：
- 需要去掉`wx-java-mp-spring-boot-starter`依赖之后再实验一次。


参考：
- https://docs.spring.io/spring-boot/docs/current/maven-plugin/reference/htmlsingle/
- https://graalvm.github.io/native-build-tools/latest/maven-plugin.html
- https://docs.spring.io/spring-boot/docs/current/reference/html/native-image.html#native-image.introducing-graalvm-native-images
