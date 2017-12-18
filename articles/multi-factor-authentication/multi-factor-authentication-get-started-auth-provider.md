---
title: "Introducción al Proveedor de Azure Multi-Factor Authentication | Microsoft Docs"
description: "Aprenda cómo crear un Proveedor de Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: b04fd6f969461cf39016df951007c59047c8857a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Introducción al proveedor de Azure Multi-Factor Authentication
La verificación en dos pasos está disponible de forma predeterminada para los administradores globales que tienen usuarios de Azure Active Directory y Office 365. Sin embargo, si desea aprovechar las [características avanzadas](multi-factor-authentication-whats-next.md), debe adquirir la versión completa de Azure Multi-Factor Authentication (MFA).

Se utiliza un Proveedor de Azure Multi-Factor Authentication para aprovechar las características proporcionadas por la versión completa de Azure MFA. Es para aquellos usuarios que **no tienen licencias a través de Azure MFA, Azure AD Premium o Enterprise Mobility + Security (EMS)**.  De forma predeterminada Azure MFA, Azure AD Premium y EMS incluyen la versión completa de Azure MFA. Si cuenta con licencias, no necesita un Proveedor de Azure Multi-Factor Authentication.

Se necesita un proveedor de Azure Multi-Factor Authentication para descargar el SDK.

> [!IMPORTANT]
> Se ha anunciado el desuso del Kit de desarrollo de Software (SDK) de Azure Multi-factor Authentication. Esta característica ya no se admite para los nuevos clientes. Los clientes actuales podrán continuar con el SDK hasta el 14 de noviembre de 2018. Después de ese momento, se producirá un error en las llamadas al SDK.

> [!IMPORTANT]
>Para descargar el SDK, debe crear un proveedor de Multi-Factor Auth de Azure, incluso si tiene licencias de Azure MFA, AAD Premium o EMS.  Si crea un proveedor de Azure Multi-Factor Authentication para este propósito y ya tiene licencias, asegúrese de crear el proveedor con el modelo **Por usuario habilitado**. A continuación, vincule el proveedor al directorio que contiene las licencias de Azure MFA, Azure AD Premium o EMS. Gracias a esta configuración, se asegura de que no se le cobrará, salvo que tenga más usuarios únicos ejecutando la verificación en dos pasos que el número de licencias que posee. 

## <a name="what-is-an-mfa-provider"></a>¿Qué es un proveedor de MFA?

Si no tiene licencias de Azure Multi-Factor Authentication, puede crear un proveedor de autenticación para que solicite la verificación en dos pasos de los usuarios.

Hay dos tipos de proveedores de autenticación y la diferencia radica en cómo se aplicarán cargos a su suscripción de Azure. La opción "por autenticación" calcula el número de autenticaciones que se realizan en el inquilino en un mes. Esta opción es la más adecuada si tiene un número de usuarios que se autentican solo en algunas ocasiones. La opción "por usuario" calcula el número de personas en el inquilino que realizan la verificación en dos pasos en un mes. Esta opción es mejor si tiene algunos usuarios con licencias pero necesita ampliar MFA a más usuarios de los permitidos por su licencia.

## <a name="create-an-mfa-provider"></a>Creación de un proveedor de MFA

Use los pasos siguientes para crear un proveedor de Azure Multi-Factor Authentication en Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global. 
2. Seleccione **Azure Active Directory** > **MFA Server** (Servidor MFA)  > **Proveedores**.

   ![Proveedores][Providers]

3. Seleccione **Agregar**.
4. Rellene los campos siguientes y seleccione **Agregar**.
   - **Nombre**: el nombre del proveedor.
   - **Modelo de uso**: puede elegir una de las dos opciones a continuación:
      * Por autenticación: modelo de adquisición que se carga por autenticación. Normalmente se usa para escenarios que utilizan Azure Multi-Factor Authentication en una aplicación orientada al consumidor.
      * Por usuario habilitado: modelo de adquisición que se carga por usuario habilitado. Suele utilizarse para el acceso de los empleados a aplicaciones como Office 365. Elija esta opción si tiene algunos usuarios que ya tienen una licencia de Azure MFA.
   - **Suscripción**: la suscripción de Azure en la que se cobra la actividad de verificación en dos pasos a través del proveedor. 
   - **Directorio**: el inquilino de Azure Active Directory al que está asociado el proveedor.
      * No es necesario tener un directorio de Azure AD para crear un proveedor. Si solo planea descargar el Servidor Azure Multi-Factor Authentication, deje este cuadro en blanco.
      * El proveedor debe estar asociado a un directorio de Azure AD para aprovechar las características avanzadas.
      * Solo un proveedor puede estar asociado a un directorio de Azure AD.

## <a name="manage-your-mfa-provider"></a>Administración del proveedor de MFA

No se puede cambiar el modelo de uso (por usuario habilitado o por autenticación) después de crear un proveedor de MFA. Sin embargo, puede eliminar el proveedor de MFA y crear otro con un modelo de uso distinto.

Si el proveedor de Multi-Factor Authentication actual está asociado a un directorio de Azure AD (también conocido como un inquilino de Azure AD), puede eliminar el proveedor de MFA con seguridad y crear uno que esté vinculado al mismo inquilino de Azure AD. Como alternativa, si compra suficientes licencias de MFA, Azure AD Premium o Enterprise Mobility + Security (EMS) para abarcar a todos los usuarios habilitados para MFA, puede eliminar por completo el proveedor de MFA.

Si el proveedor de MFA no está vinculado a un inquilino de Azure AD o si vincula el nuevo proveedor de MFA a un inquilino de Azure AD diferente, las opciones de configuración y parámetros de usuario no se transferirán. Además, los servidores Azure MFA se tendrán que reactivar mediante las credenciales de activación generadas a través del nuevo proveedor de MFA. Reactivar los servidores MFA para vincularlos al nuevo proveedor de MFA no afecta a la autenticación por llamada telefónica y mensaje de texto, sino que las notificaciones de aplicación móvil dejarán de funcionar para todos los usuarios hasta que se reactive la aplicación móvil.

## <a name="next-steps"></a>Pasos siguientes

[Configuración de Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md)

[Providers]: ./media/multi-factor-authentication-get-started-auth-provider/add-providers.png "Agregar proveedores de MFA"
