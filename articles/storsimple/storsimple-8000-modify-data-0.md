---
title: "Modificación de la configuración de DATA 0 en dispositivos de la serie StorSimple 8000 | Microsoft Docs"
description: "Obtenga información acerca de cómo usar Windows PowerShell para StorSimple para volver a configurar la interfaz de red DATA 0 en el dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 90df43e22f17fd32fe642514df098b72700e77af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Modificación de la configuración de la interfaz de red DATA 0 en dispositivos de la serie StorSimple 8000

## <a name="overview"></a>Información general

El dispositivo Microsoft Azure StorSimple tiene seis interfaces de red: de DATA de 0 a DATA 5. La interfaz DATA 0 siempre se configura a través de la interfaz de Windows PowerShell o la consola de serie y se habilita para la nube automáticamente. Tenga en cuenta que la interfaz de red DATA 0 no se puede configurar mediante Azure Portal.

La interfaz DATA 0 se configura por primera vez a través del asistente para la instalación durante la implementación inicial del dispositivo StorSimple. Cuando el dispositivo está en un modo de funcionamiento, tal vez tenga que volver a establecer la configuración de DATA 0. En este tutorial se proporcionan dos métodos para modificar la configuración de red de DATA 0, ambos a través de Windows PowerShell para StorSimple.

Después de leer este tutorial, podrá:

* Modificar la configuración de DATA 0 mediante el asistente para la instalación
* Modificar la configuración de red de DATA 0 mediante el cmdlet `Set-HcsNetInterface`

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modificar la configuración de red de DATA 0 mediante el asistente para la instalación
Puede volver a establecer la configuración de red de DATA 0 conectándose a la interfaz de Windows PowerShell del dispositivo StorSimple e iniciando una sesión del asistente para la instalación. Siga estos pasos para modificar la configuración de DATA 0.

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Para modificar la configuración de red de DATA 0 mediante el asistente para la instalación
1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**. Cuando se le solicite, proporcione la **contraseña de administrador del dispositivo**. La contraseña predeterminada es `Password1`.
2. En el símbolo del sistema, escriba:
   
    `Invoke-HcsSetupWizard`
3. Aparece un asistente para instalación que le ayudará a configurar la interfaz DATA 0 del dispositivo. Proporcione nuevos valores para la dirección IP, la pasarela y la máscara de red.

> [!NOTE]
> Las direcciones IP de los controladores fijos tendrán que reconfigurarse desde la hoja **Configuración de red** del dispositivo StorSimple en Azure Portal. Para obtener más información, vaya a [Modificar las interfaces de red](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modificación de la configuración de red de DATA 0 mediante el cmdlet Set-HcsNetInterface
Una alternativa para volver a configurar la interfaz de red DATA 0 es mediante el cmdlet `Set-HcsNetInterface` . El cmdlet se ejecuta desde la interfaz de Windows PowerShell del dispositivo StorSimple. Cuando se utiliza este procedimiento, también se puede configurar aquí el controlador de direcciones IP fijas. Siga estos pasos para modificar la configuración de DATA 0: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Para modificar la configuración de la red DATA 0 mediante el cmdlet Set-HcsNetInterface
1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**. Cuando se le solicite, proporcione la contraseña del administrador de dispositivo. La contraseña predeterminada es `Password1`.
2. En el símbolo del sistema, escriba:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    En los corchetes angulares, escriba los siguientes valores para DATA 0:
   
   * Dirección IPv4
   * Puerta de enlace IPv4
   * Máscara de subred IPv4
   * Dirección IPv4 fija para el controlador 0
   * Dirección IPv4 fija para el controlador 1
     
     Para más información sobre cómo usar este cmdlet, vaya a la [referencia de cmdlet de Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Pasos siguientes
* Para configurar interfaces de red que no sean DATA 0, consulte el artículo acerca de la [configuración de opciones de red en Azure Portal](storsimple-8000-modify-device-config.md). 
* Si experimenta problemas al configurar las interfaces de red, consulte [Solucionar problemas de implementación](storsimple-troubleshoot-deployment.md).

