---
title: "No puedo iniciar sesión en la suscripción de Azure | Microsoft Docs"
description: "Se describe cómo solucionar algunos problemas comunes de inicio de sesión de la suscripción de Azure."
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 05e23ad6bc07293f53e081b905c3a9ce1c91e6f9
ms.contentlocale: es-es
ms.lasthandoff: 04/07/2017


---
# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>No puedo iniciar sesión para administrar mi suscripción de Azure
Este artículo le guía por algunos de los métodos más comunes para resolver problemas de inicio de sesión.

## <a name="page-hangs-in-the-loading-status"></a>La página se bloquea en el estado de carga
Si la página del explorador de Internet se bloquea, pruebe cada uno de los siguientes pasos hasta que se puede acceder a [Azure Portal](https://portal.azure.com).

* Actualice la página.
* Use un explorador de Internet diferente.
* Si está usando Microsoft Internet Explorer, vaya a Azure Portal mediante el modo de exploración InPrivate. 
  
  A. Haga clic en el botón **Herramientas** ![](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Seguridad** > **Exploración de InPrivate**.
  
  B. Vaya a [Azure Portal](https://portal.azure.com)e inicie sesión en el portal.

## <a name="error-message-no-subscriptions-found"></a>Mensaje de Error No se han encontrado suscripciones
Si su cuenta no tiene los permisos necesarios, puede ver un mensaje de error **No se encontró ninguna suscripción**. Asegúrese de que inicie sesión como el administrador correcto. Un administrador de cuenta solo puede acceder al [Centro de cuentas](https://account.windowsazure.com/Subscriptions). Los administradores de cuenta (CA) y los administradores de servicios (SA) y los coadministradores (CA) solo tienen acceso a [Azure Portal](https://portal.azure.com) o al Portal de Azure clásico.

**Escenario 1: Se recibe un mensaje de error en [Azure Portal](https://portal.azure.com)**

Para corregir este problema:

* Asegúrese de haber seleccionado el directorio correcto de Azure haciendo clic en la cuenta pertinente de la parte superior derecha.

![Selección del directorio en la parte superior derecha de Azure Portal](./media/billing-cannot-login-subscription/directory-switch.png)

* Si selecciona el directorio de Azure correcto, pero sigue produciéndose el error, [agregue la cuenta como propietario](billing-add-change-azure-subscription-administrator.md).

**Escenario 2: Se recibe un mensaje de error en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions)**

Compruebe si la cuenta que ha usado es el administrador de cuentas. Para comprobar quién es el administrador de cuentas, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el menú de concentrador, seleccione **Suscripción**.
3. Seleccione la suscripción que quiera comprobar y, a continuación, seleccione **Configuración**.
4. Seleccione **Propiedades**. El administrador de cuentas de la suscripción se muestra en el cuadro **Administrador de cuentas** .

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Inicia sesión automáticamente con un usuario diferente
Este problema puede producirse si usa más de una cuenta de usuario en un explorador de Internet.

Para solucionar este problema, pruebe uno de los métodos siguientes:

* Borre la memoria caché y elimine las cookies de Internet. En Internet Explorer, haga clic en el botón **Herramientas** ![](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Opciones de Internet** > **Eliminar**. Asegúrese de que las casillas de archivos temporales, cookies, contraseñas e historial de exploración están activadas y luego haga clic en Eliminar.
* Restablezca la configuración de Internet Explorer para revertir la configuración personal que haya realizado. Haga clic en el botón **Herramientas** ![](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Opciones de Internet** > **Avanzadas** > seleccione la casilla **Eliminar configuración personal** > **Restablecer**.
* Vaya a Azure Portal en modo de exploración InPrivate. Haga clic en el botón **Herramientas** ![](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Seguridad** > **Exploración de InPrivate**.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) para resolver el problema rápidamente. 


