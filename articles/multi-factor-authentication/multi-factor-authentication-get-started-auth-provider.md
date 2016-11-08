---
title: Introducción al Proveedor de Microsoft Azure Multi-Factor Authentication
description: Aprenda cómo crear un Proveedor de Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Introducción al Proveedor de Azure Multi-Factor Authentication
La autenticación multifactor está disponible de forma predeterminada para los administradores globales que tienen usuarios de Azure Active Directory y Office 365. Sin embargo, si desea aprovechar las [características avanzadas](multi-factor-authentication-whats-next.md), debe adquirir la versión completa de Azure MFA.

> [!NOTE]
> Se utiliza un Proveedor de Azure Multi-Factor Authentication para aprovechar las características proporcionadas por la versión completa de Azure MFA. Es para aquellos usuarios que **no tienen licencias a través de Azure MFA, Azure AD Premium o EMS**. De forma predeterminada Azure MFA, Azure AD Premium y EMS incluyen la versión completa de Azure MFA. Si cuenta con licencias, no necesita un Proveedor de Azure Multi-Factor Authentication.
> 
> 

Se necesita un proveedor de Azure Multi-Factor Authentication si desea descargar el SDK.

> [!IMPORTANT]
> Si desea descargar el SDK, debe crear un proveedor de Azure Multi-Factor Authentication, incluso si tiene licencias de Azure MFA, AAD Premium o EMS. Si crea un proveedor de Azure Multi-Factor Authentication para este propósito y ya tiene licencias, hay que crear el proveedor según el modelo **Por usuario habilitado** y vincular el proveedor al directorio que contiene las licencias de Azure MFA, AD Premium o EMS. Así se asegura de que no se le cobrará a menos que tenga más usuarios únicos mediante el SDK que el número de licencias que posee.
> 
> 

Use los pasos siguientes para crear un Proveedor de Azure Multi-Factor Authentication.

## Creación de un Proveedor de autenticación multifactor
- - -
1. Inicie sesión como administrador en el **Portal de Azure clásico**.
2. En la parte izquierda, seleccione **Active Directory**.
3. En la página Active Directory, en la parte superior, seleccione **Proveedores de Multi-Factor Authentication**. ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Haga clic en **Nuevo** en la parte inferior. ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. En **Servicios de aplicaciones**, seleccione **Proveedor de Multi-Factor Authentication**.![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Seleccione **Creación rápida**. ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
7. Rellene los campos siguientes y seleccione **Crear**.
   1. **Nombre**: nombre del Proveedor de Multi-Factor Authentication.
   2. **Modelo de uso**: modelo de uso del Proveedor de Multi-Factor Authentication.
      * Por autenticación: modelo de adquisición que se carga por autenticación. Normalmente se usa para escenarios que utilizan Azure Multi-Factor Authentication en una aplicación orientada al consumidor.
      * Por usuario habilitado: modelo de adquisición que se carga por usuario habilitado. Suele utilizarse para el acceso de los empleados a aplicaciones como Office 365.
   3. **Directorio**: inquilino de Azure Active Directory con el que está asociado el proveedor de Multi-Factor Authentication. Tenga en cuenta lo siguiente:
      * No es necesario tener un directorio de Azure AD para crear un Proveedor de autenticación multifactor. Simplemente deje este cuadro en blanco si planea usar el servidor de Azure Multi-Factor Authentication o el SDK.
      * El Proveedor de Multi-Factor Authentication debe asociarse con un directorio de Azure AD para sacar el máximo partido a las características avanzadas.
      * Azure AD Connect, AAD Sync o DirSync solo son un requisito si va a sincronizar su entorno de Active Directory local con un directorio de Azure AD. Si solo utiliza un directorio de Azure AD que no está sincronizado, esto no es necesario. ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
8. Al hacer clic en Crear, se crea el Proveedor de Multi-Factor Authentication y debe ver un mensaje que indica: **El proveedor de Multi-Factor Authentication se creó correctamente**. Haga clic en **Aceptar**. ![Creación de un proveedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)

<!---HONumber=AcomDC_0921_2016-->