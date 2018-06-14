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
ms.openlocfilehash: b2a96aad793d956b3ea3de06fba74bcf68e50786
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197137"
---
Puede usar un certificado de raíz generada mediante una solución empresarial (se recomienda) o puede generar un certificado autofirmado. Después de crear el certificado raíz, exporte los datos del certificado público (no la clave privada) en forma de archivo X.509 .cer codificado en Base 64 y cargue los datos del certificado público en Azure.

* **Certificado de empresa:** si usa una solución empresarial, puede utilizar la cadena de certificados existente. Obtenga el archivo .cer para el certificado raíz que desee usar.
* **Certificado raíz autofirmado:** si no usa una solución de certificados de empresa, será preciso que cree un certificado raíz autofirmado. Es importante que siga los pasos de uno de los artículos sobre certificado P2S descritos a continuación. En caso contrario, los certificados que cree no serán compatibles con las conexiones de P2S y los clientes reciben un error de conexión al intentar conectarse. Puede usar Azure PowerShell, MakeCert o bien OpenSSL. Los pasos descritos en los artículos generan un certificado compatible:

  * [Instrucciones para Windows 10 y PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): estas instrucciones requieren Windows 10 y PowerShell para generar certificados. Los certificados de cliente que se generan desde el certificado raíz pueden instalarse en cualquier cliente P2S compatible.
  * [Instrucciones para MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): use MakeCert para generar certificados si no tiene acceso a un equipo con Windows 10. MakeCert está en desuso, pero todavía puede usar MakeCert para generar certificados. Los certificados de cliente que se generan desde el certificado raíz pueden instalarse en cualquier cliente P2S compatible.
