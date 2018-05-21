---
title: 'Tutorial: Configuración de Clarizen para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente las cuentas de usuario para Clarizen.
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
ms.date: 01/31/2018
ms.author: v-ant-msft
ms.openlocfilehash: 779fa09781b5ea6e13450d609703bec0c08e38e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>Tutorial: Configuración de Clarizen para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se dan en Clarizen y Azure Active Directory (Azure AD) para configurar Azure AD de modo que aprovisione y desaprovisione automáticamente los usuarios o grupos en Clarizen.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>requisitos previos

En el escenario que se describe en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure AD
*   Un inquilino de Clarizen con el plan [Enterprise Edition](https://www.clarizen.com/product/pricing/) o uno superior habilitado 
*   Una cuenta de usuario de Clarizen con permisos de administrador

> [!NOTE]
> La integración del aprovisionamiento de Azure AD se basa en la [API de Clarizen](https://api.clarizen.com/v2.0/services/), que está disponible para los equipos de Clarizen con el plan Enterprise Edition o uno superior.

## <a name="adding-clarizen-from-the-gallery"></a>Incorporación de Clarizen desde la Galería
Antes de configurar Clarizen para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar Clarizen desde la galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas.

**Para agregar Clarizen desde la galería de aplicaciones de Azure AD, siga estos pasos:**

1. En **[Azure Portal](https://portal.azure.com)**, en el panel de navegación izquierdo, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Sección Aplicaciones empresariales][2]
    
3. Para agregar Clarizen, haga clic en el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Clarizen**.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearch.png)

5. En el panel de resultados, seleccione **Clarizen** y, luego, haga clic en el botón **Agregar** para agregar Clarizen a la lista de aplicaciones SaaS.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>Asignación de usuarios a Clarizen

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han "asignado" a una aplicación en Azure AD. 

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a Clarizen. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a Clarizen:

*   [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>Sugerencias importantes para asignar usuarios a Clarizen

*   Se recomienda asignar un único usuario de Azure AD a Clarizen para probar la configuración del aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar un usuario a Clarizen, debe seleccionar un rol específico de la aplicación válido (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>Configuración del aprovisionamiento automático de usuarios en Clarizen 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de AD Azure para crear, actualizar y deshabilitar usuarios o grupos en Clarizen en función de las asignaciones de grupo y usuarios de Azure AD.

> [!TIP]
> También puede habilitar el inicio de sesión único basado en SAML para Clarizen siguiendo las instrucciones del [tutorial de inicio de sesión único de Clarizen](active-directory-saas-clarizen-tutorial.md). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Clarizen en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Seleccione Clarizen en la lista de aplicaciones SaaS.
 
    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. Seleccione la pestaña **Aprovisionamiento**.
    
    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningTab.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. En la sección **Credenciales de administrador**, escriba el **dominio**, el **nombre de usuario** y la **contraseña de administrador** de la cuenta de Clarizen. Algunos ejemplos de estos valores son:

    *   En el campo **Nombre de usuario de administrador**, rellene el nombre de usuario de la cuenta de administrador de su inquilino de Clarizen. Ejemplo: admin@contoso.com.

    *   En el campo **Contraseña de administrador**, rellene la contraseña de la cuenta correspondiente al nombre de usuario de administrador.

    *   En el campo **Dominio**, rellene el subdominio según el paso 6.
    
6. Recupere el parámetro **serverLocation** en su cuenta de Clarizen basándose en los pasos mencionados en la sección de **autenticación** de [Clarizen's Rest API Guide](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2) (Guía de la API REST de Clarizen). Tras obtener el parámetro serverLocation, utilice el subdominio de la dirección URL como se indica a continuación para rellenar el campo **Dominio**.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ClarizenDomain.png)

7. Tras rellenar los campos que se muestran en el paso 5, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Clarizen. Si la conexión no se establece, asegúrese de que la cuenta de Clarizen tiene permisos de administrador de Clarizen y pruebe otra vez.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/TestConnection.png)
    
8. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/NotificationEmail.png)

9. Haga clic en **Save**(Guardar).

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Clarizen** (Sincronizar usuarios de Azure Active Directory con Clarizen).

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMapping.png)

11. Revise los atributos de usuario que se sincronizan entre Azure AD y Clarizen en la sección **Asignaciones de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Clarizen con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to Clarizen** (Sincronizar grupos de Azure Active Directory con Clarizen).

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMapping.png)

13. Revise los atributos de usuario que se sincronizan entre Azure AD y Clarizen en la sección **Asignaciones de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Clarizen con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](./active-directory-saas-scoping-filters.md).

15. Para habilitar el servicio de aprovisionamiento de Azure AD para Clarizen, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. Defina los usuarios o grupos que desea que se aprovisionen en Clarizen eligiendo los valores deseados en **Ámbito**, en la sección **Configuración**.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ScopeSelection.png)

17. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Aprovisionamiento de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/SaveProvisioning.png)


Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos al informe de la actividad de aprovisionamiento, que describe todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en Clarizen.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
