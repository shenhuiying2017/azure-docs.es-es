---
title: "Azure Active Directory B2C: configuración de Twitter | Microsoft Docs"
description: "Proporcione funciones de registro e inicio de sesión a los consumidores con cuentas de Twitter en las aplicaciones protegidas por Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 579a6841-9329-45b8-a351-da4315a6634e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/06/2017
ms.author: parakhj
ms.openlocfilehash: 82a001dd53cdddcf3b360090f3250af593c96fbb
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C: provisión de registro e inicio de sesión a los usuarios con cuentas de Twitter

> [!NOTE]
> Esta característica se encuentra en su versión preliminar.
> 

## <a name="create-a-twitter-application"></a>Crear una aplicación de Twitter
Para usar Twitter como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de Twitter y suministrarle los parámetros correctos. Necesita una cuenta de desarrollador de Twitter para hacerlo. Si no tiene una, puede obtenerla en [https://dev.twitter.com/](https://dev.twitter.com/).

1. Vaya al [sitio web para desarrolladores de Twitter](https://dev.twitter.com/) e inicie sesión con sus credenciales.
2. Haga clic en **Mis aplicaciones** en **Herramientas y soporte técnico** y, después, haga clic en **Crear una aplicación nueva**. 
3. En el formulario, proporcione un valor para **Nombre**, **Descripción** y **Sitio web**.
4. Para la **URL de devolución de llamada**, escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Asegúrese de reemplazar **{tenant}** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com).
5. Active la casilla para aceptar el **Acuerdo para desarrolladores** y haga clic en **Crear su aplicación de Twitter**.
6. Una vez creada la aplicación, haga clic en **Claves y tokens de acceso**.
7. Copie el valor de **Clave de consumidor** y **Secreto de consumidor**. Necesitará los dos para configurar Twitter como proveedor de identidades de su inquilino.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configuración de Twitter como proveedor de identidades del inquilino
1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) de Azure Portal.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, escriba "Twitter".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **Twitter** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades** y escriba la **Clave de consumidor** de Twitter para el **Id. de cliente** y el **Secreto de consumidor** de Twitter para el **Secreto de cliente**.
7. Haga clic en **Aceptar** y luego en **Crear** para guardar la configuración de Twitter.

