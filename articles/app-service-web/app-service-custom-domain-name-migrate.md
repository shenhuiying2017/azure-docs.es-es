---
title: "Migración de un dominio personalizado activo a Azure App Service | Microsoft Docs"
description: "Obtenga información sobre cómo migrar un dominio personalizado que ya esté asignado a un sitio activo a su aplicación de Azure App Service sin ningún tiempo de inactividad."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: aea3e1bae3695636ec0f9bb347dbe205f701684e
ms.openlocfilehash: 4f1b4c630cd1e676db4f8b512a8973ebee6f6eba
ms.lasthandoff: 02/16/2017


---
# <a name="migrate-an-active-custom-domain-to-azure-app-service"></a>Migración de un dominio personalizado activo a Azure App Service

En este artículo se explica cómo migrar un dominio personalizado activo a [Azure App Service](../app-service/app-service-value-prop-what-is.md) sin experimentar ningún tiempo de inactividad.

Al migrar un sitio activo y su nombre de dominio a App Service, ese nombre de dominio ya estará atendiendo tráfico real y no querrá que se produzcan tiempos de inactividad en la resolución DNS durante el proceso de migración. En este caso, debe enlazar de forma preferente el nombre de dominio a la aplicación de Azure para realizar la comprobación de dominio. 

## <a name="prerequisites"></a>Requisitos previos

En este artículo se da por supuesto que ya sabe cómo [asignar manualmente un dominio personalizado a App Service](web-sites-custom-domain-name.md).

## <a name="bind-the-domain-name-preemptively"></a>Enlace del nombre de dominio de forma preventiva

Al enlazar un dominio personalizado de forma preventiva, logrará lo siguiente antes de efectuar cualquier cambio en sus registros de DNS:

- Comprobar la propiedad del dominio
- Habilitar el nombre de dominio para la aplicación

Cuando por fin cambie el registro de DNS para que apunte a su aplicación de App Service, a los clientes se les redirige de su sitio antiguo a la aplicación de App Service sin experimentar ningún tiempo de inactividad en la resolución del DNS.

Siga estos pasos:

1. Primero, cree un registro TXT de comprobación con el registro DNS siguiendo los pasos descritos en [Creación de registros DNS](web-sites-custom-domain-name.md#createdns).
El registro TXT adicional adopta la convención que asigna desde &lt;*subdominio*>.&lt;*dominioraíz*> a &lt;*nombreaplicación*>.azurewebsites.net.
Para ver ejemplos, consulte la tabla siguiente:  
 
    <table cellspacing="0" border="1">
    <tr>
    <th>Ejemplo de FQDN</th>
    <th>Host TXT</th>
    <th>Valor TXT</th>
    </tr>
    <tr>
    <td>contoso.com (raíz)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>www.contoso.com (sub)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>\*.contoso.com (comodín)</td>
    <td>awverify.\*.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    </table>

2. A continuación, agregue el nombre de dominio personalizado a la aplicación de Azure siguiendo los pasos descritos en [Habilitación del nombre de dominio personalizado para la aplicación](web-sites-custom-domain-name.md#enable).

    El dominio personalizado ya estará habilitado en la aplicación de Azure. Lo único que queda por hacer es actualizar el registro de DNS con el registrador de dominios.

3. Finalmente, actualice el registro de DNS del dominio para que apunte a la aplicación de Azure tal y como se muestra en [Creación de registros DNS](web-sites-custom-domain-name.md#createdns). 

    El tráfico de usuarios debe redirigirse a la aplicación de Azure inmediatamente después de que se produzca la propagación de DNS.

## <a name="next-steps"></a>Pasos siguientes
Aprenda a proteger su nombre de dominio personalizado con HTTPS [comprando un certificado SSL en Azure](web-sites-purchase-ssl-web-site.md) o [usando un certificado SSL desde otro lugar](web-sites-configure-ssl-certificate.md).

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [App Service](https://azure.microsoft.com/try/app-service/), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

[Introducción a DNS de Azure](../dns/dns-getstarted-create-dnszone.md)  
[Creación de registros DNS para una aplicación web en un dominio personalizado](../dns/dns-web-sites-custom-domain.md)  
[Delegación de un dominio en DNS de Azure](../dns/dns-domain-delegation.md)


