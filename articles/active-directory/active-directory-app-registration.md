---
title: Registro de aplicaciones de Azure Active Directory | Microsoft Docs
description: "En este artículo se describe cómo usar Azure Portal para registrar una aplicación en Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: priyamo
ms.reviewer: elisol
ms.openlocfilehash: 4f32f57d4c8b3e13d2aa31b04ee10bb46a6c71f7
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>Registro de una aplicación en un inquilino de Azure Active Directory

Azure Portal se puede usar para registrar una aplicación en un inquilino de Azure Active Directory (Azure AD). Esto crea un identificador para la aplicación que le permite recibir tokens.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD, seleccione una cuenta en la esquina superior derecha de la página.
3. En el panel de navegación izquierdo, elija **Más servicios**, haga clic en **Registros de aplicaciones**y, luego, en **Agregar**.
4. Siga las indicaciones y cree una nueva aplicación. Si desea ejemplos específicos de aplicaciones web o aplicaciones nativa, visite nuestras [guías de inicio rápido](active-directory-developers-guide.md).
  * En el caso de las aplicaciones web, especifique la **URL de inicio de sesión**, que es la dirección URL base de la aplicación, donde los usuarios pueden iniciar sesión; por ejemplo, `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * En el caso de las aplicaciones nativas, especifique un **URI de redireccionamiento**, que utilizará Azure AD para devolver las respuestas de token. Escriba un valor específico para la aplicación, por ejemplo, `http://MyFirstAADApp`
5. Una vez que haya completado el registro, Azure AD asigna a la aplicación un identificador de cliente único, el identificador de aplicación.

## <a name="update-application-settings-from-the-azure-portal"></a>Actualización de la configuración de la aplicación desde Azure Portal

Mediante Azure Portal se puede modificar fácilmente una configuración de la aplicación existente. Por ejemplo, puede que desee configurar una dirección URL de respuesta, que es donde Azure AD emite las respuestas a los tokens. También puede configurar permisos para otras aplicaciones, por ejemplo, para permitir que una aplicación acceda a la API de Microsoft Graph. Todo esto se puede hacer desde la página de configuración de la aplicación.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD, seleccione una cuenta en la esquina superior derecha de la página.
3. En el panel de navegación izquierdo, elija **Más servicios**, haga clic en **Registros de aplicaciones**y elija una aplicación en la lista.
4. Haga clic en **Configuración** para abrir la página de configuración de la aplicación.
  * La página **Propiedades** permite modificar la información general de la aplicación. Aquí se incluyen el nombre de la aplicación, la dirección URL de inicio de sesión y la dirección URL de cierre de sesión.
  * La página **Direcciones URL de respuesta** le permite agregar una dirección URL de respuesta, que es el lugar al que Azure AD envía las respuestas de los tokens.
  * La página **Propietarios** permite agregar los propietarios de las aplicaciones.
  * La página **Permisos necesarios** permite configurar los permisos de la aplicación. Por ejemplo, para tener acceso a la API de Microsoft Graph, haga clic en **Agregar**, seleccione **Microsoft Graph** en el selector de API y, después, elija el permiso requerido, por ejemplo, **Leer datos de directorio**.
  * La página **Claves** permite agregar secretos de aplicaciones. El secreto solo se mostrará una vez inmediatamente después de su creación, por lo que debe asegurarse de copiarlo si desea usarlo más adelante.

## <a name="use-the-inline-manifest-editor"></a>Utilice el editor de manifiestos en línea

El editor de manifiestos en línea se puede usar para modificar determinadas propiedades de la aplicación que no se exponen directamente en Azure Portal. Por ejemplo, se puede usar para modificar el identificador URI del identificador de la aplicación o para habilitar el flujo implícito OAuth2.0, en lugar del flujo de código de concesión de autorización predeterminado.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD, seleccione una cuenta en la esquina superior derecha de la página.
3. En el panel de navegación izquierdo, elija **Más servicios**, haga clic en **Registros de aplicaciones**y elija una aplicación en la lista.
4. Haga clic en **Manifiesto** en la página de la aplicación para abrir el editor de manifiestos en línea.
5. Puede realizar los cambios directamente en el manifiesto y guardarlo cuando esté listo. Como alternativa, puede descargar el manifiesto para abrirlo en el editor que prefiera y cargar el manifiesto actualizado.

## <a name="next-steps"></a>Pasos siguientes

1. Consulte las [guías de inicio rápido](active-directory-developers-guide.md) para ver tutoriales detallados de las aplicaciones que realizan la autenticación mediante Azure AD.
2. Consulte la lista completa de ejemplos de código en [GitHub](https://github.com/azure-samples).
