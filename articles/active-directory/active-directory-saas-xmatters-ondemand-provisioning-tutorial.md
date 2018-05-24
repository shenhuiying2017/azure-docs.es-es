---
title: 'Tutorial: Configuración de xMatters OnDemand para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Obtenga información para configurar Azure Active Directory para aprovisionar y cancelar el aprovisionamiento de cuentas de usuario de xMatters OnDemand automáticamente.
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
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: e0b945a99766ee52cb357f54d7135fbbdf1fada2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353796"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configuración de xMatters OnDemand para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que han de realizarse en xMatters OnDemand y Azure Active Directory (Azure AD) para configurar Azure AD para que aprovisione y cancele el aprovisionamiento de usuarios o grupos en xMatters OnDemand automáticamente.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>requisitos previos

En el escenario que se describe en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure AD
*   Un inquilino de xMatters OnDemand con el plan [Starter](https://www.xmatters.com/pricing) o superior habilitado 
*   Una cuenta de usuario de xMatters OnDemand con permisos de administrador

> [!NOTE]
> La integración del aprovisionamiento de Azure AD se basa en la [API de xMatters OnDemand](https://help.xmatters.com/xmAPI), que está disponible para los equipos de xMatters OnDemand con el plan Starter o uno superior.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Incorporación de xMatters OnDemand desde la galería

Antes de configurar xMatters OnDemand para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar xMatters OnDemand desde la galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas.

**Para agregar xMatters OnDemand desde la galería de aplicaciones de Azure AD, siga estos pasos:**

1. En **[Azure Portal](https://portal.azure.com)**, en el panel de navegación izquierdo, haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Sección Aplicaciones empresariales][2]
    
3. Para agregar xMatters OnDemand, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **xMatters OnDemand**.

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. En el panel de resultados, seleccione **xMatters OnDemand** y luego haga clic en el botón **Agregar** para agregar xMatters OnDemand a la lista de aplicaciones SaaS.

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>Asignación de usuarios a xMatters OnDemand

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han "asignado" a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a xMatters OnDemand. Una vez que lo decida, puede asignar dichos usuarios o grupos a xMatters OnDemand con estas instrucciones:

*   [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>Sugerencias importantes para asignar usuarios a xMatters OnDemand

*   Se recomienda asignar un único usuario de Azure AD a xMatters OnDemand para probar la configuración del aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar un usuario a xMatters OnDemand, debe seleccionar cualquier rol específico de la aplicación apto (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>Configuración del aprovisionamiento automático de usuarios en xMatters OnDemand 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de AD Azure para crear, actualizar y deshabilitar usuarios o grupos en xMatters OnDemand en función de las asignaciones de grupos o usuarios de Azure AD.

> [!TIP]
> También puede optar por habilitar el inicio de sesión único basado en SAML para xMatters OnDemand si sigue las instrucciones del [tutorial de inicio de sesión único de xMatters OnDemand](active-directory-saas-xmatters-ondemand-tutorial.md). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para xMatters OnDemand en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Seleccione xMatters OnDemand en la lista de aplicaciones SaaS.
 
    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. Seleccione la pestaña **Aprovisionamiento**.
    
    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. En la sección **Credenciales de administrador**, escriba el **Nombre de usuario de administrador**, la **Contraseña de administrador** y el **Dominio** de la cuenta de xMatters OnDemand.

    *   En el campo **Nombre de usuario de administrador**, rellene el nombre de usuario de la cuenta de administrador del inquilino de xMatters OnDemand. Ejemplo: admin@contoso.com.

    *   En el campo **Contraseña de administrador**, rellene la contraseña correspondiente al nombre de usuario de administrador.

    *   En el campo **Dominio**, rellene el subdominio del inquilino de xMatters OnDemand.
    Ejemplo: para una cuenta con una dirección URL de inquilino con el valor https://my-tenant.xmatters.com, el subdominio sería **my-tenant**.

6. Tras rellenar los campos que se muestran en el paso 5, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a xMatters OnDemand. Si la conexión no se establece, asegúrese de que la cuenta de xMatters OnDemand tiene permisos de administrador e inténtelo de nuevo.

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. Haga clic en **Save**(Guardar).

9. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to xMatters OnDemand** (Sincronizar usuarios de Azure Active Directory con xMatters OnDemand).

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizan entre Azure AD y xMatters OnDemand. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de xMatters OnDemand con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to xMatters OnDemand** (Sincronizar grupos de Azure Active Directory con xMatters OnDemand).

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. En la sección **Asignaciones de atributos**, revise los atributos de grupo que se sincronizan entre Azure AD y xMatters OnDemand. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con los grupos de xMatters OnDemand con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](./active-directory-saas-scoping-filters.md).

14. Para habilitar el servicio de aprovisionamiento de Azure AD para xMatters OnDemand, cambie el valor de **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. Defina los usuarios o grupos que quiere que se aprovisionen en xMatters OnDemand al elegir los valores deseados en **Ámbito**, en la sección **Configuración**.

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Aprovisionamiento de xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos al informe de la actividad de aprovisionamiento, que describe todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en xMatters OnDemand.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
