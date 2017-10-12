---
title: "Migración de un nombre de DNS activo a Azure App Service | Microsoft Docs"
description: "Obtenga información sobre cómo migrar un nombre de dominio DNS personalizado que ya esté asignado a un sitio activo a Azure App Service sin ningún tiempo de inactividad."
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
ms.date: 06/28/2017
ms.author: cephalin
ms.openlocfilehash: a1fe545e4a341709232cba36c6e3cf3b4ce82e80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migración de un nombre de DNS activo a Azure App Service

En este artículo se explica cómo migrar un nombre DNS activo a [Azure App Service](../app-service/app-service-web-overview.md) sin experimentar ningún tiempo de inactividad.

Cuando se migra un sitio activo y su nombre de dominio DNS para App Service, ese nombre DNS ya está atendiendo a tráfico activo. Puede evitar tiempos de inactividad en la resolución DNS durante la migración enlazando el nombre DNS activo a la aplicación de App Service de forma preferente.

Si no le preocupa el tiempo de inactividad en la resolución DNS, consulte [Asignar un nombre DNS personalizado a Aplicaciones web de Azure](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Requisitos previos

Para completar este procedimiento:

- [Asegúrese de que la aplicación de App Service no esté en el nivel GRATIS](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Enlace del nombre de dominio de forma preventiva

Al enlazar un dominio personalizado de forma preventiva, logrará lo siguiente antes de efectuar cualquier cambio en sus registros de DNS:

- Comprobar la propiedad del dominio
- Habilitar el nombre de dominio para la aplicación

Cuando finalmente migre el nombre DNS personalizado del sitio antiguo a la aplicación de App Service, no habrá tiempo de espera en la resolución DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Creación de un registro de comprobación de dominio

Para comprobar la propiedad de dominio, agregue un registro TXT. El registro TXT se asigna desde _awverify.&lt; subdominio >_ a  _&lt;nombreaplic >. azurewebsites.net_. 

El registro TXT que necesita depende en el registro DNS que desea migrar. Para obtener ejemplos, vea la tabla siguiente (`@` normalmente representa el dominio raíz):  

| Ejemplo de registro DNS | Host TXT | Valor TXT |
| - | - | - |
| @ (raíz) | _awverify_ | _&lt;nombreaplic&gt;.azurewebsites.net_ |
| www (sub) | _awverify.www_ | _&lt;nombreaplic&gt;.azurewebsites.net_ |
| \* (comodín) | _awverify.\*_ | _&lt;nombreaplic&gt;.azurewebsites.net_ |

En la página de registros DNS, tenga en cuenta el tipo de registro del nombre DNS que desee migrar. App Service es compatible con las asignaciones de CNAME y registros A.

### <a name="enable-the-domain-for-your-app"></a>Habilitación del dominio para la aplicación

En [Azure Portal](https://portal.azure.com), en la navegación izquierda de la página de aplicaciones, seleccione **Dominios personalizados**. 

![Menú Dominio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

En la página **Dominios personalizados**, seleccione el icono **+** junto a **Agregar nombre de host**.

![Agregar nombre de host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escriba el nombre de dominio completo para el que ha agregado un registro TXT, como `www.contoso.com`. Para un dominio con caracteres comodín (como \*. contoso.com), puede utilizar cualquier nombre DNS que coincida con el dominio con caracteres comodín. 

Seleccione **Validar**.

Se activa el botón **Agregar nombre de host**. 

Asegúrese de que el **tipo de registro de nombre de host** se establece en el tipo de registro DNS que desea migrar.

Seleccione **Agregar nombre de host**.

![Agregar nombre DNS a la aplicación](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

El nuevo nombre de host puede tardar un tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.

![Registro CNAME agregado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

El nombre DNS personalizado ya estará habilitado en la aplicación de Azure. 

## <a name="remap-the-active-dns-name"></a>Reasignación del nombre DNS activo

Lo único que queda por hacer es reasignar el registro DNS activo para que señale a App Service. En este momento todavía señala a su sitio antiguo.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Copia de la dirección IP de la aplicación (solo registro A)

Si va a reasignar un registro CNAME, omita esta sección. 

Para reasignar un registro A, necesita la dirección IP externa de la aplicación de App Service, que se muestra en la página **Dominios personalizados**.

Cierre la página **Adición de nombre de host** seleccionando la **X** en la esquina superior derecha. 

En la página **Dominios personalizados**, copie la dirección IP de la aplicación.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Actualización del registro DNS

En la página de registros DNS del proveedor de dominios, seleccione el registro DNS que reasignar.

Para el ejemplo de dominio raíz de `contoso.com`, reasigne el registro A o CNAME como los ejemplos de la siguiente tabla: 

| Ejemplo de FQDN | Tipo de registro | Host | Valor |
| - | - | - | - |
| contoso.com (raíz) | Una  | `@` | Dirección IP de [Copiar la dirección IP de la aplicación](#info) |
| www.contoso.com (sub) | CNAME | `www` | _&lt;nombreaplic&gt;.azurewebsites.net_ |
| \*.contoso.com (comodín) | CNAME | _\*_ | _&lt;nombreaplic&gt;.azurewebsites.net_ |

Guarde la configuración.

Las consultas DNS deben comenzar resolviéndose en la aplicación de App Service de inmediato después de que se produzca la propagación de DNS.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo vincular un certificado SSL a App Service.

> [!div class="nextstepaction"]
> [Enlazar un certificado SSL personalizado a Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
