<properties
	pageTitle="Publicación de aplicaciones con el proxy de la aplicación de Azure AD | Microsoft Azure"
	description="Publicar aplicaciones locales mediante en la nube con el proxy de aplicación de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/17/2016"
	ms.author="kgremban"/>


# Publicación de aplicaciones mediante el proxy de aplicación de Azure AD


Después de habilitar el proxy de aplicación de Microsoft Azure Active Directory (AD) puede publicar aplicaciones locales, de forma que los usuarios remotos puedan acceder a ellas fuera de la red privada.

Este artículo le guiará por los pasos necesarios para publicar las aplicaciones que se ejecutan en una red local y proporcionar acceso remoto seguro desde fuera de la red. Si aún no ha configurado el proxy de aplicación ni ha instalado ningún conector, siga los pasos que se indican en [Habilitación del proxy de la aplicación de Azure AD](active-directory-application-proxy-enable.md) antes de continuar aquí.

La primera vez que use el proxy de aplicación de Azure AD, pruebe el conector; para ello, publique un sitio web desde su red privada antes de publicar aplicaciones.

> [AZURE.NOTE] Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

## Publicación de una aplicación mediante el Asistente

1. Inicie sesión como administrador en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Vaya a Active Directory y seleccione el directorio en el que ha habilitado el proxy de la aplicación.

	![Active Directory (icono)](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Haga clic en la pestaña **Aplicaciones** y después haga clic en el botón **Agregar** de la parte inferior de la pantalla.

	![Agregar aplicación](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Seleccione **Publicar una aplicación que estará accesible desde fuera de la red**.

	![Publicar una aplicación a la que se podrá acceder desde fuera de la red](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Especifique la siguiente información acerca de la aplicación:

	- **Nombre**: nombre descriptivo de la aplicación. Debe ser único en el directorio.
	- **Dirección URL interna**: la dirección que usa el conector del proxy de aplicación para acceder a la aplicación desde la red privada. Puede especificar una ruta de acceso específica en el servidor back-end para publicar, mientras que el resto del servidor no se publica. De esta forma, puede publicar sitios diferentes en el mismo servidor y dar a cada uno un nombre y unas reglas de acceso propios.
	- **Método de autenticación previa**: la forma en que el proxy de aplicación comprueba los usuarios antes de concederles acceso a la aplicación. Elija una de las opciones del menú desplegable.

		- Azure Active Directory: el proxy de la aplicación redirige a los usuarios para que inicien sesión en Azure AD, que autentica sus permisos para el directorio y la aplicación.
		- Acceso directo: los usuarios no tienen que autenticarse para acceder a la aplicación.

	![Propiedades de la aplicación](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

6. Para finalizar al asistente, haga clic en la marca de verificación de la parte inferior de la pantalla. La aplicación ya está definida en Azure AD.


## Asignación de usuarios y grupos a la aplicación

Para que los usuarios accedan a su aplicación publicada, es preciso que los asigne individualmente o en grupos. Para las aplicaciones que requieren autenticación previa, concede permisos para usar la aplicación. Para las aplicaciones que no requieren autenticación previa, los usuarios aún necesitan que se les asigne a la aplicación para que aparezca en su lista de aplicaciones.

1. Tras finalizar al Asistente para agregar aplicación, verá la página de inicio rápido de la aplicación. Para administrar quién tiene acceso a la aplicación, seleccione **Usuarios y grupos**.

	![Asignación de usuarios en el inicio rápido del proxy de la aplicación (captura de pantalla)](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Busque grupos específicos en el directorio o muestre todos los usuarios. Para mostrar los resultados de la búsqueda, haga clic en la marca de verificación.

  	![Buscar grupos o usuarios (captura de pantalla)](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Seleccione cada usuario o grupo que desea asignar a esta aplicación y haga clic en **Asignar**. Se le pedirá que confirme la acción.

> [AZURE.NOTE] En el caso de las aplicaciones de autenticación integrada de Windows, solo puede asignar aquellos usuarios y grupos que se sincronizan desde la versión local de Active Directory. A los usuarios que inicien sesión con una cuenta de Microsoft y a los invitados no se les pueden asignar aplicaciones publicadas con el proxy de aplicación de Azure Active Directory. Asegúrese de que los usuarios inician sesión con credenciales que forman parte del mismo dominio que la aplicación que se publica.


## Configuración avanzada

En la página Configurar se pueden modificar las aplicaciones publicadas o configurar las opciones avanzadas. En esta página, puede personalizar su aplicación cambiando el nombre o cargando un logotipo. También puede administrar las reglas de acceso como el método de autenticación previa o Multi-Factor Authentication.

![Configuración avanzada](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Después de publicar las aplicaciones mediante el Proxy de aplicación de Azure Active Directory, estas aparecen en la lista de aplicaciones en Azure AD y se pueden administrar desde ahí.

Si deshabilita los servicios de proxy de aplicación después de haber publicado aplicaciones, estas dejarán de estar accesibles desde fuera de la red privada. Esto no las elimina.

Para ver una aplicación y asegurarse de que sea accesible, haga doble clic en su nombre. Si se deshabilita el servicio de Proxy de aplicación y la aplicación no está disponible, aparece un mensaje de advertencia en la parte superior de la pantalla.

Para eliminar una aplicación, seleccione una aplicación en la lista y después haga clic en **Eliminar**.

## Pasos siguientes

- [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)

Para ver las últimas noticias y actualizaciones, consulte el [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/) (blog de Proxy de aplicación).

<!---HONumber=AcomDC_0622_2016-->