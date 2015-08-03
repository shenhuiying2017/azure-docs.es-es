<properties 
	pageTitle="Cómo funciona Azure AD Connect" 
	description="Obtenga información acerca de cómo funciona Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Cómo funciona Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="¿Qué es?">¿Qué es?</a> <a href="../active-directory-aadconnect-how-it-works/" title="¿Cómo funciona?" class="current">¿Cómo funciona?</a> <a href="../active-directory-aadconnect-get-started/" title="Introducción">Introducción</a> <a href="../active-directory-aadconnect-whats-next/" title="¿Qué sigue?">¿Qué sigue?</a> <a href="../active-directory-aadconnect-learn-more/" title="Más información">Más información</a>
</div>
Azure Active Directory Connect se compone de tres partes principales. Se trata de los servicios de sincronización, la parte opcional de Servicios de federación de Active Directory y la parte de supervisión que se lleva a cabo mediante [Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- Sincronización: esta parte consta de los componentes y la funcionalidad publicados previamente como Dirsync y Sincronización de AAD. Esta es la parte que se encarga de crear usuarios y grupos. También es responsable de asegurarse de que la información sobre los usuarios y los grupos de su entorno local coincide con la de la nube.
- AD FS: se trata de una parte opcional de Azure AD Connect y puede utilizarse para configurar un entorno híbrido mediante una infraestructura local de AD FS. Las organizaciones pueden usar esta parte para abordar implementaciones complejas que incluyen elementos como un inicio de sesión único de unión a dominio, la aplicación de la directiva de inicio de sesión de AD y MFA mediante tarjeta inteligente o de terceros. Para obtener información adicional sobre la configuración del inicio de sesión único, vea [DirSync con inicio de sesión único](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Supervisión de estado: en el caso de las implementaciones complejas que usan AD FS, Azure AD Connect Health puede ofrecer una sólida supervisión de los servidores de federación y proporcionar una ubicación central en el portal de Azure para ver esta actividad. Para obtener más información, consulte [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


## Componentes de soporte de Azure AD Connect

La siguiente es una lista de requisitos previos y componentes de soporte que Azure AD Connect instalará en el servidor en el que configuró Azure AD Connect. Esta lista es para una instalación rápida básica. Si decide usar un servidor SQL Server diferente en la página Instalar servicios de sincronización, los componentes de SQL Server 2012 enumerados a continuación no se instalarán.

- Conector de Azure AD para Azure AD Connect
- Utilidades de línea de comandos de Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Módulo de Active Directory para Windows PowerShell
- Microsoft Online Services - Ayudante para el inicio de sesión para profesionales de TI
- Paquete de redistribución de Microsoft Visual C++ 2013




 

<!---HONumber=July15_HO4-->