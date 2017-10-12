---
title: "Conectores del proxy de aplicación de Azure AD: portal clásico | Microsoft Docs"
description: "Explica cómo crear y administrar grupos de conectores en el Proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: b283796a-9679-4c79-b703-802bb850f65d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: fc65c4053c45d9c16c62ee0fe65924133a4bb94a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicación de aplicaciones en redes independientes y ubicaciones mediante grupos de conectores
> [!div class="op_single_selector"]
> * [Portal de Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portal de Azure clásico](active-directory-application-proxy-connectors.md)
>
>

Los grupos de conectores son útiles para diversos escenarios, por ejemplo:

* Sitios con varios centros de datos interconectados. En este caso, desea mantener el mayor tráfico posible dentro del centro de datos, ya que los vínculos entre centros de datos son costosos y lentos. Puede implementar conectores en cada centro de datos para atender solamente las aplicaciones que se encuentran en él. Con este enfoque se minimizan los vínculos entre centros de datos y se proporciona una experiencia totalmente transparente a los usuarios.
* Administración de aplicaciones instaladas en redes aisladas que no forman parte de la red corporativa principal. Puede usar grupos de conectores para instalar conectores específicos en redes aisladas y así permitir también el aislamiento de aplicaciones en la red.
* En lo que respecta a las aplicaciones instaladas en IaaS para el acceso a la nube, los grupos de conectores proporcionan un servicio común para proteger el acceso a todas las aplicaciones. Los grupos de conectores no crean dependencia adicional en la red corporativa ni fragmentan la experiencia de la aplicación. Los conectores se pueden instalarse en cada centro de datos en la nube y servir solo las aplicaciones que residen en esta red. Puede instalar varios conectores para lograr alta disponibilidad.
* Soporte para entornos de varios bosques en los que se pueden implementar conectores específicos por bosque y establecerse para servir aplicaciones específicas.
* Los grupos de conectores se pueden utilizar en sitios de recuperación ante desastres para detectar la conmutación por error o como copia de seguridad para el sitio principal.
* Los grupos de conectores también se pueden utilizar para dar servicio a varias compañías desde un solo inquilino.

## <a name="prerequisite-create-your-connectors"></a>Requisito previo: Crear conectores
Para agrupar sus conectores, [instalar varios conectores](active-directory-application-proxy-enable.md) y luego asígneles un nombre y agrúpelos. Finalmente deberá asignarlos a aplicaciones específicas.

## <a name="step-1-create-connector-groups"></a>Paso 1: Crear grupos de conectores
Puede crear tantos grupos de conectores como desee. La creación de grupos de conectores se lleva a cabo en el Portal de Azure clásico.

1. Seleccione el directorio y haga clic en **Configurar**.  
    ![Captura de pantalla de la configuración del proxy de la aplicación: clic en administrar grupos de conectores](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)
2. En Proxy de la aplicación, haga clic en **Administrar grupos de conectores** y cree un grupo de conectores proporcionándole un nombre.  
    ![Captura de pantalla de grupos de conectores del proxy de la aplicación: asignar un nombre al grupo nuevo](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Paso 2: Asignar conectores a los grupos
Después de crear los grupos de conector, mueva los conectores al grupo adecuado.

1. En **Proxy de la aplicación**, haga clic en **Administrar conectores**.
2. En **Grupo**, seleccione el grupo que quiera para cada conector. Los conectores podrían tardar en activarse hasta 10 minutos en el nuevo grupo.  
    ![Captura de pantalla de conectores de proxy de la aplicación: seleccionar un grupo en el menú desplegable](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Paso 3: Asignar aplicaciones a los grupos de conectores
El último paso es establecer cada aplicación en el grupo de conectores que la da servicio.

1. En el Portal de Azure clásico, en el directorio, seleccione la aplicación que desee asignar al grupo y haga clic en **Configurar**.
2. En **Grupo conectores**, seleccione el grupo que desee que utilice la aplicación. Este cambio se aplica inmediatamente.  
    ![Captura de pantalla de grupo de conectores de proxy de la aplicación: seleccionar grupo en el menú desplegable](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)

## <a name="see-also"></a>Otras referencias
* [Habilitación del proxy de la aplicación](active-directory-application-proxy-enable.md)
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
* [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

Para ver las últimas noticias y actualizaciones, consulte el [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)
