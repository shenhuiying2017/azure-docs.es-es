<properties
	pageTitle="No puedo iniciar sesión para administrar mi suscripción de Azure | Microsoft Azure"
	description="Se describe la información de solución de problemas para algunos problemas comunes de inicio de sesión de la suscripción de Azure."
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="genli"/>

# No puedo iniciar sesión para administrar mi suscripción de Azure

> [AZURE.NOTE] Si necesita más ayuda sobre cualquier aspecto de este artículo, [póngase en contacto con el soporte técnico](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) para resolver rápidamente el problema.

Este artículo le ayudará a solucionar algunas de las causas comunes de los problemas de inicio de sesión.

## ¿A qué portal está intentando obtener acceso?

Un administrador de cuenta solo puede acceder al [Centro de cuentas](https://account.windowsazure.com/), mientras que los administradores de servicios (SA) y los coadministradores (CA) solo tienen acceso al [Portal de Azure](https://portal.azure.com) o al [Portal de Azure clásico](https://manage.windowsazure.com/).

Para obtener más información sobre los roles de administrador de Azure, vea [Incorporación o cambio de roles de administrador de Azure](billing-add-change-azure-subscription-administrator.md).

## ¿Está la suscripción asociada a una cuenta de Microsoft o a una cuenta de organización?

Su cuenta de Microsoft es la dirección de correo electrónico que usa, junto con su contraseña para iniciar sesión en cualquier servicio o programa de Windows Live, como Outlook, Hotmail, OneDrive o MSN. Puede configurar una cuenta de Microsoft usando cualquier dirección de correo electrónico que tenga, incluido el correo electrónico de la compañía. Vea [www.microsoft.com/account](http://www.microsoft.com/account) para más detalles.

Si su cuenta está asociada a una cuenta de organización, seleccione la opción de inicio de sesión correcta tal y como se muestra a continuación. Para más información sobre el uso de la cuenta de la organización, vea [Registro en Azure como una organización](./active-directory/sign-up-organization.md).

![página de inicio de sesión](./media/billing-cannot-login-subscription/signin.png)

## Coadministrador: ¿Está usando el tipo de cuenta correcto para administrar otras cuentas?

- Si ha iniciado sesión con una cuenta Microsoft, solo puede agregar otras cuentas Microsoft como coadministradores. Este es un requisito de seguridad para evitar que se detecten las cuentas no organizativas si algunas cuentas (por ejemplo, janedoe@contoso.com) son válidas.
- Si ha iniciado sesión con una cuenta organizativa, puede agregar otras cuentas organizativas de su organización como coadministradores. Por ejemplo, abby@contoso.com puede agregar bob@contoso.com como administrador de servicios o coadministrador, pero no puede agregar john@notcontoso.com. Los usuarios que han iniciado sesión con cuentas organizativas también pueden agregar usuarios de cuentas de Microsoft como coadministradores o administradores de servicios.

Ahora que es posible iniciar sesión en Azure con una cuenta profesional, estos son los cambios en los requisitos de la cuenta de administrador de servicios (SA) y coadministrador (CA):

| Método de inicio de sesión| ¿Agregar una cuenta Microsoft como coadministrador o administrador de servicios? |¿Agregar una cuenta de organización de la misma organización como coadministrador o administrador de servicios? |¿Agregar una cuenta de organización de una organización diferente como coadministrador o administrador de servicios?
| ------------- | ------------- |---------------|---------------|
|Cuenta Microsoft |Sí|No|No|
|Cuenta de organización|Sí|Sí|No|

## ¿Hay un problema con el Explorador de Internet?

Trate de eliminar caché o cookies usando el modo de exploración de InPrivate de Internet Explorer y también mediante otro explorador.

> [AZURE.NOTE] Si tiene más preguntas, [póngase en contacto con el soporte técnico](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) para resolver el problema rápidamente.

<!---HONumber=AcomDC_0824_2016-->