# Como instalar Frappe-ERPNext con Postgresql & Docker en Windows 10  

  

# Requisitos 
- Tener instalado y configurado Docker 
- Tener instalado Git 
- Tener instalado vscode 

# Paso 1 

  

### Clonar el Repositorio 
Accedemos a la consola de bash y pegamos y ejecutamos el siguiente comando: `git clone https://github.com/frappe/frappe_docker.git` 

  

### Acceder a la Carpeta Del repositorio Clonado 
En la misma consola de bash luego de haber clonado el repositorio pegamos y ejecutamos el siguiente comando: `cd frappe_docker` 

  

# Paso 2 

### Abrimos vscode 

Para abrir vscode en la misma carpeta en la que estamos en bash ejecutamos el siguiente comando: `code .` 

Abrimos una terminal interna en vscode y pegamos y ejecutamos el siguiente comando: `cp -R devcontainer-example .devcontainer` 

Luego vamos a la carpeta `/devcontainer` y al archivo `docker-compose.yml`, modifica el servicio de mariadb 
 agregando este comando: `command: ['mysqld', '--character-set-server=utf8mb4', '--collation-server=utf8mb4_unicode_ci']`


En la terminal interna de vscode pegamos y ejecutamos el siguiente comando: `code --install-extension ms-vscode-remote.remote-containers` 

Copiamos el comando: `Remote Containers : Reopen in Container` 

una vez instalada la extensión de remote containers procedemos a abrir la paleta de comandos con `ctrl+shift+p` y pegamos el comando copiado 

y ejecutamos. 

#### Nota:  

* Recuerde tener docker abierto e iniciado 
* Tenga en cuenta que el proceso tarde en ejecutarse tenga paciencia y espere hasta el final. 

# Paso 3 

### Configurar Frappe 
una vez se haya montado el contenedor de docker y se haya abierto el acceso remoto en vscode 
abrimos una terminal interna y procedemos a ejecutar los siguientes comandos en el siguiente orden en la terminal: 

1. `bench init --skip-redis-config-generation --frappe-branch version-12 frappe-bench` 

2. `cd frappe-bench` 

3. `bench set-mariadb-host mariadb` 

4. `bench set-redis-cache-host redis-cache:6379` 

5. `bench set-redis-queue-host redis-queue:6379` 

6. `bench set-redis-socketio-host redis-socketio:6379` 

7. `code Procfile` 

8. `sed -i '/redis/d' ./Procfile` 

### Crear el sitio 

Una vez hayamos ejecutado los comandos anteriores ejecutamos el siguiente comando cambiando el `{site}` por el nombre deseado 
`bench new-site {site}.localhost  --no-mariadb-socket` 

Ejemplo 
`bench new-site mysite.localhost --no-mariadb-socket` 

En la ejecucion te pedira la contraseña root de mariaDB es la que se especifica en el archivo `docker-compose.yml` ubicado en `/devcontainer`.
Por defecto esta  contraseña es `123`

luego cuando este terminado de crear el sitio le pedirá una contraseña de administrador 
ahí colocamos `admin` 

  

### Quitar el modo Mantenimiento 
La frappe puede poner la aplicación en modo mantenimiento por default para quitarlo tenemos que ir a la carpeta `/sites`  
y al archivo `common_site_config.json` y eliminar `"maintenance mode":1` 

# Establecer el modo de desarrollador
`bench --site mysite.localhost set-config developer_mode 1`
`bench --site mysite.localhost clear-cache`

# Instacion de ErpNext
`bench get-app --branch version-12 erpnext https://github.com/frappe/erpnext.git`
`bench --site mysite.localhost install-app erpnext`

# Paso final 
Ejecutamos el comando `bench start` esperamos un poco hasta que el watch termine de hacer el rebuild, esto no se presenta así que cuando aparezca  
`Rebuilding item-dashboard.min.js` podemos acceder a nuestro sitio 

### Para acceder al sitio 
Para acceder el sitio ponemos la url de nuestro sitio por ejemplo `{site}.localhost` y el puerto `8000` 
quedaría algo así `{site}.localhost:8000` 

# Extra como abrir el proyecto cuando ya está instalado 

Vamos a la carpeta del Frappe-docker y abrimos con vscode, presionamos `ctrl+shift+p` y pegamos el comando `Remote Containers : Reopen in Container` 

luego de que cargue el acceso remoto al container ejecutamos los comandos 

1. `cd frappe-bench` 

2. `bench start`  

Esperas a que inicie y Listo :smiley: 

#### Nota 

Docker debe estar abierto e iniciado

Debe iniciar session como administrador en erpnext para poder crear usuarios,

Credenciales:
`
email: Administrator
password: admin
`
Eliminar las imagenes y contenedeores que tengas en docker sobre frappe y erpnext
