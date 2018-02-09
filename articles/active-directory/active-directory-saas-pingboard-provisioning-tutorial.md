---
title: "Tutorial: Configuración de Pingboard para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs"
description: "Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente las cuentas de usuario para Pingboard."
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
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: f74e890cf716cfd4bc7b41fcc4aa244969cafde5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Tutorial: Configuración de Pingboard para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es explicar los pasos que hay que realizar para aprovisionar y desaprovisionar automáticamente las cuentas de usuario de Azure Active Directory para Pingboard.

## <a name="prerequisites"></a>requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure Active Directory
*   Una [cuenta Pro](https://pingboard.com/pricing) para un inquilino de Pingboard 
*   Una cuenta de usuario de Pingboard con permisos de administrador 

> [!NOTE] 
> La integración de aprovisionamiento de Azure Active Directory se basa en la API de Pingboard (`https://your_domain.pingboard.com/scim/v2`) que está disponible para su cuenta.

## <a name="assigning-users-to-pingboard"></a>Asignación de usuarios a Pingboard

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios que se han "asignado" a una aplicación en Azure Active Directory. 

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios de Azure Active Directory representan a los usuarios que necesitan acceso a la aplicación Pingboard. Una vez decidido, puede asignar estos usuarios a la aplicación de Pingboard siguiendo estas instrucciones:

[Asignación de un usuario a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Sugerencias importantes para asignar usuarios a Pingboard

*   Se recomienda que asigne un único usuario de Azure Active Directory a Pingboard para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios.

## <a name="configuring-user-provisioning-to-pingboard"></a>Configuración del aprovisionamiento de usuarios para Pingboard 

Esta sección lo guía a través de los pasos necesarios para conectar Azure Active Directory con la API de aprovisionamiento de cuentas de usuario de Pingboard, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas en Pingboard en función de la asignación de usuarios de Azure Active Directory.

> [!TIP]
> También puede habilitar el inicio de sesión único basado en SAML para Pingboard siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-active-directory"></a>Para configurar el aprovisionamiento de cuentas de usuario automático para Pingboard en Azure Active Directory:

1)  En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2) Si ya ha configurado Pingboard para el inicio de sesión único, busque la instancia de Pingboard mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **Pingboard** en la galería de aplicaciones. Seleccione **Pingboard** en los resultados de búsqueda y agrégalo a la lista de aplicaciones.

3)  Seleccione la instancia de Pingboard y, después, la pestaña **Aprovisionamiento**.

4)  Establezca el **modo de aprovisionamiento** en **Automático**.

![Aprovisionamiento de Pingboard](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5) En la sección Credenciales de administración, realice los pasos siguientes:

* En el cuadro de texto **URL de inquilino**, escriba `https://your_domain.pingboard.com/scim/v2` y reemplace your_domain (su_dominio) por su dominio real.
* Inicie sesión en [Pingboard](https://pingboard.com/) con la cuenta de administrador.
* Haga clic en Complementos > Integraciones > Azure Active Directory.
* Haga clic en la pestaña Configurar y seleccione **Enable user provisioning from Azure** (Habilitar el aprovisionamiento de usuarios de Azure).
* Copie el campo **OAuth Bearer Token** (Token de portador de OAuth) e insértelo en el cuadro de texto **Token secreto**.

6) En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure Active Directory puede conectarse a la aplicación de Pingboard. Si se produce un error de conexión, asegúrese de que su cuenta de Pingboard tiene permisos de administrador y vuelva a intentar el paso de **"Probar conexión"**.

7) Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla que aparece a continuación.

8) Haga clic en **Save**(Guardar). 

9) En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to Pingboard** (Sincronizar usuarios de Azure Active Directory con Pingboard).

10) En la sección **Asignaciones de atributos**, revise los atributos de usuario que se van a sincronizar desde Azure Active Directory con Pingboard. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Pingboard con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios. Para más información, consulte [Personalización de asignaciones de atributos de aprovisionamiento de usuarios](active-directory-saas-customizing-attribute-mappings.md).

11) Para habilitar el servicio de aprovisionamiento de Azure Active Directory para Pingboard, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

12) Haga clic en **Guardar** para iniciar la sincronización inicial de los usuarios asignados a Pingboard.

La sincronización inicial tardará más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 20 minutos, si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los informes de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de Pingboard.

Para más información acerca de cómo leer los registros de aprovisionamiento de Azure Active Directory, consulte [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del inicio de sesión único](active-directory-saas-pingboard-tutorial.md)
