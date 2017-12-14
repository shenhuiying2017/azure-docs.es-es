---
title: "Tutorial: Integración de Azure Active Directory con Salesforce Sandbox | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: d0efcae50b18dc2626af5510bd47ff36a27ba718
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configuring-salesforce-sandbox-for-automatic-user-provisioning"></a>Tutorial: Configuración de Salesforce Sandbox para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que debe realizar en Salesforce Sandbox y Azure AD para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD para Salesforce Sandbox.

## <a name="prerequisites"></a>Requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure Active Directory.
*   Un inquilino válido para Salesforce Sandbox for Work o Salesforce Sandbox for Education. Puede usar una cuenta de prueba gratuita de cualquiera de los servicios.
*   Una cuenta de usuario de Salesforce Sandbox con permisos de administrador de equipos.

## <a name="assigning-users-to-salesforce-sandbox"></a>Asignación de usuarios a Salesforce Sandbox

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación en Azure AD.

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a la aplicación de Salesforce Sandbox. Una vez decidido, puede asignar estos usuarios a la aplicación Salesforce Sandbox siguiendo las instrucciones de: [Asignación de un usuario o un grupo a una aplicación empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Sugerencias importantes para asignar usuarios a Salesforce Sandbox

* Se recomienda asignar un único usuario de Azure AD a Salesforce Sandbox para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a Salesforce Sandbox, debe seleccionar un rol de usuario válido. El rol "Acceso predeterminado" no funciona para realizar el aprovisionamiento.

> [!NOTE]
> Esta aplicación importa roles personalizados de Salesforce Sandbox como parte del proceso de aprovisionamiento, que podría querer seleccionar el cliente al asignar usuarios.

## <a name="enable-automated-user-provisioning"></a>Habilitación del aprovisionamiento automático de usuarios

Esta sección le guía por los pasos necesarios para conectar la API de aprovisionamiento de cuentas de usuario de Salesforce Sandbox, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas de Salesforce Sandbox en función de la asignación de grupos y usuarios en Azure AD.

>[!Tip]
>Puede elegir también habilitar el inicio de sesión único basado en SAML para Salesforce Sandbox, mediante las instrucciones que se proporcionan en [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar el aprovisionamiento automático de cuentas de usuario

El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Salesforce Sandbox.

1. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Si ya ha configurado Salesforce Sandbox para el inicio de sesión único, busque su instancia de Salesforce Sandbox mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **Salesforce Sandbox** en la galería de aplicaciones. Seleccione Salesforce Sandbox en los resultados de búsqueda y agréguela a la lista de aplicaciones.

3. Seleccione la instancia de Salesforce Sandbox y, después, seleccione la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Aprovisionamiento](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/provisioning.png)

5. En la sección **Credenciales de administrador**, proporcione los siguientes valores de configuración:
   
    a. En el cuadro de texto **Nombre de usuario del administrador**, escriba un nombre de cuenta de Salesforce Sandbox que tenga asignado el perfil **Administrador del sistema** en Salesforce.com.
   
    b. En el cuadro de texto **Contraseña de administrador**, escriba la contraseña de esta cuenta.

6. Para obtener el token de seguridad de Salesforce Sandbox, abra una nueva pestaña e inicie sesión en la misma cuenta de administrador de Salesforce Sandbox. En la esquina superior derecha de la página, haga clic en su nombre y, a continuación, haga clic en **Configuración**.

     ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Habilitar aprovisionamiento automático de usuarios")

7. En el panel de navegación izquierdo, haga clic en **Mi información personal** para expandir la sección relacionada y haga clic en **Reset My Security Token** (Restablecer mi token de seguridad).
  
    ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Habilitar aprovisionamiento automático de usuarios")

8. En la página **Reset Security Token** (Restablecer token de seguridad), haga clic en el botón **Reset Security Token** (Restablecer token de seguridad).

    ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Habilitar aprovisionamiento automático de usuarios")

9. Compruebe la bandeja de entrada de correo electrónico asociada a esta cuenta de administrador. Busque un correo electrónico de Salesforce Sandbox.com que contenga el nuevo token de seguridad.

10. Copie el token, vaya a la ventana de Azure AD y péguelo en el campo **Token secreto**.

11. En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación Salesforce Sandbox.

12. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla.

13. Haga clic en **Guardar**.  
    
14.  En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to** (Sincronizar usuarios de Azure Active Directory con Salesforce Sandbox).

15. En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizarán desde Azure AD con Salesforce Sandbox. Tenga en cuenta que los atributos seleccionados como propiedades de **Coincidencia** se usarán para buscar coincidencias con las cuentas de usuario de Salesforce Sandbox con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

16. Para habilitar el servicio de aprovisionamiento de Azure AD para Salesforce Sandbox, cambie el **estado de aprovisionamiento** a **Activado** en la sección Configuración.

17. Haga clic en **Guardar**.

Comienza la sincronización inicial de todos los usuarios y grupos asignados a Salesforce Sandbox en la sección Usuarios y grupos. La sincronización inicial tardará más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 20 minutos si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y seguir los vínculos a los informes de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de Salesforce Sandbox.

Ahora puede crear una cuenta de prueba. Espere 20 minutos para comprobar que la cuenta se ha sincronizado con Salesforce.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del inicio de sesión único](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
