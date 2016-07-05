<properties
	pageTitle="Uso del inquilino de Office 365 con una suscripción de Azure | Microsoft Azure"
	description="Aprenda cómo agregar un directorio de Office 365 (inquilino) a una suscripción de Azure para realizar la asociación."
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="mbaldwin"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="cjiang"/>

# Asociación del inquilino de Office 365 con una suscripción de Azure
Escenarios: Adquirió las suscripciones de Azure y Office 365 por separado en el pasado y desea poder acceder al inquilino de Office 365 desde la suscripción de Azure. En este artículo se explica lo fácil que es hacerlo.

> [AZURE.NOTE] Este artículo no es aplicable para clientes del contrato Enterprise (EA).

## Guía rápida
Si desea asociar el inquilino de Office 365 a su suscripción de Azure, use su cuenta de Azure para agregarlo y, después, asociar su suscripción de Azure a dicho inquilino. Consulte los pasos detallados.

## Pasos detallados
En este escenario, Kelley Wall es una usuaria que tiene una suscripción de Azure con la cuenta kelly.wall@outlook.com. Kelley también tiene una suscripción a Office 365 con la cuenta kelley.wall@contoso.onmicrosoft.com. Ahora Kelley desea obtener acceso a los inquilinos de Office 365 con la suscripción de Azure.

![msa-aad-status](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![office-365-user](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

**Requisitos previos:**

- Necesita credenciales de administrador de servicios para la suscripción de Azure. Los coadministradores no pueden ejecutar un subconjunto de los pasos.
- Necesita credenciales de administrador global para el inquilino de Office 365.
- La dirección de correo electrónico del administrador de servicios no debe estar contenida en el inquilino de Office 365.
- La dirección de correo electrónico del administrador de servicios no debe coincidir con la de ningún administrador global del inquilino de Office 365.
- Si actualmente utiliza una dirección de correo electrónico que es una cuenta de Microsoft y una cuenta de organización, se recomienda cambiar temporalmente el administrador de servicios de su suscripción de Azure para que use otra cuenta de Microsoft. Esto reducirá las limitaciones conocidas que tiene el procedimiento. Puede crear una cuenta de Microsoft en la [página de suscripción de una cuenta de Microsoft](https://signup.live.com/).

	Para cambiar el administrador de servicios, siga estos pasos:

	1. Inicie sesión en el [Portal de administración de la cuenta](https://account.windowsazure.com/subscriptions).
	2. Seleccione la suscripción que desea cancelar.
	3. En la parte derecha, haga clic en **Editar detalles de suscripción**.

		![azure-edit-subscription-details](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

	4. En el cuadro **ADMINISTRADOR DE SERVICIOS**, escriba la dirección de correo electrónico del nuevo administrador de servicios.

		![change-subscription-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

Para asociar el inquilino de Office 365 con la suscripción de Azure, siga estos pasos:

1. 	Inicie sesión en el [Portal de administración de la cuenta](https://account.windowsazure.com/subscriptions) con las credenciales de administrador de servicios.
2.	En el panel izquierdo, haga clic en **ACTIVE DIRECTORY**.

	![classic-portal-active-directory-entry](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

	> [AZURE.NOTE] No debería ver el inquilino de Office 365. Si lo ve, omita el paso siguiente.

	![aad-tenant-default](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Agregue el inquilino de Office 365 a su suscripción de Azure.
	1. Haga clic en **NUEVO** > **DIRECTORIO** > **CREACIÓN PERSONALIZADA**.

		![aad-custom-create](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

	2. En la página **Agregar directorio** , seleccione **Usar directorio existente** en **DIRECTORIO**, haga clic para seleccionar **Estoy listo para cerrar la sesión ahora** y haga clic en **Completar** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

		![add-directory-use-existing](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

	3. Después de cerrar la sesión, inicie sesión con las credenciales de administrador global de su inquilino de Office 365.

		![sign-in-global-admin-office-365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

	4. Haga clic en **Continue**.

		![use-contoso-directory-azure-verify](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

	5. Haga clic en **Cerrar sesión ahora**.

		![use-contoso-directory-azure-confirm-and-sign-out](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

	6. Inicie sesión en el [Portal de administración de la cuenta](https://account.windowsazure.com/subscriptions) con las credenciales de administrador de servicios.

		![azure-sign-in-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

	7. Debería ver el inquilino de Office 365 en el panel.

		![office-365-tenant-appear-in-azure](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Cambie el directorio asociado a la suscripción de Azure.

	1. Haga clic en **Configuración**.

		![azure-classic-portal-settings-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

	2. Haga clic en la suscripción de Azure y en **EDITAR DIRECTORIO**.

		![azure-subscription-edit-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

	3. Haga clic en **Siguiente** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

		![azure-change-associated-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

		> [AZURE.WARNING] Recibirá una advertencia que se quitarán todos los coadministradores.

		![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

		>[AZURE.WARNING] Además, también se quitarán todos los usuarios del [Control de acceso basado en roles (RBAC)](./active-directory/role-based-access-control-configure.md) con acceso asignado en los grupos de recursos existentes. Sin embargo, la advertencia que recibe únicamente menciona la eliminación de los coadministradores.

		![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

	4. Haga clic en **Completar** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Ahora puede agregar sus cuentas de la organización de Office 365 como coadministradores al inquilino de AAD.

	1. Haga clic en la pestaña **ADMINISTRADORES** y, a continuación, en **AGREGAR**.

		![azure-classic-portal-settings-administrators](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

	2. Escriba una cuenta de la organización de su inquilino de Office 365, haga clic para seleccionar la suscripción de Azure y, a continuación, haga clic en **Completar** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

		![azure-add-co-administrator](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

	3. Vuelva a la pestaña **ADMINISTRADORES**, donde debería ver la cuenta de la organización mostrada como coadministrador.

		![azure-co-administrator-added](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. A continuación, puede probar el acceso con el coadministrador.

	1. Cierre la sesión del Portal de administración de la cuenta.
	2. Abra el [Portal de administración de la cuenta](https://account.windowsazure.com/subscriptions) o el [Portal de Azure](https://portal.azure.com/).
	3. Si la página de inicio de sesión de Azure tiene un vínculo **Inicie sesión con una cuenta de la organización**, haga clic en el vínculo. De lo contrario, omita este paso.

		![Azure-sign-in-with-orgid-link](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

	4. Escriba las credenciales del coadministrador y haga clic en **Iniciar sesión**.

		![Azure-sign-in-with-co-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## Pasos siguientes
Hay escenarios relacionados donde ya dispone de una suscripción de Office 365 y desea obtener una suscripción de Azure, pero quiere utilizar sus cuentas de usuario actuales de Office 365 para la suscripción de Azure. También es posible que ya sea suscriptor de Azure y desee obtener una suscripción de Office 365 para los usuarios de su cuenta actual de Azure Active Directory. Para aprender cómo realizar estas tareas, consulte [Uso de una cuenta de Office 365 con una suscripción de Azure y viceversa](billing-use-existing-office-365-account-azure-subscription.md).

<!---HONumber=AcomDC_0622_2016-->