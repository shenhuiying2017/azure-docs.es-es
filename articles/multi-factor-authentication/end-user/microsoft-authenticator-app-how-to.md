---
title: "Aplicación Microsoft Authenticator para teléfonos móviles | Microsoft Docs"
description: "Aprenda a actualizar a la versión más reciente de Azure Authenticator."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/25/2016
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6872b216ab2bfd5823b65b1bb70d503c7628ee13
ms.openlocfilehash: 26d9f04f9d3019ab5fcad48ef1e9821a4dd6a724
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Introducción a la aplicación Microsoft Authenticator
La aplicación Microsoft Authenticator proporciona un nivel de seguridad adicional a la cuenta de Azure (por ejemplo, bsimon@contoso.onmicrosoft.com), la cuenta profesional local (por ejemplo, bsimon@contoso.com) o la cuenta de Microsoft (por ejemplo, bsimon@outlook.com).

La aplicación funciona de una de estas dos formas:

* **Notificación**. La aplicación puede ayudar a impedir el acceso no autorizado a las cuentas y detener las transacciones fraudulentas mediante el envío de una notificación al smartphone o a la tableta. Solo tiene que ver la notificación y, si es legítima, seleccionar **Comprobar**. De lo contrario, seleccione **Denegar**. Para más información sobre cómo denegar notificaciones, consulte How to use the Deny and Report Fraud Feature for Multi-Factor Authentication (Uso de la característica Denegar y notificar fraude para Multi-Factor Authentication).
* **Contraseña con código de comprobación**. La aplicación puede utilizarse como token de software para generar un código de comprobación de OAuth. Después de escribir el nombre de usuario y la contraseña, especifique el código que facilita la aplicación en la pantalla de inicio de sesión. El código de comprobación es una forma adicional de autenticación.

La aplicación Microsoft Authenticator sustituye a la aplicación Azure Authenticator.  La aplicación Azure Authenticator seguirá funcionando, pero si decide dar el paso a la nueva aplicación Microsoft Authenticator, este artículo puede servirle de ayuda.  

## <a name="install-the-app"></a>Instalación de la aplicación
La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Incorporación de cuentas a la aplicación
Siga uno de los procedimientos siguientes para cada cuenta que desee agregar a la aplicación Microsoft Authenticator.

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Incorporación de una cuenta Microsoft personal a la aplicación

Para una cuenta Microsoft personal (una que use para iniciar sesión en Outlook.com, Xbox, Skype, etc.), lo único que debe hacer es iniciar sesión en su cuenta en la aplicación Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Incorporación de una cuenta profesional o educativa a la aplicación con el escáner de códigos QR
1. Vaya a la pantalla de configuración de comprobación de seguridad.  Para información sobre cómo ir a esta pantalla, consulte la sección sobre el [cambio de la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Active la casilla junto a **aplicación Azure Authenticator** y , a continuación, seleccione **Configurar**.

    ![Botón Configurar en la pantalla de configuración de la comprobación de seguridad](./media/authenticator-app-how-to/azureauthe.png)

    Aparecerá una pantalla con un código QR.

    ![Pantalla que proporciona el código QR](./media/authenticator-app-how-to/barcode2.png)
3. Abra la aplicación Microsoft Authenticator. En la pantalla **Cuentas**, seleccione **+** y especifique que quiere agregar una cuenta profesional o educativa.
4. Utilice la cámara para digitalizar el código QR y seleccione **Listo** para cerrar la pantalla de código QR.

    Si la cámara no funciona correctamente, puede [escribir manualmente la dirección URL y el código QR](#add-an-account-to-the-app-manually).

5. Cuando la aplicación muestra el nombre de cuenta con un código de seis dígitos debajo de él, ya ha terminado. 

    ![Pantalla Cuentas](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Incorporación manual de una cuenta a la aplicación
1. Vaya a la pantalla de configuración de comprobación de seguridad.  Para información sobre cómo ir a esta pantalla, consulte la sección sobre el [cambio de la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md).
2. Seleccione **Configurar**.

    ![Botón Configurar en la pantalla de configuración de la comprobación de seguridad](./media/authenticator-app-how-to/azureauthe.png)

    Aparecerá una pantalla con un código QR.  Anote la dirección URL y el código.

    ![Pantalla que proporciona el código QR y la dirección URL](./media/authenticator-app-how-to/barcode2.png)
3. Abra la aplicación Microsoft Authenticator. En la pantalla **Cuentas**, seleccione **+** y especifique que quiere agregar una cuenta profesional o educativa.

4. En el digitalizador, elija **escribir el código manualmente**.

    ![Pantalla para digitalizar un código QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. En la aplicación, escriba el código y la dirección URL en los cuadros correspondientes y luego seleccione **Finalizar**.

    ![Pantalla para escribir el código y la dirección URL](./media/authenticator-app-how-to/manual.png)

6. Cuando la aplicación muestra el nombre de cuenta con un código de seis dígitos debajo de él, ya ha terminado.

    ![Pantalla Cuentas](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-touch-id"></a>Incorporación de una cuenta a la aplicación con Touch ID
La aplicación Microsoft Authenticator de iOS es compatible con Touch ID.  Azure Multi-Factor Authentication permite a las organizaciones pedir un PIN para los dispositivos. Con Touch ID, los usuarios de iOS no tienen por qué escribir un PIN. En su lugar, pueden digitalizar su huella y seleccionar **Aprobar**.

Configurar Touch ID con Microsoft Authenticator es sencillo. Un PIN supone un reto de comprobación normal. Si el dispositivo es compatible con Touch ID, Microsoft Authenticator lo configura automáticamente para esa cuenta.

![Comprobación de la configuración de Touch ID](./media/authenticator-app-how-to/touchid1.png)

A partir de ese momento, cuando se requiera que compruebe el inicio de sesión, seleccione la notificación push recibida y digitalice la huella dactilar en lugar de escribir su PIN.

![Notificación push](./media/authenticator-app-how-to/touchid2.png)


