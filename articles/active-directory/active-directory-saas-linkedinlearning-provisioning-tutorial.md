---
title: "Tutorial: Configuración de LinkedIn Learning para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs"
description: "Aprenda a configurar Azure Active Directory para aprovisionar y cancelar el aprovisionamiento de cuentas de usuario de LinkedIn Learning automáticamente."
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
ms.date: 04/15/2017
ms.author: asmalser-msft
ms.openlocfilehash: 5eb2b1594eedb2a135d7b8cd501a33d8264e136b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-configuring-linkedin-learning-for-automatic-user-provisioning"></a>Tutorial: Configuración de LinkedIn Learning para el aprovisionamiento automático de usuarios


El objetivo de este tutorial es explicar los pasos que debe realizar en LinkedIn Learning y Azure AD para aprovisionar y cancelar el aprovisionamiento de cuentas de usuario de Azure AD para LinkedIn Learning automáticamente. 

## <a name="prerequisites"></a>Requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure Active Directory
*   Un inquilino de LinkedIn Learning 
*   Una cuenta de administrador en LinkedIn Learning con acceso al Centro de cuentas de LinkedIn

> [!NOTE]
> Azure Active Directory se integra con LinkedIn Learning mediante el protocolo [SCIM](http://www.simplecloud.info/).

## <a name="assigning-users-to-linkedin-learning"></a>Asignación de usuarios a LinkedIn Learning

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento de cuentas de usuario automático, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación de Azure AD. 

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceder a LinkedIn Learning. Una vez decidido, puede asignar estos usuarios a LinkedIn Learning siguiendo estas instrucciones:

[Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-learning"></a>Sugerencias importantes para asignar usuarios a LinkedIn Learning

*   Se recomienda asignar un solo usuario de Azure AD a LinkedIn Learning para probar la configuración del aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar un usuario a LinkedIn Learning, debe seleccionar el rol **Usuario** en el cuadro de diálogo de asignación. El rol "Acceso predeterminado" no funciona para realizar el aprovisionamiento.


## <a name="configuring-user-provisioning-to-linkedin-learning"></a>Configuración del aprovisionamiento de usuarios en LinkedIn Learning

Esta sección le guía a través de los pasos necesarios para conectar la API de aprovisionamiento de su cuenta de usuario de SCIM de LinkedIn Learning, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas de LinkedIn Learning en función de la asignación de grupos y usuarios en Azure AD.

> [!TIP]
> También puede decidir habilitar el inicio de sesión único basado en SAML para LinkedIn Learning siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-learning-in-azure-ad"></a>Para configurar el aprovisionamiento de cuentas de usuario automático para LinkedIn Learning en Azure AD, siga estos pasos:


El primer paso consiste en recuperar el token de acceso de LinkedIn. Si es administrador de organización, puede aprovisionar automáticamente un token de acceso. En el centro de cuentas, vaya a **Configuración &gt; Configuración global** y abra el panel **SCIM Setup** (Configuración de SCIM).

> [!NOTE]
> Si obtiene acceso al centro de cuentas directamente en lugar de a través de un vínculo, puede llegar a este panel mediante los pasos siguientes.

1)  Inicie sesión en el centro de cuentas.

2)  Seleccione **Administración &gt; Configuración de administración**.

3)  Haga clic en **Advanced Integrations** (Integraciones avanzadas) en la barra lateral izquierda. Se le dirigirá al centro de cuentas.

4)  Haga clic en **+ Add new SCIM configuration** (+ Agregar nueva configuración de SCIM) y rellene cada campo del procedimiento.

> Si no está habilitada la asignación de licencias automática, solo se sincronizan los datos de usuario.

![Aprovisionamiento de LinkedIn Learning](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_1.PNG)

> Si está habilitada la asignación de licencias automática, debe tomar nota de la instancia de la aplicación y del tipo de licencia. Las licencias se asignan por orden de llegada hasta que se obtienen todas las licencias.

![Aprovisionamiento de LinkedIn Learning](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_2.PNG)

5)  Haga clic en **Generar token**. El token de acceso debería aparecer en el campo **Token de acceso**.

6)  Guarde el token de acceso en el Portapapeles o en el equipo antes de abandonar la página.

7) Después, inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

8) Si ya ha configurado LinkedIn Learning para el inicio de sesión único, busque la instancia de LinkedIn Learning con el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **LinkedIn Learning** en la galería de aplicaciones. Seleccione LinkedIn Learning en los resultados de búsqueda y agréguelo a la lista de aplicaciones.

9)  Seleccione la instancia de LinkedIn Learning y seleccione la pestaña **Aprovisionamiento**.

10) Establezca el **modo de aprovisionamiento** en **Automático**.

![Aprovisionamiento de LinkedIn Learning](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_3.PNG)

11)  Rellene los campos siguientes en **Credenciales de administrador**:

* En el campo **URL de inquilino**, escriba https://api.linkedin.com.

* En el campo **Token secreto**, escriba el token de acceso que ha generado en el paso 1 y haga clic en **Probar conexión**.

* Debería ver una notificación que le indica que el proceso se ha realizado correctamente en el lado superior derecho del portal.

12) Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla que aparece a continuación.

13) Haga clic en **Guardar**. 

14) En la sección **Asignaciones de atributos**, revise los atributos de usuario y de grupo de Azure AD que se sincronizarán con LinkedIn Learning. Tenga en cuenta que los atributos seleccionados como propiedades **Matching** se usarán para establecer coincidencias con las cuentas de usuario y los grupos de LinkedIn Learning para las operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

![Aprovisionamiento de LinkedIn Learning](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_4.PNG)

15) Para habilitar el servicio de aprovisionamiento de Azure AD para LinkedIn Learning, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

16) Haga clic en **Guardar**. 

Esta acción iniciará la sincronización inicial de todos los usuarios y grupos asignados a LinkedIn Learning en la sección Usuarios y grupos. Tenga en cuenta que la sincronización inicial tardará más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 20 minutos si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los informes de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de LinkedIn Learning.


## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
