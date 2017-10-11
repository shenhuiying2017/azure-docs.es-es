---
title: "Notas de la versión de SDK de Azure para .NET 2.9"
description: "Notas de la versión de SDK de Azure para .NET 2.9"
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
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 199f0906f73d693d7cd4b73c928f23ae83b99596
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Notas de la versión de SDK de Azure para .NET 2.9

Este tema incluye notas de la versión para las versiones 2.9 y 2.9.6 del SDK de Azure para. NET.

##<a name="azure-sdk-for-net-296-release-summary"></a>Resumen de la versión de SDK de Azure para .NET 2.9.6.

Fecha de lanzamiento: 16/11/2016
 
En esta versión no se han hecho cambios importantes en el SDK de Azure 2.9. Tampoco es necesario ningún proceso de actualización para aprovechar este SDK con proyectos de Cloud Services existentes.

### <a name="visual-studio-2017-release-candidate"></a>Versión candidata para lanzamiento de Visual Studio 2017

- En Visual Studio de 2017 RC, esta versión del SDK de Azure para .NET se basa en la carga de trabajo de Azure. Todas las herramientas que necesita para que el desarrollo de Azure forme parte de Visual Studio de 2017 RC y las versiones posteriores. Para Visual Studio 2015 y Visual Studio 2013, el SDK estará disponible a través de WebPI. Cuando la versión de Visual Studio 2017 sea definitiva, se interrumpirán las versiones del SDK de Azure para .NET para Visual Studio 2013l. Siga este vínculo para descargar Visual Studio 2017 RC: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Diagnóstico de Azure

- Ha cambiado el comportamiento para almacenar solo una cadena de conexión parcial con la clave sustituida por un token de cadena de conexión de almacenamiento de diagnóstico de Cloud Services. La clave de almacenamiento real ahora se almacena en la carpeta del perfil de usuario para poder controlar el acceso. Visual Studio leerá la clave de almacenamiento de la carpeta del perfil de usuario para los procesos de publicación y depuración local. 
- En respuesta al cambio descrito anteriormente, el equipo de Visual Studio Online ha mejorado la plantilla de tareas de implementación de Azure Cloud Services para que los usuarios puedan especificar la clave de almacenamiento para configurar la extensión de diagnósticos al publicar en Azure en la integración e implementación continuas.
- Ahora es posible almacenar la cadena de conexión segura y la tokenización para diagnósticos de Azure (WAD), para ayudarle a solucionar problemas con la configuración entre entornos.
 
### <a name="windows-server-2016-virtual-machines"></a>Máquinas virtuales Windows Server 2016

- Visual Studio ahora admite la implementación de Cloud Services en máquinas virtuales con la familia del SO 5 (Windows Server 2016). Para servicios en la nube existentes, puede cambiar la configuración para la nueva familia del SO. Al crear servicios en la nube, si decide crear el servicio con .Net 4.6 o posterior, el servicio usará la familia del SO 5 de forma predeterminada.  Para más información, puede consultar la tabla de [compatibilidad de la familia del SO invitado](https://azure.microsoft.com/en-us/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Problemas conocidos

- El SDK de Azure .NET 2.9.6 introdujo una restricción que bloquea la implementación de proyectos con componentes .NET Framework no compatibles (por ejemplo, .NET 4.6) en cualquier familia del SO inferior a la 5. Se proporciona una solución alternativa [aquí](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Caché en rol de Azure 

- La compatibilidad con Azure In-Role Cache finaliza el 30 de noviembre de 2016. Para más detalles, haga clic [aquí](https://azure.microsoft.com/en-us/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Plantillas de Azure Resource Manager para Azure Stack

- Hemos introducido Azure Stack como un destino de implementación para las plantillas de Azure Resource Manager.


## <a name="azure-sdk-for-net-29-summary"></a>Resumen del SDK de Azure para .NET 2.9

## <a name="overview"></a>Información general
Este documento contiene las notas de la versión de SDK de Azure para la versión .NET 2.9. 

Para obtener información detallada sobre las actualizaciones de esta publicación, consulte [Announcing the Visual Studio Azure Tools and SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)(Presentación de Visual Studio Azure Tools y el SDK 2.9).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>SDK de Azure 2.9 para Visual Studio 2015 Update 2 y vista previa de Visual Studio "15"
Esta actualización incluye las correcciones de errores siguientes:

* Problema relacionado con la generación de clientes de la API de REST, en el que la cadena "Tipo desconocido" aparecía como nombre de la carpeta de generación de código o como nombre del espacio de nombres que se coloca en el código generado.
* Problema relacionado con WebJobs programados, en el que la información de autenticación no se transfería al proceso de aprovisionamiento del programador.

Esta actualización incluye la siguiente característica nueva:

* Compatibilidad con Servicios de aplicaciones secundarios en la pestaña "Servicios" del cuadro de diálogo de aprovisionamiento de Servicio de aplicaciones. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Azure Data Lake Tools para Visual Studio 2015 Update 2
Esta actualización incluye lo siguiente:

* **Azure Data Lake Tools** para Visual Studio se ha combinado en el SDK de Azure para .NET. La herramienta se instala automáticamente cuando se instala el SDK de Azure. 
  
    La herramienta se actualiza con frecuencia. Vaya [aquí](http://aka.ms/datalaketool) para obtener las actualizaciones.
* **Explorador de servidores** ahora le permite verlo todo y crear algunas entidades de metadatos U-SQL. Para más información, consulte [este blog](https://azure.microsoft.com/documentation/services/data-lake-analytics/) .

## <a name="hdinsight-tools"></a>Herramientas de HDInsight
**Herramientas de HDInsight** para Visual Studio ahora admite HDInsight versión 3.3, incluida la visualización de gráficos Tez y otras correcciones de lenguaje.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Esta versión agrega compatibilidad de [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) con plantillas de Resource Manager.

## <a name="see-also"></a>Otras referencias
[Announcing the Visual Studio Azure Tools and SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

