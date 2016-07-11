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
	ms.date="05/23/2016"
	ms.author="danlep"/>
    
# Instalación de la CLI de Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [CLI de Azure](xplat-cli-install.md)

Instale rápidamente la interfaz de la línea de comandos de Azure (CLI de Azure) para utilizar un conjunto de comandos de código abierto basados en shell para crear y administrar recursos en Microsoft Azure. Cuenta con varias opciones de instalación: realizarla desde un paquete de npm (para lo que se requieren Node.js y npm), utilizar uno de los paquetes del instalador proporcionados para diferentes sistemas operativos o instalar la CLI de Azure como un contenedor de un host de Docker. Si desea obtener más opciones y los antecedentes, consulte el repositorio del proyecto en [GitHub](https://github.com/azure/azure-xplat-cli).


Una vez que instale la CLI de Azure, podrá [conectarla con su suscripción a Azure](xplat-cli-connect.md) y ejecutar los comandos **azure** desde la interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema, etc.) para trabajar con sus recursos de Azure.



## Instalación de un paquete de NPM

Para instalar la CLI desde un paquete de npm, necesitará la versión más reciente de Node.js y npm instalada en su sistema. Tras ello, ejecute el comando siguiente para instalar el paquete del CLI de Azure: (En distribuciones de Linux, es posible que tenga que usar **sudo** para ejecutar correctamente el comando __npm__).

	npm install azure-cli -g

> [AZURE.NOTE]Si necesita instalar o actualizar Node.js y npm para su sistema operativo, consulte la documentación en [Nodejs.org](https://nodejs.org/en/download/package-manager/). Se recomienda que instale la versión más reciente de Node.js LTS (4.x). Si utiliza una versión anterior, podrían producirse errores de instalación.

## Uso de un instalador

También pueden descargarse los siguientes paquetes del instalador:


* [Instalador de OS X][mac-installer]

* [Windows installer][windows-installer]

* [Archivo tar de Linux][linux-installer] (requiere Node.js y npm): se instala ejecutando `sudo npm install -g <path to downloaded tar file>`


## Uso de un contenedor Docker

En un host de Docker, ejecute:

```
docker run -it microsoft/azure-cli
```

## Ejecución de los comandos de la CLI de Azure
Una vez instalada la CLI de Azure, podrá ejecutar el comando **azure** desde su interfaz de usuario de la línea de comandos (Bash, Terminal, símbolo del sistema, etc.). Por ejemplo, si desea ejecutar el comando help, escriba lo siguiente:

```
azure help
```
> [AZURE.NOTE]En algunas distribuciones de Linux puede aparecer un error, /usr/bin/env: ‘node’: No such file or directory, que proviene del hecho de que se hayan realizado instalaciones recientes de nodejs en /usr/bin/nodejs. Para corregir este error crear un vínculo simbólico a /usr/bin/node, para lo que debe ejecutar el comando siguiente

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Para saber qué versión de la CLI de Azure instaló, escriba lo siguiente:

```
azure --version
```

De este modo, ya está listo. Para acceder a todos los comandos de la CLI con el fin de trabajar con sus propios recursos, [conéctese a su suscripción a Azure desde la CLI de Azure](xplat-cli-connect.md).

>[AZURE.NOTE] La primera vez que use la CLI de Azure, versión 0.9.20 o posterior, verá un mensaje en el que se le pregunta si desea permitir que Microsoft recopile información acerca del uso de la CLI. La participación es voluntaria. Si elige participar, puede dejar de hacerlo cuando desee mediante la ejecución de `azure telemetry --disable`. Para habilitar la participación en cualquier momento, ejecute `azure telemetry --enable`.


## Actualización de la CLI

Microsoft publica con frecuencia versiones actualizadas de la CLI de Azure. Reinstale la CLI con el instalador correspondiente a su sistema operativo, o bien, si están instaladas las versiones más recientes de Node.js y npm, escriba lo siguiente para efectuar la actualización (en las distribuciones de Linux, es posible que necesite utilizar **sudo**).

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

* [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](xplat-cli-connect.md) para crear y administrar recursos de Azure

* Si desea obtener más información acerca de la CLI de Azure, descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).

* Si tiene preguntas acerca de cómo usar la CLI de Azure o sobre Azure, visite los [foros de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=azurescripting).

* Para los sistemas Linux, también puede instalar la CLI de Azure creándola desde el [origen](http://aka.ms/linux-azure-cli). Para obtener más información sobre la creación a partir del origen, consulte el archivo INSTALL que se incluye en el archivo de origen.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=windowsazurexplatcli&mode=new
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0629_2016-->