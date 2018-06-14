---
title: Implementación de un ejecutable invitado en Azure Service Fabric | Microsoft Docs
description: Obtenga información sobre cómo empaquetar una aplicación existente como ejecutable invitado para implementarla en un clúster de Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: mfussell
ms.openlocfilehash: cdaf3dae12c2c9da1f6bcbebbff560b98e62bade
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212847"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Implementación de un ejecutable existente en Service Fabric
Puede ejecutar cualquier tipo de código, como Node.js, Java o C++ en Azure Service Fabric como servicio. Service Fabric hace referencia a estos tipos de servicios como ejecutables invitados.

Los ejecutables invitados se tratan por Service Fabric como servicios sin estado. Por ello, se colocan en los nodos de un clúster, en función de la disponibilidad y otras métricas. En este artículo se describe cómo empaquetar e implementar un ejecutable invitado en un clúster de Service Fabric con Visual Studio o una utilidad de línea de comandos.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Ventajas de ejecutar un ejecutable invitado en Service Fabric
Hay diversas ventajas inherentes a la ejecución de un ejecutable invitado en un clúster de Service Fabric:

* Alta disponibilidad. Las aplicaciones que se ejecutan en Service Fabric se crean con alta disponibilidad. Service Fabric garantiza que se están ejecutando instancias de una aplicación.
* Supervisión del estado. La supervisión del mantenimiento de Service Fabric detecta si hay una aplicación en funcionamiento y proporciona información de diagnóstico si se produce un error.   
* Administración del ciclo de vida de las aplicaciones. Además de actualizaciones sin tiempo de inactividad, Service Fabric proporciona reversión automática a la versión anterior, en caso de que se notifique un evento de mantenimiento incorrecto durante una actualización.    
* Densidad. Se pueden ejecutar varias aplicaciones en el clúster, lo que elimina la necesidad de que cada aplicación se ejecute en su propio hardware.
* Detectabilidad: con REST puede llamar al servicio de nombres de Service Fabric para buscar otros servicios en el clúster. 

## <a name="samples"></a>Ejemplos
* [Ejemplo para empaquetar e implementar un archivo ejecutable invitado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ejemplo de dos ejecutables invitados (C# y Node.js) que se comunican a través del servicio de nombres con REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Información general de los archivos de manifiesto de servicio y aplicación
Como parte de la implementación de un ejecutable invitado, resulta útil comprender el modelo de implementación y empaquetado de Service Fabric tal como se describe en el [modelo de aplicación](service-fabric-application-model.md). El modelo de empaquetado de Service Fabric se basa en dos archivos XML: los manifiestos de aplicación y de servicio. La definición de esquema para los archivos ApplicationManifest.xml y ServiceManifest.xml se instala con el SDK de Service Fabric en *C:\Archivos de programa\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifiesto de aplicación** El manifiesto de aplicación se usa para describir la aplicación. Muestra en una lista los servicios que la componen junto con otros parámetros, como el número de instancias, que se usan para definir cómo se deben implementar uno o más servicios.

  En Service Fabric, una aplicación es una unidad de implementación y actualización. Una aplicación se puede actualizar como una sola unidad donde se administran los posibles errores y reversiones. Service Fabric garantiza que el proceso de actualización es correcto, o bien, si se produce un error en la actualización, no deja a la aplicación en un estado desconocido o inestable.
* **Manifiesto de servicio** El manifiesto de servicio describe los componentes de un servicio. Incluye datos, como el nombre y el tipo de servicio, así como su código y configuración. El manifiesto de servicio también incluye algunos parámetros adicionales que pueden usarse para configurar el servicio una vez que se implementa.

## <a name="application-package-file-structure"></a>Estructura de archivo del paquete de aplicación
Para implementar una aplicación en Service Fabric, la aplicación debe seguir una estructura de directorios predefinida. El siguiente es un ejemplo de esta estructura.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot contiene el archivo ApplicationManifest.xml que define la aplicación. Se usa un subdirectorio para cada servicio incluido en la aplicación para contener todos los artefactos que requiere el servicio. Estos subdirectorios son el archivo ServiceManifest.xml y, por lo general, los siguientes:

* *Code*. Este directorio contiene el código de servicio.
* *Config*. Este directorio contiene un archivo settings.xml (y otros archivos si es necesario) a los que el servicio puede tener acceso en tiempo de ejecución para recuperar la configuración específica.
* *Data*. Se trata de un directorio adicional para almacenar datos locales adicionales que el servicio puede necesitar. El directorio Data debe usarse para almacenar solamente datos efímeros. Service Fabric no copia ni replica los cambios en el directorio de datos si el servicio tiene que reubicarse (por ejemplo, durante una conmutación por error).

> [!NOTE]
> No tiene que crear los directorios `config` y `data` si no los necesita.
>
>

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes para obtener información relacionada y tareas.
* [Implementación de una aplicación ejecutable de invitado](service-fabric-deploy-existing-app.md)
* [Implementación de varios ejecutables invitados](service-fabric-deploy-multiple-apps.md)
* [Creación de la primera aplicación ejecutable invitada con Visual Studio](quickstart-guest-app.md)
* [Ejemplo para empaquetar e implementar un ejecutable invitado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), que incluye un vínculo a la versión preliminar de la herramienta de empaquetado
* [Ejemplo de dos ejecutables invitados (C# y Node.js) que se comunican a través del servicio de nombres con REST](https://github.com/Azure-Samples/service-fabric-containers)

