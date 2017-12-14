---
title: "Tutorial: Configuración de GitHub para aprovisionar automáticamente usuarios con Azure Active Directory | Microsoft Docs"
description: "Obtenga información sobre cómo configurar Azure Active Directory para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de GitHub."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.openlocfilehash: 7e202899b697872227da4d7bea54406f99089000
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configuring-github-for-automatic-user-provisioning"></a>Tutorial: Configuración de GitHub para aprovisionar automáticamente usuarios


El objetivo de este tutorial es explicar los pasos que hay que realizar en GitHub y Azure AD para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD en GitHub. 

## <a name="prerequisites"></a>Requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure Active Directory
*   Un inquilino de GitHub con el [plan Empresa](https://help.github.com/articles/organization-billing-plans/#business-plan) o uno superior habilitado 
*   Una cuenta de usuario de GitHub con permisos de administrador 

> [!NOTE]
> La integración del aprovisionamiento de Azure AD se basa en la [API de GitHub SCIM](https://developer.github.com/v3/scim/), que está disponible para los equipos de GitHub con el plan Empresa o uno superior.

## <a name="assigning-users-to-github"></a>Asignación de usuarios a GitHub

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación de Azure AD. 

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceso a la aplicación GitHub. Una vez decidido, puede asignar estos usuarios a la aplicación de GitHub siguiendo estas instrucciones:

[Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Sugerencias importantes para asignar usuarios a GitHub

*   Se recomienda asignar un único usuario de Azure AD a GitHub para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar un usuario a GitHub, debe seleccionar el rol **Usuario** u otro válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. El rol **Acceso predeterminado** no funciona para realizar el aprovisionamiento y estos usuarios se omiten.


## <a name="configuring-user-provisioning-to-github"></a>Configuración del aprovisionamiento de usuarios en GitHub 

Esta sección lo guía a través de los pasos necesarios para conectar la API de aprovisionamiento de cuentas de usuario de GitHub, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas en GitHub en función de la asignación de grupos y usuarios de Azure AD.

> [!TIP]
> También puede habilitar el inicio de sesión único basado en SAML para GitHub siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Configuración del aprovisionamiento de cuentas de usuario automático para GitHub en Azure AD


1. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Si ya ha configurado GitHub para el inicio de sesión único, busque la instancia de GitHub mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **GitHub** en la galería de aplicaciones. Seleccione GitHub en los resultados de búsqueda y agréguelo a la lista de aplicaciones.

3. Seleccione la instancia de GitHub y, luego, la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Aprovisionamiento de GitHub](./media/active-directory-saas-github-provisioning-tutorial/GitHub1.png)

5. En **Credenciales de administrador**, haga clic en **Autorizar**. Con esta operación se abre un cuadro de diálogo de autorización de GitHub en una nueva ventana del explorador. 

6. En esa nueva ventana, inicie sesión en GitHub con su cuenta de administrador. En el cuadro de diálogo de autorización que aparece, seleccione el equipo de GitHub para el que desea habilitar el aprovisionamiento y, luego, seleccione **Autorizar**. Cuando termina, vuelva a Azure Portal para completar la configuración de aprovisionamiento.

    ![Cuadro de diálogo de autorización](./media/active-directory-saas-github-provisioning-tutorial/GitHub2.png)

7. En Azure Portal, escriba la **dirección URL del inquilino** y haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación de GitHub. Si la conexión no se establece, asegúrese de que la cuenta de GitHub tiene permisos de administrador y de que la **dirección URL del inquilino** se escribió correctamente; luego repita el paso "Autorizar" (puede constituir la **dirección URL de inquilino** con la regla: "https://api.github.com/scim/v2/organizations/ + <Nombre_de_organizaciones>", puede encontrar las organizaciones en la cuenta de GitHub: **Configuración** > **Organizaciones**).

    ![Cuadro de diálogo de autorización](./media/active-directory-saas-github-provisioning-tutorial/GitHub3.png)

8. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla "Enviar una notificación por correo electrónico cuando se produzca un error".

9. Haga clic en **Guardar**. 

10. En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to GitHub** (Sincronizar usuarios de Azure Active Directory con GitHub).

11. En la sección **Attribute Mappings** (Asignaciones de atributos), revise los atributos de usuario que se sincronizan entre Azure AD y GitHub. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de GitHub con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

12. Para habilitar el servicio de aprovisionamiento de Azure AD para GitHub, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

13. Haga clic en **Guardar**. 

Esta operación inicia la sincronización inicial de todos los usuarios y grupos asignados a GitHub en la sección Usuarios y grupos. La sincronización inicial tardará más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 20 minutos si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los informes de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](active-directory-saas-provisioning-reporting.md)
