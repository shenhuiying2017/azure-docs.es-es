---
title: "Información general sobre las conexiones híbridas | Microsoft Docs"
description: "Obtenga información acerca de las conexiones híbridas, la seguridad, los puertos TCP y las configuraciones admitidas. MABS, WABS."
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: 216e4927-6863-46e7-aa7c-77fec575c8a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 819af52bb10c9ffcb7e1133437f6d0afbe6105ae
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="hybrid-connections-overview"></a>Introducción a las conexiones híbridas

> [!IMPORTANT]
> Las Conexiones híbridas de BizTalk se han retirado y se han reemplazado por las Conexiones híbridas de App Service. Para obtener más información, incluida la forma de administrar las Conexiones híbridas de BizTalk existentes, consulte [Conexiones híbridas de Azure App Service](../app-service/app-service-hybrid-connections.md).

En la introducción a las conexiones híbridas, se muestran las configuraciones admitidas y se indican los puertos TCP requeridos.

## <a name="what-is-a-hybrid-connection"></a>¿Qué es una conexión híbrida?
Las conexiones híbridas son una característica de Azure BizTalk Services: Las conexiones híbridas proporcionan una manera fácil y cómoda de conectar la característica Web Apps en Azure App Service (denominado anteriormente Websites) y la característica Mobile Apps en Azure App Service (denominado anteriormente Mobile Services) con recursos locales protegidos por un firewall.

![conexiones híbridas][HCImage]

Las ventajas de las conexiones híbridas incluyen:

* Web Apps y Azure Mobile Apps pueden acceder a los datos y servicios locales existentes de forma segura.
* Varias aplicaciones web y aplicaciones móviles pueden compartir una conexión híbrida para acceder a un recurso local.
* Se requieren puertos TCP mínimos para acceder a la red.
* Las aplicaciones que usan conexiones híbridas solo acceden al recurso local específico que se publica a través de la conexión híbrida.
* Pueden conectarse a cualquier recurso local que use un puerto TCP estático, como SQL Server, MySQL, API Web HTTP y la mayoría de los servicios web personalizados.
  
  > [!NOTE]
  > Los servicios basados en TCP que usan puertos dinámicos (como modo pasivo FTP o modo pasivo extendido) no se admiten actualmente. Tampoco se admite LDAP. LDAP utiliza un puerto TCP estático, pero también podría usar UDP. Como resultado, no se admite.
  > 
  > 
* Se pueden usar con todos los marcos compatibles con Web Apps (.NET, PHP, Java, Python, Node.js) y Mobile Apps (Node.js, .NET).
* Las aplicaciones web y las aplicaciones móviles pueden acceder a los recursos locales igual que si se encontraran en la red local. Por ejemplo, la misma cadena de conexión usada en el entorno local se puede utilizar en Azure.

Las conexiones híbridas también ofrecen a los administradores de empresa control y visibilidad sobre los recursos corporativos a los que acceden las aplicaciones híbridas, por ejemplo:

* Mediante el uso de la configuración de directiva de grupo, los administradores pueden permitir conexiones híbridas en la red y designar también recursos a los que pueden acceder las aplicaciones híbridas.
* Los registros de eventos y de auditoría de la red corporativa proporcionan visibilidad sobre los recursos a los que acceden las conexiones híbridas.

## <a name="example-scenarios"></a>Escenarios de ejemplo
Las conexiones híbridas admiten las siguientes combinaciones de aplicaciones y marcos de trabajo:

* Acceso del marco de trabajo .NET a SQL Server
* Acceso del marco de trabajo .NET a los servicios HTTP/HTTPS con WebClient
* Acceso de PHP a SQL Server, MySQL
* Acceso de Java a SQL Server, MySQL y Oracle
* Acceso de Java a los servicios HTTP/HTTPS

A la hora de usar conexiones híbridas para acceder a SQL Server local, tenga en cuenta los siguientes aspectos:

* Las instancias con nombre de SQL Express se deben configurar para usar puertos estáticos. De forma predeterminada, las instancias con nombre de SQL Express usan puertos dinámicos.
* Las instancias predeterminadas de SQL Express usan un puerto estático, pero TCP debe estar habilitado. De forma predeterminada, TCP no está habilitado.
* Si se usan la agrupación en clústeres o los grupos de disponibilidad, el modo `MultiSubnetFailover=true` no se admite.
* Actualmente, el modo `ApplicationIntent=ReadOnly` no se admite.
* Puede que se requiera la autenticación de SQL como método de autorización completo que se admite en la aplicación de Azure y en el servidor SQL local.

## <a name="security-and-ports"></a>Seguridad y puertos
Las conexiones híbridas emplean la autorización de firma de acceso compartido (SAS) para proteger las conexiones entre las aplicaciones de Azure y el administrador de conexiones híbridas local y la conexión híbrida. Se crean claves de conexión diferentes para la aplicación y el Administrador de conexiones híbridas local. Estas claves de conexión se pueden sustituir y revocar de manera independiente.

Las conexiones híbridas proporcionan una distribución adecuada y segura de las claves a las aplicaciones y al administrador de conexiones híbridas local.

Consulte [Creación y administración de conexiones híbridas](integration-hybrid-connection-create-manage.md).

*La autorización de la aplicación es independiente de la conexión híbrida*. Se puede usar cualquier método de autorización adecuado. El método de autorización depende de los métodos de autorización completos que se admitan en la nube de Azure y de los componentes locales. Por ejemplo, su aplicación de Azure accede a un servidor SQL local. En este escenario, la autorización de SQL puede ser el método de autorización que se admita completamente.

#### <a name="tcp-ports"></a>Puertos TCP
Las conexiones híbridas requieren únicamente conectividad TCP o HTTP saliente de su red privada. No es necesario abrir los puertos de firewall ni cambiar la configuración del perímetro de red para permitir la conectividad entrante a la red.

Los siguientes puertos TCP se usan en las conexiones híbridas:

| Port | Por qué es necesario |
| --- | --- |
| 9350 - 9354 |Estos puertos se usan para la transmisión de datos. El administrador de retransmisiones de Service Bus sondea el puerto 9350 para determinar si la conectividad TCP está disponible. Si lo está, asume que el puerto 9352 está también disponible. El tráfico de datos pasa por el puerto 9352. <br/><br/>Permitir conexiones salientes a estos puertos. |
| 5671 |Cuando se usa el puerto 9352 para el tráfico de datos, el puerto 5671 se usa como canal de control. <br/><br/>Permitir conexiones salientes a este puerto. |
| 80, 443 |Estos puertos se usan para algunas solicitudes de datos en Azure. Además, si los puertos 9352 y 5671 no se pueden usar, *los* puertos 80 y 443 son los puertos de reserva que se usan para la transmisión de datos y el canal de control.<br/><br/>Permitir conexiones salientes a estos puertos. <br/><br/>**Nota** : No es recomendable usar estos elementos como puertos de reserva en lugar de los demás puertos TCP. HTTP/WebSocket se utiliza como protocolo, en lugar del TCP nativo, para los canales de datos. Podría provocar un rendimiento menor. |

## <a name="next-steps"></a>Pasos siguientes
[Create and manage Hybrid Connections](integration-hybrid-connection-create-manage.md)

## <a name="see-also"></a>Otras referencias
[API REST para administrar BizTalk Services en Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk Services: Gráfico de ediciones](biztalk-editions-feature-chart.md)  
[Creación de un servicio de BizTalk](biztalk-provision-services.md)  
[BizTalk Services: pestañas Panel, Monitor y Escala](biztalk-dashboard-monitor-scale-tabs.md)  

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
