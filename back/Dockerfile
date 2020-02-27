# DocDoku, Professional Open Source
# Copyright 2006 - 2017 DocDoku SARL
#
# This file is part of DocDokuPLM.
#
# DocDokuPLM is free software: you can redistribute it and/or modify
# it under the terms of the GNU Affero General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# DocDokuPLM is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU Affero General Public License for more details.
#
# You should have received a copy of the GNU Affero General Public License
# along with DocDokuPLM.  If not, see <http://www.gnu.org/licenses/>.
#
## DocDokuPLM server docker image based on docdokuplm:payara (base image with system dependencies)
#

FROM docdokuplm:payara

ARG DOCDOKU_PLM_CODEBASE=http://localhost
ARG HEAP_SIZE=2g
ARG JWT_ENABLED=true
ARG JWT_KEY=MyVerySecretPhrase
ARG SESSION_ENABLED=false
ARG BASIC_AUTH_ENABLED=false
ARG ES_SERVER_URI=http://localhost:9200
ARG ES_SERVER_INDEX_PREFIX=local
ARG ES_SERVER_PWD=changeme
ARG ES_SERVER_USERNAME=elastic
ARG ES_SERVER_AWS_SERVICE
ARG ES_SERVER_AWS_REGION
ARG ES_SERVER_AWS_KEY
ARG ES_SERVER_AWS_SECRET
ARG SMTP_HOST=localhost
ARG SMTP_PORT=25
ARG SMTP_USER=myuser
ARG SMTP_FROM_ADDR=docdokuplm@localhost
ARG DATABASE_USER=my_user
ARG DATABASE_PWD=my_password
ARG DATABASE_SERVER_NAME=db
ARG DATABASE_NAME=my_db

ENV KEYSTORE_LOCATION /opt/payara/dplm.jceks
ENV KEYSTORE_KEY_ALIAS mykey
ENV KEYSTORE_PASS changeit
ENV KEYSTORE_KEY_PASS changeit
ENV KEYSTORE_TYPE JCEKS

ENV ASADMIN_PATH ${PAYARA_DIR}/bin
ENV DOMAIN_DIR /opt/payara/appserver/glassfish/domains/production

RUN \
 keytool -genseckey -storetype JCEKS -keyalg AES -keysize 256 -keystore ${KEYSTORE_LOCATION} -storepass ${KEYSTORE_PASS} -keypass ${KEYSTORE_KEY_PASS}

RUN \
 mkdir -p /opt/payara/appserver/glassfish/domains/production/autodeploy

RUN \
 ${ASADMIN_PATH}/asadmin start-domain && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-custom-resource --restype java.util.Properties --factoryclass org.glassfish.resources.custom.factory.PropertiesFactory --property="" docdokuplm.config && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-custom-resource --restype java.util.Properties --factoryclass org.glassfish.resources.custom.factory.PropertiesFactory --property="" auth.config && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-custom-resource --restype java.util.Properties --factoryclass org.glassfish.resources.custom.factory.PropertiesFactory --property="" security.config && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-custom-resource --restype java.util.Properties --factoryclass org.glassfish.resources.custom.factory.PropertiesFactory --property="" elasticsearch.config && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-custom-resource --restype java.util.Properties --factoryclass org.glassfish.resources.custom.factory.PropertiesFactory --property="" office.config && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-custom-resource --restype java.util.Properties --factoryclass org.glassfish.resources.custom.factory.PropertiesFactory --property="" oauth.config && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-jdbc-connection-pool --restype javax.sql.ConnectionPoolDataSource --datasourceclassname org.postgresql.ds.PGConnectionPoolDataSource --property="" DocDokuPLMPool && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-jdbc-resource --connectionpoolid DocDokuPLMPool jdbc/docdokuPU && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-jvm-options -Dfile.encoding=UTF-8 && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} delete-jvm-options '-Xmx512m' && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-jvm-options -Xmx${HEAP_SIZE} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} create-javamail-resource --mailhost ${SMTP_HOST} --mailuser ${SMTP_USER} --fromaddress ${SMTP_FROM_ADDR} --property mail.smtp.host=${SMTP_HOST}:mail.smtp.starttls.enable=false:mail.smtp.port=${SMTP_PORT}:mail.smtp.auth=false mail/docdokuSMTP && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set server.network-config.protocols.protocol.http-listener-1.http.upload-timeout-enabled=false && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set server.network-config.protocols.protocol.http-listener-1.http.connection-upload-timeout-millis=-1 && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.jdbc-connection-pool.DocDokuPLMPool.property.ServerName=${DATABASE_SERVER_NAME} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.jdbc-connection-pool.DocDokuPLMPool.property.User=${DATABASE_USER} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.jdbc-connection-pool.DocDokuPLMPool.property.Password=${DATABASE_PWD} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.jdbc-connection-pool.DocDokuPLMPool.property.DatabaseName=${DATABASE_NAME} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.docdokuplm.config.property.vaultPath=/var/lib/docdoku/vault && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.docdokuplm.config.property.conversionsPath=/var/lib/docdoku/conversions && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.docdokuplm.config.property.codebase=${DOCDOKU_PLM_CODEBASE} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.auth.config.property."jwt\.enabled"=${JWT_ENABLED} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.auth.config.property."jwt\.key"=${JWT_KEY} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.auth.config.property."session\.enabled"=${SESSION_ENABLED} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.auth.config.property."basic\.header\.enabled"=${BASIC_AUTH_ENABLED} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.elasticsearch.config.property.serverUri=${ES_SERVER_URI} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.elasticsearch.config.property.password=${ES_SERVER_PWD} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.elasticsearch.config.property.indexPrefix=${ES_SERVER_INDEX_PREFIX} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.elasticsearch.config.property.username=${ES_SERVER_USERNAME} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.elasticsearch.config.property.awsService=${ES_SERVER_AWS_SERVICE} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.elasticsearch.config.property.awsRegion=${ES_SERVER_AWS_REGION} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.elasticsearch.config.property.awsAccessKey=${ES_SERVER_AWS_KEY} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.elasticsearch.config.property.awsSecretKey=${ES_SERVER_AWS_SECRET} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.office.config.property.office_port=8100 && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.office.config.property.office_home=/var/lib/libreoffice && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.security.config.property.keystoreLocation=${KEYSTORE_LOCATION} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.security.config.property.keystorePass=${KEYSTORE_PASS} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.security.config.property.keyAlias=${KEYSTORE_KEY_ALIAS} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.security.config.property.keystoreType=${KEYSTORE_TYPE} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set resources.custom-resource.security.config.property.keyPass=${KEYSTORE_KEY_PASS} && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set configs.config.server-config.admin-service.das-config.autodeploy-enabled=true && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set configs.config.server-config.admin-service.das-config.autodeploy-polling-interval-in-seconds=10 && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} set configs.config.server-config.admin-service.das-config.autodeploy-retry-timeout=10 && \
 ${ASADMIN_PATH}/asadmin --user=${ADMIN_USER} --passwordfile=${PASSWORD_FILE} stop-domain

VOLUME ["/var/lib/docdoku/vault", "/var/lib/docdoku/conversions"]
ENTRYPOINT ${ASADMIN_PATH}/asadmin start-domain --debug true -v production

