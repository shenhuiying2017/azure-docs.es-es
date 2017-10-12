---
title: "Tutorial: Integración de Azure Active Directory con Jive | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Jive."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 957b152fdd40d08a867e788b0cb9f7d57ed481e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-jive-for-user-provisioning"></a>Tutorial: Configuración de Jive para el aprovisionamiento de usuarios

El objetivo de este tutorial es explicar los pasos que debe realizar en Jive y Azure AD para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD para Jive.

## <a name="prerequisites"></a>Requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure Active Directory.
*   Una suscripción habilitada para inicio de sesión único en Jive.
*   Una cuenta de usuario de Jive con permisos de Team Admin (administrador de equipo).

## <a name="assigning-users-to-jive"></a>Asignación de usuarios a Jive

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación de Azure AD.

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceso a la aplicación Jive. Una vez decidido, puede asignar estos usuarios a la aplicación de Jive siguiendo estas instrucciones:

[Asignar un usuario o grupo a una aplicación empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Sugerencias importantes para asignar usuarios a Jive

*   Se recomienda asignar un único usuario de Azure AD a Jive para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar a un usuario a Jive, debe seleccionar un rol de usuario válido. El rol "Acceso predeterminado" no funciona para realizar el aprovisionamiento.

## <a name="enable-user-provisioning"></a>Habilitación del aprovisionamiento de usuarios

Esta sección le guía en la conexión de Azure AD a la API de aprovisionamiento de cuentas de usuario de Jive, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas de Jive en función de la asignación de grupos y usuarios Azure AD.

> [!TIP]
> También puede habilitar el inicio de sesión único basado en SAML para Jive siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="to-configure-user-account-provisioning"></a>Para configurar el aprovisionamiento automático de cuentas de usuario:

El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Jive.
Como parte de este procedimiento, es necesario proporcionar un token de seguridad de usuario que deberá solicitar a Jive.com.

1. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Si ya ha configurado Jive para el inicio de sesión único, busque la instancia de Jive mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **Jive** en la Galería de aplicaciones. Seleccione Jive en los resultados de búsqueda y agréguelo a la lista de aplicaciones.

3. Seleccione la instancia de Jive y, después, seleccione la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**. 

    ![Aprovisionamiento](./media/active-directory-saas-jive-provisioning-tutorial/provisioning.png)

5. En la sección **Credenciales de administrador**, proporcione los siguientes valores de configuración:
   
    a. En el cuadro de texto **Nombre de usuario del administrador de Jive**, escriba un nombre de cuenta de Jive que tenga asignado el perfil **Administrador del sistema** en Jive.com.
   
    b. En el cuadro de texto **Contraseña de administrador de Jive** , escriba la contraseña para esta cuenta.
   
    c. En el cuadro de texto **Dirección URL de inquilino de Jive** , escriba la dirección URL del inquilino de Jive.
      
      > [!NOTE]
      > La dirección URL del inquilino de Jive es la que se usa en su organización para iniciar sesión en Jive.  
      > Normalmente, la dirección URL tiene el formato siguiente: **www.\<organización\>.jive.com**.          

6. En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación de Jive.

7. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla que aparece a continuación.

8. Haga clic en **Guardar**.

9. En la sección Asignaciones, seleccione **Sincronizar usuarios de Azure Active Directory con Jive.**

10. En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizan entre Azure AD y Jive. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Jive con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

11. Para habilitar el servicio de aprovisionamiento de Azure AD para Jive, cambie el **Estado de aprovisionamiento** a **Activado** en la sección Configuración

12. Haga clic en **Guardar**.

Esta acción inicia la sincronización inicial de todos los usuarios y grupos asignados a Jive en la sección Usuarios y grupos. La sincronización inicial tardará más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 20 minutos, si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los informes de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de Jive.

Ahora puede crear una cuenta de prueba. Espere 20 minutos para comprobar que la cuenta se ha sincronizado con Jive.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del inicio de sesión único](active-directory-saas-jive-tutorial.md)