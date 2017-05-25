---
title: "Introducción a Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: "Introducción a Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 63f1c468b5f8f4b0bb298cb67adea8c01b065427
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017

---
# <a name="overview-of-azure-cloud-shell-preview"></a>Introducción a Azure Cloud Shell (versión preliminar)
Azure Cloud Shell es un shell interactivo, accesible desde el explorador, para administrar recursos de Azure.

![](media/startup.gif)

## <a name="features"></a>Características
### <a name="browser-based-shell-experience"></a>Experiencia de shell basada en explorador
Cloud Shell permite el acceso a una experiencia de línea de comandos basada en explorador compilada con las tareas de administración de Azure en mente. Aproveche Cloud Shell para trabajar sin restricción de un equipo local que una forma que solo puede proporcionar la nube.

### <a name="pre-configured-azure-workstation"></a>Estación de trabajo de Azure configurada previamente
Cloud Shell viene preinstalado con conocidas populares herramientas de línea de comandos y compatibilidad de idiomas para que pueda trabajar con mayor rapidez.

[Vea aquí la lista completa de herramientas para Azure Cloud Shell.](features.md#tools)

### <a name="automatic-authentication"></a>Autenticación automática
Cloud Shell autentica de forma segura y automática en cada sesión para obtener acceso inmediato a los recursos a través de la CLI de Azure 2.0.

### <a name="connect-your-azure-file-storage"></a>Conexión a Azure File Storage
Las máquinas de Cloud Shell son temporales y, como resultado, requieren que un recurso compartido de archivos de Azure se monte como `clouddrive` para conservar el directorio $Home.
Al iniciarse por primera vez, Cloud Shell le indica que va a crear un grupo de recursos, una cuenta de almacenamiento y un recurso compartido de archivos en su nombre. Esto es un paso único y se adjuntará automáticamente en todas las sesiones. 

![](media/storage-prompt.png)

Se crea una cuenta de almacenamiento LRS en su nombre con un recurso compartido de archivos de Azure que contiene una imagen de disco de 5 GB de forma predeterminada. El recurso compartido de archivos se monta como `clouddrive` para hacer posible la interacción del recurso compartido de archivos con la imagen de disco que se va a usar para sincronizar y hacer persistente el directorio $Home. Se aplican costos por almacenamiento normal.

Se crearán tres recursos en su nombre:
1. Grupo de recursos llamado: `cloud-shell-storage-<region>`
2. Cuenta de almacenamiento llamada: `cs-uniqueGuid`
3. Recurso compartido de archivos llamado: `cs-<user>-<domain>-com-uniqueGuid`

> [!Note]
> Todos los archivos en el directorio $Home, como las claves de SSH, se conservan en la imagen de disco de usuario almacenada en el recurso compartido de archivos montado. Ponga en práctica los procedimientos recomendados para guardar archivos en el directorio $Home y en el recurso compartido de archivos montado.

Obtenga más información sobre el almacenamiento de Cloud Shell, la actualización de recursos compartidos de archivos y la carga/descarga de archivos. (persisting-shell-storage.md).

## <a name="concepts"></a>Conceptos
* Cloud Shell se ejecuta en un equipo temporal proporcionado en cada sesión y por usuario
* Cloud Shell agota el tiempo de espera tras 10 minutos sin actividad interactiva.
* Solo se puede acceder a Cloud Shell mediante un recurso compartido de archivo adjuntado.
* Se asigna a Cloud Shell una máquina por cuenta de usuario.
* Los permisos se establecen como un usuario de Linux regular

[Más información sobre todas las características de Cloud Shell.](features.md)

## <a name="examples"></a>Ejemplos
* Creación o modificación de scripts para administrar recursos de Azure desde cualquier explorador
* Administración simultánea los recursos a través de Azure Portal y la CLI de Azure 2.0
* Prueba de la CLI de Azure 2.0

[Pruebe todos estos ejemplos en el tutorial rápido de Cloud Shell.](quickstart.md)

## <a name="pricing"></a>Precios
El equipo que hospeda Cloud Shell es gratis, con un requisito previo de un recurso compartido de archivos de Azure montados para conservar el directorio $Home. Se aplican costos por almacenamiento normal.

## <a name="supported-browsers"></a>Exploradores compatibles
Se recomienda Cloud Shell para Chrome, Edge y Safari. Aunque se admite Cloud Shell para Chrome, Firefox, Safari, Internet Explorer y Edge, Cloud Shell está sujeto a la configuración específica del explorador.
Para conocer las limitaciones conocidas específicas, consulte las [limitaciones de Cloud Shell](limitations.md).
