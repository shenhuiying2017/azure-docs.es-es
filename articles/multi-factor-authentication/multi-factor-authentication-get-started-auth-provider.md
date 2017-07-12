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
ms.date: 06/28/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 977640041f4b58a751848c96e2aa48eb2b284154
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017

---

<a id="getting-started-with-an-azure-multi-factor-auth-provider" class="xliff"></a>

# Introducción al Proveedor de Azure Multi-Factor Authentication
La verificación en dos pasos está disponible de forma predeterminada para los administradores globales que tienen usuarios de Azure Active Directory y Office 365. Sin embargo, si desea aprovechar las [características avanzadas](multi-factor-authentication-whats-next.md), debe adquirir la versión completa de Azure Multi-Factor Authentication (MFA).

Se utiliza un Proveedor de Azure Multi-Factor Authentication para aprovechar las características proporcionadas por la versión completa de Azure MFA. Es para aquellos usuarios que **no tienen licencias a través de Azure MFA, Azure AD Premium o Enterprise Mobility + Security (EMS)**.  De forma predeterminada Azure MFA, Azure AD Premium y EMS incluyen la versión completa de Azure MFA. Si cuenta con licencias, no necesita un Proveedor de Azure Multi-Factor Authentication.

Se necesita un proveedor de Azure Multi-Factor Authentication para descargar el SDK.

> [!IMPORTANT]
> Para descargar el SDK, cree un proveedor de Azure Multi-Factor Authentication, incluso si tiene licencias de Azure MFA, AAD Premium o EMS.  Si crea un proveedor de Azure Multi-Factor Authentication para este propósito y ya tiene licencias, asegúrese de crear el proveedor con el modelo **Por usuario habilitado**. A continuación, vincule el proveedor al directorio que contiene las licencias de Azure MFA, Azure AD Premium o EMS. Gracias a esta configuración, se asegura de que no se le cobrará, salvo que tenga más usuarios únicos ejecutando la verificación en dos pasos que el número de licencias que posee.

<a id="what-is-an-azure-multi-factor-auth-provider" class="xliff"></a>

## ¿Qué es un Proveedor de Azure Multi-Factor Authentication?

Si no tiene licencias de Azure Multi-Factor Authentication, puede crear un proveedor de autenticación para que solicite la verificación en dos pasos de los usuarios. Si está desarrollando una aplicación personalizada y desea habilitar Azure MFA, cree un proveedor de autenticación y [descargue el SDK](multi-factor-authentication-sdk.md).

Hay dos tipos de proveedores de autenticación y la diferencia radica en cómo se aplicarán cargos a su suscripción de Azure. La opción "por autenticación" calcula el número de autenticaciones que se realizan en el inquilino en un mes. Esta opción es la más adecuada si tiene un número de usuarios que se autentican solo en algunas ocasiones o si necesita MFA para una aplicación personalizada. La opción "por usuario" calcula el número de personas en el inquilino que realizan la verificación en dos pasos en un mes. Esta opción es mejor si tiene algunos usuarios con licencias pero necesita ampliar MFA a más usuarios de los permitidos por su licencia.

<a id="create-a-multi-factor-auth-provider" class="xliff"></a>

## Creación de un proveedor de autenticación multifactor
Use los pasos siguientes para crear un Proveedor de Azure Multi-Factor Authentication.

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
      * No es necesario tener un directorio de Azure AD para crear un Proveedor de autenticación multifactor. Deje este cuadro en blanco si planea usar el servidor de Azure Multi-Factor Authentication o el SDK.
      * El Proveedor de Multi-Factor Authentication debe asociarse con un directorio de Azure AD para sacar el máximo partido a las características avanzadas.
      * Azure AD Connect, AAD Sync o DirSync solo son un requisito si va a sincronizar su entorno de Active Directory local con un directorio de Azure AD.  Si solo utiliza un directorio de Azure AD que no está sincronizado, esto no es necesario.
        
        ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. Al hacer clic en Crear, se crea el Proveedor de Multi-Factor Authentication y debe ver un mensaje que indica: **El proveedor de Multi-Factor Authentication se creó correctamente**. Haga clic en **Aceptar**.
   
   ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

