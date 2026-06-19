# Proy_ms-reviews

Proy_ms-reviews es un microservicio del sistema `Proy_ms` orientado al dominio de reviews o resenas. Esta construido con Kotlin y Spring Boot, y esta preparado para consumir configuracion centralizada desde Spring Cloud Config y registrarse en Eureka.

Forma parte del mismo ecosistema que `Proy_ms-config-server`, `Proy_ms-inventory` y `Proy_ms-orders`.

## Caracteristicas principales

- Microservicio Spring Boot en Kotlin.
- Configuracion externa mediante Spring Cloud Config.
- Registro como cliente Eureka.
- Perfil activo configurable por variable de entorno.
- Dockerfile para empaquetado y ejecucion en contenedor.
- Logging con Logback y rotacion de archivos.
- Prueba base de carga de contexto con Spring Boot Test.

## Stack tecnologico

- Java 11
- Kotlin 1.6.21
- Spring Boot 2.7.11
- Spring Cloud 2021.0.7
- Spring Cloud Config Client
- Spring Cloud Netflix Eureka Client
- Maven
- Docker

## Arquitectura

```text
Proy_ms-reviews
|-- pom.xml
|-- Dockerfile
|-- mvnw / mvnw.cmd
|-- src/main/kotlin/       # Aplicacion Kotlin/Spring Boot
|-- src/main/resources/    # application.yml, bootstrap.properties, logback.xml
`-- src/test/kotlin/       # Pruebas de contexto
```

## Relacion con los otros servicios

| Servicio | Rol | Relacion |
| --- | --- | --- |
| `ms-config-server` | Configuracion central | `ms-reviews` lee sus propiedades desde este servicio |
| `ms-inventory` | Inventario | Servicio del mismo ecosistema de negocio |
| `ms-orders` | Ordenes | Servicio del mismo ecosistema de negocio |
| `ms-reviews` | Reviews | Servicio de dominio para resenas |

El servicio se conecta al config server con:

```properties
spring.cloud.config.uri=${SERVER_URI:http://localhost:8888}
spring.application.name=ms-reviews
spring.profiles.active=${SPRING_PROFILE:prod}
```

## Configuracion principal

Archivo local:

```text
src/main/resources/application.yml
```

Valores actuales:

```yaml
server:
  port: 9112

spring:
  application:
    name: ms-reviews

eureka:
  client:
    registerWithEureka: true
    serviceUrl:
      defaultZone: http://localhost:8710/eureka/
```

Variables de entorno soportadas:

| Variable | Valor por defecto | Uso |
| --- | --- | --- |
| `SERVER_URI` | `http://localhost:8888` | URL del config server |
| `SPRING_PROFILE` | `prod` | Perfil activo para configuracion externa |

## Requisitos previos

- Java 11
- Maven 3.8+ o wrapper incluido (`mvnw` / `mvnw.cmd`)
- `ms-config-server` disponible en `http://localhost:8888`
- Eureka Server disponible en `http://localhost:8710/eureka/` si se usara service discovery
- Docker opcional

## Ejecucion local

```bash
./mvnw spring-boot:run
```

En Windows:

```bash
mvnw.cmd spring-boot:run
```

El servicio queda disponible por defecto en:

```text
http://localhost:9112
```

## Build

```bash
./mvnw clean package
```

En Windows:

```bash
mvnw.cmd clean package
```

## Docker

Construir imagen:

```bash
docker build -t ms-reviews:0.0.1 .
```

## Pruebas

```bash
./mvnw test
```

En Windows:

```bash
mvnw.cmd test
```

## Orden sugerido de arranque

1. Eureka Server.
2. `Proy_ms-config-server`.
3. `Proy_ms-reviews`.
4. Resto de servicios de dominio.

