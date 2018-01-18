---
title: "Configuración de un nombre de dominio personalizado para una aplicación web en Azure App Service que usa el Administrador de tráfico para el equilibrio de carga."
description: "Use un nombre de dominio personalizado para un una aplicación web en Azure App Service que incluya el Administrador de tráfico para el equilibrio de carga."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: c78fb7883559e46ebaa1d8dab59a15c55fb76fdf
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configuración de un nombre de dominio personalizado para una aplicación web en Azure App Service utilizando el Administrador de tráfico
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

En este artículo se ofrecen instrucciones generales para usar un nombre de dominio personalizado con una aplicación [App Service](app-service-web-overview.md) que se integra con [Traffic Manager](../traffic-manager/traffic-manager-overview.md) para el equilibrio de carga.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Descripción de los registros DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Configuración de las aplicaciones web para el modo estándar
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Incorporación de un registro DNS para el dominio personalizado
> [!NOTE]
> Si ha adquirido el dominio a través de Azure App Service Web Apps, omita los pasos siguientes y consulte el paso final del artículo [Comprar dominio para Web Apps](custom-dns-web-site-buydomains-web-app.md).
> 
> 

Para asociar el dominio personalizado a una aplicación web de Azure App Service, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado. Para ello, use las herramientas de administración de su proveedor de dominio.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Aunque los detalles de cada proveedor de dominio varían, en general se asigna *desde* el nombre del dominio personalizado (como **contoso.com**) *al* nombre de dominio de Traffic Manager (**contoso.trafficmanager.net**) que se integra con la aplicación web.
   
> [!NOTE]
> Si un registro ya se está usando y necesita enlazar de forma preventiva sus aplicaciones a él, puede crear otro registro CNAME. Por ejemplo, para enlazar de forma preferente **www.contoso.com** a su aplicación web, cree un registro CNAME de **awverify.www** a **contoso.trafficmanager.net**. Después, puede agregar "www.contoso.com" a la aplicación web sin cambiar el registro CNAME de "www". Para más información, consulte [Creación de registros DNS para una aplicación web en un dominio personalizado][CREATEDNS].
> 
> 

Una vez que haya terminado de agregar o modificar registros DNS en su proveedor de dominio, guarde los cambios.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Habilitación del Administrador de tráfico
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>pasos siguientes
Para más información, vea el [Centro para desarrolladores de Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
