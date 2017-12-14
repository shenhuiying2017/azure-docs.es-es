---
title: "Información acerca las opciones de soporte técnico de Azure Service Fabric | Microsoft Docs"
description: "Versiones de clúster de Azure Service Fabric compatibles y vínculos para presentar incidencias de soporte técnico"
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2017
ms.author: pkc
ms.openlocfilehash: 0e4a2aa0ed7327a8ed19e9a716b0bd97abc71d5c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-service-fabric-support-options"></a>Opciones de soporte técnico de Azure Service Fabric

Hay varias opciones configuradas para usted con el fin de brindar el soporte técnico adecuado para los clústeres de Service Fabric en los que ejecuta las cargas de trabajo de la aplicación. Tiene la posibilidad de elegir las opciones correctas según el nivel del soporte técnico que se necesita y la gravedad del problema. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Notifique problemas en el entorno de producción o bien solicite soporte técnico de pago para Azure

Si desea informar sobre problemas en el clúster de Service Fabric que implementó en Azure, abra una incidencia de soporte técnico [en Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) o en el [Portal de soporte técnico de Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Más información sobre:
 
- [Soporte técnico de Microsoft para Azure](https://azure.microsoft.com/en-us/support/plans/?b=16.44).
- [Soporte técnico Premier de Microsoft](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Notifique problemas en el entorno de producción o bien solicite soporte técnico de pago para clústeres independientes de Service Fabric

Si desea informar sobre problemas en el clúster de Service Fabric que implementó de forma local o en otras nubes, abra una incidencia de soporte técnico profesional en el [Portal de soporte técnico de Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Más información sobre:

- [Soporte técnico profesional para Microsoft local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Soporte técnico Premier de Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Información de problemas con Azure Service Fabric 
Configuramos un repositorio de GitHub para informar problemas con Service Fabric.  También supervisamos activamente los foros siguientes.

### <a name="github-repo"></a>Repositorio de GitHub 
Informe problemas con Azure Service Fabric en el [repositorio de Git Service-Fabric-issues](https://github.com/Azure/service-fabric-issues). Este repositorio está diseñado para informar y rastrear problemas con Azure Service Fabric y para generar solicitudes de funciones pequeñas. **No lo use para informar problemas con el sitio activo**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow y foros de MSDN
La [etiqueta Service Fabric en StackOverflow][stackoverflow] y el [foro de Service Fabric en MSDN][msdn-forum] son ideales para hacer preguntas sobre cómo funciona la plataforma y cómo podría realizar ciertas tareas con ella.

### <a name="azure-feedback-forum"></a>Foro de comentarios de Azure
El [foro de comentarios de Azure para Service Fabric][uservoice-forum] es el lugar ideal para enviar ideas sobre características importantes que tenga para el producto, porque revisamos las solicitudes más populares que forman parte de nuestro planeamiento de mediano a largo plazo. Se recomienda buscar apoyo para sus sugerencias dentro de la comunidad.


## <a name="supported-service-fabric-versions"></a>Versiones admitidas de Service Fabric.

Asegúrese de que el clúster siempre ejecute una versión compatible de Service Fabric. Cuando anunciamos el lanzamiento de una nueva versión de Service Fabric, se marca la versión anterior para que finalice el soporte después de un mínimo de 60 días a partir de esa fecha. Las versiones nuevas se anuncian [en el blog del equipo de Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consulte los documentos siguientes para ver detalles sobre cómo hacer que el clúster ejecute una versión compatible de Service Fabric.

- [Actualización de la versión de Service Fabric en un clúster de Azure ](service-fabric-cluster-upgrade.md)
- [Actualización de la versión de Service Fabric en un clúster de servidores de Windows independiente ](service-fabric-cluster-upgrade-windows-server.md)
 
A continuación, aparece una lista con las versiones compatibles de Service Fabric y las fechas de finalización del soporte técnico.

| **Entorno de tiempo de ejecución de Service Fabric en el clúster** | **Se puede actualizar directamente desde la versión del clúster** |**Versiones del SDK o paquete de NuGet compatibles** | **Fecha de finalización de soporte técnico** |
| --- | --- |--- | --- |
| Todas las versiones de clústeres anteriores a 5.3.121 | 5.1.158* |Versión 2.3 o anterior |20 de enero de 2017 |
| 5.3* | 5.1.158.* |Versión 2.3 o anterior |24 de febrero de 2017 |
| 5.4.* | 5.1.158.* |Versión 2.4 o anterior |10 de mayo de 2017       |
| 5.5.* | 5.4.164.* |Versión 2.5 o anterior |10 de agosto de 2017    |
| 5.6.* | 5.4.164.* |Versión 2.6 o anterior |13 de octubre de 2017   |
| 5.7.* | 5.4.164.* |Versión 2.7 o anterior |15 de diciembre de 2017  |
| 6.0.* | 5.6.205.* |Versión menor o igual que la 2.8 |Versión actual y, por lo tanto, sin fecha de finalización |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versiones de vista previa de Service Fabric: no admitidas para su uso en producción.
De vez en cuando, publicamos versiones que tienen características importantes sobre las que deseamos saber su opinión, que se publican como versiones preliminares. Estas versiones preliminares solo se deben usar con fines de prueba. El clúster de producción debe estar ejecutando siempre una versión de Service Fabric compatible y estable. Una versión preliminar siempre comienza con un número de versión principal y secundaria de 255. Por ejemplo, si ve una versión 255.255.5703.949 de Service Fabric, esa versión es solo para su uso en clústeres de prueba y se encuentra en versión preliminar. Estas versiones preliminares también se anuncian en el [blog del equipo de Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) y se detallan en las características incluidas.

No hay ninguna opción de soporte técnico de pago para estas versiones preliminares. Use una de las opciones enumeradas en [Notificación de problemas Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) para formular preguntas o proporcionar comentarios.

## <a name="next-steps"></a>Pasos siguientes

- [Actualización de la versión de Service Fabric en un clúster de Azure ](service-fabric-cluster-upgrade.md)
- [Actualización de la versión de Service Fabric en un clúster de servidores de Windows independiente ](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
