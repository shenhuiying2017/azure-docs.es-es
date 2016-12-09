---
title: "Uso de un inquilino de Office 365 con una suscripción de Azure | Microsoft Docs"
description: "Aprenda cómo agregar un directorio de Office 365 (inquilino) a una suscripción de Azure para realizar la asociación."
services: 
documentationcenter: 
author: JiangChen79
manager: mbaldwin
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 36cd9eac7be2d99971d8d2e227cd4b761df82d08


---
# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Asociación del inquilino de Office 365 con una suscripción de Azure
Si adquirió las suscripciones de Azure y Office 365 por separado en el pasado y desea poder acceder al inquilino de Office 365 desde la suscripción de Azure, es fácil hacerlo. Este artículo le muestra cómo.

> [!NOTE]
> Este artículo no es aplicable para clientes del contrato Enterprise (EA).
> 
> 

## <a name="quick-guidance"></a>Guía rápida
Para asociar el inquilino de Office 365 a su suscripción de Azure, use su cuenta de Azure para agregarlo y, después, asocie su suscripción de Azure a ese inquilino.

## <a name="detailed-steps"></a>Pasos detallados
En este escenario, Kelley Wall es una usuaria que tiene una suscripción de Azure con la cuenta kelley.wall@outlook.com. Kelley también tiene una suscripción a Office 365 con la cuenta kelley.wall@contoso.onmicrosoft.com. Ahora Kelley desea obtener acceso a los inquilinos de Office 365 con la suscripción de Azure.

