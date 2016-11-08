---
title: Información general sobre el protocolo .NET de Azure AD | Microsoft Docs
description: Descubra cómo utilizar mensajes HTTP para autorizar el acceso a aplicaciones y API web en su inquilino de Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo

---
<!--TODO: Introduction -->

## Registro de la aplicación con el inquilino de AD
En primer lugar, tendrá que registrar la aplicación con el inquilino de Active Directory. De este modo, se generará un id. de cliente para la aplicación y también se podrán recibir tokens.

* Inicie sesión en el Portal de administración de Azure.
* En el panel de navegación izquierdo, haga clic en **Active Directory**.
* Seleccione el inquilino en el que va a registrar la aplicación.
* Haga clic en la pestaña **Aplicaciones** y en el botón **Agregar** del cajón inferior.
* Siga las indicaciones y cree una nueva aplicación. Para este tutorial, no es relevante que se trate de una aplicación web o nativa, pero si desea ver ejemplos específicos de aplicaciones web o nativas, consulte nuestras guías de inicio rápido [aquí](../articles/active-directory/active-directory-developers-guide.md).
* Para aplicaciones web, proporcione la **dirección URL de inicio de sesión**, que es la URL base de la aplicación, donde los usuarios pueden iniciar sesión; por ejemplo, `http://localhost:12345`. El **URI de id. de aplicación** es un identificador único de su aplicación. La convención consiste en usar `https://<tenant-domain>/<app-name>`, p. ej. `https://contoso.onmicrosoft.com/my-first-aad-app`
* Para las aplicaciones nativas, proporcione un **URI de redirección**, que utilizará Azure AD para devolver las respuestas de token. Escriba un valor específico para la aplicación, por ejemplo, `http://MyFirstAADApp`.
* Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de cliente único. Necesitará este valor en las secciones siguientes, de modo que cópielo en la pestaña **Configurar** de la aplicación.

<!---HONumber=AcomDC_0601_2016-->