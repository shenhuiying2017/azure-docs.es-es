---
title: 'Tutorial: Configuración de ServiceNow para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD para ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: d3e64cc232aa01a5619a55290f9b07421165a6d5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698737"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Tutorial: Configuración de ServiceNow para el aprovisionamiento automático de usuarios con Azure Active Directory

El objetivo de este tutorial es explicar los pasos que debe realizar en ServiceNow y Azure AD para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD para ServiceNow.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con ServiceNow, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Para ServiceNow, una instancia o inquilino de ServiceNow, versión Calgary o superior
- Para ServiceNow Express, una instancia de ServiceNow Express, versión Helsinki o superior

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).


## <a name="assigning-users-to-servicenow"></a>Asignación de usuarios a ServiceNow

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación de Azure AD.

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceso a la aplicación ServiceNow. Una vez decidido, puede asignar estos usuarios a la aplicación de ServiceNow según las instrucciones del artículo sobre la [asignación de un usuario o un grupo a una aplicación empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).


> [!IMPORTANT]
>*   Se recomienda asignar un único usuario de Azure AD a ServiceNow para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.
>*   Al asignar a un usuario a ServiceNow, debe seleccionar un rol de usuario válido. El rol "Acceso predeterminado" no funciona para realizar el aprovisionamiento.

## <a name="enable-automated-user-provisioning"></a>Habilitación del aprovisionamiento automático de usuarios

Esta sección lo guía a través de los pasos necesarios para conectar la API de aprovisionamiento de su cuenta de usuario de ServiceNow, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas de ServiceNow en función de la asignación de grupos y usuarios de Azure AD.

> [!TIP]
>También puede decidir habilitar el inicio de sesión único basado en SAML para ServiceNow según las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar el aprovisionamiento automático de cuentas de usuario

1. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Si ya ha configurado ServiceNow para el inicio de sesión único, busque la instancia de ServiceNow con el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **ServiceNow** en la galería de aplicaciones. Seleccione ServiceNow en los resultados de la búsqueda y agréguelo a la lista de aplicaciones.

3. Seleccione la instancia de ServiceNow y la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**. 

    ![Aprovisionamiento](./media/active-directory-saas-servicenow-provisioning-tutorial/provisioning.png)

5. En la sección Credenciales de administración, realice los pasos siguientes:
   
    a. En el cuadro de texto **Nombre de la instancia de ServiceNow** , escriba el nombre de la instancia de ServiceNow.

    b. En el cuadro de texto del **nombre de usuario de administrador de ServiceNow**, escriba el nombre de usuario de un administrador.

    c. En el cuadro de texto de **contraseña de administrador de ServiceNow**, escriba la contraseña del administrador.

6. En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación ServiceNow. Si se produce un error de conexión, asegúrese de que su cuenta de ServiceNow tiene permisos de administrador de equipo y vuelva a intentar el paso de **"Credenciales de administración"**.

7. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla.

8. Haga clic en **Guardar**.

9. En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to** (Sincronizar usuarios de Azure Active Directory con ServiceNow).

10. En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizan entre Azure AD y ServiceNow. Los atributos seleccionados como propiedades **Matching** se usan para buscar coincidencias con las cuentas de usuario de ServiceNow con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

11. Para habilitar el aprovisionamiento del servicio de aprovisionamiento de Azure AD para ServiceNow, cambie el **estado de aprovisionamiento** a **Activado** en la sección Configuración.

12. Haga clic en **Guardar**.

Esta acción inicia la sincronización inicial de todos los usuarios y grupos asignados a ServiceNow en la sección Usuarios y grupos. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y seguir los vínculos a los registros de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de ServiceNow.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Configuración del inicio de sesión único](active-directory-saas-servicenow-tutorial.md)


