---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9fa18b14b82376a25bb434acd770d340b1ef9262
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197143"
---
Si tiene problemas para conectarse, compruebe los siguientes elementos:

- Si ha exportado un certificado de cliente asegúrese que lo exporta como un archivo .pfx con el valor predeterminado "Incluir todos los certificados en la ruta de certificación (si es posible)". Cuando se realiza la exportación utilizando este valor, también se exporta la información del certificado raíz. Cuando se instala el certificado en el equipo cliente, el certificado raíz que se encuentra en el archivo .pfx, también se instala en el equipo cliente. El equipo cliente tiene que tener instalada la información del certificado raíz. Para comprobarlo abra **Administrar certificados de usuario** y vaya a **Entidades de certificación raíz de confianza\Certificados**. Verifique que aparece el certificado raíz. El certificado raíz debe estar presente para que funcione la autenticación.

- Si va a usar un certificado que se emitió con una solución de CA empresarial y está teniendo problemas de autenticación, compruebe el orden de autenticación en el certificado de cliente. Para comprobar el orden de la lista de autenticación, haga doble clic en el certificado de cliente y vaya a **Detalles > Uso mejorado de claves**. Asegúrese de que la lista muestre "Autenticación de cliente" como primer elemento. Si no es así, debe emitir un certificado de cliente basado en la plantilla Usuario que tenga Autenticación de cliente como primer elemento de la lista.

- Para información adicional de solución de problemas de P2S, consulte [Solución de problemas de conexiones P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).