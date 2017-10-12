---
title: "Introducción a Azure Cloud Shell (versión preliminar) | Microsoft Docs"
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
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 44d2ac2fd35621ab8cd8d7584744139ee3bab5c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-cloud-shell-preview"></a>Introducción a Azure Cloud Shell (versión preliminar)
Azure Cloud Shell es un shell interactivo, accesible desde el explorador, para administrar recursos de Azure.
Ofrece la flexibilidad de poder elegir la experiencia de shell que mejor se adapte a su forma de trabajar.
Los usuarios de Linux pueden elegir una experiencia de Bash, mientras que los usuarios de Windows pueden optar por PowerShell.

![Bash en Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell en Cloud Shell](media/overview/overview-ps-pic.png)

## <a name="features"></a>Características
### <a name="browser-based-shell-experience"></a>Experiencia de shell basada en explorador
Cloud Shell permite el acceso a una experiencia de línea de comandos basada en explorador compilada con las tareas de administración de Azure en mente.
Aproveche Cloud Shell para trabajar sin restricción de un equipo local que una forma que solo puede proporcionar la nube.

### <a name="choice-of-preferred-shell-experience"></a>Elección de la experiencia de shell de su preferencia
Azure Cloud Shell ofrece la flexibilidad de poder elegir la experiencia de shell que mejor se adapte a su forma de trabajar.
Los usuarios de Linux pueden elegir una experiencia de Bash, mientras que los usuarios de Windows pueden optar por PowerShell.

### <a name="pre-configured-azure-workstation"></a>Estación de trabajo de Azure configurada previamente
Cloud Shell viene preinstalado con conocidas populares herramientas de línea de comandos y compatibilidad de idiomas para que pueda trabajar con mayor rapidez.

Vea la lista completa de herramientas para la [experiencia de Bash](features.md#tools) y la [experiencia de PowerShell](features-powershell.md#tools).

### <a name="automatic-authentication"></a>Autenticación automática
Cloud Shell autentica de forma segura y automática en cada sesión para obtener acceso inmediato a los recursos a través de la CLI de Azure 2.0.

### <a name="connect-your-azure-file-storage"></a>Conexión a Azure File Storage
Las máquinas de Cloud Shell son temporales y, como resultado, requieren que un recurso compartido de archivos de Azure se monte como `clouddrive` para conservar el directorio $Home.
Al iniciarse por primera vez, Cloud Shell le indica que va a crear un grupo de recursos, una cuenta de almacenamiento y un recurso compartido de archivos en su nombre. Esto es un paso único y se adjuntará automáticamente en todas las sesiones. Se puede asignar un recurso de archivos único que Bash y PowerShell usarán en Cloud Shell.

#### <a name="create-new-storage"></a>creación de nuevo almacenamiento
![](media/overview/basic-storage.png)

Se pueden crear una cuenta de almacenamiento con redundancia local (LRS) y un recurso compartido de archivos de Azure en su nombre. El recurso compartido de archivos de Azure se usará para entornos de Bash y PowerShell, si decide usar ambos. Se aplican costos por almacenamiento normal.

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
* Cloud Shell se ejecuta en un equipo temporal proporcionado en cada sesión y por usuario
* Cloud Shell agota el tiempo de espera tras 20 minutos sin actividad interactiva.
* Solo se puede acceder a Cloud Shell mediante un recurso compartido de archivo adjuntado.
* Cloud Shell usa el mismo recurso compartido de archivos para Bash y para PowerShell
* Se asigna a Cloud Shell una máquina por cuenta de usuario.
* Los permisos se establecen como un usuario de Linux normal (Bash)

Obtenga más información sobre las características en [Bash en Cloud Shell](features.md) y [PowerShell en Cloud Shell](features-powershell.md).

## <a name="examples"></a>Ejemplos
* Uso de scripts para automatizar las tareas de administración de Azure
* Administración simultánea de los recursos de Azure mediante Azure Portal y las herramientas de línea de comandos de Azure
* Prueba de la CLI de Azure 2.0 o los cmdlets de Azure PowerShell

Pruebe estos ejemplos en las guías de inicio rápido para [Bash en Cloud Shell](quickstart.md) y [PowerShell en Cloud Shell](quickstart-powershell.md).

## <a name="pricing"></a>Precios
La máquina que hospeda Cloud Shell es gratis, con un requisito previo de un recurso compartido de archivos de Azure montado. Se aplican costos por almacenamiento normal.

## <a name="supported-browsers"></a>Exploradores compatibles
Se recomienda Cloud Shell para Chrome, Edge y Safari.
Aunque se admite Cloud Shell para Chrome, Firefox, Safari, Internet Explorer y Edge, Cloud Shell está sujeto a la configuración específica del explorador.