---
title: "Usar la nueva autenticación del servicio de Administrador de dispositivos StorSimple 8000 en Azure | Documentos de Microsoft"
description: "Aquí se explica cómo usar la autenticación basada en AAD del servicio, cómo generar una nueva clave de registro y cómo realizar el registro manual de los dispositivos."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 37f44538d94ed78509bbcb09e726dc34a9e92e95
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Usar la nueva autenticación de StorSimple

## <a name="overview"></a>Información general

El servicio StorSimple Device Manager se ejecuta en Microsoft Azure y se conecta a varios dispositivos de StorSimple. Hasta la fecha, el servicio del Administrador de dispositivos de StorSimple siempre ha utilizado Access Control Service (ACS) para autenticar el servicio en el dispositivo StorSimple. El mecanismo de ACS caerá pronto en desuso y se reemplazará con la autenticación de Azure Active Directory (AAD). Para obtener más información, vaya a los siguientes anuncios que indican el desuso de ACS y el uso de la autenticación de AAD.

- [El futuro de Azure ACS es Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Próximos cambios en Microsoft Access Control Service](https://azure.microsoft.com/en-in/blog/acs-access-control-service-namespace-creation-restriction/)

En este artículo se describen los detalles de la autenticación de AAD, la nueva clave de registro del servicio asociada y las modificaciones realizadas en las reglas de firewall que se aplican a los dispositivos de StorSimple. La información que se presenta en este artículo solo se aplica a los dispositivos de la serie StorSimple 8000.

Se realiza la autenticación de AAD en aquellos dispositivos de la serie StorSimple 8000 que ejecutan la actualización Update 5 o posterior. Debido a la incorporación de la autenticación de AAD, se producen cambios en:

- Los patrones de direcciones URL para reglas de firewall.
- La clave de registro del servicio.

En las siguientes secciones se detalla cada uno de estos cambios.

## <a name="url-changes-for-aad-authentication"></a>Cambios en la dirección URL para la autenticación de AAD

Para garantizar que el servicio utiliza la autenticación basada en AAD, todos los usuarios deben incluir las nuevas direcciones URL de autenticación en sus reglas de firewall.

Si utiliza la serie StorSimple 8000, asegúrese de que la siguiente dirección URL se incluye en las reglas de firewall:

| Patrón de URL                         | Nube | Componente o funcionalidad         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |Servicio de autenticación de AAD      |
| `https://login.microsoftonline.us` | Gobierno de Estados Unidos |Servicio de autenticación de AAD      |

Para obtener una lista completa de patrones de direcciones URL para dispositivos de la serie StorSimple 8000, vaya a [Patrones de URL para reglas de firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Si la dirección URL de autenticación no se incluye en las reglas de firewall más allá de la fecha de desuso, y el dispositivo ejecuta la actualización Update 5, los usuarios verán una alerta de dirección URL. Es entonces cuando deben incluir la nueva dirección URL de autenticación. Si el dispositivo está ejecutando una versión anterior a la actualización Update 5, los usuarios verán una alerta de latido. En cada caso, el dispositivo de StorSimple no se puede autenticar con el servicio, y el servicio no será capaz de comunicarse con el dispositivo.

## <a name="device-version-and-authentication-changes"></a>Cambios de versión y autenticación del dispositivo

Si utiliza un dispositivo de la serie StorSimple 8000, use la siguiente tabla para determinar qué acción debe realizar en función de la versión de software de dispositivo que esté ejecutando.

| Si el dispositivo ejecuta| Siga estos pasos                                    |
|--------------------------|------------------------|--------------------|--------------------------------------------------------------|
| La actualización Update 5 (o posterior) y el dispositivo está sin conexión. <br> Se muestra una alerta que indica que la dirección URL no está incluida en la lista blanca.| Modifique las reglas de firewall para que incluyan la dirección URL de autenticación.<br> Consulte [las direcciones URL de autenticación](#url-changes-for-aad-authentication). |
| La actualización Update 5 (o posterior) y el dispositivo está en línea.| no se requiere ninguna acción.                                       |
| La actualización Update 4 (o anterior) y el dispositivo está sin conexión. | Modifique las reglas de firewall para que incluyan la dirección URL de autenticación.<br>[Descargue la actualización Update 5 a través del servidor de catálogo](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>[Aplique la actualización Update 5 a través del método de revisión](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix). <br> [Obtenga la clave de registro de AAD a través del servicio](#aad-based-registration-keys). <br> [Conéctese a la interfaz de Windows PowerShell del dispositivo de la serie StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>Use el cmdlet `Redo-DeviceRegistration` para registrar el dispositivo a través de Windows PowerShell. Proporcione la clave que obtuvo en el paso anterior.|
| La actualización Update 4 (o anterior) y el dispositivo está en línea. |Modifique las reglas de firewall para que incluyan la dirección URL de autenticación.<br> Instale la actualización Update 5 a través de Azure Portal.              |
| El restablecimiento de fábrica a una versión anterior a la actualización Update 5.      |El portal muestra una clave de registro basada en AAD mientras el dispositivo está ejecutando software anterior. Siga los pasos descritos en el escenario anterior, en el cual el dispositivo ejecuta la actualización Update 4 o versiones anteriores.              |

## <a name="aad-based-registration-keys"></a>Claves del registro basadas en AAD

Al iniciar la actualización Update 5 de los dispositivos de la serie StorSimple 8000, se usan las nuevas claves de registro basadas en AAD. Debe usar las claves de registro para registrar el servicio de Administrador de dispositivos de StorSimple con el dispositivo.

Tenga en cuenta que no puede utilizar las nuevas claves de registro del servicio de AAD si usa un dispositivo de la serie StorSimple 8000 que ejecute Update 4 o una versión anterior (se incluyen los dispositivos más antiguos que se vayan a activar).
En este escenario debe regenerar la clave de registro del servicio. Cuando se regenera la clave, la nueva clave solo se utiliza para registrar todos los dispositivos posteriores. La clave anterior ya no es válida.

- La nueva clave de registro de AAD expira pasados 3 días.
- Las claves de registro de AAD solo funcionan con dispositivos de la serie StorSimple 8000 que ejecuten la actualización Update 5 o versiones posteriores.
- Las claves de registro de AAD son más largas que las de las claves de registro de ACS.

Realice los pasos siguientes para volver a crear una clave de registro de servicio de AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Generar la clave de registro del servicio de ADD

1. En el **Administrador de dispositivos de StorSimple**, vaya a **Administración &gt;** **Claves**. También puede utilizar la barra de búsqueda para buscar las _claves_.
    
2. Haga clic en **Generar clave**.

    ![Haga clic en Regenerar](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie la clave nueva. La clave anterior dejará de funcionar.

    ![Confirmar regeneración](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Si va a crear StorSimple Cloud Appliance en el servicio que se registró en el dispositivo de la serie StorSimple 8000, no genere ninguna clave de registro mientras esté en curso el proceso de creación. Espere a que se complete el proceso de creación y, a continuación, genere la clave de registro.

## <a name="next-steps"></a>pasos siguientes

* Obtenga más información sobre cómo implementar [dispositivos de la serie StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md).

