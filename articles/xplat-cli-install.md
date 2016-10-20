<properties
	pageTitle="Instalación de la interfaz de la línea de comandos de Azure | Microsoft Azure"
	description="Instalación de la interfaz de la línea de comandos de Azure (CLI) para Mac, Linux y Windows para comenzar a utilizar los servicios de Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2016"
	ms.author="danlep"/>
    
# Instalación de la CLI de Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [CLI de Azure](xplat-cli-install.md)

Instale rápidamente la interfaz de la línea de comandos de Azure (CLI de Azure) para utilizar un conjunto de comandos de código abierto basados en shell para crear y administrar recursos en Microsoft Azure. Tiene varias opciones para instalar estas herramientas multiplataforma en su equipo:

* **Paquete de npm**: ejecute npm (el administrador de paquetes para JavaScript) para instalar el paquete de la CLI de Azure más reciente en su distribución de Linux o sistema operativo. Se requiere que node.js y npm estén instalados en su equipo.
* **Instalador**: descargue un instalador para facilitar la instalación en Mac o Windows.
* **Contenedor de Docker**: empiece usando la CLI más reciente en un contenedor de Docker listos para ejecutarse. Se requiere el host de Docker en su equipo.
    
Si desea obtener más opciones y los antecedentes, consulte el repositorio del proyecto en [GitHub](https://github.com/azure/azure-xplat-cli).

Una vez que instale la CLI de Azure, [conéctela con su suscripción a Azure](xplat-cli-connect.md) y ejecute los comandos **azure** desde la interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema, etc.) para trabajar con sus recursos de Azure.



## Opción 1. Instalación de un paquete de NPM

Para instalar la CLI desde un paquete de npm, necesita la versión más reciente de Node.js y npm instalada en su sistema. Tras ello, ejecute el comando siguiente para instalar el paquete de la CLI de Azure publicado en [npmjs.com](https://www.npmjs.com). (En distribuciones de Linux, es posible que tenga que usar **sudo** para ejecutar correctamente el comando __npm__).

	npm install -g azure-cli

> [AZURE.NOTE]Si necesita instalar o actualizar Node.js y npm para su sistema operativo o distribución de Linux, consulte la documentación en [Nodejs.org](https://nodejs.org/en/download/package-manager/). Se recomienda que instale la versión más reciente de Node.js LTS (4.x). Si utiliza una versión anterior, podrían producirse errores de instalación.

Si lo prefiere, descargue el [archivo .tar][linux-installer] de Linux más reciente para el paquete de npm localmente. Después, instale el paquete de npm descargado de la siguiente manera (en las distribuciones de Linux podría tener que usar **sudo**):

    npm install -g <path to downloaded tar file>

## Opción 2. Uso de un instalador

Si utiliza un equipo Mac o Windows, los instaladores de la CLI siguientes están disponibles para descargarse:

* [Instalador de Mac OS X][mac-installer]

* [MSI de Windows][windows-installer]

>[AZURE.TIP]En Windows, también puede descargar el [Instalador de plataforma web](https://go.microsoft.com/?linkid=9828653) para instalar la CLI. Este instalador ofrece la opción de instalar otros Azure SDK y herramientas de línea de comandos después de instalar la CLI.


## Opción 3. Uso de un contenedor Docker

Si ha configurado un host de [Docker](https://docs.docker.com/engine/understanding-docker/), puede ejecutar la CLI de Azure más reciente en un contenedor de Docker. Ejecutar:

```
docker run -it microsoft/azure-cli
```


## Ejecución de los comandos de la CLI de Azure
Una vez instalada la CLI de Azure, ejecute el comando **azure** desde su interfaz de usuario de la línea de comandos (Bash, Terminal, símbolo del sistema, etc.). Por ejemplo, si desea ejecutar el comando help, escriba lo siguiente:

```
azure help
```
> [AZURE.NOTE]En algunas distribuciones de Linux, puede recibir un error similar al `/usr/bin/env: ‘node’: No such file or directory`. Este error procede de las instalaciones recientes de Node.js que se instala en /usr/bin/nodejs. Para corregirlo, cree un vínculo simbólico a /usr/bin/node, para lo que debe ejecutar este comando:

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Para saber qué versión de la CLI de Azure instaló, escriba lo siguiente:

```
azure --version
```

De este modo, ya está listo. Para obtener acceso a todos los comandos de la CLI para trabajar con sus propios recursos, [conéctese a su suscripción a Azure desde la CLI de Azure](xplat-cli-connect.md).

>[AZURE.NOTE] La primera vez que usa la CLI de Azure, ve un mensaje en el que se le pregunta si quiere permitir que Microsoft recopile información. La participación es voluntaria. Si elige participar, puede dejar de hacerlo cuando desee mediante la ejecución de `azure telemetry --disable`. Para habilitar la participación en cualquier momento, ejecute `azure telemetry --enable`.


## Actualización de la CLI

Microsoft publica con frecuencia versiones actualizadas de la CLI de Azure. Vuelva a instalar la CLI mediante el programa de instalación para su sistema operativo o ejecute el contenedor de Docker más reciente. Si tiene Node.js y npm más recientes instalados, escriba lo siguiente para efectuar la actualización (en las distribuciones de Linux, es posible que necesite utilizar **sudo**).

```
npm update -g azure-cli
```

## Habilitación de la función de autocompletar

La función de autocompletar de los comandos de la CLI es compatible con Mac y Linux.

Para habilitarla en zsh, ejecute el siguiente código:

```
echo '. <(azure --completion)' >> .zshrc
```

Para habilitarla en Bash, ejecute el siguiente código:

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## Pasos siguientes 

* [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](xplat-cli-connect.md) para crear y administrar recursos de Azure.

* Si desea obtener más información acerca de la CLI de Azure, descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).

* Si tiene preguntas acerca de cómo usar la CLI de Azure o sobre Azure, visite los [foros de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=azurescripting).

* Para los sistemas Linux, también puede instalar la CLI de Azure creándola desde el [origen](http://aka.ms/linux-azure-cli). Para obtener más información sobre la creación a partir del origen, consulte el archivo INSTALL que se incluye en el archivo de origen.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0928_2016-->