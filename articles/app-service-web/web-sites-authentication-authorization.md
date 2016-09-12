<properties 
	pageTitle="Autenticación con Active Directory local en aplicaciones de Azure | Microsoft Azure" 
	description="Obtenga información acerca de las diferentes opciones de las aplicaciones de línea de negocio en el Servicio de aplicaciones de Azure para autenticarse con Active Directory local" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="08/31/2016" 
	ms.author="cephalin"/>

# Autenticación con Active Directory local en aplicaciones de Azure #

En este artículo se muestra cómo autenticarse con Active Directory (AD) local en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md). Una aplicación de Azure se hospeda en la nube pero existen métodos para autenticar a usuarios de AD locales de forma seguridad.

## Autenticación mediante Azure Active Directory
Un inquilino de Azure Active Directory puede estar sincronizado con directorios con una implementación de AD local. Este enfoque permite a los usuarios de AD tener acceso a la aplicación desde Internet y autenticarse con sus credenciales locales. Además, el Servicio de aplicaciones de Azure proporciona una [solución completa para este método](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Con algunos clics de botón, puede habilitar la autenticación con un inquilino sincronizado con directorios para la aplicación de Azure. Este enfoque presenta las siguientes ventajas:

-	No requiere ningún código de autenticación en la aplicación. Permita que el Servicio de aplicaciones realice la autenticación por usted y dedique su tiempo a proporcionar funcionalidad en la aplicación.
-	La [API Graph de Azure AD](http://msdn.microsoft.com/library/azure/hh974476.aspx) permite el acceso a datos de directorio desde la aplicación de Azure.
-	Proporciona SSO para [todas las aplicaciones compatibles con Azure Active Directory](/marketplace/active-directory/), incluidas Office 365, Dynamics CRM Online, Microsoft Intune y miles de aplicaciones en la nube que no son de Microsoft.
-	Azure Active Directory admite el control de acceso basado en roles. Puede usar el patrón [Authorize(Roles="X")] con unos cambios mínimos en su código.

Para ver cómo se escribe una aplicación de línea de negocio de Azure que se autentica con Azure Active Directory, consulte [Creación de una aplicación de línea de negocio de Azure con autenticación de Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md).

## Autenticación mediante STS local
Si tiene un servicio de token seguro (STS) local como Servicios de federación de Active Directory (AD FS), puede utilizarlo para federar la autenticación de la aplicación de Azure. Este enfoque funciona mejor cuando la directiva de empresa prohíbe almacenar datos de AD en Azure. No obstante, tenga en cuenta lo siguiente:

-	La topología de STS debe implementarse localmente, con la sobrecarga de administración y costo.
-	Solo los administradores de AD FS pueden configurar las [relaciones de confianza para usuario autenticado y las reglas de notificaciones](http://technet.microsoft.com/library/dd807108.aspx), lo que puede limitar las opciones de desarrollador. Por otra parte, es posible administrar y personalizar [notificaciones](http://technet.microsoft.com/library/ee913571.aspx) por aplicación.
-	El acceso a datos de AD locales requiere una solución independiente a través del firewall corporativo.

Para ver cómo se escribe una aplicación de línea de negocio de Azure que se autentica con STS local, consulte [Creación de una aplicación de Azure de línea de negocio con autenticación de AD FS](web-sites-dotnet-lob-application-adfs.md).
 

<!---HONumber=AcomDC_0831_2016-->