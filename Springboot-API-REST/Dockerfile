# ─── Stage 1: Build con Maven ─────────────────────────────────────────────────
FROM maven:3.9-eclipse-temurin-17 AS builder

WORKDIR /app

# Copia pom.xml primero para aprovechar caché de dependencias de Docker.
# Si el pom no cambia, Maven no vuelve a descargar dependencias en el siguiente build.
COPY pom.xml .
RUN mvn dependency:go-offline -B

COPY src ./src
RUN mvn package -DskipTests -B

# ─── Stage 2: Runtime mínimo ──────────────────────────────────────────────────
# Se usa solo el JRE (no el JDK completo) para reducir el tamaño de la imagen final.
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

# Usuario no-root por seguridad — evita que el proceso tenga permisos de root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

COPY --from=builder /app/target/Springboot-API-REST-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8081

ENTRYPOINT ["java", "-jar", "app.jar"]