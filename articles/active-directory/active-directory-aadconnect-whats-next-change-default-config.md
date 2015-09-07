<properties 
	pageTitle="Cambio de la configuración predeterminada de Azure AD Connect"
	description="Obtenga información acerca de cómo cambiar la configuración predeterminada de Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Cambio de la configuración predeterminada de Azure AD Connect 


La configuración predeterminada de Azure AD Connect en la mayoría de casos es suficiente para ampliar fácilmente los directorios locales llevándolos a la nube. Sin embargo, hay ciertas instancias en las que necesita modificar el valor predeterminado y adaptarlo a su lógica de negocios de sus organizaciones. En estos casos, puede modificar la configuración predeterminada; sin embargo, hay algunos aspectos que debe tener en cuenta antes de hacerlo.

Si necesita cambiar la configuración predeterminada, haga lo siguiente:

- Si necesita modificar un flujo de atributos de una regla de sincronización lista para su aplicación, no lo cambie directamente. En su lugar, cree una nueva regla de sincronización con una prioridad más alta (valor numérico más bajo) que contenga el flujo de atributos necesario.
- Exporte las reglas de sincronización personalizadas mediante el editor de reglas de sincronización. De este modo, obtiene un script de PowerShell que puede utilizar fácilmente para volver a crearlas en un posible escenario de recuperación ante desastres.
- Si necesita cambiar el ámbito o la configuración de unión de una regla de sincronización lista para su aplicación, documéntelo y vuelva a aplicar el cambio tras actualizar a una versión más reciente de Azure AD Connect. 

<!---HONumber=August15_HO9-->