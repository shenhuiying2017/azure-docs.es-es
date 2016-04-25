<properties
	pageTitle="Instalación de la interfaz de la línea de comandos de Azure | Microsoft Azure"
	description="Instalación de la interfaz de la línea de comandos de Azure para Mac, Linux y Windows para comenzar a utilizar los servicios de Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="danlep"/>

# Instalación de la CLI de Azure

Instale rápidamente la interfaz de la línea de comandos de Azure (CLI de Azure) para utilizar un conjunto de comandos de código abierto basados en shell para crear y administrar recursos en Microsoft Azure. Cuenta con varias opciones de instalación: utilizar uno de los paquetes de instalación proporcionados para diferentes sistemas operativos, instalarla desde un paquete de NPM o instalarla como contenedor en un host de Docker. Si desea obtener más opciones y antecedentes, consulte el repositorio del proyecto en [GitHub](https://github.com/azure/azure-xplat-cli).


Una vez que instale la CLI de Azure, podrá [conectarla con su suscripción a Azure](xplat-cli-connect.md) y ejecutar los comandos **azure** desde la interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema, etc.) para trabajar con los recursos de Azure.


## Uso de un instalador

Están disponibles los siguientes paquetes del instalador:

* [Windows installer][windows-installer]

* [Instalador de OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Instalador de Linux][linux-installer]


## Instalación de un paquete de NPM

De manera alternativa, si ya ha instalado las versiones más recientes de Node.js y NPM, utilice el comando siguiente para instalar el paquete de CLI de Azure. (en distribuciones Linux, puede que tenga que usar **sudo** para ejecutar correctamente el comando __npm__).

	npm install azure-cli -g

> [AZURE.NOTE]Si necesita instalar o actualizar Node.js y NPM para su sistema operativo, consulte la documentación en [Nodejs.org](https://nodejs.org/en/download/package-manager/). Se recomienda que instale la versión más reciente de Node.js LTS (4.x). Si utiliza una versión anterior, podrían producirse errores de instalación.


## Uso de un contenedor Docker

En un host de Docker, ejecute:

```
docker run -it microsoft/azure-cli
```

## Ejecución de los comandos de la CLI de Azure
Una vez instalada la CLI de Azure, podrá usar el comando **azure** desde su interfaz de usuario de la línea de comandos (Bash, Terminal, símbolo del sistema, etc.). Por ejemplo, si desea ejecutar el comando help, escriba lo siguiente:

```
azure help
```

Para saber qué versión de la CLI de Azure instaló, escriba lo siguiente:

```
azure --version
```

De este modo, ya está listo. Para acceder a todos los comandos de la CLI con el fin de trabajar con sus propios recursos, [conéctese a su suscripción a Azure desde la CLI de Azure](xplat-cli-connect.md).


## Actualización de la CLI

Microsoft publica con frecuencia versiones actualizadas de la CLI de Azure. Reinstale la CLI con el instalador correspondiente a su sistema operativo, o bien, si las versiones más recientes de Node.js y NPM están instaladas, escriba lo siguiente para actualizarla (en las distribuciones de Linux, es posible que necesite utilizar **sudo**).

```
npm update -g azure-cli
```

## Pasos siguientes 

* [Conectarse desde la CLI a su suscripción de Azure](xplat-cli-connect.md) para crear y administrar recursos de Azure

* Si desea obtener más información acerca de la CLI de Azure, descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).

* Si tiene preguntas sobre cómo utilizar la CLI de Azure o Azure, visite los [foros de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=azurescripting).

* Para los sistemas Linux, también puede instalar la CLI de Azure creándola desde el [origen](http://aka.ms/linux-azure-cli). Para obtener más información sobre la creación a partir del origen, consulte el archivo INSTALL que se incluye en el archivo de origen.

[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0413_2016-->