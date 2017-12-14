---
title: "Registro de una aplicación con el punto de conexión de Azure AD v2.0 mediante el portal | Microsoft Docs"
description: "Cómo registrar una aplicación con Microsoft para habilitar el inicio de sesión y obtener acceso a servicios de Microsoft con el punto de conexión v2.0"
services: active-directory
documentationcenter: 
author: lnalepa
manager: mtillman
editor: 
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: eba8ecd27542b23676c08b8ce072c91134d27fa5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Cómo registrar una aplicación con el punto de conexión v2.0
Para crear una aplicación que acepte tanto el inicio de sesión de MSA como de Azure AD, tendrá que registrar primero una aplicación con Microsoft.  En este momento, no podrá usar ninguna aplicación existente que tenga con Azure AD o MSA. Tendrá que crear una completamente nueva.

> [!NOTE]
> No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0.  Para determinar si debe usar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="visit-the-microsoft-app-registration-portal"></a>Visitar el Portal de registro para aplicaciones de Microsoft
Lo primero es lo primero: vaya a [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Este es un nuevo portal de registro para aplicaciones donde puede administrar sus aplicaciones de Microsoft.

Inicie sesión con una cuenta personal o una cuenta profesional o educativa de Microsoft.  Si no tiene ninguna de ellas, suscríbase para una nueva cuenta personal. Adelante, no le llevará mucho tiempo; aquí le esperamos.

¿Ha acabado? Ahora debería estar viendo su lista de aplicaciones de Microsoft, que probablemente esté vacía.  Cambiemos eso.

Haga clic en **Agregar una aplicación** y asígnele un nombre.  El portal le asignará a su aplicación un identificador de aplicación único global que usará más adelante en su código.  Si su aplicación incluye un componente del lado del servidor que necesita tokens de acceso para llamar a las API (como Office, Azure o su propia API web), aquí también le interesará crear un **Secreto de la aplicación**.

A continuación, agregue las plataformas que usará la aplicación.

* Para las aplicaciones basadas en web, proporcione un **URI de redirección** adonde se puedan enviar los mensajes de inicio de sesión.
* Para las aplicaciones móviles, copie el URI de redirección predeterminado creado automáticamente para usted.

Si lo desea, puede personalizar la apariencia de la página de inicio de sesión en la sección Perfil.  Asegúrese de hacer clic en **Guardar** antes de continuar.

> [!NOTE]
> Cuando cree una aplicación con [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), la aplicación se registrará en el inquilino principal de la cuenta que use para iniciar sesión en el portal.  Esto significa que no se puede registrar una aplicación en el inquilino de Azure AD con una cuenta personal de Microsoft.  Si desea registrar explícitamente una aplicación en un inquilino determinado, inicie sesión con una cuenta creada originalmente en ese inquilino.
> 
> 

## <a name="build-a-quick-start-app"></a>Compilar una aplicación de inicio rápido
Ahora que tiene una aplicación de Microsoft, puede completar uno de nuestros tutoriales de inicio rápido de v2.0.  Estas son algunas recomendaciones:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

