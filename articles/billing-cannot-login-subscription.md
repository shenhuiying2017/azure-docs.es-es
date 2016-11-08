---
title: No puedo iniciar sesión para administrar mi suscripción de Azure | Microsoft Docs
description: Se describe la información de solución de problemas para algunos problemas comunes de inicio de sesión de la suscripción de Azure.
services: ''
documentationcenter: ''
author: genlin
manager: msmbaldwin
editor: ''
tags: billing

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: genli

---
# No puedo iniciar sesión para administrar mi suscripción de Azure
Este artículo le guía por algunos de los métodos más comunes para resolver problemas de inicio de sesión.

> [!NOTE]
> Si necesita más ayuda sobre cualquier aspecto de este artículo, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver rápidamente el problema.
> 
> 

## La página se bloquea en el estado "cargando"
Puede ser debido a un problema que afecta el explorador de Internet.

Para resolver este problema, pruebe los métodos siguientes, en el orden especificado. Después de realizar cada método, intente volver a conectarse a la página de inicio de sesión en el portal.

* Actualice la página.
* Use un explorador de Internet diferente.
* Si está usando Microsoft Internet Explorer, vaya a Azure Portal mediante el modo de exploración InPrivate. Para ello, siga estos pasos.
  
  A. Haga clic en **Herramientas** ![botón herramientas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Seguridad** > **Exploración de InPrivate**.
  
  B. Vaya a [Azure Portal](https://portal.azure.com) e inicie sesión en el portal.

## Mensaje de Error No se han encontrado suscripciones
Este problema puede producirse si la cuenta no tiene derechos de usuario suficientes. Un administrador de cuenta solo puede acceder al [Centro de cuentas](https://account.windowsazure.com/), mientras que los administradores de servicios (SA) y los coadministradores (CA) solo tienen acceso a [Azure Portal](https://portal.azure.com).

**Escenario 1: se recibe un mensaje de error en [Azure portal](https://portal.azure.com)**

Para resolver este problema, [agregue el rol de coadministrador o propietario](billing-add-change-azure-subscription-administrator.md) para la cuenta.

**Escenario 2: se recibe un mensaje de error en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions)**

Compruebe si la cuenta que ha usado es el administrador de cuentas. Para comprobar quién es el administrador de cuentas, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el menú de concentrador, seleccione **Suscripción**.
3. Seleccione la suscripción que quiera comprobar y, a continuación, seleccione **Configuración**.
4. Seleccione **Propiedades**. El administrador de cuentas de la suscripción se muestra en el cuadro **Administrador de cuentas**.

## Inicia sesión automáticamente con un usuario diferente
Este problema puede producirse si usa más de una cuenta de usuario en un explorador de Internet.

Para solucionar este problema, pruebe uno de los métodos siguientes:

* Cierre sesión en el portal y, a continuación, vuelva a iniciarla con la cuenta que quiera usar.
* Borre la memoria caché y elimine las cookies de Internet. Para hacer esto en Internet Explorer, haga clic en **Herramientas** ![botón herramientas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Opciones de Internet** > **Eliminar**, asegúrese de que las casillas de archivos temporales, cookies, contraseñas e historial de exploración están activadas y, luego, haga clic en Eliminar.
* Restablezca la configuración de Internet Explorer para revertir la configuración personal que haya realizado. Para ello, haga clic en **Herramientas** ![botón herramientas](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Opciones de Internet** > **Avanzadas** > seleccione la casilla **Eliminar configuración personal** > **Restablecer**.
* Vaya a Azure Portal en modo de exploración InPrivate. Para ello, haga clic en **Herramientas** ![botón herramientas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Seguridad** > **Exploración InPrivate**.

## Tiene que iniciar sesión usando una cuenta organizativa.
Su cuenta de Microsoft es la dirección de correo electrónico que usa, junto con su contraseña para iniciar sesión en cualquier servicio o programa de Windows Live, como Outlook, Hotmail, OneDrive o MSN. Puede configurar una cuenta de Microsoft usando cualquier dirección de correo electrónico que tenga, incluido el correo electrónico de la compañía. Vea [www.microsoft.com/account](http://www.microsoft.com/account) para más detalles.

La página de inicio de sesión predeterminada de Azure Portal es para la cuenta Microsoft. Si su cuenta está asociada a una cuenta de organización, seleccione la opción de inicio de sesión correcta tal y como se muestra a continuación. Para más información sobre el uso de la cuenta de la organización, vea [Registro en Azure como una organización](active-directory/sign-up-organization.md).

![página de inicio de sesión](./media/billing-cannot-login-subscription/signin.png)

> [!NOTE]
> Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
> 
> 

<!---HONumber=AcomDC_0928_2016-->