<properties title="Introducci&oacute;n a la autenticaci&oacute;n de Active Directory" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Tareas iniciales][Tareas iniciales]
> -   [¿Qué ha ocurrido?][¿Qué ha ocurrido?]

## Introducción a Azure Active Directory (proyectos Web API)

##### Requerimiento de autenticación para obtener acceso a los controladores

Todos los controladores de su proyecto cuentan ahora con el atributo **Authorize**. Este atributo requerirá que el usuario se autentique antes de tener acceso a las API definidas por estos controladores. Para permitir el acceso anónimo al controlador, quite este atributo del controlador. Si desea establecer los permisos a un nivel más detallado, aplique el atributo a cada método que requiere autorización en vez de aplicarlo a la clase del controlador.

[Más información acerca de Azure Active Directory][Más información acerca de Azure Active Directory]

  [Tareas iniciales]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [¿Qué ha ocurrido?]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [Más información acerca de Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
