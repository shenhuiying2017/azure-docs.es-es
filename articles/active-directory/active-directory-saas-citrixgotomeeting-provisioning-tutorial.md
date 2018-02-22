---
title: "Tutorial: Configuración de GoToMeeting para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y GoToMeeting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 48b8072b5ebe61f5a9dccd9d8ea31e5a6945f265
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Tutorial: Configuración de GoToMeeting para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es explicar los pasos que debe realizar en GoToMeeting y Azure AD para aprovisionar y cancelar el aprovisionamiento de cuentas de usuario de Azure AD para GoToMeeting automáticamente.

## <a name="prerequisites"></a>requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure Active Directory.
*   Una suscripción habilitada para el inicio de sesión único en GoToMeeting.
*   Una cuenta de usuario de GoToMeeting con permisos de administrador de equipo.

## <a name="assigning-users-to-gotomeeting"></a>Asignación de usuarios a GoToMeeting

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación de Azure AD.

Antes de configurar y habilitar el servicio de aprovisionamiento, tiene que decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceso a la aplicación GoToMeeting. Una vez decidido, puede asignar estos usuarios a la aplicación de GoToMeeting siguiendo estas instrucciones:

[Asignar un usuario o grupo a una aplicación empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Sugerencias importantes para asignar usuarios a GoToMeeting

*   Se recomienda asignar un único usuario de Azure AD a GoToMeeting para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar a un usuario a GoToMeeting, tiene que seleccionar un rol de usuario válido. El rol "Acceso predeterminado" no funciona para realizar el aprovisionamiento.

## <a name="enable-automated-user-provisioning"></a>Habilitación del aprovisionamiento automático de usuarios

Esta sección lo guía a través de los pasos necesarios para conectar la API de aprovisionamiento de cuentas de usuario de GoToMeeting, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas en GoToMeeting en función de la asignación de grupos y usuarios de Azure AD.

> [!TIP]
> También puede decidir habilitar el inicio de sesión único basado en SAML para GoToMeeting siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar el aprovisionamiento automático de cuentas de usuario, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Si ya ha configurado GoToMeeting para el inicio de sesión único, busque la instancia de GoToMeeting con el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **GoToMeeting** en la galería de aplicaciones. Seleccione GoToMeeting en los resultados de búsqueda y agréguelo a la lista de aplicaciones.

3. Seleccione la instancia de GoToMeeting y después la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**. 

    ![Aprovisionamiento](./media/active-directory-saas-citrixgotomeeting-provisioning-tutorial/provisioning.png)

5. En la sección Credenciales de administración, realice los pasos siguientes:
   
    a. En el cuadro de texto **Nombre de usuario de administrador de GoToMeeting**, escriba el nombre de usuario de un administrador.

    b. En el cuadro de texto **Contraseña de administrador de GoToMeeting**, escriba la contraseña del administrador.

6. En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación de GoToMeeting. Si se produce un error de conexión, asegúrese de que su cuenta de GoToMeeting tiene permisos de administrador de equipo y vuelva a intentar el paso de **"Credenciales de administración"**.

7. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla.

8. Haga clic en **Guardar**.

9. En la sección Asignaciones seleccione **Synchronize Azure Active Directory Users to GoToMeeting** (Sincronizar usuarios de Azure Active Directory con GoToMeeting).

10. En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizarán entre Azure AD y GoToMeeting. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de GoToMeeting con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

11. Para habilitar el servicio de aprovisionamiento de Azure AD para GoToMeeting, cambie el **Estado de aprovisionamiento** a **Activado** en la sección Configuración

12. Haga clic en **Guardar**.

Comienza la sincronización inicial de todos los usuarios y grupos asignados a GoToMeeting en la sección Usuarios y grupos. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los registros de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de GoToMeeting.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del inicio de sesión único](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


