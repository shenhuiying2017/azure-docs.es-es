---
title: "Nueva autenticación de las matrices virtuales de StorSimple | Microsoft Docs"
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
ms.openlocfilehash: 8d033cc09de8e115324067d7bbdf052751730d63
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Uso de la nueva autenticación de StorSimple

## <a name="overview"></a>Información general

El servicio StorSimple Device Manager se ejecuta en Microsoft Azure y se conecta a varias instancias de StorSimple Virtual Arrays. Hasta la fecha, el servicio del Administrador de dispositivos de StorSimple siempre ha utilizado Access Control Service (ACS) para autenticar el servicio en el dispositivo StorSimple. El mecanismo de ACS caerá pronto en desuso y se reemplazará con la autenticación de Azure Active Directory (AAD).

La información contenida en este artículo se aplica solo a ambas matrices virtuales de StorSimple de la serie 1200. En este artículo se describen los detalles de la autenticación de AAD, la nueva clave de registro del servicio asociada y las modificaciones realizadas en las reglas de firewall que se aplican a los dispositivos de StorSimple.

Se realiza la autenticación de AAD en las matrices virtuales de StorSimple (modelo 1200) que ejecutan la actualización Update 1 o posterior.

Debido a la incorporación de la autenticación de AAD, se producen cambios en:

- Los patrones de direcciones URL para reglas de firewall.
- La clave de registro del servicio.

En las siguientes secciones se detalla cada uno de estos cambios.

## <a name="url-changes-for-aad-authentication"></a>Cambios en la dirección URL para la autenticación de AAD

Para garantizar que el servicio utiliza la autenticación basada en AAD, todos los usuarios deben incluir las nuevas direcciones URL de autenticación en sus reglas de firewall.

Si utiliza la matriz virtual de StorSimple, asegúrese de que la siguiente dirección URL se incluye en las reglas de firewall:

| Patrón de URL                         | Nube | Componente o funcionalidad         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |Servicio de autenticación de AAD      |
| `https://login.microsoftonline.us` | Gobierno de Estados Unidos |Servicio de autenticación de AAD      |

Para obtener una lista completa de patrones de direcciones URL para matrices virtuales de StorSimple, vaya a [Patrones de URL para reglas de firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Si la dirección URL de autenticación no se incluye en las reglas de firewall después de la fecha de desuso, los usuarios verán una alerta crítica sobre que su dispositivo de StorSimple no se pudo autenticar con el servicio. El servicio no podrá comunicarse con el dispositivo. Si los usuarios ven esta alerta, deben incluir la nueva dirección URL de autenticación. Para obtener más información sobre la alerta, vaya a [Uso de alertas para supervisar el dispositivo de StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Cambios de versión y autenticación del dispositivo

Si utiliza una matriz virtual de StorSimple, use la siguiente tabla para determinar qué acción debe realizar en función de la versión de software de dispositivo que se ejecuta.

| Si el dispositivo ejecuta  | Siga estos pasos                                    |
|----------------------------|--------------------------------------------------------------|
| La actualización Update 1.0 o posterior y está sin conexión. <br> Se muestra una alerta que indica que la dirección URL no está incluida en la lista blanca.| Modifique las reglas de firewall para que incluyan la dirección URL de autenticación. Consulte [las direcciones URL de autenticación](#url-changes-for-aad-authentication). |
| La actualización Update 1.0 o posterior y el dispositivo está en línea.| no se requiere ninguna acción.                                       |
| La actualización Update 0.6 o anterior y el dispositivo está sin conexión. | [Descargue la actualización Update 1.0 del servidor de catálogo](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>[Aplique la actualización Update 1.0 mediante la interfaz de usuario web local](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix). <br> [Obtenga la clave de registro de AAD del servicio](#aad-based-registration-keys). <br> Realice los pasos 1 a 5 para [conectarse a la interfaz de Windows PowerShell de la matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> Use el cmdlet `Invoke-HcsReRegister` para registrar el dispositivo mediante Windows PowerShell. Proporcione la clave que obtuvo en el paso anterior.|
| La actualización Update 0.6 o anterior y el dispositivo está en línea. | Modifique las reglas de firewall para que incluyan la dirección URL de autenticación.<br> Instale la actualización Update 1.0 mediante Azure Portal. |

## <a name="aad-based-registration-keys"></a>Claves del registro basadas en AAD

Al iniciar la actualización Update 1.0 de las matrices virtuales de StorSimple, se usan las nuevas claves de registro basadas en AAD. Debe usar las claves de registro para registrar el servicio de Administrador de dispositivos de StorSimple con el dispositivo.

No puede utilizar las nuevas claves de registro del servicio AAD si usa una matriz virtual de StorSimple que ejecuta la actualización Update 0.6 o anterior. Debe regenerar la clave de registro del servicio. Una vez regenerada la clave, la nueva clave se utiliza para registrar todos los dispositivos posteriores. La clave anterior ya no es válida.

- La nueva clave de registro de AAD expira pasados tres días.
- Las claves de registro de AAD solo funcionan con matrices virtuales de StorSimple de la serie 1200 que ejecuten la actualización Update 1 o versiones posteriores. La clave de registro de AAD de un dispositivo de StorSimple de la serie 8000 no funcionará.
- Las claves de registro de AAD son más largas que las de ACS.

Realice los pasos siguientes para generar una clave de registro del servicio AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para generar la clave de registro del servicio de ADD

1. En el **Administrador de dispositivos de StorSimple**, vaya a **Administración &gt;** **Claves**.
    
    ![Ir a las claves](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Haga clic en **Generar clave**.

    ![Haga clic en Regenerar](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie la clave nueva. La clave anterior deja de funcionar.

    ![Confirmar regeneración](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>pasos siguientes

* Obtenga información sobre cómo implementar la [matriz virtual de StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
