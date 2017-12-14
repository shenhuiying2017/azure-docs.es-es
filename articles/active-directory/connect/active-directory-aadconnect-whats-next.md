---
title: "Pasos siguientes y cómo administrar Azure AD Connect en Azure AD Connect| Microsoft Docs"
description: "Aprenda a ampliar la configuración predeterminada y las tareas operativas de Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: e898ee203276b072605fe0f21af633ecfdaae1fe
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Pasos siguientes y cómo administrar Azure AD Connect
Utilice los procedimientos operativos de este artículo para personalizar Azure Active Directory (Azure AD) Connect para satisfacer las necesidades y requisitos de su organización.  

## <a name="add-additional-sync-admins"></a>Pasos para agregar administradores de sincronización adicionales
De manera predeterminada, solo el usuario que realizó la instalación y los administradores locales pueden administrar el motor de sincronización instalado. Para que otras personas puedan acceder al motor de sincronización y administrarlo, busque el grupo denominado "ADSyncAdmins" en el servidor local y agréguelas a dicho grupo.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Asignación de licencias a usuarios de Azure AD Premium y Enterprise Mobility Suite
Ahora que los usuarios se han sincronizado a la nube, debe asignarles una licencia para que puedan empezar a trabajar con aplicaciones en la nube, como Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Para asignar una licencia de Azure AD Premium o Enterprise Mobility Suite

1. Inicie sesión en Azure Portal como administrador.
2. En la parte izquierda, seleccione **Active Directory**.
3. En la página **Active Directory**, haga doble clic en el directorio que tiene los usuarios que desea configurar.
4. En la parte superior de la página de directorios, seleccione **Licencias**.
5. En la página **Licencias**, seleccione **Active Directory Premium** o **Enterprise Mobility Suite** y, a continuación, haga clic en **Asignar**.
6. En el cuadro de diálogo, seleccione los usuarios a los que desee asignar las licencias y, a continuación, haga clic en el icono de marca de verificación para guardar los cambios.

## <a name="verify-the-scheduled-synchronization-task"></a>Comprobación de la tarea de sincronización programada
Utilice Azure Portal para comprobar el estado de una sincronización.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Para comprobar la tarea de sincronización programada
1. Inicie sesión en Azure Portal como administrador.
2. En la parte izquierda, seleccione **Active Directory**.
3. En la página **Active Directory**, haga doble clic en el directorio que tiene los usuarios que desea configurar.
4. En la parte superior de la página de directorios, seleccione **Integración de directorios**.
5. En la **integración con Active Directory local**, anote la hora de la última sincronización.

<center>![Hora de sincronización de directorios](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Inicio de una tarea de sincronización programada
Si necesita ejecutar una tarea de sincronización, ejecute de nuevo el Asistente para Azure AD Connect.  Deberá proporcionar sus credenciales de Azure AD.  En el asistente, seleccione la tarea **Personalizar las opciones de sincronización** y haga clic en **Siguiente** para avanzar por el asistente. Al final, asegúrese de que la casilla **Inicie el proceso de sincronización en cuanto se complete la configuración inicial** está seleccionada.

<center>![Inicio de la sincronización](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Para más información sobre el programador de sincronización de Azure AD Connect, consulte [Programador de Azure AD Connect](active-directory-aadconnectsync-feature-scheduler.md)

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tareas adicionales disponibles en Azure AD Connect
Después de la instalación inicial de Azure AD Connect, siempre puede volver a iniciar el asistente desde la página de inicio de Azure AD Connect o el acceso directo de escritorio.  Verá que al volver a pasar por el asistente aparecen algunas opciones nuevas en el formulario de tareas adicionales.  

En la tabla siguiente se proporciona un resumen de estas tareas y una breve descripción de cada una de ellas.

![Lista de tareas adicionales](./media/active-directory-aadconnect-whats-next/addtasks.png)

| Tarea adicional | Descripción |
| --- | --- |
| **Ver el escenario seleccionado** |Visualice su solución actual de Azure AD Connect.  Incluye la configuración general, los directorios sincronizados y la configuración de sincronización. |
| **Personalizar las opciones de sincronización** |Puede cambiar la configuración actual, como la adición de bosques de Active Directory adicionales a la configuración o habilitar las opciones de sincronización, como usuario, grupo, dispositivo o reescritura de contraseña. |
| **Habilitar el modo provisional** |Información provisional que no se sincroniza inmediatamente y no se exporta a Azure AD ni a Active Directory local.  Con esta función puede obtener una vista previa de las sincronizaciones antes de que se produzcan. |

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de la [integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
