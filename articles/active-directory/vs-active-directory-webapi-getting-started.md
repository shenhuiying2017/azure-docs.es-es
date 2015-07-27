<properties 
	pageTitle="" 
	description="Información sobre cómo empezar con el Asistente de Active Directory (proyectos Web API) de Azure" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Introducción a Azure Active Directory (proyectos Web API)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

#####Requerimiento de autenticación para obtener acceso a los controladores
 
Todos los controladores de su proyecto cuentan ahora con el atributo **Authorize**. Este atributo requerirá que el usuario se autentique antes de tener acceso a las API definidas por estos controladores. Para permitir el acceso anónimo al controlador, quite este atributo del controlador. Si desea establecer los permisos a un nivel más detallado, aplique el atributo a cada método que requiere autorización en vez de aplicarlo a la clase del controlador.

[Más información acerca de Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=July15_HO3-->