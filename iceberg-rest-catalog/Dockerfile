FROM azul/zulu-openjdk:17 as builder

COPY . /app/
WORKDIR /app/

RUN ["./gradlew", "build", "shadowJar"]

FROM azul/zulu-openjdk:17-jre-headless

RUN \
    set -xeu && \
    groupadd iceberg --gid 1000 && \
    useradd iceberg --uid 1000 --gid 1000 --create-home

COPY --from=builder --chown=iceberg:iceberg /app/build/libs/iceberg-rest-catalog-all.jar /usr/lib/iceberg-rest/iceberg-rest-catalog-all.jar

ENV CATALOG_CATALOG__IMPL=org.apache.iceberg.jdbc.JdbcCatalog
ENV CATALOG_URI=jdbc:sqlite:file:/tmp/iceberg_rest_mode=memory
ENV CATALOG_JDBC_USER=user
ENV CATALOG_JDBC_PASSWORD=password
ENV REST_PORT=8181

EXPOSE $REST_PORT
USER iceberg:iceberg
ENV LANG en_US.UTF-8
WORKDIR /usr/lib/iceberg-rest
CMD ["java", "-jar", "iceberg-rest-catalog-all.jar"]