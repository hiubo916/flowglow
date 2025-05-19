# 構建階段
FROM maven:3.8.1-jdk-8-slim as build

# 指定构建过程中的工作目录
WORKDIR /app
# 拷貝代碼文件到工作目錄中
COPY src ./src
# 将pom.xml文件，拷贝到工作目录下
COPY settings.xml pom.xml ./

# 执行代码编译命令
RUN mvn -s /app/settings.xml -f /app/pom.xml clean package


# 运行时阶段
FROM alpine:3.13

# 安裝依賴包
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.tencent.com/g' /etc/apk/repositories \
    && apk add --update --no-cache openjdk8-jre-base \
    && rm -f /var/cache/apk/*

# 修改時區: 容器默认时区为UTC, 國內設置到上海的
RUN apk add tzdata && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo Asia/Shanghai > /etc/timezone

# 使用 HTTPS 协议访问容器云调用证书安装
RUN apk add ca-certificates

# 指定运行时的工作目录
WORKDIR /app

# 将构建产物jar包拷贝到运行时目录中
COPY --from=build /app/target/*.jar .

# 暴露端口, 所有端口必須一致
EXPOSE 80

# 执行启动命令, 只有最后一行CMD命令会被执行
CMD ["java", "-jar", "/app/flow-backend-0.0.1.jar"]