---
title: Registro de una aplicación con el punto de conexión de Azure AD v2.0 mediante el portal | Microsoft Docs
description: Cómo registrar una aplicación con Microsoft para habilitar el inicio de sesión y obtener acceso a servicios de Microsoft con el punto de conexión v2.0
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157850"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Cómo registrar una aplicación con el punto de conexión v2.0
Para compilar una aplicación que acepte tanto el inicio de sesión con una cuenta Microsoft (MSA) personal como una cuenta profesional o educativa de Azure AD, tendrá que registrar primero una aplicación con Microsoft. En este momento, no podrá usar ninguna aplicación existente que tenga con Azure AD o MSA. Tendrá que crear una completamente nueva.

> [!NOTE]
> No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe utilizar la versión 2.0 del punto de conexión, obtenga información sobre las [limitaciones de esta versión](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Visitar el Portal de registro para aplicaciones de Microsoft
Primero, vaya al Portal de registro para aplicaciones de Microsoft en [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Inicie sesión con una cuenta personal o una cuenta profesional o educativa de Microsoft. Si no tiene ninguna de ellas, suscríbase para una nueva cuenta personal.

¿Ha acabado? Ahora debería estar viendo su lista de aplicaciones de Microsoft, que probablemente esté vacía. Cambiemos eso.

Haga clic en **Agregar una aplicación** y asígnele un nombre. El portal le asignará a su aplicación un identificador de aplicación único global que usará más adelante en su código. Si su aplicación incluye un componente del lado del servidor que necesita tokens de acceso para llamar a las API (como Office, Azure o su propia API web), aquí también le interesará crear un **Secreto de la aplicación**.

A continuación, agregue las **plataformas** que usará la aplicación.

* Para las aplicaciones basadas en web, proporcione un **URI de redirección** al que se puedan enviar los mensajes de inicio de sesión.
* Para las aplicaciones móviles, copie el URI de redirección predeterminado creado automáticamente para usted.
* Para las API web, se crea automáticamente un ámbito predeterminado para acceder a ellas. Puede elegir agregar ámbitos adicionales mediante el botón **Agregar ámbito**. También puede agregar todas las aplicaciones que estén previamente autorizadas para usar la API web mediante el formulario **Aplicaciones preautorizadas**. 

Si lo desea, puede personalizar la apariencia de la página de inicio de sesión en la sección **Perfil**. Asegúrese de hacer clic en **Guardar** antes de continuar.

> [!NOTE]
> Cuando cree una aplicación con [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), la aplicación se registrará en el inquilino principal de la cuenta que use para iniciar sesión en el portal. Esto significa que no se puede registrar una aplicación en el inquilino de Azure AD con una cuenta personal de Microsoft. Si desea registrar explícitamente una aplicación en un inquilino determinado, inicie sesión con una cuenta creada originalmente en ese inquilino.


## <a name="build-a-quickstart-app"></a>Compilación de una aplicación de inicio rápido
Ahora que tiene una aplicación de Microsoft, puede completar uno de los tutoriales de inicio rápido de v2.0. Estas son algunas recomendaciones:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

