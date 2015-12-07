<properties
	pageTitle="Trabajar con las aplicaciones para notificaciones en Proxy de aplicación"
	description="Explica cómo poner en funcionamiento el proxy de la aplicación de Azure AD."
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
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="kgremban"/>



# Trabajar con las aplicaciones para notificaciones en Proxy de aplicación


Las aplicaciones para notificaciones realizan una redirección del STS, que a su vez solicita las credenciales del usuario a cambio de un token antes de redirigir al usuario a la aplicación. Para habilitar Proxy de aplicación a fin de poder trabajar con estas redirecciones, deben realizarse los siguientes pasos para habilitar Proxy de aplicación a fin de trabajar con las aplicaciones para notificaciones.

> [AZURE.IMPORTANT]Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).


## Requisito previo

Antes de realizar este procedimiento, asegúrese de que el STS al que redirige la aplicación para notificaciones está disponible fuera de la red local.

### Configuración del Portal de Azure

1. Publique la aplicación según las instrucciones de [Publicar aplicaciones con el proxy de aplicación](active-directory-application-proxy-publish.md).
2. En la lista de aplicaciones, seleccione la aplicación para notificaciones y haga clic en **Configurar**.
3. Si elige **Acceso directo** como su **Método de autenticación previa**, asegúrese de seleccionar **HTTPS** como su esquema **Dirección URL externa**.
4. Si elige Azure Active Directory como su **Método de autenticación previa**, asegúrese de seleccionar **Ninguno** como su **Método de autenticación interno**.

### Configuración de AD FS

1. Abra **Administración de AD FS**.
2. Vaya a **Relaciones de confianza para usuario autenticado** y haga clic con el botón derecho en la aplicación que va a publicar con Proxy de aplicación y seleccione **Propiedades**.

![Relaciones de confianza para usuario autenticado: haga clic con el botón derecho en el nombre de la aplicación - captura de pantalla](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)

3. En la pestaña **Extremos**, en **Tipo de extremo**, seleccione **WS-Federation**.
4. En **Dirección URL de confianza**, escriba la dirección URL que indicó en Proxy de aplicación en **Dirección URL externa** y haga clic en **Aceptar**.

![Agregar un extremo: establezca el valor de Dirección URL de confianza - captura de pantalla](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)

<!---HONumber=AcomDC_1125_2015-->