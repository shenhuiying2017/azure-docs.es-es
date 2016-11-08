---
title: Docker y Compose en una máquina virtual | Microsoft Docs
description: Introducción rápida al trabajo con Compose y Docker en máquinas virtuales Linux
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: danlep

---
# Introducción a Docker y Compose para definir y ejecutar una aplicación de contenedores múltiples en una máquina virtual de Azure
Empiece a usar Docker y [Compose](http://github.com/docker/compose) para definir y ejecutar una aplicación compleja en una máquina virtual Linux en Azure. Con Compose, use un archivo de texto simple para definir una aplicación compuesta de varios contenedores de Docker. A continuación, gire la aplicación en un único comando que hace todo para ejecutarlo en la máquina virtual.

Como ejemplo, en este artículo se muestra cómo configurar rápidamente un blog de WordPress con una base de datos SQL MariaDB de back-end en una máquina virtual Ubuntu. También puede utilizar Compose para configurar aplicaciones más complejas.

## Paso 1: Configuración de una máquina virtual de Linux como host de Docker
Puede emplear diversos procedimientos de Azure, así como las imágenes y plantillas de Azure Resource Manager disponibles en Azure Marketplace, para crear una máquina virtual Linux y configurarla como host de Docker. Por ejemplo, consulte [Uso de la extensión de VM de Docker para implementar el entorno](virtual-machines-linux-dockerextension.md) para crear rápidamente una máquina virtual de Ubuntu con la extensión de máquina virtual de Azure Docker mediante el uso de una [plantilla de inicio rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Cuando utilice la extensión de máquina virtual de Docker, la máquina virtual se configurará automáticamente como un host de Docker. En el ejemplo de este artículo se muestra cómo usar la [interfaz de la línea de comandos de Azure para Mac, Linux y Windows](../xplat-cli-install.md) (la CLI de Azure) en el modo de Resource Manager para crear la máquina virtual.

## Paso 2: Comprobación de que se ha instalado Compose
Una vez que haya terminado la implementación, aplique SSH a su nuevo host de Docker mediante el nombre DNS que proporcionó durante la implementación.

Para comprobar que Compose esté instalado en la máquina virtual, ejecute el siguiente comando:

```
$ docker-compose --version
```

Verá un resultado similar a `docker-compose 1.6.2, build 4d72027`.

> [!TIP]
> Si utiliza otro método para crear un host de Docker y necesita instalar Compose de forma manual, consulte la [documentación de esta herramienta](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).
> 
> 

## Paso 3: Creación de un archivo de configuración docker-compose.yml
A continuación, debe crear un archivo `docker-compose.yml`, que es simplemente un archivo de configuración para definir los contenedores de Docker para ejecutar la máquina virtual. El archivo especifica la imagen que se ejecutará en cada contenedor (o podría ser una compilación de un Dockerfile), variables de entorno y dependencias necesarias, puertos y vínculos entre contenedores. Para más información sobre la sintaxis del archivo YML, consulte la [referencia del archivo de Compose](http://docs.docker.com/compose/yml/).

Cree un directorio de trabajo en su máquina virtual y utilice el editor de texto para crear `docker-compose.yml`. Para realizar una prueba de concepto, copie el texto siguiente en el archivo. Esta configuración usa imágenes del [Registro de DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar WordPress (el sistema de administración de contenido y blogs de código abierto) y una base de datos SQL MariaDB de back-end vinculada.

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## Paso 4: inicio de los contenedores con Compose
En el directorio de trabajo de la máquina virtual, ejecute el comando siguiente. En función del entorno, es posible que tenga que ejecutar `docker-compose` mediante `sudo`.

```
$ docker-compose up -d

```

Este comando inicia los contenedores de Docker especificados en `docker-compose.yml`. Este paso tarda uno o dos minutos en completarse. Verá un resultado similar al siguiente:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Asegúrese de utilizar la opción **-d** al iniciar para que los contenedores se ejecuten continuamente en segundo plano.
> 
> 

Para comprobar que los contenedores están activos, escriba `docker-compose ps`. Debería ver algo parecido a lo siguiente:

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Ahora puede conectarse a WordPress directamente en la máquina virtual a través del puerto 80. Si utiliza una plantilla de Azure Resource Manager para crear la máquina virtual, trate de conectarse a `http://<dnsname>.<region>.cloudapp.azure.com` o, si la ha creado mediante el modelo de implementación clásica, trate de conectarse a `http://<cloudservicename>.cloudapp.net`. Ahora debería ver la pantalla de inicio de WordPress, donde se puede completar la instalación e iniciar la aplicación.

![Pantalla de inicio de WordPress][wordpress_start]

## Pasos siguientes
* Consulte el [manual del usuario de extensión de máquina virtual de Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) para obtener más opciones de configuración de Docker y Compose en su máquina virtual de Docker. Por ejemplo, una de ellas consiste en colocar el archivo YML de Compose (convertido a JSON) directamente en la configuración de la extensión de máquina virtual de Docker.
* Consulte la [referencia de línea de comandos de Compose](http://docs.docker.com/compose/reference/) y el [manual del usuario](http://docs.docker.com/compose/) para obtener más ejemplos de compilación e implementación de aplicaciones con varios contenedores.
* Use una plantilla del Administrador de recursos de Azure, o bien una propia o una proporcionada por la [comunidad](https://azure.microsoft.com/documentation/templates/), para implementar una VM de Azure con Docker y una aplicación configurada con Compose. Por ejemplo, la plantilla [Implementación de un blog de WordPress con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) usa Docker y Compose para implementar rápidamente WordPress con un back-end de MySQL en una máquina virtual de Ubuntu.
* Pruebe a integrar Docker Compose con un clúster de [Docker Swarm](virtual-machines-linux-docker-swarm.md). Consulte [Using Compose with Swarm](https://docs.docker.com/compose/swarm/) (Uso de Compose con Swarm) para conocer distintos escenarios.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_0928_2016-->