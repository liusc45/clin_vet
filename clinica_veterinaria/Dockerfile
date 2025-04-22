# Etapa de construcción
FROM csanchez/maven:3.9-azulzulu-17-debian AS builder
WORKDIR /app
COPY pom.xml .
COPY src src

# Construir el proyecto
RUN mvn clean package -DskipTests

# Etapa de ejecución
FROM amazoncorretto:17-alpine3.19-jdk
WORKDIR /app

# Crear usuario no root y directorios necesarios
RUN addgroup -S javauser && \
    adduser -S -D -h /app -s /sbin/nologin -G javauser javauser && \
    mkdir -p /app/logs && \
    chown -R javauser:javauser /app

# Copiar el jar desde la etapa de construcción
COPY --from=builder /app/target/*.jar app_veterinaria.jar
RUN chown javauser:javauser app_veterinaria.jar

USER javauser

# Configurar variables de entorno para la JVM
ENV JAVA_OPTS="-XX:+UseG1GC -XX:+UseContainerSupport -XX:MaxRAMPercentage=75 -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/app/logs/heapdump.hprof"
ENV SPRING_PROFILES_ACTIVE="prod"

# Declarar volumen para logs
VOLUME /app/logs

# Exponer el puerto del servicio
EXPOSE 8080

# Comando de inicio
ENTRYPOINT ["java", "-XX:+UseG1GC", "-XX:+UseContainerSupport", "-XX:MaxRAMPercentage=75", "-XX:+HeapDumpOnOutOfMemoryError", "-XX:HeapDumpPath=/app/logs/heapdump.hprof", "-jar", "app_veterinaria.jar"]
