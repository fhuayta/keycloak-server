
# Configuracion de Postgres

1. Dentro de la ruta.
```
cd /opt/keycloak-15.0.2/modules/system/layers/keycloak/com

```
2. Generar la siguiente ruta.
```
com
│   ├── postgres
│   │   └── main
│   │       ├── module.xml
│   │       └── postgresql-42.2.20.jar
```
3. En el archivo module.xml copiar lo siguiente.
```
<?xml version="1.0" ?>
<module xmlns="urn:jboss:module:1.3" name="com.postgres">
 <resources>
  <resource-root path="postgresql-42.2.20.jar" />
 </resources>
 <dependencies>
  <module name="javax.api"/>
  <module name="javax.transaction.api"/>
 </dependencies>
</module>

```

4. Puede descargar el archivo ```postgresql-42.2.20.jar ``` del siguiente [Post](https://github.com/akoserwal/keycloak-integrations/tree/master/keycloak-postgresql/keycloak-config).

5. En la Base de datos de Postgres, crear una base de datos y un usuario con privilegios a esa base de datos.
```psql
CREATE DATABASE keycloak;
CREATE USER keycloak WITH PASSWORD 'SecretPassword';
GRANT ALL PRIVILEGES ON DATABASE keycloak TO keycloak;
```
6. Agregar en datasource las configuraciones para postgres 
```
sudo nano /opt/keycloak-15.0.2/standalone/configuration/standalone.xml 
```
Agregar en la ruta.
```

               <datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true" statistics-enabled="${wildfly.datasources.statistics-enabled:${wildfly.statistics-enabled:false}}">
                    <connection-url>jdbc:postgresql://localhost:5432/keycloak</connection-url>
                    <driver>postgres</driver>
                    <security>
                        <user-name>keycloak</user-name>
                        <password>Pass-keycloak</password>
                    </security>
                </datasource>
```
7. Verificar la siguiente linea en el archivo abierto,donde el datasource sea KeycloakDS.
```
<default-bindings context-service="java:jboss/ee/concurrency/context/default" datasource="java:jboss/datasources/KeycloakDS" managed-executor-service="java:jboss/ee/concurrency/executor/default" managed-scheduled-executor-service="java:jboss/ee/concurrency/scheduler/default" managed-thread-factory="java:jboss/ee/concurrency/factory/default"/>

```

Guardar y cerrar.
Iniciar el servicio.
