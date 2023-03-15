# Spring Boot com Localstack

## Passo a passo para criar e configurar o projeto

- Gerar projeto no Spring Initialzr:
    - Nome: spring-boot-localstack
- Incluir dependência do Spring Cloud AWS:
```
<dependency>
    <groupId>io.awspring.cloud</groupId>
    <artifactId>spring-cloud-aws-dependencies</artifactId>
    <version>${spring.cloud-aws}</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```
- Incluir versão do Spring Cloud AWS no properties:
```
<spring.cloud-aws>2.4.2</spring.cloud-aws>
```
- Incluir dependência do Spring Cloud AWS Starter:
```
<dependency>
    <groupId>io.awspring.cloud</groupId>
    <artifactId>spring-cloud-starter-aws</artifactId>
</dependency>
```        
- Incluir dependência do Spring Cloud Starter Bootstrap:
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```
- Incluir dependência do Parameter Store:
```
<dependency>
    <groupId>io.awspring.cloud</groupId>
    <artifactId>spring-cloud-starter-aws-parameter-store-config</artifactId>
</dependency>
```
- Incluir bootstrap.yml:
```
spring:
  application:
    name: spring-boot-localstack
---
spring:
  config:
    activate:
      on-profile: localstack
aws:
  paramstore:
    enabled: true
    endpoint: http://localhost:4566
```
- Atualizar application.properties:
```
### Server
server.port=8080

### Log
logging.file.path=@logs.path@
logging.path=@logs.path@

### Proxy
proxy.host=
proxy.port=
proxy.enabled=

### Spring
server.error.whitelabel.enabled=false
spring.mvc.throw-exception-if-no-handler-found=true
spring.web.resources.add-mappings=false
management.endpoints.web.exposure.include=health, info

### Info
info.app.name=@artifactId@
info.app.version=@version@
info.app.description=@name@
info.app.contact=thomasdacosta@gmail.com
info.app.ip=${spring.cloud.client.ip-address}
info.app.profile=${spring.profiles.active}

### AWS
s3.bucket=awslocalstack
```
- Adicionar na inicialização o profile do localstack
- Incluir docker-compose.yml do LocalStack na aplicação:
```
version: "3.8"

services:
  localstack:
    container_name: "${LOCALSTACK_DOCKER_NAME-localstack_main}"
    image: localstack/localstack
    ports:
      - "127.0.0.1:4566:4566"            # LocalStack Gateway
      - "127.0.0.1:4510-4559:4510-4559"  # external services port range
    environment:
      - DEBUG=${DEBUG-}
      - LAMBDA_EXECUTOR=${LAMBDA_EXECUTOR-}
      - DOCKER_HOST=unix:///var/run/docker.sock
    volumes:
      - "${LOCALSTACK_VOLUME_DIR:-./volume}:/var/lib/localstack"
      - "/var/run/docker.sock:/var/run/docker.sock"
```
- Inicializar LocalStack
- Incluir diretório de log no pom.xml:
```
<logs.path>./logs</logs.path>
```
- Criar uma configuração no Parameter Store:
```
@echo off
aws --endpoint http://localhost:4566 --profile localstack ssm put-parameter --name "/config/spring-boot-localstack_localstack/helloWorld" --value "Hello World Parameter Store" --type String
```
- Executar aplicação
- Criar classe para obter configuração do Parameter Store
- Criar classe de Controller
- Executar aplicação
- Executar no Insomnia
