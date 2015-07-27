
<properties
    pageTitle="Cambio del inquilino de Azure Active Directory en Azure RemoteApp"
    description="Obtenga información sobre cómo cambiar el inquilino de Azure Active Directory asociado con Azure RemoteApp"
    services="remoteapp"
	documentationCenter="" 
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/24/2015"
    ms.author="elizapo" />



# Cambio del inquilino de Azure Active Directory en Azure RemoteApp

Azure RemoteApp usa Azure Active Directory (Azure AD) para permitir el acceso de usuario. El único inquilino de Azure AD que puede utilizar es el que se encuentra asociado a la suscripción de Azure. Puede ver la suscripción asociada en la página Configuración del portal. Observe la columna Directorio en la pestaña Suscripciones.

> [AZURE.NOTE]Para que el cambio de Azure Active Directory se realice correctamente, antes debe quitar todos los usuarios del inquilino de Azure Active Directory existente de todas las colecciones de Azure RemoteApp. Para ello, vaya al Portal de Azure y en la pestaña Azure RemoteApp abra todas las colecciones de Azure RemoteApp. Vaya a la pestaña **Usuarios** y quite los usuarios que pertenecen al inquilino de Azure Active Directory actual. Repita esta acción para todas las colecciones de Azure RemoteApp existentes. Si no lleva esto a cabo, no podrá crear ni aplicar parches a las colecciones.

Si desea usar otro inquilino, use estos pasos para cambiar la asociación a su suscripción:

1. En el portal, quite todo usuario de Azure AD al que haya dado acceso a las colecciones de Azure RemoteApp.


2. Establezca una cuenta Microsoft (anteriormente llamada Live ID) como el administrador de servicios. (Observe **Configuración -> Administradores**).
	1. Haga clic en el usuario conectado actualmente en la esquina superior derecha y, a continuación, haga clic en **Ver mi factura**.
	2. Seleccione su suscripción y, a continuación, haga clic en **Editar detalles de suscripción**.
	3. Realice los cambios necesarios.



3. Cierre sesión en el portal y, a continuación, vuelva a iniciarla con la cuenta Microsoft que especificó en el paso anterior.


4. Haga clic en **Nuevo -> Servicios de aplicaciones -> Active Directory -> Creación personalizada -> Usar directorio existente**. Agregue el inquilino de Azure AD que desea asociar con esta suscripción.


5. En **Configuración -> Suscripciones**, seleccione su suscripción y, a continuación, haga clic en **Editar directorio**. Seleccione el inquilino de Azure AD que desea usar.



Ahora puede usar el nuevo inquilino de Azure AD para controlar el acceso a la suscripción de Azure y para configurar el acceso de usuario en Azure RemoteApp.

<!---HONumber=July15_HO3-->