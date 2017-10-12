---
title: 'Azure Active Directory B2C: atributos personalizados | Microsoft Docs'
description: "Uso de atributos personalizados en Azure Active Directory B2C para recopilar información sobre sus consumidores"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 055ffb0a-197b-4716-8dad-1fd8a01e174f
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 356aaeff3a78fc7b682d621e8e0de9312582b2fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory B2C: uso de atributos personalizados para recopilar información sobre los consumidores
El directorio de Azure Active Directory (Azure AD) B2C incluye un conjunto integrado de información (atributos): Nombre propio, Apellido, Ciudad, Código postal y otros atributos. Sin embargo, todas las aplicaciones orientadas al consumidor tienen requisitos únicos en lo relativo a qué atributos recopilan de los consumidores. Con Azure AD B2C, puede ampliar el conjunto de atributos que se almacenan en cada cuenta de cliente. Puede crear atributos personalizados en el [Portal de Azure](https://portal.azure.com/) y usarlos en las directivas de registro, como se muestra a continuación. También puede leer y escribir estos atributos mediante la [API de Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [!NOTE]
> Los atributos personalizados usan las [Extensiones de esquema de directorio de la API de Azure AD Graph](https://msdn.microsoft.com/library/azure/dn720459.aspx).
> 
> 

## <a name="create-a-custom-attribute"></a>Creación de un atributo personalizado
1. [Siga estos pasos para ir a la hoja de características de B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Haga clic en **Atributos de usuario**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** para el atributo personalizado (por ejemplo, "ShoeSize") y, opcionalmente, una **Descripción**. Haga clic en **Crear**.
   
   > [!NOTE]
   > Actualmente solo está disponible el **Tipo de datos** "String".
   > 
   > 

El atributo personalizado ahora está disponible en la lista de **Atributos de usuario**, y puede usarlo en las directivas de registro.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Uso de un atributo personalizado en la directiva de registro
1. [Siga estos pasos para ir a la hoja de características de B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Haga clic en **Directivas de registro**.
3. Haga clic en la directiva de registro (por ejemplo, "B2C_1_SiUp") para abrirla. Haga clic en **Editar** en la parte superior de la hoja.
4. Haga clic en **Atributos de registro** y seleccione el atributo personalizado (por ejemplo, "ShoeSize"). Haga clic en **OK**.
5. Haga clic en **Notificaciones de aplicación** y seleccione el atributo personalizado. Haga clic en **OK**.
6. Haga clic en **Guardar** en la parte superior de la hoja.

Puede usar la característica "Ejecutar ahora" en la directiva para comprobar la experiencia del consumidor. Ahora debe ver "ShoeSize" en la lista de atributos que se recopilan durante el registro del consumidor y en el token enviado de vuelta a la aplicación.

## <a name="notes"></a>Notas
* Junto con las directivas de registro, los atributos personalizados también se pueden utilizar en las directivas de registro o inicio de sesión y en las directivas de edición del perfil.
* Hay un límite conocido de atributos personalizados. Solo se crea la primera vez que se utiliza en cualquier directiva y no cuando se agrega a la lista de **atributos de usuario**.

