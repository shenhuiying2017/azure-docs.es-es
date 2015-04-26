<properties title="Add a user in RemoteApp" pageTitle="Agregar un usuario en RemoteApp" description="Obtenga información sobre cómo agregar usuarios en RemoteApp" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Cómo agregar un usuario en RemoteApp
 
Para que los usuarios puedan ver y usar las aplicaciones en RemoteApp, debe concederles acceso. Esta es la parte fácil: En la ficha **Acceso de usuario**, escriba la información de cuenta del usuario al que quiere concederle acceso a este servicio.

¿Qué información de cuenta necesita? Depende del tipo de colección que haya creado (en nube o híbrida) y si está usando Office 365 ProPlus en esa colección.

##Información de la cuenta de usuario híbrida frente a en la nube
La colección en la nube admite cuentas de Microsoft y cuentas de trabajo de Azure Active Directory sincronizadas con directorio (que también son cuentas de Office 365). 

La colección híbrida solo admite cuentas de Azure Active Directory que se han sincronizado (con una herramienta como DirSync) desde una implementación de Windows Server Active Directory; en concreto, sincronizadas con la opción de sincronización de contraseña o sincronizadas con la federación Servicios de federación de Active Directory (ADFS) configurada. Consulte [Configuración de Active Directory para Azure RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-ad/) para obtener más información acerca de los requisitos de Azure AD.

**Nota:** los usuarios de Azure Active Directory deben ser del inquilino al que está asociada la suscripción. (Puede ver y modificar su suscripción en la ficha **Configuración** del portal. Consulte [Cambio del inquilino de Azure Active Directory que usa RemoteApp](http://msdn.microsoft.com/es-es/3d6c4fd1-c981-4c57-9402-59fe31b11883) para obtener más información.)

##Información de la cuenta de usuario de Office 365 ProPlus
Si está usando la imagen de plantilla de Office 365 ProPlus en su colección *o* si ha creado una imagen personalizada que usa Office 365, solo puede agregar usuarios de Azure Active Directory que tienen suscripciones a Office 365 para el dominio predeterminado de su suscripción. Consulte [Uso de Office 365 con RemoteApp de Azure](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-o365/) para obtener más información.

<!--HONumber=35.2-->
