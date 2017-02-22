---
title: "Información acerca las opciones de soporte técnico de Azure Service Fabric | Microsoft Docs"
description: "Versiones de clúster de Azure Service Fabric compatibles y vínculos para presentar incidencias de soporte técnico."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: ca1ff55fa0ad4b7466b900c8c36386649d021909
ms.openlocfilehash: 9d71fb0e92f58771dac989a4d83a8e314c6a52b6


---
# <a name="azure-service-fabric-support-options"></a>Opciones de soporte técnico de Azure Service Fabric

Hay varias opciones configuradas para usted con el fin de brindar el soporte técnico adecuado para los clústeres de Service Fabric en los que ejecuta las cargas de trabajo de la aplicación. Tiene la posibilidad de elegir las opciones correctas según el nivel del soporte técnico que se necesita y la gravedad del problema. 


<a id="getlivesitesupportonazure"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-azure"></a>Notifique problemas en el entorno de producción o con el sitio activo, o bien solicite soporte técnico de pago para Azure

Si desea informar sobre problemas con el sitio activo en el clúster de Service Fabric que implementó en Azure, abra una incidencia de soporte técnico profesional [en Azure portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) o en el [Portal de soporte técnico de Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Más información sobre:
 
- [Soporte técnico profesional de Microsoft para Azure](https://azure.microsoft.com/en-us/support/plans/?b=16.44).
- [Soporte técnico Premier de Microsoft](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Notifique problemas en el entorno de producción o con el sitio activo, o bien solicite soporte técnico de pago para clústeres independientes de Service Fabric

Si desea informar sobre problemas con el sitio activo en el clúster de Service Fabric que implementó de forma local o en otras nubes, abra una incidencia de soporte técnico profesional en el [Portal de soporte técnico de Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Más información sobre:

- [Soporte técnico profesional para Microsoft local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Soporte técnico Premier de Microsoft](https://support.microsoft.com/en-us/premier).


<a id="getsupportonissues"></a>
## <a name="report-azure-service-fabric-issues"></a>Información de problemas con Azure Service Fabric 
Configuramos un repositorio de GitHub para informar problemas con Service Fabric.  También supervisamos activamente los foros siguientes.

### <a name="github-repo"></a>Repositorio de GitHub 
Informe problemas con Azure Service Fabric en el [repositorio de Git Service-Fabric-issues](https://github.com/Azure/service-fabric-issues). Este repositorio está diseñado para informar y rastrear problemas con Azure Service Fabric y para generar solicitudes de funciones pequeñas. **No lo use para informar problemas con el sitio activo**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow y foros de MSDN

La [etiqueta Service Fabric en StackOverflow][stackoverflow] y el [foro de Service Fabric en MSDN][msdn-forum] son ideales para hacer preguntas sobre cómo funciona la plataforma y cómo podría realizar ciertas tareas con ella.

### <a name="azure-feedback-forum"></a>Foro de comentarios de Azure

El [foro de comentarios de Azure para Service Fabric][uservoice-forum] es el lugar ideal para enviar ideas sobre características importantes que tenga para el producto, porque revisamos las solicitudes más populares que forman parte de nuestro planeamiento de mediano a largo plazo. Se recomienda buscar apoyo para sus sugerencias dentro de la comunidad.


<a id="releasesuport"></a>
## <a name="supported-service-fabric-versions"></a>Versiones admitidas de Service Fabric.

Asegúrese de que el clúster siempre ejecute una versión compatible de Service Fabric. Cuando anunciamos el lanzamiento de una nueva versión de Service Fabric, se marca la versión anterior para que finalice el soporte después de un mínimo de 60 días a partir de esa fecha. Las versiones nuevas se anuncian [en el blog del equipo de Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consulte los documentos siguientes para ver detalles sobre cómo hacer que el clúster ejecute una versión compatible de Service Fabric.

- [Actualización de la versión de Service Fabric en un clúster de Azure ](service-fabric-cluster-upgrade.md)
- [Actualización de la versión de Service Fabric en un clúster de servidores de Windows independiente ](service-fabric-cluster-upgrade-windows-server.md)
 
A continuación, aparece una lista con las versiones compatibles de Service Fabric y las fechas de finalización del soporte técnico.

| **Clúster de tiempo de ejecución de Service Fabric** | **Fecha de finalización de soporte técnico** |
| --- | --- |
| Todas las versiones de clústeres anteriores a 5.3.121 |20 de enero de 2017 |
| 5.3.121.* |24 de febrero de 2017 |
| 5.3.204.* |24 de febrero de 2017 |
| 5.3* |24 de febrero de 2017 |
| 5.4.*     |17 de abril de 2017     |
| 5.5.*     |Versión actual y, por lo tanto, sin fecha de finalización


## <a name="next-steps"></a>Pasos siguientes

- [Actualización de la versión de Service Fabric en un clúster de Azure ](service-fabric-cluster-upgrade.md)
- [Actualización de la versión de Service Fabric en un clúster de servidores de Windows independiente ](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples



<!--HONumber=Feb17_HO2-->


