# Configuracion de MariaDB

Primero instalar MySQL o MariaDB


Seguidamente crear la BD y los usuarios para keycloak
```
mysql> CREATE DATABASE keycloak CHARACTER SET utf8 COLLATE utf8_unicode_ci;
mysql> CREATE USER 'keycloak-user'@'%' IDENTIFIED BY 'PasswordSeguro';
mysql> GRANT ALL PRIVILEGES ON keycloak.* TO 'keycloak-user'@'%';
mysql> flush privileges;
```
Seguidamente descargar el conector JDBC de MySQL y copiarlo a la ruta
```
/opt/keycloak//modules/system/layers/keycloak/com/mysql/main/mysql-connector-java-8.0.20.jar
```
Crear el archivo "module.xml"
```
mkdir /opt/keycloak//modules/system/layers/keycloak/com/mysql/main/module.xml
```
El contenido del archivo sera:
```xml
<?xml version="1.0" ?>
<module xmlns="urn:jboss:module:1.3" name="com.mysql">
 <resources>
  <resource-root path="mysql-connector-java-8.0.20.jar" />
 </resources>
 <dependencies>
  <module name="javax.api"/>
  <module name="javax.transaction.api"/>
 </dependencies>
</module>
```
## Agregando Driver

Ingresar al archivo "{Keycloak Home Folder}/standalone/configuration/standalone.xml"
```
sudo nano {Keycloak Home Folder}/standalone/configuration/standalone.xml
```
Y agregar el siguiente contenido en el tag "drivers"

```xml
<driver name="mysql" module="com.mysql"> 
     <driver-class>com.mysql.jdbc.Driver</driver-class> 
</driver>
```
Dentro del mismo archivo: "{Keycloak Home Folder}/standalone/configuration/standalone.xml"; Comentar el anterior KeycloakDS y agregar el nuevo

```xml
<!--                 

              <datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true" statistics-enabled="${wildfly.datasources.statistics-enabled:${wildfly.statistics-enabled:false}}">
                    <connection-url>jdbc:h2:${jboss.server.data.dir}/keycloak;AUTO_SERVER=TRUE</connection-url>
                    <driver>h2</driver>
                    <security>
                        <user-name>sa</user-name>
                        <password>sa</password>
                    </security>
                </datasource> 
-->

                <datasource jndi-name="java:/jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true">
                  <connection-url>jdbc:mysql://localhost:3306/keycloak?useSSL=false&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC</connection-url>
                  <driver>mysql</driver>
                  <pool>
                      <min-pool-size>5</min-pool-size>
                      <max-pool-size>15</max-pool-size>
                  </pool>
                  <security>
                      <user-name>keycloak-user</user-name>
                      <password>1h7rHhfy3</password>
                  </security>
                  <validation>
                      <valid-connection-checker class-name="org.jboss.jca.adapters.jdbc.extensions.mysql.MySQLValidConnectionChecker"/>
                      <validate-on-match>true</validate-on-match>
                      <exception-sorter class-name="org.jboss.jca.adapters.jdbc.extensions.mysql.MySQLExceptionSorter"/>
                  </validation>

              </datasource>
```
Dentro del mismo archivo: "{Keycloak Home Folder}/standalone/configuration/standalone.xml"; agregar un nuevo elemento "< spi>"

```xml
<subsystem xmlns="urn:jboss:domain:keycloak-server:1.1">
    ...
    <spi name="connectionsJpa">
        <provider name="default" enabled="true">
            <properties>
                <property name="dataSource" value="java:jboss/datasources/KeycloakDS"/>
                <property name="initializeEmpty" value="true"/>
                <property name="migrationStrategy" value="update"/>
                <property name="migrationExport" value="${jboss.home.dir}/keycloak-database-update.sql"/>
            </properties>
        </provider>
    </spi>
    ...
</subsystem>
```
Reiniciar el servicio y crear nuevo usuario admin para que las tablas se generen y puedan poblarse.

```
sudo service keycloak restart
```
