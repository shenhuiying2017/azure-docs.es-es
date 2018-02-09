---
title: "Tutorial: Configuración de Asana para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs"
description: "Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente las cuentas de usuario para Asana."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 4f3bd11a99f43d6405ea285a7a283179d561f92a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Tutorial: Configuración de Asana para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es explicar los pasos que debe realizar en Asana y Azure AD para aprovisionar y desaprovisionar automáticamente las cuentas de usuario de Azure AD para Asana.

## <a name="prerequisites"></a>requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de directorio de Azure Active Directory
*   Un inquilino de Asana con el plan [Enterprise](https://www.asana.com/pricing) o uno superior habilitado 
*   Una cuenta de usuario de Asana con permisos de administrador 

> [!NOTE] 
> El aprovisionamiento de integración de Azure AD se basa en la [API de Asana ](https://app.asana.com/api/1.0/scim/Users) que está disponible para Asana.

## <a name="assigning-users-to-asana"></a>Asignación de usuarios a Asana

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios que se hayan "asignado" a una aplicación de Azure AD. 

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios de Azure AD representan a los usuarios que necesitan acceder a la aplicación de Asana. Una vez decidido, puede asignar estos usuarios a la aplicación de Asana siguiendo estas instrucciones:

[Asignación de un usuario a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Sugerencias importantes para asignar usuarios a Asana

*   Se recomienda asignar un único usuario de Azure AD a Asana para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios.

## <a name="configuring-user-provisioning-to-asana"></a>Configuración del aprovisionamiento de usuarios para Asana 

Esta sección le guía en la conexión de Azure AD a la API de aprovisionamiento de cuentas de usuario de Asana, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas de Asana en función de la asignación de usuarios en Azure AD.

> [!TIP]
> También puede habilitar el inicio de sesión único basado en SAML para Asana siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Para configurar el aprovisionamiento automático de cuentas de usuario para Asana en Azure AD:

1)  En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2) Si ya ha configurado Asana para el inicio de sesión único, busque la instancia de Asana mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **Asana** en la galería de aplicaciones. Seleccione **Asana** en los resultados de búsqueda y agrégalo a la lista de aplicaciones.

3)  Seleccione la instancia de Asana y, después, la pestaña **Aprovisionamiento**.

4)  Establezca el **modo de aprovisionamiento** en **Automático**.

![Aprovisionamiento de Asana](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5) En la sección Credenciales de administrador, siga las instrucciones indicadas para generar el token y escríbalo en el cuadro de texto **Token secreto**.

* Inicie sesión en [Asana](https://app.asana.com) mediante la cuenta de administrador.
* Haga clic en la foto del perfil de la barra superior y seleccione la configuración del nombre de organización actual.
* Vaya a la pestaña Service Accounts (Cuentas de servicio).
* Haga clic en Add Service Account (Agregar una cuenta de servicio).
* Actualice Name (Nombre), About (Acerca de) y la foto del perfil según sea necesario, copie **Token** y haga clic en Save Changes (Guardar cambios).

6) En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación Asana. Si se produce un error de conexión, asegúrese de que su cuenta de Asana tiene permisos de administrador y vuelva a intentar el paso de **"Probar conexión"**.

7) Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla que aparece a continuación.

8) Haga clic en **Save**(Guardar). 

9) En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to Box** (Sincronizar usuarios de Azure Active Directory con Asana).

10) En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizarán de Azure AD con Asana. Tenga en cuenta que los atributos seleccionados como propiedades de **Coincidencia** se usarán para establecer coincidencias con las cuentas de usuario de Asana con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios. Para información más detallada, consulte [Personalización de asignaciones de atributos de aprovisionamiento de usuarios](active-directory-saas-customizing-attribute-mappings.md).

11) Para habilitar el aprovisionamiento del servicio de aprovisionamiento de Azure AD para Asana, cambie **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

12) Haga clic en **Save**(Guardar). 

Esto inicia la sincronización inicial de todos los usuarios asignados a Asana en la sección Usuarios. La sincronización inicial tardará más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 20 minutos, si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los informes de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de Asana.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del inicio de sesión único](active-directory-saas-asana-tutorial.md)
