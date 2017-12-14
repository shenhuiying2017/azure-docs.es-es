---
title: "Tutorial: Integración de Azure Active Directory con NetSuite | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Netsuite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 20a670a3843586ab57ce776f400bd382947d7861
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Tutorial: Configuración de Netsuite para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es explicar los pasos que hay que realizar en Netsuite y Azure AD para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD en Netsuite.

## <a name="prerequisites"></a>Requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure Active Directory.
*   Una suscripción habilitada para el inicio de sesión único en Netsuite.
*   Una cuenta de usuario de Netsuite con permisos de administrador de equipo.

## <a name="assigning-users-to-netsuite"></a>Asignación de usuarios a Netsuite

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación en Azure AD.

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceso a la aplicación Netsuite. Una vez decidido, puede asignar estos usuarios a la aplicación Netsuite siguiendo estas instrucciones:

[Asignar un usuario o grupo a una aplicación empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite"></a>Sugerencias importantes para asignar usuarios a Netsuite

*   Se recomienda asignar un único usuario de Azure AD a Netsuite para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar a un usuario a Netsuite, debe seleccionar un rol de usuario válido. El rol "Acceso predeterminado" no funciona para realizar el aprovisionamiento.

## <a name="enable-user-provisioning"></a>Habilitación del aprovisionamiento de usuarios

Esta sección le guía en la conexión de Azure AD a la API de aprovisionamiento de cuentas de usuario de Netsuite, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas de Netsuite en función de la asignación de grupos y usuarios Azure AD.

> [!TIP] 
> También puede habilitar el inicio de sesión único basado en SAML para Netsuite siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="to-configure-user-account-provisioning"></a>Para configurar el aprovisionamiento automático de cuentas de usuario:

El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Netsuite.

1. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Si ya ha configurado Netsuite para el inicio de sesión único, busque la instancia de Netsuite mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **Netsuite** en la galería de aplicaciones. Seleccione Netsuite en los resultados de búsqueda y agréguelo a la lista de aplicaciones.

3. Seleccione la instancia de Netsuite y luego, la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**. 

    ![Aprovisionamiento](./media/active-directory-saas-netsuite-provisioning-tutorial/provisioning.png)

5. En la sección **Credenciales de administrador**, proporcione los siguientes valores de configuración:
   
    a. En el cuadro de texto **Nombre de usuario administrador**, escriba un nombre de cuenta de Netsuite que tenga asignado el perfil **Administrador del sistema** en Netsuite.com.
   
    b. En el cuadro de texto **Contraseña de administrador**, escriba la contraseña de esta cuenta.
      
6. En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación Netsuite.

7. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla.

8. Haga clic en **Guardar**.

9. En la sección Asignaciones, seleccione **Sincronizar usuarios de Azure Active Directory con Netsuite.**

10. En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizan entre Azure AD y Netsuite. Tenga en cuenta que los atributos seleccionados como propiedades de **Coincidencia** se usarán para buscar coincidencias con las cuentas de usuario de Netsuite con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

11. Para habilitar el servicio de aprovisionamiento de Azure AD para Netsuite, cambie el **Estado de aprovisionamiento** a **Activado** en la sección Configuración

12. Haga clic en **Guardar**.

Esta acción inicia la sincronización inicial de todos los usuarios y grupos asignados a Netsuite en la sección Usuarios y grupos. Tenga en cuenta que la sincronización inicial tardará más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 20 minutos si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los informes de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de Netsuite.

Ahora puede crear una cuenta de prueba. Espere 20 minutos para comprobar que la cuenta se ha sincronizado con Netsuite.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del inicio de sesión único](active-directory-saas-netsuite-tutorial.md)