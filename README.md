### docker部署spring boot
- 使用idea，创建spring boot项目
- 在pom.xml中，添加属性
    ```xml
    <docker.image.prefix>springboot</docker.image.prefix>
    ```
  并且，添加插件
  ```xml
  <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <configuration>
          <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
          <dockerDirectory>src/main/docker</dockerDirectory>
          <resources>
              <rescource>
                  <targetPath>/</targetPath>
                  <directory>${project.build.directory}</directory>
                  <include>${project.build.finalName}.jar</include>
              </rescource>
          </resources>
      </configuration>
  </plugin>
  ```
- 创建src/main/docker路径，并创建DOCKERFILE文件
  ```xml
    FROM openjdk:8-jdk-alpine
    VOLUME /tmp
    ADD springboot_docker-0.0.1-SNAPSHOT.jar app.jar
    ENTRYPOINT ["java","-jar","/app.jar"]
  ```
- 打包
    ```xml
    mvn package docker:build
    ```
build成功后，查看镜像
    ```xml
    docker images
    ```
- 运行镜像
    ```xml
    docker run -p 8080:8080 -t springboot/spring-boot-docker
    ```
- 上传镜像
    - 在hub.docker创建一个REPOSITORY
    - 由于有可能本地镜像和我们在线创建REPOSITORY名不一致，所以将本地镜像重新标记一下
        ```xml
        docker tag springboot/springboot_docker xxx/spring-boot-docker
        ```
    - 本地登录docker
        ```xml
        docker login
        ```
    - push 镜像
        ```xml
       docker push xxx/spring-boot-docker
        ```
    
- **以上操作均需有java、maven、docker环境正确配置**