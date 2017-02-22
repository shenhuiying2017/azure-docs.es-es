---
title: Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes
description: "Se explica cómo el nuevo Servicio de aplicaciones de Azure y sus características afectan a los servicios ya existentes de Azure."
services: app-service
documentationcenter: 
author: yochay
manager: nirma
editor: 
ms.assetid: 86c6a292-3c33-49f4-890c-89cc0321b397
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2016
ms.author: yochaykk
translationtype: Human Translation
ms.sourcegitcommit: 75d42f41e6218d83cda00f2ef8926d6ca1f0aacd
ms.openlocfilehash: 8f458884d0ea649dffa5f92fd459bbaaa9f5e4cf


---
# <a name="azure-app-service-and-existing-azure-services"></a>Servicio de aplicaciones de Azure y los servicios de Azure existentes
En este artículo se describen las modificaciones de los servicios de Azure ya existentes como parte del cambio para reunir varios servicios de Azure en el [Servicio de aplicaciones de Azure](https://azure.microsoft.com/services/app-service/), una nueva oferta integrada.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>Información general
[Servicio de aplicaciones de Azure](https://azure.microsoft.com/services/app-service/) es un servicio en la nube nuevo y exclusivo que permite a los desarrolladores crear aplicaciones web y móviles destinadas a cualquier plataforma y dispositivo. Se trata de una solución integral diseñada para simplificar las funciones de codificación repetitivas, integrarse en sistemas Saas y empresariales y automatizar los procesos de negocio a la vez que cumplir sus necesidades de seguridad, confiabilidad y escalabilidad.

App Service reúne los siguientes servicios de Azure existentes ([Websites](https://azure.microsoft.com/services/websites/), [Mobile Services](https://azure.microsoft.com/services/mobile-services/) y [BizTalk Services](https://azure.microsoft.com/services/biztalk-services/)) en un único servicio único combinado que, a su vez, agrega nuevas funcionalidades eficaces.  Además, permite hospedar los siguientes tipos de aplicaciones:

* Aplicaciones web
* Mobile Apps
* API Apps
* Aplicaciones lógicas

En la tabla siguiente se explica cómo se asignan los servicios de Azure existentes al Servicio de aplicaciones y los tipos de aplicaciones disponibles en este.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Servicio de Azure existente</th>
<th align="left", style="width:10%">Servicio de aplicaciones de Azure</th>
<th align="left", style="width:80%">Qué cambia</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Sitios web Azure</td>
<td align="left">Web Apps</td>
<td align="left"><li>En el caso de Azure Websites, App Service se limita estrictamente a cambiar el nombre de Websites por Web Apps.
<p><li>Todas las instancias existentes de Sitios web serán ahora Aplicaciones web en el Servicio de aplicaciones.</p>
<p><li>Puede tener acceso a los sitios web existentes a través de <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Azure Portal</a>, donde todos los sitios existentes se encuentran en <em>Web Apps</em>.</p>
<p><li><em>Plan de hospedaje web</em> es ahora <em>Plan de App Service</em>. Un <em>plan de App Service</em> puede hospedar cualquier tipo de aplicación de App Service, como Web Apps, Mobile Apps, Logic Apps o API Apps.</p>
<p><li>Aplicaciones web del Servicio de aplicaciones de Azure tiene ahora disponibilidad general.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Más información acerca de Web Apps</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Servicios móviles de Azure</td>
<td align="left">Aplicaciones móviles</td>
<td align="left"><p><li>Servicios móviles sigue estando disponible como servicio independiente con plena compatibilidad.</p>
<p><li>Aplicaciones móviles es un tipo de aplicación del servicio de aplicaciones, que integra toda la funcionalidad de los servicios móviles y mucho más.</p>
<p><li>Es fácil <a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">migrar de Mobile Services a Mobile Apps</a>.</p>
<p><li>Como parte de App Service, Mobile Apps incluye nuevas funcionalidades, además de Mobile Services, como la integración con sistemas locales y SaaS, espacios de ensayo, WebJobs y opciones de escalado mejoradas, entre otras.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Más información acerca de Mobile Apps</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API Apps</td>
<td align="left">
<p><li>Aplicaciones de API es un nuevo tipo de aplicación del Servicio de aplicaciones que permite compilar y consumir API en la nube fácilmente.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Más información acerca de API Apps</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Logic Apps</td>
<td align="left">
<p><li>Aplicaciones lógicas es un nuevo tipo de aplicación de App Service que permite automatizar los procesos de negocio fácilmente.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Más información acerca de Logic Apps</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Servicios de BizTalk de Azure</td>
<td align="left">Aplicaciones de API de BizTalk</td>
<td align="left">
<li><p>Servicios de Biztalk sigue estando disponible como servicio independiente con plena compatibilidad.</p>
<li><p>Todas las funcionalidades de BizTalk Services se integran en App Service como API Apps, lo que permite a los usuarios realizar tareas de integración de aplicaciones empresariales y de integración B2B con cualquiera de los tipos de aplicaciones de App Service.</p>
<li><p>Ahora, con Logic Apps, puede automatizar los procesos de negocio a través de una experiencia de diseño visual para crear flujos de trabajo.</p></td>
</tr>
</tbody>
</table>

Para obtener más información, visite [Documentación del Servicio de aplicaciones](https://azure.microsoft.com/documentation/services/app-service/).




<!--HONumber=Dec16_HO1-->


