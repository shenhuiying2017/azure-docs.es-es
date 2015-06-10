<properties 
	pageTitle="Cómo funciona Azure AD Connect" 
	description="Obtenga información acerca de cómo funciona Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Cómo funciona Azure AD Connect


<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/active-directory-aadconnect/" title="¿Qué es?" class="current">¿Qué es?</a> <a href="/es-es/documentation/articles/active-directory-aadconnect-how-it-works/" title="¿Cómo funciona?">¿Cómo funciona?</a> <a href="/es-es/documentation/articles/active-directory-aadconnect-get-started/" title="Introducción">Introducción</a> <a href="/es-es/documentation/articles/active-directory-aadconnect-whats-next/" title="¿Qué sigue?">¿Qué sigue?</a> <a href="/es-es/documentation/articles/active-directory-aadconnect-learn-more/" title="Más información">Más información</a>
</div>

Azure Active Directory Connect se compone de tres partes principales que conforman Azure Active Directory Connect. Se trata de los servicios de sincronización, la parte opcional de Servicios de federación de Active Directory y la parte de supervisión que se lleva a cabo mediante Azure AD Connect Health.


<center>![Pila de Azure AD Connect](./media/active-directory-aadconnect-how-it-works/AADConnectStack.png) </center>

- Sincronización: esta parte consta de los componentes y la funcionalidad publicados previamente como Dirsync y Sincronización de AAD. Esta es la parte que se encarga de crear usuarios y grupos. También es responsable de asegurarse de que la información sobre los usuarios y los grupos de su entorno local coincide en la nube.
- AD FS: se trata de una parte opcional de Azure AD Connect y puede utilizarse para configurar un entorno híbrido mediante una infraestructura local de AD FS. Esta parte puede utilizarse por la organización para abordar implementaciones complejas que incluyen elementos como un inicio de sesión único de unión a dominio, la aplicación de la directiva de inicio de sesión de AD y MFA mediante tarjeta inteligente o de terceros. Para obtener información adicional sobre la configuración del inicio de sesión único, vea [DirSync con inicio de sesión único](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Supervisión de estado: en el caso de las implementaciones complejas que usan AD FS, Azure AD Connect Health puede ofrecer una sólida supervisión de los servidores de federación y proporcionar una ubicación central en el portal de Azure para ver esta actividad. Para obtener más información, consulte [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).






**Recursos adicionales**

* [Uso de la infraestructura de identidad local en la nube](active-directory-aadconnect.md)
* [Introducción a Azure AD Connect](active-directory-aadconnect-get-started.md)
* [Qué sigue en Azure AD Connect](active-directory-aadconnect-whats-next.md)
* [Más información](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect en MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->