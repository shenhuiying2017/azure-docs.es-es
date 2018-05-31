---
title: Renovar un certificado de Azure Application Gateway
description: Obtenga información sobre cómo renovar un certificado asociado con un agente de escucha de Application Gateway.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/17/2018
ms.author: victorh
ms.openlocfilehash: b125f707e8de17764701e981736a53492e5e756c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356960"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados de Application Gateway

En algún momento, tendrá que renovar los certificados si ha configurado Application Gateway para el cifrado SSL.

Puede renovar un certificado asociado con un agente de escucha a través de Azure Portal o Azure PowerShell:

## <a name="azure-portal"></a>Azure Portal

Para renovar un certificado de agente de escucha desde el portal, vaya a los agentes de escucha de Application Gateway. Haga clic en el agente de escucha que tiene un certificado que debe renovarse y, a continuación, haga clic en **Renew or edit selected certificate** (Renovar o editar el certificado seleccionado).

![Renovar un certificado](media/renew-certificate/ssl-cert.png)

Cargue el nuevo certificado PFX, asígnele un nombre, escriba la contraseña y, a continuación, haga clic en **Guardar**.

## <a name="azure-powershell"></a>Azure PowerShell

Para renovar el certificado con Azure PowerShell, use el siguiente cmdlet:

```PowerShell
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```

## <a name="next-steps"></a>Pasos siguientes

Para aprender a configurar la descarga de SSL con Azure Application Gateway, consulte [Configuración de la descarga SSL](application-gateway-ssl-portal.md)
