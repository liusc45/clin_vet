# Etapa de construcción
FROM amazoncorretto:17.0.13-al2023-headless AS builder
WORKDIR /app
COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .
COPY src src

# Agregar permisos de ejecución al mvnw
RUN chmod +x mvnw
RUN ./mvnw dependency:go-offline
RUN ./mvnw clean package -DskipTests

# Etapa de ejecución
FROM amazoncorretto:17.0.13-al2023-headless
WORKDIR /app

# Instalar las herramientas necesarias para gestión de usuarios
RUN yum install -y shadow-utils && \
    yum clean all && \
    groupadd -r javauser && \
    useradd -r -g javauser -s /bin/false javauser && \
    mkdir -p /app/logs && \
    chown -R javauser:javauser /app

# Copiar el jar desde la etapa de construcción
COPY --from=builder /app/target/clinica_veterinaria-0.0.1.jar app_veterinaria.jar
RUN chown javauser:javauser app_veterinaria.jar

USER javauser

# Configurar variables de entorno para la JVM
ENV JAVA_OPTS="-XX:+UseG1GC -XX:+UseContainerSupport -XX:MaxRAMPercentage=75 -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/app/logs/heapdump.hprof"
ENV SPRING_PROFILES_ACTIVE="prod"

EXPOSE 8080

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app_veterinaria.jar"]