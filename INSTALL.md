# Keycloak

KeyCloak es un proyecto open source que se usa para escenarios que van desde pequeños sitios web con solo un puñado de usuarios hasta grandes empresas con millones de usuarios.

KeyCloak proporciona paginas de inicio de sesión totalmente personalizables , incluida una autenticación sólida, así como varios flujos, como la recuperacion de contraseñas, que requieren que los usuarios actualicen periodicamente las contraseñas, acepten términos condiciones, y mucho más. Todo esto sin necesidad de agregar nada a sus aplicaciones, ni codificacion algun. Todas las páginas visibles para sus usuarios admiten temas personalizados, lo que hace que sea muy fácil modificar la apariencia de las páginas para integrarlas con una marca corporativa y aplicaciones existentes.

### Instalación

Keycloak provee una serie de opciones sobre como puede ser instalado, incluyendo los siguientes:

- Ejecutarse en un contenedor docker
- Instalando y ejecutando keycloak localmente(requiere Java Virtual Machine,OpenJDK )
- Ejecutar Keycloak en Kubernetes
- Usando Keycloak kubernetes operator


### OPCIÓN 2: Instalando y ejecutando keycloak localmente(requiere Java Virtual Machine,OpenJDK)

Para realizar la instalación es necesario contrar con OpenJDK de la version 8 en adelante.

Se muestra la instalación de OpenJDK v11

```
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt update
sudo apt install openjdk-11-jdk
sudo apt install openjdk-11-jre
```
Una vez ejecutados los comandos verificar la instalación.
```
java -version
```
Una vez concluida la instalación de OpenJDK, procedemos a bajar el binario del KeyCloak

```
wget https://github.com/keycloak/keycloak/releases/download/15.0.1/keycloak-15.0.1.zip
```

Descomprimir 
```
unzip keycloak-15.0.1.zip
```
Mover el contenido a /opt/

```
sudo mv keycloak-15.0.1 /opt/keycloak
```
### Crear servicio para Keycloak

Agregar el siguiente archivo "/etc/systemd/system/keycloak.service"

```
sudo tee /etc/systemd/system/keycloak.service<<EOF
[Unit]
Description=Keycloak service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/opt/keycloak15/bin/standalone.sh

[Install]
WantedBy=multi-user.target
EOF
```

Habilitar el servicio
```
sudo systemctl daemon-reload
sudo systemctl start keycloak.service
```
Verificar servicio
```
sudo systemctl status keycloak
```

### Crear cuenta de administrador

Ingresar a la carpeta
```
cd /opt/keycloak
```
Crear el usuario "admin"
```
bin/add-user-keycloak.sh -u admin -p admin
```
Por último ingresar desde un navegador web a la siguiente dirección
```
http://localhost:8080/
```
o bien:
```
http://<IP-SERVIDOR-INSTALACION>:8080/
```
### Opcional

En caso de hacer correr por IP p´ublica o dominios por los que no quiera usar https sino http, ejecutar las siguientes instrucciones
```
cd <folder-keycloak>/bin
./kcadm.sh config credentials --server http://localhost:8080/auth --realm master --user admin
./kcadm.sh update realms/realmname -s sslRequired=NONE
```

## Migración 

Proceso de migracion de v14 a v15 

1. Realizar un backup de la DB , aplicación.
2. Descargar la v15 de keycloak en el directorio ```/opt```
```
sudo wget https://github.com/keycloak/keycloak/releases/download/15.0.2/keycloak-15.0.2.tar.gz
tar -xvf keycloak-15.0.2.tar.gz
```

3. Copiar la libreria de postgres para la conexion a la DB

```
cd /opt/keycloak-15.0.2/modules/system/layers/keycloak/com
sudo cp -r /opt/keycloak-14.0.0/modules/system/layers/keycloak/com/postgres ./
```
4. Copiar la carpeta Standalone de la anterior version a la nueva version
```
cd /opt/keycloak-15.0.2
sudo cp -r /opt/keycloak-14.0.0/standalone/ ./

```
5. script de upgrada keycloak
```
sudo bin/jboss-cli.sh --file=bin/migrate-standalone.cli
```
 
Guardar y cerrar.
Iniciar el servicio.

