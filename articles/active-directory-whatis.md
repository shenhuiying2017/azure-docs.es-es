<properties 
	pageTitle="¿Qué es Azure Active Directory?" 
	description="Use Azure Active Directory para ampliar sus identidades locales existentes en la nube para lograr una experiencia de usuario final y administración mejoradas, mientras que Microsoft mantiene Active Directory en ejecución en la nube con recuperación ante desastres integrada y alta escala y disponibilidad. O bien, desarrolle aplicaciones integradas de Azure AD para su organización o para otras organizaciones." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="justinha"/>





<h1 id="whatisaad">¿Qué es Azure Active Directory?</h1>

Azure Active Directory es un servicio que proporciona funcionalidades de administración de acceso e identidad en la nube. En un proceso muy similar al que sigue Active Directory para permitir a los clientes administrar identidades en entornos locales a través del sistema operativo Windows Server, Azure Active Directory (Azure AD) es un servicio disponible a través de Azure para administrar identidades en la nube.  [Más información](http://msdn.microsoft.com/library/hh967611.aspx)

Debido a que es el directorio en la nube de su organización, usted es quien decide quiénes son sus usuarios, qué información desea mantener en la nube, quién puede usar la información o administrarla y qué aplicaciones o servicios pueden tener acceso a esa información. 

Al usar Azure AD, es responsabilidad de Microsoft mantener a Active Directory en funcionamiento en la nube con una alta escala, alta disponibilidad y recuperación ante desastres integrada, mientras respeta cabalmente sus requisitos de privacidad y seguridad de la información de su organización.

<h3>Integración con Active Directory de un entorno local</h3>

Azure AD se puede usar como un directorio de la nube independiente para su organización; sin embargo, también puede integrar un servicio Active Directory existente en un entorno local con Azure AD. Algunas de las características de integración incluyen la sincronización de directorios y el inicio de sesión único, las que extienden aún más el alcance de sus identidades del entorno local existentes hacia la nube para lograr una mejor administración y experiencia de usuario final. 
 [Más información](http://msdn.microsoft.com/library/jj573653)

<h3>Integración con sus aplicaciones</h3>

Los desarrolladores de aplicaciones pueden integrar sus aplicaciones con Azure AD para ofrecer a sus usuarios la funcionalidad de inicio de sesión único. Esto permite que las aplicaciones empresariales se hospeden en la nube y autentiquen fácilmente a los usuarios con las credenciales corporativas. También permite que los proveedores de software como servicio (SaaS) faciliten la autenticación para los usuarios en las organizaciones de Azure AD cuando se autentican en sus servicios. Los desarrolladores pueden también usar el API Graph para consultar los datos del directorio a fin de administrar entidades, como por ejemplo usuarios o grupos. [Más información](http://go.microsoft.com/fwlink/?LinkID=290817&clcid=0x409)

**Recursos adicionales**

* [Inicio de sesión en Azure como una organización](/es-es/manage/services/identity/organizational-account/)
* [Identidad de Azure](/es-es/manage/windows/fundamentals/identity/)
* [Biblioteca de Azure AD en MSDN](http://go.microsoft.com/fwlink/?LinkId=293425)

<!--HONumber=46--> 
