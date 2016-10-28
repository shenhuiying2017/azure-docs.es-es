<properties
   pageTitle="Comprobación de una conexión de puerta de enlace | Microsoft Azure"
   description="En este artículo se muestra cómo comprobar una conexión de puerta de enlace mediante el modelo de implementación de Resource Manager."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/03/2016"
   ms.author="cherylmc"/>

# Comprobación de una conexión de puerta de enlace

Puede comprobar la conexión de la puerta de enlace de varias maneras diferentes. En este artículo se explica cómo comprobar el estado de una conexión de puerta de enlace de Resource Manager mediante el Portal de Azure y PowerShell.


## Antes de empezar

Si planea usar PowerShell, necesitará instalar la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar los cmdlets de PowerShell. Para obtener más información sobre los cmdlets de Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

1. Abre la consola de PowerShell y conéctate a tu cuenta.

		Login-AzureRmAccount

2. Compruebe las suscripciones para la cuenta.

		Get-AzureRmSubscription 

3. Especifique la suscripción que desea usar.

		Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## Comprobación de la conexión


[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## Pasos siguientes

- Puede agregar máquinas virtuales a las redes virtuales. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para ver los pasos.

<!---HONumber=AcomDC_0810_2016-->