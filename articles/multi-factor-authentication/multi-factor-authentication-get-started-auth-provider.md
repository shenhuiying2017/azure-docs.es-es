---
title: "Introducción al Proveedor de Azure Multi-Factor Authentication | Microsoft Docs"
description: "Aprenda cómo crear un Proveedor de Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/28/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: ed14a5a762bab20a1ccde699504dd21f25009b52
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Introducción al Proveedor de Azure Multi-Factor Authentication
La verificación en dos pasos está disponible de forma predeterminada para los administradores globales que tienen usuarios de Azure Active Directory y Office 365. Sin embargo, si desea aprovechar las [características avanzadas](multi-factor-authentication-whats-next.md), debe adquirir la versión completa de Azure Multi-Factor Authentication (MFA).

Se utiliza un Proveedor de Azure Multi-Factor Authentication para aprovechar las características proporcionadas por la versión completa de Azure MFA. Es para aquellos usuarios que **no tienen licencias a través de Azure MFA, Azure AD Premium o Enterprise Mobility + Security (EMS)**.  De forma predeterminada Azure MFA, Azure AD Premium y EMS incluyen la versión completa de Azure MFA. Si cuenta con licencias, no necesita un Proveedor de Azure Multi-Factor Authentication.

Se necesita un proveedor de Azure Multi-Factor Authentication para descargar el SDK.

> [!IMPORTANT]
> Para descargar el SDK, debe crear un proveedor de Multi-Factor Auth de Azure, incluso si tiene licencias de Azure MFA, AAD Premium o EMS.  Si crea un proveedor de Azure Multi-Factor Authentication para este propósito y ya tiene licencias, asegúrese de crear el proveedor con el modelo **Por usuario habilitado**. A continuación, vincule el proveedor al directorio que contiene las licencias de Azure MFA, Azure AD Premium o EMS. Gracias a esta configuración, se asegura de que no se le cobrará, salvo que tenga más usuarios únicos ejecutando la verificación en dos pasos que el número de licencias que posee.

## <a name="what-is-an-azure-multi-factor-auth-provider"></a>¿Qué es un Proveedor de Azure Multi-Factor Authentication?

Si no tiene licencias de Azure Multi-Factor Authentication, puede crear un proveedor de autenticación para que solicite la verificación en dos pasos de los usuarios. Si está desarrollando una aplicación personalizada y desea habilitar Azure MFA, cree un proveedor de autenticación y [descargue el SDK](multi-factor-authentication-sdk.md).

Hay dos tipos de proveedores de autenticación y la diferencia radica en cómo se aplicarán cargos a su suscripción de Azure. La opción "por autenticación" calcula el número de autenticaciones que se realizan en el inquilino en un mes. Esta opción es la más adecuada si tiene un número de usuarios que se autentican solo en algunas ocasiones o si necesita MFA para una aplicación personalizada. La opción "por usuario" calcula el número de personas en el inquilino que realizan la verificación en dos pasos en un mes. Esta opción es mejor si tiene algunos usuarios con licencias pero necesita ampliar MFA a más usuarios de los permitidos por su licencia.

## <a name="create-a-multi-factor-auth-provider"></a>Creación de un proveedor de autenticación multifactor
Use los pasos siguientes para crear un Proveedor de Azure Multi-Factor Authentication. Los proveedores de Azure Multi-Factor Authentication solo se pueden crear en el Portal de Azure clásico. Si no puede iniciar sesión en dicho portal, asegúrese de que su inquilino de Azure AD esté [asociado a una suscripción de Azure](../active-directory/active-directory-how-subscriptions-associated-directory.md). 

1. Inicie sesión como administrador en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En la parte izquierda, seleccione **Active Directory**.
3. En la página Active Directory, en la parte superior, seleccione **Proveedores de Multi-Factor Authentication**.
   
   ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)

4. Haga clic en **Nuevo**en la parte inferior.
   
   ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)

5. En App Services, seleccione **Proveedor de autenticación multifactor**
   
   ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)

6. Seleccione **Creación rápida**.
   
   ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)

7. Rellene los campos siguientes y seleccione **Crear**.
   1. **Nombre** : nombre del Proveedor de Multi-Factor Authentication.
   2. **Modelo de uso**: puede elegir una de las dos opciones a continuación:
      * Por autenticación: modelo de adquisición que se carga por autenticación. Normalmente se usa para escenarios que utilizan Azure Multi-Factor Authentication en una aplicación orientada al consumidor.
      * Por usuario habilitado: modelo de adquisición que se carga por usuario habilitado. Suele utilizarse para el acceso de los empleados a aplicaciones como Office 365. Elija esta opción si tiene algunos usuarios que ya tienen una licencia de Azure MFA.
   3. **Directorio** : inquilino de Azure Active Directory con el que está asociado el proveedor de Multi-Factor Authentication. Tenga en cuenta lo siguiente:
      * No es necesario tener un directorio de Azure AD para crear un Proveedor de autenticación multifactor. Si solo planea descargar el Servidor Azure Multi-Factor Authentication o el SDK, deje este cuadro en blanco.
      * El Proveedor de Multi-Factor Authentication debe asociarse con un directorio de Azure AD para sacar el máximo partido a las características avanzadas.
      * Solo un proveedor de Multi-Factor Authentication puede asociarse con un directorio de Azure AD.  
      ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. Al hacer clic en Crear, se crea el Proveedor de Multi-Factor Authentication y debe ver un mensaje que indica: **El proveedor de Multi-Factor Authentication se creó correctamente**. Haga clic en **Aceptar**.  
   
   ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

## <a name="manage-your-multi-factor-auth-provider"></a>Administración del proveedor de Multi-Factor Authentication

No se puede cambiar el modelo de uso (por usuario habilitado o por autenticación) después de crear un proveedor de MFA. Sin embargo, puede eliminar el proveedor de MFA y crear otro con un modelo de uso distinto.

Si el proveedor de Multi-Factor Authentication actual está asociado a un directorio de Azure AD (también conocido como un inquilino de Azure AD), puede eliminar el proveedor de MFA con seguridad y crear uno que esté vinculado al mismo inquilino de Azure AD. Como alternativa, si compra suficientes licencias de MFA, Azure AD Premium o Enterprise Mobility + Security (EMS) para abarcar a todos los usuarios habilitados para MFA, puede eliminar por completo el proveedor de MFA.

Si el proveedor de MFA no está vinculado a un inquilino de Azure AD o si vincula el nuevo proveedor de MFA a un inquilino de Azure AD diferente, las opciones de configuración y parámetros de usuario no se transferirán. Además, los servidores Azure MFA se tendrán que reactivar mediante las credenciales de activación generadas a través del nuevo proveedor de MFA. Reactivar los servidores MFA para vincularlos al nuevo proveedor de MFA no afecta a la autenticación por llamada telefónica y mensaje de texto, sino que las notificaciones de aplicación móvil dejarán de funcionar para todos los usuarios hasta que se reactive la aplicación móvil.

## <a name="next-steps"></a>Pasos siguientes

[Descarga del SDK de Multi-Factor Authentication](multi-factor-authentication-sdk.md)

[Configuración de Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md)
