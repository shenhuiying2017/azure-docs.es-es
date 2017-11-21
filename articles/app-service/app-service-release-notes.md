---
title: "Notas de la versión de SDK de Azure para .NET 2.5.1."
description: "Notas de la versión de SDK de Azure para .NET 2.5.1."
services: app-service
documentationcenter: .net,nodejs,java
author: Juliako
manager: erikre
editor: 
ms.assetid: 8d3d815f-bb58-447e-8ff0-f9b9603c7b00
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/10/2016
ms.author: juliako
ms.openlocfilehash: 0a422b02623a18ac6a1eef460bbada681672e69f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="azure-sdk-for-net-251-release-notes"></a>Notas de la versión de SDK de Azure para .NET 2.5.1.
Este documento contiene las notas de la versión de SDK de Azure para la versión .NET 2.5.1. 

## <a name="azure-sdk-for-net-251-release-notes"></a>Notas de la versión de SDK de Azure para .NET 2.5.1.
A continuación se muestran características nuevas y actualizaciones en el SDK de Azure para .NET 2.5.1.

* Nuevas características o escenarios relacionados con **Extensiones de herramientas web**. 
  
  * Azure Websites ha cambiado el nombre a Azure App Service. 
  * Se ha agregado compatibilidad de aplicaciones de la API de Azure (vista previa) para que los clientes puedan publicar proyectos ASP.NET como aplicaciones de la API y, a continuación, usar el gesto Agregar > Cliente de aplicaciones de la API de Azure en C# para generar código basado en la estructura de la aplicación de la API implementada. 
  * El nodo de Websites en el Explorador de servidores se ha sustituido por el nodo de Azure App Service, que incluye compatibilidad para el conjunto basado en grupos de recursos de Azure API Apps, Mobile Apps y Web Apps.
  * Se ha agregado compatibilidad con Azure Mobile Apps (versión preliminar) para que los clientes pueden crear nuevos proyectos de aplicaciones móviles, agregar controladores de aplicaciones móviles, publicar proyectos y depurar las aplicaciones de forma remota.
  * El gesto Agregar > Cliente de aplicaciones de la API de Azure ahora admite archivos JSON de Swagger locales, por lo que los desarrolladores de la API web pueden utilizar NuGets de terceros como Swashbuckle para generar Swagger o crearlo manualmente. De este modo, los desarrolladores cliente pueden usar las características de generación de código para consumir cualquier extremo Swagger en proyectos de C#. 
  * Los cuadros de diálogo de publicación de aplicaciones de API y aplicaciones web se han mejorado para admitir el concepto de Azure Portal de agrupación de recursos, y la selección y creación de grupos de recursos de Azure y planes de App Service se representan en el nuevo cuadro de diálogo de aprovisionamiento de aplicaciones de API y aplicaciones web. 
  * Los nodos del Explorador de servidores de aplicaciones de API de Azure proporcionan vínculos al vínculo profundo de API Apps en Azure Portal, así como a otras características como el streaming de registros y la depuración remota.
    
    Para saber los problemas conocidos y las limitaciones actuales del SDK de Azure para .NET 2.5.1 consulte [esta](app-service-release-notes.md#known_issues_2_5_1) sección más adelante.
* Las nuevas características o escenarios relacionados con **Herramientas de HDInsight** en Visual Studio se encuentran habilitadas en esta versión. 
  
  * Validación local de los scripts de hive. Haga clic en el botón Validar script en la barra de herramientas para ver si hay errores en el script. 
  * Mejora de la depuración de los trabajos de Hive. Ahora puede depurar los trabajos de Hive mediante el acceso a los registros de hilo en Visual Studio. Si la aplicación tiene problemas de rendimiento, investigue los registros YARN para encontrar información útil.
  * (Vista previa pública) Finalización automática de palabra clave y compatibilidad con IntelliSense para Hive. Para ayudar a crear scripts de Hive, las herramientas de HDInsight para Visual Studio agregan la palabra clave de finalización automática y compatibilidad con IntelliSense para Hive.
  * Compatibilidad con Storm. Ahora puede usar herramientas de HDInsight para Visual Studio para desarrollar topologies/Spouts/Bolts de Storm en C#. A continuación, puede enviar la topología desarrollada a un clúster de Storm y ver el estado de topology/bolt/spout. Puede usar los registros del sistema y los registros de cliente para solucionar problemas de topologies/Bolts/Spouts de Storm. También puede utilizar los activos existentes de JAVA en Storm en HDInsight.
    
    Para obtener más información, consulte [Introducción al uso de las herramientas de Hadoop de HDInsight para Visual Studio](../hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a id="known_issues_2_5_1"></a>Problemas y limitaciones conocidos del SDK de Azure para .NET 2.5.1
* Azure API Apps es visible como un destino de implementación para Mobile Apps. Web Apps debe ser único destino para Mobile Apps hasta una versión posterior. 
* El aprovisionamiento de aplicaciones de la API de Azure puede ser correcto pero fallar intermitentemente al actualizar el progreso en la ventana de actividad de servicio de la aplicación de Azure. La solución consiste en comprobar el estado de la nueva aplicación de API de Azure en Azure Portal. 
* La experiencia de Archivo > Nuevo proyecto > Aplicación de la API > F5 produce un error HTTP porque no existe default/index.html. La solución es dirigirse manualmente a la dirección URL de /api/values. 
* De forma intermitente, los iconos del explorador de servidores aparecen planos. Si reinicia el VS se resuelve este problema. 
* Si se produce una excepción durante el aprovisionamiento de aplicaciones web o de la aplicación de la API (por ejemplo, errores de cuota excedida o nombre duplicado de puerta de enlace de aplicación de la API de Azure), los errores de mostrarán texto sin formato JSON. 
* Problemas intermitentes de la creación del proyecto cuando Application Insights se activa en el momento de creación del proyecto.
* En ocasiones, el código generado del cliente de aplicación de la API de Azure carece de espacios de nombres, deben incluirse manualmente (o importarse automáticamente a través de las indicaciones de Visual Studio) para compilar el código. 
* Los proyectos de aplicación móvil deben publicarse en aplicaciones web, pero debe elegir un sitio que haya creado como una aplicación móvil en Azure Portal ya que los proyectos de aplicaciones móviles requieren una base de datos. 
* La página de inicio de Mobile Apps utiliza el término "servicio móvil" en lugar de "aplicaciones móviles" 
* La creación de proyectos de aplicaciones móviles puede tardar hasta un minuto en crearse. 
* Durante el aprovisionamiento de la aplicación de la API (en algunos casos), se produce un error en la API de Azure que refleja que los permisos no podrían establecerse correctamente, mientras que la API de la aplicación se ha aprovisionado correctamente y está lista para su uso. Puede establecer manualmente los permisos mediante Azure Portal.
* Application Insights no es compatible con plantillas de aplicación de la API y plantillas de la aplicación móvil.
* Los proyectos de aplicación de la API no pueden usarse junto con los proyectos de servicio en la nube.
* Las plantillas de proyecto de la aplicación de la API solo están disponibles en C#.
* El consumo de la aplicación de la API a través del menú contextual "Agregar cliente de aplicación de la API de Azure" solo es compatible en C#.

