---
title: 'Tutorial: Configuración de Replicon para aprovisionar usuarios automáticamente con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente las cuentas de usuario para Replicon.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: b6f536ba1711076eab0eb9dcbea7d357e9cebf3d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345602"
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>Tutorial: Configuración de Replicon para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se dan en Replicon y Azure Active Directory (Azure AD) para configurar Azure AD de modo que aprovisione y desaprovisione automáticamente los usuarios o grupos en Replicon.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure AD
*   Un inquilino de Replicon con el plan [Plus](https://www.replicon.com/time-bill-pricing/) o uno superior habilitado
*   Una cuenta de usuario de Replicon con permisos de administrador

> [!NOTE]
> La integración del aprovisionamiento de Azure AD se basa en la [API de Replicon](https://www.replicon.com/help/developers), que está disponible para los equipos de Replicon con el plan Plus o uno superior.

## <a name="adding-replicon-from-the-gallery"></a>Agregar Replicon desde la galería
Antes de configurar Replicon para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar Replicon desde la galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas.

**Para agregar Replicon desde la galería de aplicaciones de Azure AD, siga estos pasos:**

1. En **[Azure Portal](https://portal.azure.com)**, en el panel de navegación izquierdo, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Sección Aplicaciones empresariales][2]
    
3. Para agregar Replicon, haga clic en el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Replicon**.

    ![Búsqueda de la aplicación Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearch.png)

5. En el panel de resultados, seleccione **Replicon** y, luego, haga clic en el botón **Agregar** para agregar Replicon la lista de aplicaciones SaaS.

    ![Resultados de la búsqueda de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Aplicación de creación de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Asignación de usuarios a Replicon

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han "asignado" a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a Replicon. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a Replicon:

*   [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>Sugerencias importantes para asignar usuarios a Replicon

*   Se recomienda asignar un único usuario de Azure AD a Replicon para probar la configuración del aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar un usuario a Replicon, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Configuración del aprovisionamiento automático de usuarios en Replicon 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de AD Azure para crear, actualizar y deshabilitar usuarios o grupos en Replicon en función de las asignaciones de grupo y usuarios de Azure AD.

> [!TIP]
> También puede habilitar el inicio de sesión único basado en SAML para Replicon siguiendo las instrucciones del [tutorial de inicio de sesión único de Replicon](active-directory-saas-replicon-tutorial.md). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Replicon en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Seleccione Replicon en la lista de aplicaciones SaaS.

    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon2.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon1.png)

5. En la sección **Credenciales de administrador**, escriba el **nombre de usuario de administrador**, la **contraseña de administrador**, el **identificador de la compañía** y el **dominio** de la cuenta de Replicon. Algunos ejemplos de estos valores son:

    *   En el campo **Nombre de usuario administrador**, rellene el nombre de usuario de la cuenta de administrador del inquilino de Replicon. Ejemplo: contosoadmin.

    *   En el campo **Contraseña de administrador**, rellene la contraseña correspondiente al nombre de usuario de administrador.

    *   En el campo **CompanyId** (Identificador de la compañía), rellene el identificador de compañía de su inquilino de Replicon. Ejemplo: el valor de CompanyID basado en el inicio de sesión siguiente es Contoso.

    ![Inicio de sesión de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconLogin.png)

    *   En el campo **Dominio**, rellene el dominio tal y como se describe en el paso 6.
    
6. Obtenga el valor de **serviceEndpointRootURL** (dirección URL raíz del punto de conexión del servicio) de la cuenta del inquilino de Replicon según los pasos mencionados en la [ayuda del servicio de Replicon](https://www.replicon.com/help/determining-the-url-for-your-service-calls). Al obtener la dirección URL, el **dominio** sería el subdominio de **serviceEndpointRootURL** resaltado. 

    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconEndpoint.png)

7. Tras rellenar los campos que se muestran en el paso 5, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Replicon. Si la conexión no se establece, asegúrese de que la cuenta de Replicon tiene permisos de administrador de Replicon y pruebe otra vez.

    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconTestConnection.png)

8. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. Haga clic en **Save**(Guardar).

10. En la sección **Mappings** (Asignaciones), seleccione **Synchronize Azure Active Directory Users to Replicon** (Sincronizar usuarios de Azure Active Directory con Replicon).
    
    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMapping.png)

11. Revise los atributos de usuario que se sincronizan entre Azure AD y Replicon en la sección **Attribute Mappings** (Asignaciones de atributos). Los atributos seleccionados como propiedades de **Matching** (Coincidencia) se usan para buscar coincidencias con las cuentas de usuario de Replicon con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](./active-directory-saas-scoping-filters.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para Replicon, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en Replicon.

    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/UserGroupSelection.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Aprovisionamiento de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/SaveProvisioning.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos al informe de la actividad de aprovisionamiento, que describe todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en Replicon.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
