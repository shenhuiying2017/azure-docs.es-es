<properties
	pageTitle="Adición o cambio del coadministrador, el administrador de servicios y el administrador de cuenta de Azure | Microsoft Azure"
	description="Se describe cómo agregar o cambiar el coadministrador, el administrador de servicios y el administrador de cuenta de Azure."
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="meerak"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="genli"/>

# Adición o cambio del coadministrador, el administrador de servicios y el administrador de cuenta de Azure
## Roles de administrador

Hay tres tipos de roles de administrador en Microsoft Azure:

| Rol administrativo | Límite | Descripción
| ------------- | ------------- |---------------|
|Administrador de cuenta | 1 por cuenta de Azure |Es la persona que se registró para una suscripción de Azure o que compró una, y está autorizada para tener acceso al Centro de cuentas y realizar diversas tareas de administración. Entre estas se incluyen poder crear suscripciones, cancelar suscripciones, cambiar la facturación de una suscripción y cambiar el administrador de servicios.
| Administrador de servicios | 1 por cada suscripción de Azure |Esta persona está autorizada para acceder al Portal de administración de Azure de las suscripciones en la cuenta que tiene asignada. De forma predeterminada, en una nueva suscripción, el administrador de cuenta es también el administrador de servicios.|
|Coadministrador|200 por suscripción (además del administrador de servicios)|Esta persona tiene los mismos privilegios de acceso que el administrador de servicios, pero no puede cambiar la asociación de suscripciones a directorios de Azure.|

## Adición de un coadministrador a una suscripción
1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2. En el panel de navegación, seleccione **Configuración**> **Administradores**> **Agregar**. </br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Escriba la dirección de correo electrónico de la persona que quiere agregar como coadministrador y luego seleccione la suscripción a la que quiere que el coadministrador tenga acceso.</br> ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

Se puede agregar la siguiente dirección de correo electrónico como coadministrador:

* **Cuenta Microsoft** (anteriormente Windows Live ID) </br> Puede usar una cuenta Microsoft para iniciar sesión en todos los productos y servicios en la nube de Microsoft orientados al consumidor, como Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone y Xbox LIVE.
* **Cuenta de organización**</br> Una cuenta de organización es una cuenta que se crea en Azure Active Directory. La dirección de la cuenta de organización se parece a esta: usuario@<your domain>.onmicrosoft.com.

**Nota**: *si ha iniciado sesión con una cuenta Microsoft, solo puede agregar otras cuentas Microsoft como coadministrador. Se trata de una consideración de seguridad para evitar que las cuentas que no son de organización detecten si determinadas cuentas (p. ej. janedoe@contoso.com) son válidas. * Si ha iniciado sesión con una cuenta de organización, puede agregar otras cuentas de organización a su organización como coadministrador. Por ejemplo, abby@contoso.com puede agregar bob@contoso.com como administrador de servicios o coadministrador, pero no puede agregar john@notcontoso.com. Los usuarios que iniciaron sesión con cuentas de organización pueden seguir agregando usuarios de cuentas Microsoft como administrador de servicios o coadministrador. * Ahora que es posible iniciar sesión en Azure con una cuenta de organización, estos son los cambios en los requisitos de la cuenta de administrador de servicios y coadministrador:

| Método de inicio de sesión| ¿Agregar una cuenta Microsoft como coadministrador o administrador de servicios? |¿Agregar una cuenta de organización de la misma organización como coadministrador o administrador de servicios? |¿Agregar una cuenta de organización de una organización diferente como coadministrador o administrador de servicios?
| ------------- | ------------- |---------------|---------------|
|Cuenta Microsoft |Sí|No|No|
|Cuenta de organización|Sí|Sí|No|

## Cambio del administrador de servicios de una suscripción
Solo el administrador de cuenta puede cambiar el administrador de servicios de una suscripción.

1. Inicie sesión en [Portal de administración de cuentas](https://account.windowsazure.com/subscriptions) con el administrador de cuenta.
2. Seleccione la suscripción que desea cancelar.
3. En el lado derecho, haga clic en **Editar suscripción**. </br> ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. En el cuadro **ADMINISTRADOR DE SERVICIOS**, escriba la dirección de correo electrónico del nuevo administrador de servicios. ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Cambio del administrador de cuenta (transferencia de la propiedad de la cuenta de Azure a otra cuenta)

Para transferir la propiedad de la cuenta de Azure a otra cuenta, consulte [Transferencia de una suscripción de Azure](../billing-subscription-transfer.md).

<!---HONumber=Nov15_HO1-->