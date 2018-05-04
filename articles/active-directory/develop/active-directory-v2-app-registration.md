---
title: Registro de una aplicación con el punto de conexión de Azure AD v2.0 mediante el portal | Microsoft Docs
description: Cómo registrar una aplicación con Microsoft para habilitar el inicio de sesión y obtener acceso a servicios de Microsoft con el punto de conexión v2.0
services: active-directory
documentationcenter: ''
author: lnalepa
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: da9dd5099d8175f1f7347cb022f149979b618909
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Cómo registrar una aplicación con el punto de conexión v2.0
Para crear una aplicación que acepte tanto el inicio de sesión de MSA como de Azure AD, tendrá que registrar primero una aplicación con Microsoft.  En este momento, no podrá usar ninguna aplicación existente que tenga con Azure AD o MSA. Tendrá que crear una completamente nueva.

> [!NOTE]
> No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0.  Para determinar si debe usar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="visit-the-microsoft-app-registration-portal"></a>Visitar el Portal de registro para aplicaciones de Microsoft
En primer lugar, vaya a [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Este es un nuevo portal de registro para aplicaciones donde puede administrar sus aplicaciones de Microsoft.

Inicie sesión con una cuenta personal o una cuenta profesional o educativa de Microsoft.  Si no tiene ninguna de ellas, suscríbase para una nueva cuenta personal. Adelante, no le llevará mucho tiempo; aquí le esperamos.

¿Ha acabado? Ahora debería estar viendo su lista de aplicaciones de Microsoft, que probablemente esté vacía.  Cambiemos eso.

Haga clic en **Agregar una aplicación** y asígnele un nombre.  El portal le asignará a su aplicación un identificador de aplicación único global que usará más adelante en su código.  Si su aplicación incluye un componente del lado del servidor que necesita tokens de acceso para llamar a las API (como Office, Azure o su propia API web), aquí también le interesará crear un **Secreto de la aplicación**.

A continuación, agregue las plataformas que usará la aplicación.

* Para las aplicaciones basadas en web, proporcione un **URI de redirección** al que se puedan enviar los mensajes de inicio de sesión.
* Para las aplicaciones móviles, copie el URI de redirección predeterminado creado automáticamente para usted.
* Para las API web, se crea automáticamente un ámbito predeterminado para acceder a ellas. Puede elegir agregar ámbitos adicionales mediante el botón **Agregar ámbito**. También puede agregar todas las aplicaciones que estén previamente autorizadas para usar la API web mediante el formulario **Aplicaciones preautorizadas**. 


Si lo desea, puede personalizar la apariencia de la página de inicio de sesión en la sección Perfil.  Asegúrese de hacer clic en **Guardar** antes de continuar.

> [!NOTE]
> Cuando cree una aplicación con [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), la aplicación se registrará en el inquilino principal de la cuenta que use para iniciar sesión en el portal.  Esto significa que no se puede registrar una aplicación en el inquilino de Azure AD con una cuenta personal de Microsoft.  Si desea registrar explícitamente una aplicación en un inquilino determinado, inicie sesión con una cuenta creada originalmente en ese inquilino.
> 
> 

## <a name="build-a-quickstart-app"></a>Compilación de una aplicación de inicio rápido
Ahora que tiene una aplicación de Microsoft, puede completar uno de nuestros tutoriales de inicio rápido de v2.0.  Estas son algunas recomendaciones:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

