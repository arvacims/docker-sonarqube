FROM openjdk:8-alpine

ENV SONAR_VERSION=7.3 \
    SONAR_LDAP_PLUGIN_VERSION=2.2.0.608 \
    SONARQUBE_HOME=/opt/sonarqube \
    # Database configuration
    # Defaults to using H2
    SONARQUBE_JDBC_USERNAME=sonar \
    SONARQUBE_JDBC_PASSWORD=sonar \
    SONARQUBE_JDBC_URL=

# Http port
EXPOSE 9000

RUN addgroup -S sonarqube && adduser -S -G sonarqube sonarqube

# Import Sonarsource Deployer public key for download verification
COPY files/sonarsource_deployer_key.gpg .

RUN set -x \
    && apk add --no-cache gnupg unzip \
    && apk add --no-cache libressl wget \
    && apk add --no-cache su-exec \
    && apk add --no-cache bash \
    && gpg --import sonarsource_deployer_key.gpg \
    && mkdir /opt \
    && cd /opt \
    && wget -O sonarqube.zip --no-verbose https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-$SONAR_VERSION.zip \
    && wget -O sonarqube.zip.asc --no-verbose https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-$SONAR_VERSION.zip.asc \
    && gpg --batch --verify sonarqube.zip.asc sonarqube.zip \
    && unzip sonarqube.zip \
    && mv sonarqube-$SONAR_VERSION sonarqube \
    && rm sonarqube.zip* \
    && rm -rf $SONARQUBE_HOME/bin/*

VOLUME "$SONARQUBE_HOME/data"

WORKDIR $SONARQUBE_HOME
COPY files/sonar-ldap-plugin-"$SONAR_LDAP_PLUGIN_VERSION".jar $SONARQUBE_HOME/extensions/plugins/sonar-ldap-plugin-"$SONAR_LDAP_PLUGIN_VERSION".jar
COPY run.sh $SONARQUBE_HOME/bin/

RUN chown -R sonarqube:sonarqube $SONARQUBE_HOME
ENTRYPOINT ["./bin/run.sh"]