![Captura de pantalla del estado de Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Captura de pantalla de los usuarios activos del centro de administración de Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>Requisitos previos
Para que la asociación funcione correctamente, son necesarios los siguientes requisitos previos:

* Necesita las credenciales de administrador de servicios para la suscripción de Azure. Los coadministradores no pueden ejecutar un subconjunto de los pasos.
* Necesita las credenciales de administrador global para el inquilino de Office 365.
* La dirección de correo electrónico del administrador de servicios no debe estar contenida en el inquilino de Office 365.
* La dirección de correo electrónico del administrador de servicios no debe coincidir con la de ningún administrador global del inquilino de Office 365.
* Si actualmente utiliza una dirección de correo electrónico que es una cuenta de Microsoft y una cuenta de organización, cambie temporalmente el administrador de servicios de su suscripción de Azure para que use otra cuenta de Microsoft. Puede crear una cuenta de Microsoft en la [página de suscripción de una cuenta de Microsoft](https://signup.live.com/).

Para cambiar el administrador de servicios, siga estos pasos:

1. Cierre la sesión del [Portal de administración de la cuenta](https://account.windowsazure.com/subscriptions).
2. Seleccione la suscripción que desea cambiar.
3. Seleccione **Editar detalles de la suscripción**.
   
    ![Captura de pantalla de la información de suscripción de Azure, con la opción "Editar detalles de la suscripción" resaltada](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)
4. En el cuadro **ADMINISTRADOR DE SERVICIOS** , escriba la dirección de correo electrónico del nuevo administrador de servicios.
   
    ![Captura de pantalla del cuadro de diálogo "Modificar su suscripción"](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Asociación del inquilino de Office 365 con la suscripción de Azure
Para asociar el inquilino de Office 365 con la suscripción de Azure, siga estos pasos:

1. Inicie sesión en el [Portal de administración de la cuenta](https://account.windowsazure.com/subscriptions) con las credenciales de administrador de servicios.
2. En el panel izquierdo, seleccione **ACTIVE DIRECTORY**.
   
   ![Captura de pantalla de la entrada de Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
   
   > [!NOTE]
   > No debería ver el inquilino de Office 365. Si lo ve, omita el paso siguiente.
   > 
   > 
   
   ![Captura de pantalla del directorio predeterminado de Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)
3. Agregue el inquilino de Office 365 a su suscripción de Azure.
   
    a. Seleccione **NUEVO** > **DIRECTORIO** > **CREACIÓN PERSONALIZADA**.
   
    ![Captura de pantalla de la creación personalizada de Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
    b. En la página **Agregar directorio**, en **DIRECTORIO**, seleccione **Usar directorio existente**. A continuación, seleccione **Estoy listo para cerrar la sesión ahora** y seleccione **Completar** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Captura de pantalla de "Usar directorio existente"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
    c. Después de cerrar la sesión, inicie sesión con las credenciales de administrador global de su inquilino de Office 365.
   
    ![Captura de pantalla de inicio de sesión del administrador global de Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
    d. Seleccione **Continuar**.
   
    ![Captura de pantalla de comprobación](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
    e. Seleccione **Cerrar sesión ahora**.
   
    ![Captura de pantalla del cierre de sesión](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
    f. Inicie sesión en el [Portal de administración de la cuenta](https://account.windowsazure.com/subscriptions) con las credenciales de administrador de servicios.
   
    ![Captura de pantalla de inicio de sesión en Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
    g. Debería ver el inquilino de Office 365 en el panel.
   
    ![Captura de pantalla del panel](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)
4. Cambie el directorio asociado a la suscripción de Azure.
   
    a. Seleccione **Configuración**.
   
    ![Captura de pantalla del icono de configuración del Portal de Azure clásico](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
    b. Seleccione la suscripción de Azure y, después, **EDITAR DIRECTORIO**.
    ![Captura de pantalla de la opción Editar directorio de la suscripción de Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
    c. Seleccione **Siguiente** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).
   
    ![Captura de pantalla de "Cambiar el directorio asociado"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
   > [!WARNING]
   > Recibirá una advertencia que se quitarán todos los coadministradores.
   > 
   > 
   
    ![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
   > [!WARNING]
   > Además, también se quitarán todos los usuarios del [Control de acceso basado en roles (RBAC)](active-directory/role-based-access-control-configure.md) con acceso asignado en los grupos de recursos existentes. Sin embargo, la advertencia que recibe únicamente menciona la eliminación de los coadministradores.
   > 
   > 
   
    ![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
    d. Seleccione **Completar** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
5. Ahora puede agregar sus cuentas de la organización de Office 365 como coadministradores al inquilino de Azure Active Directory.
   
    a. Seleccione la pestaña **ADMINISTRADORES** y, a continuación, **AGREGAR**.
   
    ![Captura de pantalla de la pestaña de administradores de la configuración del Portal de Azure clásico](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
    b. Escriba una cuenta de la organización de su inquilino de Office 365, seleccione la suscripción de Azure y, a continuación, seleccione **Completar** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Captura de pantalla del cuadro de diálogo Agregar coadministrador de Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
    c. Vuelva a la pestaña **ADMINISTRADORES**. Debería ver la cuenta de la organización mostrada como coadministrador.
   
    ![Captura de pantalla de la pestaña Administradores](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
6. A continuación, puede probar el acceso con el coadministrador.
   
    a. Cierre la sesión del Portal de administración de la cuenta.
   
    b. Abra el [Portal de administración de la cuenta](https://account.windowsazure.com/subscriptions) o [Azure portal](https://portal.azure.com/).
   
    c. Si la página de inicio de sesión de Azure tiene un vínculo **Inicie sesión con una cuenta de la organización**, selecciónelo. De lo contrario, omita este paso.
   
    ![Captura de pantalla de la página de inicio de sesión en Azure](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)
   
    d. Escriba las credenciales del coadministrador y, a continuación, seleccione **Iniciar sesión**.
   
    ![Captura de pantalla de la página de inicio de sesión en Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>Pasos siguientes
Entre los escenarios relacionados se incluyen los siguientes:

* Ya dispone de una suscripción de Office 365 y desea obtener una suscripción de Azure, y quiere utilizar sus cuentas de usuario actuales de Office 365 para la suscripción de Azure.
* Ya es suscriptor de Azure y desea obtener una suscripción de Office 365 para los usuarios de la instancia existente de Azure Active Directory.

Para aprender cómo realizar estas tareas, consulte [Uso de una cuenta de Office 365 con una suscripción de Azure y viceversa](billing-use-existing-office-365-account-azure-subscription.md).




<!--HONumber=Nov16_HO3-->


