
<properties 
    pageTitle="Cambio del inquilino de Azure Active Directory en RemoteApp"
    description="Obtenga información sobre cómo cambiar el inquilino de Azure Active Directory asociado con RemoteApp" 
    services="remoteapp" 
    solutions=""
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2015" 
    ms.author="elizapo" />



# Cambio del inquilino de Azure Active Directory en RemoteApp

RemoteApp usa Azure Active Directory (Azure AD) para permitir el acceso de usuario. El único inquilino de Azure AD que puede utilizar es el que se encuentra asociado a la suscripción de Azure. Puede ver la suscripción asociada en la página Configuración del portal. Observe la columna Directorio en la pestaña Suscripciones. 

Si desea usar otro inquilino, use estos pasos para cambiar la asociación a su suscripción:

1. En el portal, quite todo usuario de Azure AD al que haya dado acceso a los servicios de RemoteApp.


2. Establezca una cuenta Microsoft (anteriormente llamada Live ID) como el administrador de servicios. (Observe **Configuración -> Administradores**).
	1. Haga clic en el usuario conectado actualmente en la esquina superior derecha y, a continuación, haga clic en **Ver mi factura**.
	2. Seleccione su suscripción y, a continuación, haga clic en **Editar detalles de suscripción**.
	3. Realice los cambios necesarios.



3. Cierre sesión en el portal y, a continuación, vuelva a iniciarla con la cuenta Microsoft que especificó en el paso anterior.


4. Haga clic en **Nuevo -> Servicios de aplicaciones -> Active Directory -> Creación personalizada -> Usar directorio existente**. Agregue el inquilino de Azure AD que desea asociar con esta suscripción.


5. En **Configuración -> Suscripciones**, seleccione su suscripción y, a continuación, haga clic en **Editar directorio**. Seleccione el inquilino de Azure AD que desea usar.



Ahora puede usar el nuevo inquilino de Azure AD para controlar el acceso a la suscripción de Azure y para configurar el acceso de usuario en RemoteApp.


<!--HONumber=52--> 