---
title: Cambio del inquilino de Azure Active Directory en Azure RemoteApp | Microsoft Docs
description: "Obtenga información sobre cómo cambiar el inquilino de Azure Active Directory asociado con Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 20faf169-6e48-428a-8bdd-f231daff19fa
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1199ca2378cfe4adba2d591bfab204113b4a0468
ms.lasthandoff: 03/31/2017


---
# <a name="change-the-azure-active-directory-tenant-in-azure-remoteapp"></a>Cambio del inquilino de Azure Active Directory en Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp dejará de estar disponible el 31 de agosto de 2017. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Azure RemoteApp usa Azure Active Directory (Azure AD) para permitir el acceso de usuario. El único inquilino de Azure AD que puede utilizar en Azure RemoteApp es el que se encuentra asociado a la suscripción de Azure. Puede ver la suscripción asociada en la página **Configuración** del portal. Observe la columna **Directorio** en la pestaña **Suscripciones**.

> [!NOTE]
> Para que este cambio surta efecto, primero debe quitar todos los usuarios del inquilino de Azure Active Directory existente de todas las colecciones de Azure RemoteApp. Para ello, vaya al Portal de Azure y en la pestaña **Azure RemoteApp** abra todas las colecciones de Azure RemoteApp. Vaya a la pestaña **Usuarios** y quite los usuarios que pertenecen al inquilino de Azure Active Directory actual. Repita esta acción para todas las colecciones de Azure RemoteApp existentes. Si no lleva esto a cabo, no podrá crear ni aplicar parches a las colecciones.
> 
> 

Si desea usar otro inquilino, use estos pasos para cambiar la asociación a su suscripción:

1. En el portal, quite todo usuario de Azure AD al que haya dado acceso a las colecciones de Azure RemoteApp. (Consulte la nota anterior para conocer los pasos sobre cómo hacerlo).
2. Establezca una cuenta Microsoft (anteriormente llamada Live ID) como administrador de servicios. (¿No sabe si ya es administrador de servicios? Puede averiguarlo si hace clic en **Configuración -> Administradores**). Ahora, le mostramos cómo cambiar eso:
   
   1. Haga clic en el usuario en la esquina superior derecha y, a continuación, haga clic en **Ver mi factura**.
   2. Haga clic en la suscripción. A continuación, en la nueva página, desplácese hacia abajo y haga clic en **Editar detalles de suscripción** a la derecha. (Hacia el medio de la parte inferior derecha, por si eso le ayuda a encontrarlo).
   3. Escriba la cuenta de Microsoft para el usuario que debe ser el Administrador de servicios.
3. Ahora, cierre sesión en el portal y, a continuación, vuelva a iniciarla con la cuenta Microsoft que especificó en el paso anterior.
4. Haga clic en **Nuevo > Servicios de aplicaciones > Active Directory > Directorio > Creación personalizada**.
5. En **Directorio**, elija **Usar directorio existente**. Vamos a tener que iniciar sesión en el portal ahora, así que elija **Estoy listo para cerrar la sesión ahora**.
6. Inicie sesión de nuevo en el portal como administrador global del directorio que desea agregar. (Si no fuera todavía administrador global, lo será después de volver a iniciar sesión y luego cerrarla).
7. Se le preguntará al iniciar sesión si desea usar al inquilino existente de AD con su suscripción. Haga clic en **Continuar**, y luego haga clic en **Cerrar sesión ahora**.
8. Inicie sesión de nuevo y vuelva a **Configuración -> Suscripciones**. Seleccione la suscripción y haga clic en **Editar directorio**. Seleccione el inquilino de Azure AD que desea usar.

Ahora puede usar el nuevo inquilino de Azure AD para controlar el acceso a la suscripción de Azure y para configurar el acceso de usuario en Azure RemoteApp.


