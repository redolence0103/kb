  ```yaml
  kafdrop:
    image: obsidiandynamics/kafdrop
    restart: "no"
    ports:
      - "9000:9000"
    environment:
      KAFKA_BROKERCONNECT: "kafka:29092"
      JVM_OPTS: "-Xms16M -Xmx48M -Xss180K -XX:-TieredCompilation -XX:+UseStringDeduplication -noverify"
    depends_on:
      - "kafka"
```

```yaml
  db:
    image: mariadb:10
    ports:
      - 3306:3306
    volumes:
      - ./db/conf.d:/etc/mysql/conf.d
      - ./db/data:/var/lib/mysql
      - ./db/initdb.d:/docker-entrypoint-initdb.d
    env_file: .env
    environment:
      TZ: Asia/Seoul
    depends_on:
      - kafka
```
### .env
```text
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_ROOT_PASSWORD=dbdpswls
MYSQL_DATABASE=dashboard
MYSQL_USER=inti
MYSQL_PASSWORD=inti1234
```

### my.cnf
```text
[client]
default-character-set = utf8mb4

[mysql]
default-character-set = utf8mb4

[mysqld]
character-set-client-handshake = FALSE
character-set-server           = utf8mb4
collation-server               = utf8mb4_unicode_ci
```
### application.yaml
```yaml
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://218.236.22.90:3306/dashboard?serverTimezone=UTC&characterEncoding=UTF-8
    username: root
    password: dbdpswls
  jpa:
    hibernate:
      naming:
        physical-strategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
      ddl-auto: update
    properties:
      hibernate:
        show_sql: true
        format_sql: true
        dialect: org.hibernate.dialect.MySQL57Dialect
```
### pom.xml
```xml		
    <dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>


```
### application.yaml
```yaml
  jpa:
    hibernate:
      naming:
        physical-strategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
      ddl-auto: update
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL57Dialect
  datasource:
    url: ${MYSQL_URL}
    username: ${MYSQL_USER}
    password: ${MYSQL_PASSWD}
    driverClassName: com.mysql.cj.jdbc.Driver
  cloud:
    stream:
      kafka:
        binder:
          brokers: ${KAFKA_BROKER}
```

### myenv.yaml
```yaml
apiVersion: v1
data:
  KAFKA_BROKER: "my-kafka.kafka:9092"
  MYSQL_URL: "jdbc:mysql://mysql.db:3306/dream?serverTimezone=UTC&characterEncoding=UTF-8"
  MYSQL_USER: "root"
  MYSQL_PASSWD: "kjT30U58R4"
  EXTERNAL_SERVER: "check-server:8080"
kind: ConfigMap
metadata:
  name: hanadream
```
