# Stage 1: Builder
FROM eclipse-temurin:19 AS builder
WORKDIR /application

# Correction : On définit des valeurs par défaut pour éviter les variables vides
ARG ARTIFACT_NAME=spring-petclinic
ARG DOCKERIZE_VERSION=v0.6.1

# On s'assure de copier le JAR depuis le dossier target ou la racine
COPY ${ARTIFACT_NAME}.jar application.jar

RUN java -Djarmode=layertools -jar application.jar extract

# Installation de dockerize
RUN wget -O dockerize.tar.gz https://github.com/jwilder/dockerize/releases/download/${DOCKERIZE_VERSION}/dockerize-alpine-linux-amd64-${DOCKERIZE_VERSION}.tar.gz \
    && tar xzf dockerize.tar.gz \
    && chmod +x dockerize

# Stage 2: Final Image
FROM eclipse-temurin:19
WORKDIR /application

# Récupération de dockerize depuis le builder
COPY --from=builder /application/dockerize ./

# Configuration du port avec une valeur par défaut (8080 est standard pour Spring)
ARG EXPOSED_PORT=8080
EXPOSE ${EXPOSED_PORT}

# Utilisation du format recommandé pour les variables d'environnement
ENV SPRING_PROFILES_ACTIVE=docker

# Copie des couches extraites par layertools
COPY --from=builder /application/dependencies/ ./
COPY --from=builder /application/spring-boot-loader/ ./
COPY --from=builder /application/snapshot-dependencies/ ./
COPY --from=builder /application/application/ ./

ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]