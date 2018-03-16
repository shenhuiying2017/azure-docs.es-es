---
title: "Introducción a Azure Cloud Shell | Microsoft Docs"
description: "Introducción a Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 72338a4c168b4d3b7c918fbb16758724f73fefc2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="overview-of-azure-cloud-shell"></a>Introducción a Azure Cloud Shell
Azure Cloud Shell es un shell interactivo, accesible desde el explorador, para administrar recursos de Azure.
Ofrece la flexibilidad de poder elegir la experiencia de shell que mejor se adapte a su forma de trabajar.
Los usuarios de Linux pueden elegir una experiencia de Bash, mientras que los usuarios de Windows pueden optar por PowerShell.

Pruebe desde shell.azure.com mediante este botón.

[![](https://shell.azure.com/images/launchcloudshell.png "Inicio de Azure Cloud Shell")](https://shell.azure.com)

Pruebe desde Azure Portal con el icono de Cloud Shell.

![Inicio en Azure Portal](media/overview/portal-launch-icon.png)

## <a name="features"></a>Características
### <a name="browser-based-shell-experience"></a>Experiencia de shell basada en explorador
Cloud Shell permite el acceso a una experiencia de línea de comandos basada en explorador compilada con las tareas de administración de Azure en mente.
Aproveche Cloud Shell para trabajar sin restricción de un equipo local que una forma que solo puede proporcionar la nube.

### <a name="choice-of-preferred-shell-experience"></a>Elección de la experiencia de shell de su preferencia
Los usuarios de Linux pueden usar Bash en Cloud Shell, mientras que los usuarios de Windows pueden utilizar PowerShell en Cloud Shell (versión preliminar) desde el menú desplegable del shell.

![Bash en Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell en Cloud Shell (versión preliminar)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Estación de trabajo de Azure autenticada y configurada
Microsoft administra Cloud Shell, por lo que incluye herramientas de línea de comandos populares y compatibilidad para distintos lenguajes. Cloud Shell también se autentica de forma segura y automática para obtener acceso inmediato a los recursos mediante la CLI de Azure 2.0 o de los cmdlets de Azure PowerShell.

Vea la lista completa de herramientas para la [experiencia de Bash](features.md#tools) y la [experiencia de PowerShell (versión preliminar).](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Varios puntos de acceso
Cloud Shell es una herramienta flexible que puede utilizarse desde:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentación de "Pruébelo" de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Aplicación móvil de Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extensión de la cuenta de Azure de VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Conexión con el almacenamiento de Microsoft Azure Files
Las máquinas de Cloud Shell son temporales y requieren que se monte un recurso compartido de Azure Files como `clouddrive` para conservar los archivos.

Al iniciarse por primera vez, Cloud Shell le indica que va a crear un grupo de recursos, una cuenta de almacenamiento y un recurso compartido de Azure Files en su nombre. Esto es un paso único y se adjuntará automáticamente en todas las sesiones. Se puede asignar un recurso compartido de archivos único que Bash y PowerShell usarán en Cloud Shell (versión preliminar).

#### <a name="create-new-storage"></a>creación de nuevo almacenamiento
![](media/overview/basic-storage.png)

Se pueden crear una cuenta de almacenamiento con redundancia local (LRS) y un recurso compartido de Azure Files en su nombre. El recurso compartido de Azure Files se usará para entornos de Bash y PowerShell, si decide usar ambos. Se aplican costos por almacenamiento normal.

Se crearán tres recursos en su nombre:
1. Grupo de recursos llamado: `cloud-shell-storage-<region>`
2. Cuenta de almacenamiento llamada: `cs<uniqueGuid>`
3. Recurso compartido de archivos llamado: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash en Cloud Shell también crea una imagen de disco de 5 GB para conservar `$Home`. Todos los archivos del directorio $Home, como las claves de SSH, se conservan en la imagen de disco de usuario almacenada en el recurso compartido de archivos de Azure montado. Ponga en práctica los procedimientos recomendados para guardar archivos en el directorio $Home y en el recurso compartido de archivos de Azure montado.

#### <a name="use-existing-resources"></a>Uso de recursos existentes
![](media/overview/advanced-storage.png)

Se ofrece una opción avanzada que permite asociar los recursos existentes a Cloud Shell.
En el mensaje del programa de instalación, haga clic en "Mostrar configuración avanzada" para ver otras opciones.

> [!Note]
> Las listas desplegables se filtran por la región de Cloud Shell previamente asignada y las cuentas de almacenamiento LRS, GRS y ZRS.

[Obtenga más información sobre el almacenamiento de Cloud Shell, la actualización de recursos de archivos de Azure compartidos y la carga o descarga de archivos](persisting-shell-storage.md).

## <a name="concepts"></a>Conceptos
* Cloud Shell se ejecuta en un host temporal que se proporciona por cada sesión y usuario.
* Cloud Shell agota el tiempo de espera tras 20 minutos sin actividad interactiva.
* Cloud Shell requiere montar un recurso compartido de archivos de Azure.
* Cloud Shell usa el mismo recurso compartido de archivos de Azure para Bash y para PowerShell.
* Se asigna a Cloud Shell una máquina por cuenta de usuario.
* Bash conserva $Home con una imagen de 5 GB en el recurso compartido de archivos
* Los permisos se establecen como usuario de Linux normal en Bash.

Obtenga más información sobre las características en [Bash en Cloud Shell](features.md) y [PowerShell en Cloud Shell (versión preliminar)](features-powershell.md).

## <a name="pricing"></a>Precios
La máquina que hospeda Cloud Shell es gratis, con un requisito previo de un recurso compartido de Azure Files montado. Se aplican costos por almacenamiento normal.

## <a name="next-steps"></a>Pasos siguientes
[Guía de inicio rápido de Bash en Cloud Shell](quickstart.md) <br>
[Guía de inicio rápido de PowerShell en Cloud Shell (versión preliminar)](quickstart-powershell.md)