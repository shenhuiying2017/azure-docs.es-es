---
title: "Tutorial: Configuración de LucidChart para aprovisionar automáticamente usuarios con Azure Active Directory | Microsoft Docs"
description: "Obtenga información sobre cómo configurar Azure Active Directory para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de LucidChart."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.openlocfilehash: 1f9344a5e750360e21ed7dc8e3ed013c2c2e1a45
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2017
---
# <a name="tutorial-configuring-lucidchart-for-automatic-user-provisioning"></a>Tutorial: Configuración de LucidChart para aprovisionar a los usuarios automáticamente


El objetivo de este tutorial es explicar los pasos que hay que realizar en LucidChart y Azure AD para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD en LucidChart. 

## <a name="prerequisites"></a>Requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure Active Directory
*   Un inquilino de LucidChart con el [plan Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) o uno superior habilitado 
*   Una cuenta de usuario de LucidChart con permisos de administrador 

## <a name="assigning-users-to-lucidchart"></a>Asignación de usuarios a LucidChart

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación de Azure AD. 

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceso a la aplicación LucidChart. Una vez decidido, puede asignar estos usuarios a la aplicación LucidChart siguiendo estas instrucciones:

[Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Sugerencias importantes para asignar usuarios a LucidChart

*   Se recomienda asignar un único usuario de Azure AD a LucidChart para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar un usuario a LucidChart, debe seleccionar el rol **Usuario** u otro válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. El rol **Acceso predeterminado** no funciona para realizar el aprovisionamiento y estos usuarios se omiten.


## <a name="configuring-user-provisioning-to-lucidchart"></a>Configuración del aprovisionamiento de usuarios en LucidChart 

Esta sección le guía en la conexión de Azure AD a la API de aprovisionamiento de cuentas de usuario de LucidChart, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas de LucidChart en función de la asignación de grupos y usuarios Azure AD.

> [!TIP]
> También puede habilitar el inicio de sesión único basado en SAML para LucidChart siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.


### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Configuración del aprovisionamiento de cuentas de usuario automático para LucidChart en Azure AD


1. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Si ya ha configurado LucidChart para el inicio de sesión único, busque la instancia de LucidChart mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **LucidChart** en la galería de aplicaciones. Seleccione LucidChart en los resultados de búsqueda y agréguelo a la lista de aplicaciones.

3. Seleccione la instancia de LucidChart y luego, la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Aprovisionamiento de LucidChart](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart1.png)

5. En la sección **Credenciales de administrador**, escriba el **Token secreto** generado por la cuenta de LucidChart (puede encontrar el token en su cuenta: **Equipo** > **Integración de la aplicación** > **SCIM**). 

    ![Aprovisionamiento de LucidChart](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart2.png)

6. En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación de LucidChart. Si la conexión no se establece, asegúrese de que la cuenta de LucidChart tiene permisos de administrador e intente el paso 5 de nuevo.

7. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla "Enviar una notificación por correo electrónico cuando se produzca un error".

8. Haga clic en **Guardar**. 

9. En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to LucidChart** (Sincronizar usuarios de Azure Active Directory con LucidChart).

10. En la sección **Attribute Mappings** (Asignaciones de atributos), revise los atributos de usuario que se sincronizan entre Azure AD y LucidChart. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de LucidChart con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

11. Para habilitar el servicio de aprovisionamiento de Azure AD para LucidChart, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**

12. Haga clic en **Guardar**. 

Esta operación inicia la sincronización inicial de todos los usuarios y grupos asignados a LucidChart en la sección Usuarios y grupos. La sincronización inicial tardará más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 20 minutos, si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los informes de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](active-directory-saas-provisioning-reporting.md)
