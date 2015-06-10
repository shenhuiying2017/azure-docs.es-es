<properties
   pageTitle="Introducción a Docker y Compose en una máquina Virtual de Azure"
   description="Introducción rápida a la utilización de Compose y Docker en Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/07/2015"
   ms.author="danlep"/>

# Introducción a Docker y Compose en una máquina Virtual de Azure


Puede empezar a trabajar rápidamente con [Compose](http://github.com/docker/compose) (el sucesor de *Fig*) para definir y ejecutar aplicaciones complejas con Docker en una máquina virtual Linux en Azure. Con Compose, defina una aplicación con múltiples contenedores en un único archivo y acelerar la aplicación en un único comando que hace todo para ejecutarlo en la máquina virtual.




## Creación de una máquina virtual de Azure con la extensión VM Docker e instalación de Compose

Puede utilizar una serie de procedimientos de Azure y las imágenes disponibles en Azure Marketplace para crear una máquina virtual de Azure e instalar Docker y Compose en ella. Por ejemplo, consulte [Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)](virtual-machines-docker-with-xplat-cli) para obtener un procedimiento rápido crear una VM de Ubuntu con la extensión de VM Docker mediante la interfaz de la línea de comandos de Azure para Mac, Linux y Windows (la CLI de Azure). El ejemplo de este artículo usa la CLI en el modo de administración de servicios (**asm**).


Después de la VM Ubuntu se ejecute con Docker, conéctese a ella mediante SSH y, a continuación, instale [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) ejecutando dos comandos:

```
curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Si recibe un error "Permiso denegado", el directorio /usr/local/bin probablemente no es modificable y necesitará instalar Compose como superusuario. Ejecute `sudo -i`; a continuación, los dos comandos anteriores; a continuación, `exit`.

Para probar la instalación de Compose, ejecute

```
docker-compose --version
```

Verá un resultado similar a ```
docker-compose 1.2.0
```


## Creación de un archivo de configuración de docker-compose.yml

Compose utiliza un archivo de configuración de texto denominado `docker-compose.yml` para definir los contenedores que se ejecutarán en la máquina virtual. El archivo especifica la imagen que se ejecutará en cada contenedor (o podría ser una compilación de un Dockerfile), variables de entorno y dependencias necesarias, puertos, vínculos entre contenedores, etc. Para obtener más información sobre la sintaxis del archivo yml, consulte [Referencia de docker-compose.yml](http://docs.docker.com/compose/yml/).

Cree un directorio de trabajo en su máquina virtual y utilice el editor de texto para crear `docker-compose.yml`. Para probar un ejemplo sencillo, copie el texto siguiente en el archivo. Esta configuración instala WordPress (el sistema de creación de blogs y administración de contenido de código abierto) y un servidor de Base de datos SQL vinculado MariaDB con las imágenes del [Registro d eDockerHub](https://registry.hub.docker.com/_/wordpress/).

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL_ROOT_PASSWORD: <your password>

```

## Start the containers with Compose

In your working directory simply run

```
docker-compose up -d

```

to start the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1... Creating wordpress_wordpress_1... ```

>[AZURE.NOTE]Asegúrese de utilizar la opción **-d** al iniciar para que los contenedores se ejecuten continuamente en segundo plano.

Para comprobar que los contenedores están activos, escriba `docker-compose ps`. Debería ver algo parecido a lo siguiente:

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

Ahora puede conectarse a WordPress directamente en la máquina virtual; para ello, vaya a `http://localhost:8080`. Si desea conectarse a la máquina virtual a través de Internet, primero configure un extremo HTTP en la máquina virtual que asigne el puerto público 80 al puerto privado 8080. Por ejemplo, ejecute el siguiente comando de la CLI de Azure:

```
azure vm endpoint create <machine-name> 80 8080

```

Ahora debería ver la pantalla de inicio de WordPress, donde se puede completar la instalación.

![Pantalla de inicio de WordPress][wordpress_start]




## Pasos siguientes

* Desproteja la [Referencia de comandos de Compose](http://docs.docker.com/compose/cli/) y el [Manual del usuario](http://docs.docker.com/compose/) para obtener más ejemplos de creación e implementación de aplicaciones con múltiples contenedores.
* Pruebe a integrar Docker Compose con un clúster de [Docker Swarm](virtual-machines-docker-swarm.md). Consulte [Integración de Docker Compose/Swarm](https://github.com/docker/compose/blob/master/SWARM.md) para ver escenarios.
* Use una plantilla del Administrador de recursos de Azure, o bien una propia o una proporcionada por la [comunidad](http://azure.microsoft.com/documentation/templates/), para implementar una VM de Azure con Docker y una aplicación configurada con Compose. Por ejemplo, la [plantilla de VM de Ubuntu con Docker y tres contenedores](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/) le ayuda a implementar rápidamente tres servicios de imágenes en el [Registro de DockerHub](https://registry.hub.docker.com/).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=58-->