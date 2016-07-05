<properties
	pageTitle="Uso de conectores del proxy de aplicación de Azure AD | Microsoft Azure"
	description="Explica cómo crear y administrar grupos de conectores en el Proxy de aplicación de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="kgremban"/>


# Publicación de aplicaciones en redes independientes y ubicaciones mediante grupos de conectores

Los grupos de conectores son útiles para diversos escenarios, por ejemplo:

- Sitios con varios centros de datos interconectados. En este caso, desea mantener el mayor tráfico posible dentro del centro de datos, ya que los vínculos entre centros de datos suelen ser costosos y lentos. Puede implementar conectores en cada centro de datos para servir solamente las aplicaciones que se encuentran en él. Con este enfoque se minimizan los vínculos entre centros de datos y se proporciona una experiencia totalmente transparente a los usuarios.
- Administración de aplicaciones instaladas en redes aisladas que no forman parte de la red corporativa principal. Puede usar grupos de conectores para instalar conectores dedicados en redes aisladas y así permitir también el aislamiento de aplicaciones en la red.
- Para las aplicaciones instaladas en IaaS para el acceso en la nube, los grupos de conectores proporcionan un servicio común para proteger el acceso a todas las aplicaciones sin crear mayor dependencia de la red corporativa ni fragmentar la experiencia. Los conectores se pueden instalarse en cada centro de datos en la nube y servir solo las aplicaciones que residen en esta red. Puede instalar varios conectores para lograr alta disponibilidad.
- Soporte para entornos de varios bosques en los que se pueden implementar conectores específicos por bosque y establecerse para servir aplicaciones específicas.
- Los grupos de conectores se pueden utilizar en sitios de recuperación ante desastres para detectar la conmutación por error o como copia de seguridad para el sitio principal.
- Los grupos de conectores también se pueden utilizar para dar servicio a varias compañías desde un solo inquilino.

## Requisito previo: Crear conectores
Para agrupar los conectores, debe asegurarse de que [instaló varios conectores](active-directory-application-proxy-enable.md), que le asigna un nombre. Después puede agruparlos. Finalmente deberá asignarlos a aplicaciones específicas.

## Paso 1: Crear grupos de conectores
Puede crear tantos grupos de conectores como desee. La creación de grupos de conectores se lleva a cabo en el Portal de Azure clásico.

1. Seleccione el directorio y haga clic en **Configurar**. ![Captura de la configuración del proxy de la aplicación: clic en administrar grupos de conectores](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. En Proxy de la aplicación, haga clic en **Administrar grupos de conectores** y cree un grupo de conectores proporcionándole un nombre. ![Captura de pantalla de grupos de conectores de proxy de la aplicación - dar nombre a nuevo grupo](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## Paso 2: Asignar conectores a los grupos
Después de crear los grupos de conector, mueva los conectores al grupo adecuado.

1. En **Proxy de la aplicación**, haga clic en **Administrar conectores**.
2. En **Grupo**, seleccione el grupo que quiera para cada conector. Tenga en cuenta que los conectores pueden tardar en activarse hasta 10 minutos en el nuevo grupo. ![Captura de pantalla de conectores de proxy de la aplicación: seleccionar grupo en el menú desplegable](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## Paso 3: Asignar aplicaciones a los grupos de conectores
El último paso es establecer cada aplicación en el grupo de conectores que la servirá.

1. En el Portal de Azure clásico, en el directorio, seleccione la aplicación que desee asignar al grupo y haga clic en **Configurar**.
2. En **Grupo conectores**, seleccione el grupo que desee que utilice la aplicación. Este cambio se aplica inmediatamente. ![Captura de pantalla de grupo de conectores de proxy de la aplicación: seleccionar grupo en el menú desplegable](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## Consulte también

- [Habilitación del proxy de la aplicación](active-directory-application-proxy-enable.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

Para ver las últimas noticias y actualizaciones, consulte el [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/) (Blog de Proxy de aplicación).

<!---HONumber=AcomDC_0622_2016-->