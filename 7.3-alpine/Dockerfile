FROM openjdk:8-alpine

ENV SONAR_VERSION=7.3 \
    SONARQUBE_JDBC_USERNAME="sonar" \
    SONARQUBE_JDBC_PASSWORD="sonar" \
    SONARQUBE_JDBC_URL="" \
    SONARQUBE_WEB_JVM_OPTS="" \
    SONARQUBE_SECURITY_REALM="" \
    SONARQUBE_AUTHENTICATOR_DOWNCASE="true" \
    SONARQUBE_LDAP_URL="" \
    SONARQUBE_LDAP_BINDDN="" \
    SONARQUBE_LDAP_BINDPASSWORD="" \
    SONARQUBE_LDAP_AUTHENTICATION="simple" \
    SONARQUBE_LDAP_REALM="" \
    SONARQUBE_LDAP_STARTTLS="false" \
    SONARQUBE_LDAP_FOLLOWREFERRALS="true" \
    SONARQUBE_LDAP_USER_BASEDN="cn=users,dc=example,dc=org" \
    SONARQUBE_LDAP_USER_REQUEST="(&(objectClass=inetOrgPerson)(uid={login}))" \
    SONARQUBE_LDAP_USER_REALNAMEATTRIBUTE="cn" \
    SONARQUBE_LDAP_USER_EMAILATTRIBUTE="mail" \
    SONARQUBE_LDAP_GROUP_BASEDN="" \
    SONARQUBE_LDAP_GROUP_REQUEST="(&(objectClass=groupOfUniqueNames)(uniqueMember={dn}))" \
    SONARQUBE_LDAP_GROUP_IDATTRIBUTE="cn"

# Http port
EXPOSE 9000

RUN addgroup -S sonarqube && adduser -S -G sonarqube sonarqube

RUN set -x \
    && apk add --no-cache gnupg unzip \
    && apk add --no-cache libressl wget \
    && apk add --no-cache su-exec \
    && apk add --no-cache bash \
    # pub   2048R/D26468DE 2015-05-25
    #       Key fingerprint = F118 2E81 C792 9289 21DB  CAB4 CFCA 4A29 D264 68DE
    # uid                  sonarsource_deployer (Sonarsource Deployer) <infra@sonarsource.com>
    # sub   2048R/06855C1D 2015-05-25
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys F1182E81C792928921DBCAB4CFCA4A29D26468DE \
    && mkdir /opt \
    && cd /opt \
    && wget -O sonarqube.zip --no-verbose https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-$SONAR_VERSION.zip \
    && wget -O sonarqube.zip.asc --no-verbose https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-$SONAR_VERSION.zip.asc \
    && gpg --batch --verify sonarqube.zip.asc sonarqube.zip \
    && unzip sonarqube.zip \
    && mv sonarqube-$SONAR_VERSION sonarqube \
    && chown -R sonarqube:sonarqube sonarqube \
    && rm sonarqube.zip* \
    && rm -rf sonarqube/bin/*

VOLUME /opt/sonarqube/data

WORKDIR /opt/sonarqube
COPY sonar.properties /opt/sonarqube/conf/
COPY run.sh /opt/sonarqube/bin/
ENTRYPOINT ["./bin/run.sh"]
