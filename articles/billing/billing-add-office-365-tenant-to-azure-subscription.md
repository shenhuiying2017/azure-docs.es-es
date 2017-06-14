---
title: "Uso de un inquilino de Office 365 con una suscripción de Azure | Microsoft Docs"
description: "Aprenda cómo agregar un directorio de Office 365 (inquilino) a una suscripción de Azure."
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: cjiang
ms.translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 42db903244e5642dbf273e71994d402f8aeda1e9
ms.contentlocale: es-es
ms.lasthandoff: 02/22/2017


---
# <a name="associate-an-office-365-tenant-to-an-azure-subscription"></a>Asociación de un inquilino de Office 365 a una suscripción de Azure
Vincule las suscripciones independientes de Azure y Office 365 para poder acceder al inquilino de Office 365 desde su suscripción de Azure. Para vincular sus suscripciones, inicie sesión en Azure con la cuenta de administrador de servicio de Azure, agregue un directorio y agregue las cuentas de organización de Office 365 al inquilino de Azure Active Directory.

Si desea una suscripción de Office 365 para los usuarios de la instancia de Azure Active Directory o tiene una cuenta de Office 365, pero no una cuenta de Azure, consulte [Uso de una cuenta de Office 365 con la suscripción de Azure y viceversa](billing-use-existing-office-365-account-azure-subscription.md). 

## <a name="before-you-begin"></a>Antes de empezar
* Necesita las credenciales de administrador de servicios de la suscripción de Azure. Las cuentas de coadministrador no pueden realizar algunos de los pasos de este artículo. Para cambiar el administrador del servicio, consulte [Incorporación o cambio de roles de administrador de Azure](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription).
* Debe tener las credenciales de administrador global del inquilino de Office 365.
* La dirección de correo electrónico del administrador de servicios no debe estar en el inquilino de Office 365.
* La dirección de correo electrónico del administrador de servicios no debe coincidir con la de ningún administrador global del inquilino de Office 365.
* Si utiliza una dirección de correo electrónico que es una cuenta de Microsoft y una cuenta de organización, cambie temporalmente el administrador de servicios de su suscripción de Azure para que use otra cuenta de Microsoft. Puede crear una cuenta de Microsoft en la [página de suscripción de una cuenta de Microsoft](https://signup.live.com/).

## <a name="link-office-365-tenant-to-azure-subscription"></a>Vinculación de un inquilino de Office 365 a una suscripción de Azure
Para asociar el inquilino de Office 365 con la suscripción de Azure, siga estos pasos:

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>Paso 1: agregue el inquilino de Office 365 a su suscripción de Azure.

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con las credenciales de administrador de servicios.

    ![Captura de pantalla de inicio de sesión en Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

2. En el panel izquierdo, seleccione **ACTIVE DIRECTORY**. No debería ver el inquilino de Office 365. Si lo ve, vaya al [Paso 2: cambio del directorio asociado a la suscripción de Azure](#Step2).
   
   ![Captura de pantalla de la entrada de Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

3. Seleccione **NUEVO** > **DIRECTORIO** > **CREACIÓN PERSONALIZADA**.
   
    ![Captura de pantalla de la creación personalizada de Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
4. En la página **Agregar directorio**, en **DIRECTORIO**, seleccione **Usar directorio existente**. A continuación, seleccione **Estoy listo para cerrar la sesión ahora** y seleccione **Completar** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Captura de pantalla de "Usar directorio existente"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
5. Después de cerrar la sesión, inicie sesión con las credenciales de administrador global de su inquilino de Office 365.
   
    ![Captura de pantalla de inicio de sesión del administrador global de Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
6. Seleccione **Continuar**.
   
    ![Captura de pantalla de comprobación](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
7. Seleccione **Cerrar sesión ahora**.
   
    ![Captura de pantalla del cierre de sesión](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
8. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con las credenciales de administrador de servicios.
   
    ![Captura de pantalla de inicio de sesión en Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
9. Debería ver el inquilino de Office 365 en el panel.
   
    ![Captura de pantalla del panel](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="Step2"></a>Paso 2: cambio del directorio asociado a la suscripción de Azure.
   
1. Seleccione **Configuración**.
   
    ![Captura de pantalla del icono de configuración del Portal de Azure clásico](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
2. Seleccione la suscripción de Azure y, después, **EDITAR DIRECTORIO**.

    ![Captura de pantalla del directorio de edición de la suscripción de Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
3. Seleccione **Siguiente** ![Siguiente icono](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).
   
    ![Captura de pantalla de "Cambiar el directorio asociado"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
4. Revise las cuentas afectadas. Se eliminan todos los coadministradores y usuarios del [control de acceso basado en roles (RBAC)](../active-directory/role-based-access-control-configure.md) con acceso asignado en los grupos de recursos existentes. La advertencia que recibe únicamente menciona la eliminación de los coadministradores.
      
    ![Captura de pantalla que muestra las cuentas de coadministrador que se eliminarán.](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![Captura de pantalla que muestra la cuenta de usuario de ejemplo que se eliminará.](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
5. Seleccione **Completar** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

### <a name="step-3-add-your-office-365-organizational-accounts-as-co-administrators-to-the-azure-active-directory-tenant"></a>Paso 3: agregue sus cuentas de organización de Office 365 como coadministradores al inquilino de Azure Active Directory.
   
1. Seleccione la pestaña **ADMINISTRADORES** y, a continuación, **AGREGAR**.
   
    ![Captura de pantalla de la pestaña de administradores de la configuración del Portal de Azure clásico](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
2. Escriba una cuenta de la organización de su inquilino de Office 365, seleccione la suscripción de Azure y, a continuación, seleccione **Completar** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Captura de pantalla del cuadro de diálogo Agregar coadministrador de Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
3. Vuelva a la pestaña **ADMINISTRADORES**. Debería ver la cuenta de la organización mostrada como coadministrador.
   
    ![Captura de pantalla de la pestaña Administradores](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
4.  Pruebe el acceso a Azure con la cuenta de coadministrador.
   
    a. Cierre sesión en el Portal de Azure clásico.
   
    b. Abra el [Portal de Azure](https://portal.azure.com/).
   
    c. Escriba las credenciales del coadministrador y, a continuación, seleccione **Iniciar sesión**.
   
    ![Captura de pantalla de la página de inicio de sesión en Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.



