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
ms.date: 11/16/2017
ms.author: juluk
ms.openlocfilehash: 08ab3b38e4c1fbeb1fac67c5d1b6f6749f7a0a3e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Introducción a Azure Cloud Shell
Azure Cloud Shell es un shell interactivo, accesible desde el explorador, para administrar recursos de Azure.
Ofrece la flexibilidad de poder elegir la experiencia de shell que mejor se adapte a su forma de trabajar.
Los usuarios de Linux pueden elegir una experiencia de Bash, mientras que los usuarios de Windows pueden optar por PowerShell.

Se puede iniciar a través de Azure Portal con el icono de Cloud Shell:

![Inicio en Azure Portal](media/overview/portal-launch-icon.png)

Seleccione Bash o PowerShell en el menú desplegable del selector de Shell:

![Bash en Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell en Cloud Shell (versión preliminar)](media/overview/overview-ps-pic.png)

## <a name="features"></a>Características
### <a name="browser-based-shell-experience"></a>Experiencia de shell basada en explorador
Cloud Shell permite el acceso a una experiencia de línea de comandos basada en explorador compilada con las tareas de administración de Azure en mente.
Aproveche Cloud Shell para trabajar sin restricción de un equipo local que una forma que solo puede proporcionar la nube.

### <a name="choice-of-preferred-shell-experience"></a>Elección de la experiencia de shell de su preferencia
Azure Cloud Shell ofrece la flexibilidad de poder elegir la experiencia de shell que mejor se adapte a su forma de trabajar.
Los usuarios de Linux pueden optar por Bash en Cloud Shell, mientras que los usuarios de Windows pueden optar por PowerShell en Cloud Shell (versión preliminar).

### <a name="authenticated-and-configured-azure-workstation"></a>Estación de trabajo de Azure autenticada y configurada
Microsoft administra Cloud Shell, de modo que viene preinstalado con herramientas populares de línea de comandos y compatibilidad de idiomas para que pueda trabajar con mayor rapidez. Además, Cloud Shell se autentica de forma segura y automática para obtener acceso inmediato a los recursos a través de la CLI de Azure 2.0 o de los cmdlets de Azure PowerShell.

Vea la lista completa de herramientas para la [experiencia de Bash](features.md#tools) y la [experiencia de PowerShell (versión preliminar).](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Varios puntos de acceso
Además de estar disponible desde Azure Portal, también se puede acceder a Cloud Shell desde:
* [Documentación de "Pruébelo" de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)
* [Aplicación móvil de Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extensión de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-azure-files-storage"></a>Conexión al almacenamiento de Azure Files
Las máquinas de Cloud Shell son temporales y, como resultado, requieren que un recurso compartido de Azure Files se monte como `clouddrive` para conservar el directorio $Home.
Al iniciarse por primera vez, Cloud Shell le indica que va a crear un grupo de recursos, una cuenta de almacenamiento y un recurso compartido de archivos en su nombre. Esto es un paso único y se adjuntará automáticamente en todas las sesiones. Se puede asignar un recurso compartido de archivos único que Bash y PowerShell usarán en Cloud Shell (versión preliminar).

#### <a name="create-new-storage"></a>creación de nuevo almacenamiento
![](media/overview/basic-storage.png)

Se pueden crear una cuenta de almacenamiento con redundancia local (LRS) y un recurso compartido de Azure Files en su nombre. El recurso compartido de Azure Files se usará para entornos de Bash y PowerShell, si decide usar ambos. Se aplican costos por almacenamiento normal.

Se crearán tres recursos en su nombre:
1. Grupo de recursos llamado: `cloud-shell-storage-<region>`
2. Cuenta de almacenamiento llamada: `cs<uniqueGuid>`
3. Recurso compartido de archivos llamado: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash en Cloud Shell también crea una imagen de disco de 5 GB para conservar `$Home`. Todos los archivos en el directorio $Home, como las claves de SSH, se conservan en la imagen de disco de usuario almacenada en el recurso compartido de archivos montado. Ponga en práctica los procedimientos recomendados para guardar archivos en el directorio $Home y en el recurso compartido de archivos montado.

#### <a name="use-existing-resources"></a>Uso de recursos existentes
![](media/overview/advanced-storage.png)

Se ofrece una opción avanzada que permite asociar los recursos existentes a Cloud Shell.
En el mensaje del programa de instalación, haga clic en "Mostrar configuración avanzada" para ver otras opciones.
Las listas desplegables se filtran para las cuentas de almacenamiento redundante local o globalmente y para la región asignada de Cloud Shell.

[Obtenga más información sobre el almacenamiento de Cloud Shell, la actualización de recursos de archivos compartidos y la carga o descarga de archivos](persisting-shell-storage.md).

## <a name="concepts"></a>Conceptos
* Cloud Shell se ejecuta en un host temporal que se proporciona por cada sesión y usuario.
* Cloud Shell agota el tiempo de espera tras 20 minutos sin actividad interactiva.
* Cloud Shell requiere montar un recurso compartido de archivos.
* Cloud Shell usa el mismo recurso compartido de archivos para Bash y para PowerShell.
* Se asigna a Cloud Shell una máquina por cuenta de usuario.
* Los permisos se establecen como usuario de Linux normal en Bash.

Obtenga más información sobre las características en [Bash en Cloud Shell](features.md) y [PowerShell en Cloud Shell (versión preliminar)](features-powershell.md).

## <a name="examples"></a>Ejemplos
* Uso de scripts para automatizar las tareas de administración de Azure
* Administración simultánea de los recursos de Azure mediante Azure Portal y las herramientas de línea de comandos de Azure
* Prueba de la CLI de Azure 2.0 o los cmdlets de Azure PowerShell

Pruebe estos ejemplos en las guías de inicio rápido para [Bash en Cloud Shell](quickstart.md) y [PowerShell en Cloud Shell (versión preliminar)](quickstart-powershell.md).

## <a name="pricing"></a>Precios
La máquina que hospeda Cloud Shell es gratis, con un requisito previo de un recurso compartido de Azure Files montado. Se aplican costos por almacenamiento normal.

## <a name="next-steps"></a>Pasos siguientes
[Guía de inicio rápido de Bash en Cloud Shell](quickstart.md) <br>
[Guía de inicio rápido de PowerShell en Cloud Shell (versión preliminar)](quickstart-powershell.md)