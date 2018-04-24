---
title: "Tutorial: Configuración de Cornerstone OnDemand para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs"
description: "Aprenda a configurar Azure Active Directory para aprovisionar y cancelar el aprovisionamiento de cuentas de usuario de Cornerstone OnDemand automáticamente."
services: active-directory
documentationcenter: 
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 8aed9bdcdd54f2f0ef3cd81f979635e5716f0b5f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configuración de Cornerstone OnDemand para el aprovisionamiento automático de usuarios


El objetivo de este tutorial es mostrar los pasos que se DAN en Cornerstone OnDemand y Azure Active Directory (Azure AD) para configurar Azure AD para que aprovisione y desaprovisione automáticamente los usuarios o grupos en Cornerstone OnDemand.


> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Requisitos previos

En el escenario que se describe en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure AD
*   Un inquilino de Cornerstone OnDemand
*   Una cuenta de usuario de Cornerstone OnDemand con permisos de administrador


> [!NOTE]
> La integración del aprovisionamiento de Azure AD utiliza el [servicio web de Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), que está disponible para los equipos de Cornerstone OnDemand.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Adición de Cornerstone OnDemand desde la galería
Antes de configurar Cornerstone OnDemand para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar Cornerstone OnDemand desde la Galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas.

**Para agregar Cornerstone OnDemand desde la Galería de aplicaciones de Azure AD, siga estos pasos:**

1. En **[Azure Portal](https://portal.azure.com)**, en el panel de navegación izquierdo, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Sección Aplicaciones empresariales][2]
    
3. Para agregar Cornerstone OnDemand, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Cornerstone OnDemand**.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearch.png)

5. En el panel de resultados, seleccione **Cornerstone OnDemand** y, luego, haga clic en el botón **Agregar** para agregar Cornerstone OnDemand a la lista de aplicaciones SaaS.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearchResults.png)

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Asignación de usuarios a Cornerstone OnDemand

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han "asignado" a una aplicación en Azure AD. 

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a Cornerstone OnDemand. Una vez que lo decida, puede asignar dichos usuarios o grupos a Cornerstone OnDemand siguiendo estas instrucciones:

*   [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Sugerencias importantes para asignar usuarios a Cornerstone OnDemand

*   Se recomienda asignar un único usuario de Azure AD a Cornerstone OnDemand para probar la configuración del aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar un usuario a Cornerstone OnDemand, debe seleccionar cualquier el rol específico de la aplicación válido (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configuración del aprovisionamiento automático de usuarios en Cornerstone OnDemand

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de AD Azure para crear, actualizar y deshabilitar usuarios o grupos en Cornerstone OnDemand en función de las asignaciones de grupo y usuarios de Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Cornerstone OnDemand en Azure AD:


1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Seleccione Cornerstone OnDemand en la lista de aplicaciones SaaS.
 
    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/Successcenter2.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningTab.png)

4. En **Modo de aprovisionamiento**, elija **Automático**.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningCredentials.png)

5. En la sección **Credenciales de administrador**, escriba el **nombre de usuario de administrador**, la **contraseña de administrador** y el **dominio** de la cuenta de Cornerstone OnDemand.

    *   En el campo **Nombre de usuario de administrador**, rellene el nombre de usuario de la cuenta de administrador de su inquilino de Cornerstone OnDemand. Ejemplo: admin.

    *   En el campo **Contraseña de administrador**, rellene la contraseña correspondiente al nombre de usuario de administrador.

    *   En el campo **Dominio**, rellene la dirección URL del servicio web de Cornerstone. Ejemplo: el servicio se encuentra en `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, para Contoso el dominio es `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`.

6. Tras rellenar los campos que se muestran en el paso 5, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Cornerstone OnDemand. Si la conexión no se establece, asegúrese de que la cuenta de Cornerstone OnDemand tiene permisos de administrador e inténtelo de nuevo.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/TestConnection.png)

7. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/EmailNotification.png)

8. Haga clic en **Guardar**.

9. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Cornerstone OnDemand (Sincronizar usuarios de Azure Active Directory con Cornerstone OnDemand)**.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMapping.png)

10. En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizan entre Azure AD y Cornerstone OnDemand. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Cornerstone OnDemand con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMappingAttributes.png)

11. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](./active-directory-saas-scoping-filters.md).

12. Para habilitar el servicio de aprovisionamiento de Azure AD para Cornerstone OnDemand, cambie el valor de **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningStatus.png)

13. Defina los usuarios o grupos que desea que se aprovisionen en Cornerstone OnDemand eligiendo los valores deseados en **Ámbito** en la sección **Configuración**.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/SyncScope.png)

14. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Aprovisionamiento de Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/Save.png) 


Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos al informe de la actividad de aprovisionamiento, que describe todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en Cornerstone OnDemand.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](./active-directory-saas-provisioning-reporting.md).
## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_03.png
