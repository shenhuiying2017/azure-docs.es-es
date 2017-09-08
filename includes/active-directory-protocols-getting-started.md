---
title: "Introducción al protocolo .NET de Azure AD | Microsoft Docs"
description: "Descubra cómo utilizar mensajes HTTP para autorizar el acceso a aplicaciones y API web en su inquilino de Azure AD."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo
ms.translationtype: HT
ms.sourcegitcommit: 8bdd78473b506fd3946ab12839ccbc20ca697f38
ms.openlocfilehash: 42ce8dfd30cda7d4085778954350550fd9fdf13d
ms.contentlocale: es-es

---
## Registro de la aplicación con el inquilino de AD
En primer lugar, tendrá que registrar la aplicación con el inquilino de Azure Active Directory (Azure AD). De este modo, se generará un id. de aplicación para la aplicación y también se habilitará esta para que reciba tokens.

* Inicie sesión en el [Portal de Azure](https://portal.azure.com).
* Para elegir el inquilino de Azure AD, haga clic en su cuenta en la esquina superior derecha de la página.
* En el panel de navegación izquierdo, haga clic en **Azure Active Directory**.
* Haga clic en **Registros de aplicaciones** y en **Agregar**.
* Siga las indicaciones y cree una nueva aplicación. Para este tutorial, no importa si se trata de una aplicación web o nativa, pero si desea ver ejemplos específicos de aplicaciones web o nativas, consulte nuestras [guías de inicio rápido](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Para aplicaciones web, proporcione la **dirección URL de inicio de sesión**, que es la URL base de la aplicación, donde los usuarios pueden iniciar sesión; por ejemplo, `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Para las aplicaciones nativas, proporcione un **URI de redirección**, que utilizará Azure AD para devolver las respuestas de token. Escriba un valor específico para la aplicación, por ejemplo, `http://MyFirstAADApp`
* Una vez que haya completado el registro, Azure AD asignará a su aplicación un identificador de cliente único, el id. de aplicación. Necesitará este valor en las secciones siguientes, así que cópielo de la página de la aplicación.

