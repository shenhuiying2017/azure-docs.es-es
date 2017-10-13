---
title: "Notas de la versión de SDK de Azure para .NET 3.0 | Microsoft Docs"
description: "Notas de la versión de SDK de Azure para .NET 3.0"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/07/2017
ms.author: juliako
ms.openlocfilehash: eea4e569ac2d0192ed7872d2fcb9bed03614832b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-30-release-notes"></a>Notas de la versión de SDK de Azure para .NET 3.0

Este tema incluye las notas de la versión para la versión 3.0 del SDK de Azure para. NET.

##<a name="azure-sdk-for-net-30-release-summary"></a>Resumen de la versión de SDK de Azure para .NET 3.0

Fecha de lanzamiento: 07/03/2017
 
En esta versión no se realizaron cambios importantes en el SDK de Azure 3.0. Tampoco es necesario ningún proceso de actualización para aprovechar este SDK con proyectos de Cloud Services existentes. Para permitir el uso de Azure SDK 3.0 sin necesidad de emplear un proceso de actualización, se instala en los mismos directorios que Azure SDK 2.9. La mayoría de los componentes no cambiaron la versión principal desde la 2.9, pero actualizaron el número de compilación.

## <a name="visual-studio-2017-rtw"></a>Visual Studio 2017 RTW

- En Visual Studio 2017, esta versión del SDK de Azure para .NET se basa en la carga de trabajo de Azure. Todas las herramientas que necesita para el desarrollo de Azure formarán parte de Visual Studio de 2017 y las versiones posteriores. Para Visual Studio 2015, el SDK estará disponible a través de WebPI. Se ha suspendido el SDK de Azure para las versiones de .NET para Visual Studio 2013 ahora que se publicado Visual Studio 2017.

### <a name="azure-diagnostics"></a>Diagnóstico de Azure

- Ha cambiado el comportamiento para almacenar solo una cadena de conexión parcial con la clave sustituida por un token de cadena de conexión de almacenamiento de diagnóstico de Cloud Services. La clave de almacenamiento real ahora se almacena en la carpeta del perfil de usuario para poder controlar el acceso. Visual Studio leerá la clave de almacenamiento de la carpeta del perfil de usuario para los procesos de publicación y depuración local. 
- En respuesta al cambio descrito anteriormente, el equipo de Visual Studio Online ha mejorado la plantilla de tareas de implementación de Azure Cloud Services para que los usuarios puedan especificar la clave de almacenamiento para configurar la extensión de diagnósticos al publicar en Azure en la integración e implementación continuas.
- Ahora es posible almacenar la cadena de conexión segura y la tokenización para diagnósticos de Azure (WAD), para ayudarle a solucionar problemas con la configuración entre entornos.
 
### <a name="windows-server-2016-virtual-machines"></a>Máquinas virtuales Windows Server 2016

- Visual Studio ahora admite la implementación de Cloud Services en máquinas virtuales con la familia del SO 5 (Windows Server 2016). Para servicios en la nube existentes, puede cambiar la configuración para la nueva familia del SO. Al crear servicios en la nube, si decide crear el servicio con .Net 4.6 o posterior, el servicio usará la familia del SO 5 de forma predeterminada.  Para más información, puede consultar la tabla de [compatibilidad de la familia del SO invitado](../cloud-services/cloud-services-guestos-update-matrix.md).

### <a name="known-issues"></a>Problemas conocidos

- El SDK de Azure para .NET 3.0 provoca un problema cuando se quita Visual Studio 2017 en una configuración en paralelo con Visual Studio 2015.  Si tiene el SDK de Azure instalado para Visual Studio 2015, el Emulador de Microsoft Azure Storage y el Emulador de proceso de Microsoft Azure se quitarán si desinstala Visual Studio 2017.  Esto generará un error al crear y depurar nuevos proyectos de servicios en la nube en Visual Studio 2015. Para corregir este problema, vuelva a instalar el SDK de Azure desde el instalador de plataforma web.  El problema se solucionará en una futura actualización de Visual Studio 2017.  .

 
### <a name="azure-in-role-cache"></a>Caché en rol de Azure 

- La compatibilidad con Azure In-Role Cache finalizó el 30 de noviembre de 2016. Para más detalles, haga clic [aquí](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).




