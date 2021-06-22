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

Luego vamos a la carpeta `/devcontainer` y al archivo `docker-compose.yml` y comentamos la configuración de mariadb  

y des comentamos la configuración de postgresql. 

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

9. `bench config set-common-config -c root_login postgres` 

10. `bench config set-common-config -c root_password '"123"'` 

### Crear el sitio 

Una vez hayamos ejecutado los comandos anteriores ejecutamos el siguiente comando cambiando el `{site}` por el nombre deseado 
`bench new-site {site}.localhost --db-type postgres --db-host postgresql` 

Ejemplo 
`bench new-site mysitio.localhost --db-type postgres --db-host postgresql` 

Mientras este cargando el sitio le pedirá una contraseña de administrador 
ahí colocamos `admin` 

  

### Actualizar y Migrar 
Una vez hayamos creado el sitio tenemos que ejecutar el siguiente comando: `bench update` 

  

#### Nota  
este comando suele tardar un poco. 

  

Una vez hayamos actualizado el sitio tenemos que ejecutar el siguiente comando cambiando la palabra  
`{site.localhost}` por el nombre de tu site: `bench --site {site.localhost} migrate` 

  

Ejemplo: 
`bench --site mysitio.localhost migrate` 

  

#### Nota  
este comando suele tardar un poco. 

  

### Quitar el modo Mantenimiento 
La frappe puede poner la aplicación en modo mantenimiento por default para quitarlo tenemos que ir a la carpeta `/sites`  
y al archivo `common_site_config.json` y eliminar `"maintenance mode":1` 

# Paso final 
Ejecutamos el comando `bench start` esperamos un poco hasta que el watch termine de hacer el rebuild, esto no se presenta así que cuando aparezca  
`Rebuilding data_import_tools.min.js` podemos acceder a nuestro sitio 

### Para acceder al sitio 
Para acceder el sitio ponemos la url de nuestro sitio por ejemplo `mysite.localhost` y el puerto `8000` 
quedaría algo así `mysite.localhost:8000` 

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
# Extra Posible error de contraseña

### Como solucionar el error de contraseña incorrecta al crear el sitio

1. Nos vamos a la docker y abrimos `frappe_docker_devcontainer` dentro saldran varios contenedores nos vamos al de postgres `frappe_docker_devcontainer_postgresql_1` le damos abrir una cli
2. pegamos el siguiente comando en la cli `psql postgres postgres`
3. cuando estemos dentro de psql escribimos el siguiente comando: `\password postgres`
4. escribimos la nueva contraseña en este caso `123` recuerden por la contraseña igual en el common_site_config.json
5. Borrramos la carpeta de nuestro sitio fallido de la carpeta sites
6. y volvemos a crear el sitio y seguir los mismos pasos de ahi en adelante
