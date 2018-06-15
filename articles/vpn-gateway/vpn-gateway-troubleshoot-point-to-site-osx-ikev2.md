---
title: Solución de problemas de conexiones VPN de punto a sitio de Azure desde clientes de Mac OS X | Microsoft Docs
description: Pasos para solucionar problemas de conexiones de cliente de VPN de P2S de Mac OS X
services: vpn-gateway
documentationcenter: na
author: anzaman
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 1cf8195cbf65f27c71a4db18c0c61c8a25673acd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
ms.locfileid: "30247714"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Solución de problemas de conexiones VPN de punto a sitio desde clientes de VPN de Mac OS X

Este artículo le ayuda a solucionar problemas de conectividad de punto a sitio de Mac OS X con el cliente VPN nativo e IKEv2. El cliente VPN de Mac para IKEv2 es muy básico y no permite muchas personalizaciones. Hay solo cuatro opciones de configuración que deben comprobarse:

* Dirección del servidor
* Id. remoto
* Id. local
* Configuración de autenticación
* Versión del SO (10.11 o posterior)


## <a name="VPNClient"></a> Solución de problemas de autenticación basada en certificados
1. Compruebe la configuración del cliente de VPN. Vaya a la **Configuración de red** presionando Comando + Mayús y, a continuación, escriba "VPN" para comprobar la configuración del cliente de VPN. En la lista, haga clic en la entrada VPN que debe investigarse.

  ![Autenticación basada en certificados IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Compruebe que la **dirección del servidor** es el FQDN completo e incluye cloudapp.net.
3. El **id. remoto** debe ser el mismo que la dirección del servidor (FQDN de puerta de enlace).
4. El **id. local** debe ser el mismo que el **asunto** del certificado de cliente.
5. Haga clic en **Configuración de autenticación** para abrir la página Configuración de autenticación.

  ![Configuración de autenticación](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Compruebe que la opción **Certificado** está seleccionada en la lista desplegable.
7. Haga clic en el botón **Seleccionar** y compruebe que se ha seleccionado el certificado correcto. Haga clic en **Aceptar** para guardar los cambios.

## <a name="ikev2"></a>Solución de problemas de autenticación de nombre de usuario y contraseña

1. Compruebe la configuración del cliente de VPN. Vaya a la **Configuración de red** presionando Comando + Mayús y, a continuación, escriba "VPN" para comprobar la configuración del cliente de VPN. En la lista, haga clic en la entrada VPN que debe investigarse.

  ![Contraseña del nombre de usuario de IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Compruebe que la **dirección del servidor** es el FQDN completo e incluye cloudapp.net.
3. El **id. remoto** debe ser el mismo que la dirección del servidor (FQDN de puerta de enlace).
4. El **id. local** puede estar en blanco.
5. Haga clic en el botón **Configuración de autenticación** y compruebe que se ha seleccionado "Nombre de usuario" en la lista desplegable.

  ![Configuración de autenticación](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Compruebe que se han introducido las credenciales correctas.

## <a name="additional"></a>Pasos adicionales

Si ha llevado a cabo los pasos anteriores y todo está configurado correctamente, descargue [Wireshark](https://www.wireshark.org/#download) y realice una captura de paquetes.

1. Filtre por *iskmp* y examine los paquetes **IKE_SA**. Podrá consultar los detalles de la propuesta de asociación de seguridad en **Payload: Security Association** (Carga: asociación de seguridad). 
2. Compruebe que el cliente y el servidor disponen de un conjunto común.

  ![paquete](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg)

## <a name="next-steps"></a>Pasos siguientes
Para obtener más ayuda, consulte el [Soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
