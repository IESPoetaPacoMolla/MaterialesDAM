### Instalación de ObjectDb Server con Docker

Si queremos realizar una instalación de la base de datos ObjectDb que actúe como servidor podemos utilizar la tecnológia de contenedores Docker. El servidor de ObjectDb se ejecuta a partir del jar que podemos ejecutar con el comando java con la siguiente instrucción.

```shell
java -cp objectdb.jar com.objectdb.Server start
```

Este comando ejecutará un servidor que escuchará peticiones en el puerto 6136 y acpetará conexiones desde cualquier IP.

Aunque podemos ejecutar este comando de forma sencilla en cualquier máquina que disponga de la maquina virtual java instalada, podemos utilizar Docker para crearlo dentro de un contenedor que nos permita gestionarlo de forma aislada y controlada.

Para crear una nueva imagen de Docker con nuestro servidor ObjectDb seguiremos lo siguientes pasos:

Creamos una nueva carpeta donde guardaremos toda la configuración y ficheros necesarios para construir la máquina.

```shell
mkdir objectdb-server
```

Dentro de esta carpeta copiamos el fichero jar que nos permite la arrancar una instancia de esta base de datos en modo servidor. Para este manual se utilizará la versión 2.8.3 que hemos descargado desde la página oficial https://www.objectdb.com/download y que posteriormente hemos descomprimido. Si nos situamos en el directorio donde hemos descargado el zip, copiamos a la carpeta anteriormente creada con el siguiente comando

```shell
cp objectdb-2.8.3/bin/objectdb.jar /home/miguel/objectdb-server
```

Para configurar la imagen de Docker que vamos a crear, utilizamos un fichero de texto que nombramos como Dockerfile y al que añadimos el siguiente contenido.

```dockerfile
FROM java:latest

WORKDIR /app/

# Copiamos el JAR de nuestra aplicación a la imagen Docker
COPY ./objectdb.jar .

# Corremos el archivo JAR
CMD ["java", "-cp", "objectdb.jar", "com.objectdb.Server", "start"]
```

En este script de configuración de la imagen tenemos los siguientes elementos.

* *FROM java:latest*. La imagen se realizará a partir de la última versión de java. 
* *WORKDIR /app/*. Establecemos un directorio dentro del contenedor que será donde se ejecute la aplicación que genera el servidor.
* *COPY ./objectdb.jar .* Copiarmos dentro de nuestra imagen el binario que permite la creación del servidor objectdb.
* *CMD ["java", "-cp", "objectdb.jar", "com.objectdb.Server", "start"]*. Comando que se ejecutará cuando se inicie el contenedor.

Cuando tenemos todos estos pasos realizados pasamos a crear nuestra imagen a partir del Dockerfile que acabamos de definir.

```shell
docker build -t objectdb-server .
```

Este comando creará la imagen y le pasamos los parámetros *-t nombre_de_imagen* y el . para indicar que podrá encontrar el Dockerfile en el directorio donde nos encontramos.

Después de recuperar la imagen de base y establecer la configuración si ejecutamos el siguiente comando podremos comprobar que efectivamente tenemos la imagen creada en el repositorio local de imagenes de docker

```shell
docker image ls
```

Por último solo nos hace falta arrancar el contenedor para tener el servidor funcionando.

```shell
docker run -d -p 6136:6136 --name container-objectdb objectdb-server
```

* *-d*. Ejecutará el container de forma independiente de la terminal desde la cual hayamos lanzado el comando.
* *-p 6136:6136*. Establecemos un mapeo de puertos entre en ordenador donde ejecutamos docker y el contenedor donde se ejecuta el servidor objectdb.
* *--name nombre_contenedor*. Nombre simbólico que le damos al contendor que vamos a crear y ejecutar.
* *objectdb-server*. Nombre de la imagen que vamos a utilizar como referencia para crear el contendor.

Para comprobar que el contendor se está ejecutando en estos momentos podemos utilizar el comando:

```shell
docker ps
```

Para comprobar el registro del contenedor donde se informa de que el servidor queda escuchando en el puerto 6136 podemos ejecutar el comando:

```shell
docker logs container-objectdb
```

Con todo ello tendremos la imagen y el contenedor creado y podremos arrancarlo y pararlo de forma sencilla con los siguientes comandos:

```shell
docker start container-objectdb
```

```shell
docker stop container-objectdb
```

