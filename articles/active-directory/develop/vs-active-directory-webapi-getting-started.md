---
title: "Introducción a Azure AD en proyectos de WebApi de Visual Studio | Microsoft Docs"
description: "Cómo empezar a usar Azure Active Directory en los proyectos de WebApi después de crear un Azure AD usando los servicios conectados de Visual Studio o de conectarse a él"
services: active-directory
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f3f8292eb505c73b5fda86499581fe85ad3f8e47
ms.openlocfilehash: b42bd57c8a7dde854208c65f4477327fbf1108a4


---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>Introducción a Azure Active Directory y servicios conectados de Visual Studio (proyectos de WebApi)
> [!div class="op_single_selector"]
> * [Introducción](vs-active-directory-webapi-getting-started.md)
> * [¿Qué ha ocurrido?](vs-active-directory-webapi-what-happened.md)
> 
> 

### <a name="requiring-authentication-to-access-controllers"></a>Requerimiento de autenticación para obtener acceso a los controladores
Todos los controladores de su proyecto cuentan ahora con el atributo **Authorize** . Este atributo requiere que el usuario se autentique antes de tener acceso a las API definidas por estos controladores. Para permitir el acceso anónimo al controlador, quite este atributo del controlador. Si desea establecer los permisos a un nivel más detallado, aplique el atributo a cada método que requiere autorización en vez de aplicarlo a la clase del controlador.

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)




<!--HONumber=Jan17_HO5-->


