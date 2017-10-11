---
title: "Tutorial: Integración de Azure Active Directory con Workplace by Facebook | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Workplace by Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 9b22679c304248ed7ba7a6bd9eaf82b64f7143cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-configuring-workplace-by-facebook-for-user-provisioning"></a>Tutorial: Configuración de Workplace by Facebook para el aprovisionamiento de usuarios

El objetivo de este tutorial es explicar los pasos que debe realizar en Workplace by Facebook y Azure AD para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD para Workplace by Facebook.

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Workplace by Facebook, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único de Workplace by Facebook

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Asignación de usuarios a Workplace by Facebook

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación de Azure AD.

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceso a la aplicación Workplace by Facebook. Una vez decidido, puede asignar estos usuarios a la aplicación Workplace by Facebook siguiendo estas instrucciones:

[Asignar un usuario o grupo a una aplicación empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Sugerencias importantes para la asignación de usuarios a Workplace by Facebook

*   Se recomienda asignar un único usuario de Azure AD a Workplace by Facebook para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar a un usuario a Workplace by Facebook, debe seleccionar un rol de usuario válido. El rol "Acceso predeterminado" no funciona para realizar el aprovisionamiento.

## <a name="enable-user-provisioning"></a>Habilitación del aprovisionamiento de usuarios

Esta sección lo guía a través de los pasos necesarios para conectar la API de aprovisionamiento de su cuenta de usuario de Workplace by Facebook, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas de Workplace by Facebook en función de la asignación de grupos y usuarios Azure AD.

>[!Tip]
>También puede habilitar el inicio de sesión único basado en SAML para Workplace by Facebook siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Para configurar el aprovisionamiento de cuentas de usuario para Workplace by Facebook en Azure AD, siga estos pasos:

El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de las cuentas de usuario de Active Directory en Workplace by Facebook.

Azure AD admite la capacidad de sincronizar automáticamente los detalles de la cuenta de usuarios asignados a Workplace by Facebook. Esta sincronización automática permite a Workplace by Facebook obtener los datos que necesita para autorizar a los usuarios a acceder, antes de que intenten iniciar sesión por primera vez. También desaprovisiona usuarios de Workplace by Facebook cuando se revoque el acceso en Azure AD.

1. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory**  > **Aplicaciones empresariales** > **Todas las aplicaciones**.

2. Si ya ha configurado Workplace by Facebook para el inicio de sesión único, busque la instancia de Workplace by Facebook mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **Workplace by Facebook**  en la galería de aplicaciones. Seleccione Workplace by Facebook en los resultados de búsqueda y agrégalo a la lista de aplicaciones.

3. Seleccione la instancia de Workplace by Facebook y, después, seleccione la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**. 

    ![Aprovisionamiento](./media/active-directory-saas-workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. En la sección **Credenciales de administrador**, escriba el token del secreto y la dirección URL del inquilino de su administrador de Workplace by Facebook.

6. En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación Workplace by Facebook. Si la conexión no se establece, asegúrese de que la cuenta de Workplace by Facebook tiene permisos de administrador de equipo.

7. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla.

8. Haga clic en **Guardar**.

9. En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to Workplace by Facebook** (Sincronizar usuarios de Azure Active Directory con Workplace by Facebook).

10. En la sección **Attribute Mappings** (Asignaciones de atributos), revise los atributos de usuario que se sincronizan entre Azure AD y Workplace by Facebook. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Workplace by Facebook con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

11. Para habilitar el servicio de aprovisionamiento de Azure AD para Workplace by Facebook, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

12. Haga clic en **Guardar**.

Para más información sobre cómo configurar el aprovisionamiento automático, consulte [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Ahora puede crear una cuenta de prueba. Espere 20 minutos para comprobar que la cuenta se ha sincronizado con Workplace by Facebook.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del inicio de sesión único](active-directory-saas-workplacebyfacebook-tutorial.md)

