---
title: 'Tutorial: Configuración de BlueJeans para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente las cuentas de usuario para BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: v-ant
ms.openlocfilehash: 55a907bdab57ce73533361782a3890466e3076ea
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Tutorial: Configuración de BlueJeans para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se dan en BlueJeans y Azure Active Directory (Azure AD) para configurar Azure AD de modo que aprovisione y desaprovisione automáticamente los usuarios o grupos en BlueJeans.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>requisitos previos

En el escenario que se describe en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure AD
*   Un inquilino de BlueJeans con el plan [My Company](https://www.BlueJeans.com/pricing) o superior habilitado
*   Una cuenta de usuario de BlueJeans con permisos de administrador

> [!NOTE]
> La integración del aprovisionamiento de Azure AD se basa en la [API de BlueJeans](https://BlueJeans.github.io/developer), que está disponible para los equipos de BlueJeans con el plan Standard o uno superior.

## <a name="adding-bluejeans-from-the-gallery"></a>Incorporación de BlueJeans desde la galería
Antes de configurar BlueJeans para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar BlueJeans desde la galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas.

**Para agregar BlueJeans desde la galería de aplicaciones de Azure AD, siga estos pasos:**

1. En **[Azure Portal](https://portal.azure.com)**, en el panel de navegación izquierdo, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Sección Aplicaciones empresariales][2]
    
3. Para agregar BlueJeans, haga clic en el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **BlueJeans**.

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppSearch.png)

5. En el panel de resultados, seleccione **BlueJeans** y, luego, haga clic en el botón **Agregar** para agregar BlueJeans a la lista de aplicaciones SaaS.

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppSearchResults.png)

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppCreate.png)
    
## <a name="assigning-users-to-bluejeans"></a>Asignación de usuarios a BlueJeans

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han "asignado" a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a BlueJeans. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a BlueJeans:

*   [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Sugerencias importantes para asignar usuarios a BlueJeans

*   Se recomienda asignar un único usuario de Azure AD a BlueJeans para probar la configuración del aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar un usuario a BlueJeans, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Configuración del aprovisionamiento automático de usuarios en BlueJeans

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de AD Azure para crear, actualizar y deshabilitar usuarios o grupos en BlueJeans en función de las asignaciones de grupo y usuarios de Azure AD.

> [!TIP]
> También puede habilitar el inicio de sesión único basado en SAML para BlueJeans siguiendo las instrucciones del [tutorial de inicio de sesión único de BlueJeans](active-directory-saas-bluejeans-tutorial.md). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para BlueJeans en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Seleccione BlueJeans en la lista de aplicaciones SaaS.
 
    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/Bluejeans2.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/Bluejeans1.png)

5. En la sección **Credenciales de administrador**, escriba el **nombre de usuario** y la **contraseña de administrador** de la cuenta de BlueJeans. Algunos ejemplos de estos valores son:

    *   En el campo **Nombre de usuario de administrador**, rellene el nombre de usuario de la cuenta de administrador de su inquilino de BlueJeans. Ejemplo: admin@contoso.com.

    *   En el campo **Contraseña de administrador**, rellene la contraseña correspondiente al nombre de usuario de administrador.

6. Tras rellenar los campos que se muestran en el paso 5, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a BlueJeans. Si la conexión no se establece, asegúrese de que la cuenta de BlueJeans tiene permisos de administrador de BlueJeans y pruebe otra vez.

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Haga clic en **Save**(Guardar).

9. En la sección **Mappings** (Asignaciones), seleccione **Synchronize Azure Active Directory Users to BlueJeans** (Sincronizar usuarios de Azure Active Directory con BlueJeans).

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Revise los atributos de usuario que se sincronizan entre Azure AD y BlueJeans en la sección **Attribute Mapping** (Asignaciones de atributos). Los atributos seleccionados como propiedades de **Matching**(Coincidencia) se usan para buscar coincidencias con las cuentas de usuario de BlueJeans con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](./active-directory-saas-scoping-filters.md).

12. Para habilitar el aprovisionamiento del servicio de aprovisionamiento de Azure AD para BlueJeans, cambie el **estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Defina los usuarios o grupos que desea que se aprovisionen en BlueJeans eligiendo los valores deseados en **Ámbito**, en la sección **Configuración**.

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Aprovisionamiento de BlueJeans](./media/active-directory-saas-bluejeans-provisioning-tutorial/SaveProvisioning.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos al informe de la actividad de aprovisionamiento, que describe todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en BlueJeans.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_03.png