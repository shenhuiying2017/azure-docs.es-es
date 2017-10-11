---
title: "Configuración de un nombre de dominio personalizado para una aplicación web en Servicio de aplicaciones de Azure que usa el Administrador de tráfico para el equilibrio de carga."
description: "Use un nombre de dominio personalizado para un una aplicación web en el Servicio de aplicaciones de Azure que incluya el Administrador de tráfico para el equilibrio de carga."
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
ms.openlocfilehash: 5f099201d9018a6f8577cb3daf127d09560fb94b
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configuración de un nombre de dominio personalizado para una aplicación web en Servicio de aplicaciones de Azure utilizando el Administrador de tráfico
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

En este artículo se ofrecen instrucciones generales para usar un nombre de dominio personalizado con el Servicio de aplicaciones de Azure que usa el Administrador de tráfico para el equilibrio de carga.

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
> Si ha adquirido el dominio a través de Aplicaciones web del Servicio de aplicaciones de Azure, omita los pasos siguientes y consulte el paso final del artículo [Comprar dominio para Aplicaciones web](custom-dns-web-site-buydomains-web-app.md) .
> 
> 

Para asociar su dominio personalizado con una aplicación web del Servicio de aplicaciones de Azure, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador de dominios al que ha adquirido su nombre de dominio. Siga estos pasos para ubicar y utilizar las herramientas DNS.

1. Inicie sesión en su cuenta en el registrador de dominios y busque la página de administración de los registros DNS. Busque los vínculos o áreas del sitio etiquetados como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**. A menudo se puede encontrar un vínculo a esta página al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**.
2. Cuando haya encontrado la página de administración para su nombre de dominio, busque un vínculo que le permita modificar los registros DNS. Debe aparecer como **archivo Zona**, **Registros DNS** o como un vínculo de configuración de **Opciones avanzadas**.
   
   * Esta página seguramente mostrará algunos que ya se han creado, como una entrada en la que se asocia "**@**" o "\*" a una página donde figuran los dominios. Es posible también que contenga registros para los subdominios más comunes, como **www**.
   * Esta página mencionará los **registros CNAME**, o bien facilitará una lista desplegable donde podrá seleccionar un tipo de registro. También es posible que mencione otros registros, como los **registros A** y los **registros MX**. En algunos casos, los registros CNAME tendrán otros nombres, como **Registro de Alias**.
   * Esta página contendrá también campos que le permiten **asignar** desde un **nombre de host** o un **nombre de dominio** hasta otro nombre de dominio.
3. Aunque los detalles varían en función del registrador que se esté utilizando, en general se asigna *desde* el nombre del dominio personalizado (como **contoso.com**,) *hasta* el nombre de dominio de Traffic Manager (**contoso.trafficmanager.net**) usado para la aplicación web.
   
   > [!NOTE]
   > Como alternativa, si un registro ya se está usando y necesita enlazar de forma preferente sus aplicaciones a él, puede crear otro registro CNAME. Por ejemplo, para enlazar de forma preferente **www.contoso.com** a su aplicación web, cree un registro CNAME de **awverify.www** a **contoso.trafficmanager.net**. Después, puede agregar "www.contoso.com" a la aplicación web sin cambiar el registro CNAME de "www". Para más información, consulte [Creación de registros DNS para una aplicación web en un dominio personalizado][CREATEDNS].
   > 
   > 
4. Una vez que haya terminado de agregar o modificar registros DNS en su registrador, guarde los cambios.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Habilitación del Administrador de tráfico
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Pasos siguientes
Para más información, vea el [Centro para desarrolladores de Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
