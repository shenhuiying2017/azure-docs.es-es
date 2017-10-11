---
title: "Instalación de la CLI de Azure 1.0 | Microsoft Docs"
description: "Instalación de la CLI de Azure 1.0 para Mac, Linux y Windows con el objetivo de comenzar a utilizar los servicios de Azure"
editor: 
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: bdb776c8-7a76-4f3a-887c-236b4fffee10
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: rasquill
ms.openlocfilehash: 63b35ed25b809a16b61b685fd35aa67474b0a369
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="install-the-azure-cli-10"></a>Instalación de la CLI de Azure 1.0
> [!div class="op_single_selector"]
> * [PowerShell](/powershell/azure/overview)
> * [CLI de Azure 1.0](cli-install-nodejs.md)
> * [CLI de Azure 2.0](/cli/azure/install-azure-cli)

> [!IMPORTANT]
> En este tema se describe cómo instalar la CLI de Azure 1.0, que se integra en nodeJS y admite todas las llamadas a la API de implementación clásica, así como un gran número de actividades de implementación de Resource Manager. Debe utilizar la [CLI de Azure 2.0](/cli/azure/overview) para administrar o implementar CLI nuevas o futuras.

Instale rápidamente la interfaz de la línea de comandos de Azure (CLI de Azure 1.0) para utilizar un conjunto de comandos de código abierto basados en shell para crear y administrar recursos en Microsoft Azure. Tiene varias opciones para instalar estas herramientas multiplataforma en su equipo:

* **Paquete de npm** : ejecute npm (el administrador de paquetes de JavaScript) para instalar el paquete de la CLI de Azure 1.0 más reciente en su distribución de Linux o sistema operativo. Se requiere que node.js y npm estén instalados en su equipo.
* **Instalador** : descargue un instalador para facilitar la instalación en Mac o Windows.
* **Contenedor de Docker** : empiece usando la CLI más reciente en un contenedor de Docker listos para ejecutarse. Se requiere el host de Docker en su equipo.

Si desea obtener más opciones y los antecedentes, consulte el repositorio del proyecto en [GitHub](https://github.com/azure/azure-xplat-cli).

Una vez que instale la CLI de Azure 1.0, [conéctela con su suscripción a Azure](xplat-cli-connect.md) y ejecute los comandos **azure** desde la interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema, etc.) para trabajar con sus recursos de Azure.

## <a name="option-1-install-an-npm-package"></a>Opción 1: Instalación de un paquete de NPM
Para instalar la CLI desde un paquete de npm, necesita la versión [más reciente de Node.js y npm instalada en su sistema](https://nodejs.org/en/download/package-manager/). A continuación, ejecute **instalar npm** para instalar el paquete azure-cli:

```bash
npm install -g azure-cli
```

En distribuciones de Linux, es posible que tenga que usar **sudo** para ejecutar correctamente el comando **npm**, tal como se muestra a continuación:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Si necesita instalar o actualizar Node.js y npm en la distribución de Linux o el SO, recomendamos instalar la versión LTS de Node.js más reciente (4.x). Si utiliza una versión anterior, podrían producirse errores de instalación.

Si lo prefiere, descargue el [archivo .tar][linux-installer] de Linux más reciente para el paquete de npm localmente. Después, instale el paquete de npm descargado de la siguiente manera (en las distribuciones de Linux podría tener que usar **sudo**):

```bash
npm install -g <path to downloaded tar file>
```

## <a name="option-2-use-an-installer"></a>Opción 2: Uso de un instalador
Si utiliza un equipo Mac o Windows, los instaladores de la CLI siguientes están disponibles para descargarse:

* [Instalador de Mac OS X][mac-installer]
* [MSI de Windows][windows-installer]

> [!TIP]
> En Windows, también puede descargar el [Instalador de plataforma web](https://go.microsoft.com/?linkid=9828653) para instalar la CLI. Este instalador ofrece la opción de instalar otros Azure SDK y herramientas de línea de comandos después de instalar la CLI.

## <a name="option-3-use-a-docker-container"></a>Opción 3: Uso de un contenedor Docker
Si ha configurado un host de [Docker](https://docs.docker.com/engine/understanding-docker/), puede ejecutar la CLI de Azure 1.0 más reciente en un contenedor de Docker. Ejecute el comando siguiente (en las distribuciones de Linux, es posible que tenga que usar **sudo**):

```bash
docker run -it microsoft/azure-cli
```

## <a name="run-azure-cli-10-commands"></a>Ejecución de los comandos de la CLI de Azure 1.0
Una vez instalada la CLI de Azure 1.0, ejecute el comando **azure** desde su interfaz de usuario de la línea de comandos (Bash, Terminal, símbolo del sistema, etc.). Por ejemplo, si desea ejecutar el comando help, escriba lo siguiente:

```azurecli
azure help
```

> [!NOTE]
> En algunas distribuciones de Linux, puede recibir un error similar al `/usr/bin/env: ‘node’: No such file or directory`. Este error procede de las instalaciones recientes de Node.js que se instala en /usr/bin/nodejs. Para corregirlo, cree un vínculo simbólico a /usr/bin/node, para lo que debe ejecutar este comando:

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Para saber qué versión de la CLI de Azure 1.0 instaló, escriba lo siguiente:

```azurecli
azure --version
```

De este modo, ya está listo. Para obtener acceso a todos los comandos de la CLI para trabajar con sus propios recursos, [conéctese a su suscripción a Azure desde la CLI de Azure](xplat-cli-connect.md).

> [!NOTE]
> La primera vez que usa la CLI de Azure, ve un mensaje en el que se le pregunta si quiere permitir que Microsoft recopile información. La participación es voluntaria. Si elige participar, puede dejar de hacerlo cuando desee mediante la ejecución de `azure telemetry --disable`. Para habilitar la participación en cualquier momento, ejecute `azure telemetry --enable`.

## <a name="update-the-cli"></a>Actualización de la CLI
Microsoft publica con frecuencia versiones actualizadas de la CLI de Azure. Vuelva a instalar la CLI mediante el programa de instalación para su sistema operativo o ejecute el contenedor de Docker más reciente. Si tiene Node.js y npm más recientes instalados, escriba lo siguiente para efectuar la actualización (en las distribuciones de Linux, es posible que necesite utilizar **sudo**).

```bash
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Habilitación de la función de autocompletar
La función de autocompletar de los comandos de la CLI es compatible con Mac y Linux.

Para habilitarla en zsh, ejecute el siguiente código:

```bash
echo '. <(azure --completion)' >> .zshrc
```

Para habilitarla en Bash, ejecute el siguiente código:

```bash
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Pasos siguientes
* [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](xplat-cli-connect.md) para crear y administrar recursos de Azure.
* Si desea obtener más información acerca de la CLI de Azure, descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).
* Si tiene preguntas acerca de cómo usar la CLI de Azure o sobre Azure, visite los [foros de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).


[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: /cli/azure/get-started-with-az-cli2
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
