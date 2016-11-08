---
title: Conectores del proxy de aplicación de Azure AD | Microsoft Docs
description: Se explica cómo crear y administrar grupos de conectores en el proxy de aplicación de Azure AD.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: kgremban

---
# Publicación de aplicaciones en redes y ubicaciones independientes mediante grupos de conectores (versión preliminar pública)
> [!div class="op_single_selector"]
> * [Portal de Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portal de Azure clásico](active-directory-application-proxy-connectors.md)
> 
> 

Los grupos de conectores son útiles para diversos escenarios, por ejemplo:

* Sitios con varios centros de datos interconectados. En este caso, desea mantener el mayor tráfico posible dentro del centro de datos, ya que los vínculos entre centros de datos son costosos y lentos. Puede implementar conectores en cada centro de datos para atender solamente las aplicaciones que se encuentran en él. Con este enfoque se minimizan los vínculos entre centros de datos y se proporciona una experiencia totalmente transparente a los usuarios.
* Administración de aplicaciones instaladas en redes aisladas que no forman parte de la red corporativa principal. Puede usar grupos de conectores para instalar conectores dedicados en redes aisladas y así permitir también el aislamiento de aplicaciones en la red.
* En lo que respecta a las aplicaciones instaladas en IaaS para el acceso a la nube, los grupos de conectores proporcionan un servicio común para proteger el acceso a todas las aplicaciones. Los grupos de conectores no crean dependencia adicional en la red corporativa ni fragmentan la experiencia de la aplicación. Los conectores se pueden instalarse en cada centro de datos en la nube y servir solo las aplicaciones que residen en esta red. Puede instalar varios conectores para lograr alta disponibilidad.
* Se puede implementar compatibilidad con varios bosques en la que se pueden implementar conectores específicos por bosque y establecerse para atender aplicaciones específicas.
* Los grupos de conectores se pueden utilizar en sitios de recuperación ante desastres para detectar la conmutación por error o como copia de seguridad para el sitio principal.
* Los grupos de conectores también se pueden utilizar para dar servicio a varias compañías desde un solo inquilino.

## Requisito previo: Creación de los conectores
Para agrupar los conectores, debe asegurarse de [tener instalados varios conectores](active-directory-application-proxy-enable.md). Cuando se instala un nuevo conector, se une automáticamente al grupo de conectores **predeterminado**.

## Paso 1: Creación de grupos de conectores
Puede crear tantos grupos de conectores como quiera. La creación de grupos de conectores se lleva a cabo en [Azure Portal](https://portal.azure.com).

1. Seleccione **Azure Active Directory** para ir al panel de administración de su directorio. Ahí, seleccione **Enterprise applications** (Aplicaciones empresariales) > **Proxy de la aplicación**.
2. Seleccione el botón **Connector Groups** (Grupos de conectores). Aparece la hoja New Connector Group (Nuevo grupo de conectores).
3. Asigne un nombre al nuevo grupo de conectores y use luego el menú deplegable para seleccionar qué conectores pertenecen a este grupo.
4. Cuando el grupo de conectores esté completo, seleccione **Guardar**.

## Paso 2: Asignación de aplicaciones a los grupos de conectores
El último paso es establecer cada aplicación en el grupo de conectores que la atenderá.

1. En el panel de administración del directorio, seleccione **Enterprise applications** (Aplicaciones empresariales) > **All applications** (Todas las aplicaciones) > la aplicación que quiere asignar a un grupo de conectores > **Application Proxy** (Proxy de la aplicación).
2. En **Grupo de conectores**, use el menú desplegable para seleccionar el grupo que quiere que use la aplicación.
3. Seleccione **Guardar** para aplicar el cambio.

## Otras referencias
* [Habilitación del proxy de la aplicación](active-directory-application-proxy-enable.md)
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
* [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

Para ver las últimas noticias y actualizaciones, consulte el [blog de Proxy de la aplicación](http://blogs.technet.com/b/applicationproxyblog/).

<!---HONumber=AcomDC_0914_2016-->