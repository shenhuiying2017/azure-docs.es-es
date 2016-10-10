<properties
   pageTitle="Restablecer una Puerta de enlace de VPN de Azure | Microsoft Azure"
   description="Este artículo te guía a través del restablecimiento de la Puerta de enlace de VPN de Azure. El artículo se aplica a puertas de enlace VPN en y los modelos de implementación clásico y de Resource Manager."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# Restablecer una Puerta de enlace de VPN de Azure con PowerShell


Este artículo lo guiará por el restablecimiento de Puerta de enlace de VPN de Azure mediante cmdlets de PowerShell. Estas instrucciones incluyen el modelo de implementación clásico y el modelo de implementación de Resource Manager.

El restablecer la Puerta de enlace de VPN de Azure es útil si se pierde la conectividad VPN entre locales en uno o varios túneles VPN de S2S. En esta situación, todos tus dispositivos VPN locales funcionan correctamente, pero no pueden establecer túneles IPsec con las Puertas de enlace de VPN de Azure.

Todas las Puertas de enlace de VPN de Azure se componen de dos instancias de VM que se ejecutan en una configuración activa o en modo de espera. Cuando usa el cmdlet de PowerShell para reiniciar la puerta de enlace, este reinicia la puerta de enlace y después vuelve a aplicar las configuraciones entre locales. La puerta de enlace conserva la dirección IP pública que ya tiene. Esto significa que no tendrás que actualizar la configuración del enrutador VPN con una nueva dirección IP pública de Puerta de enlace de VPN de Azure.

Una vez que se emite el comando, se reiniciará inmediatamente la instancia activa actual de Azure VPN Gateway. Habrá un breve intervalo durante la conmutación por error de la instancia activa (que se está reiniciando) a la instancia en modo de espera. El intervalo debe ser inferior a un minuto.

Si la conexión no se restaura después del primer reinicio, vuelve a ejecutar el mismo comando para reiniciar la segunda instancia de VM (la nueva puerta de enlace activa). Si se solicitan los dos reinicios consecutivamente, habrá un período un poco más largo durante el que se estén reiniciando ambas instancias de máquina virtual (activa y en espera). Esto causará una interrupción mayor en la conectividad de VPN, de 2 a 4 minutos, para que las máquinas virtuales completen los reinicios.

Después de dos reinicios, si sigue teniendo problemas de conectividad entre locales, abra una incidencia de soporte técnico en Azure Portal.

## Antes de empezar

Antes de restablecer la puerta de enlace, compruebe los elementos clave que se enumeran a continuación para todos los túneles VPN de sitio a sitio (S2S) de IPsec. Cualquier incoherencia que haya en los elementos provocará la desconexión de los túneles VPN de S2S. Comprobar y corregir la configuración de la puerta de enlace local y de Azure VPN Gateway le evitará reinicios e interrupciones innecesarios para las demás conexiones en funcionamiento de las puertas de enlace.

Compruebe los elementos siguientes antes de restablecer la puerta de enlace:

- Las direcciones IP de Internet (VIP) tanto de la puerta de enlace VPN de Azure como de la puerta de enlace VPN local están configuradas correctamente en Azure así como en las directivas VPN locales.
- La clave previamente compartida tiene que ser la misma en las Puertas de enlace VPN de Azure y en las locales.
- Si aplica la configuración específica de IPsec/IKE, como el cifrado, los algoritmos hash y PFS (confidencialidad directa total), asegúrese de que tanto las puertas de enlace de VPN de Azure como las locales tengan la misma configuración.

## Restablecimiento de una instancia de VPN Gateway usando el modelo de implementación de Resource Manager

El cmdlet de PowerShell Resource Manager para restablecer la puerta de enlace es `Reset-AzureRmVirtualNetworkGateway`. En el ejemplo siguiente se restablece la instancia de Azure VPN Gateway, "VNet1GW", en el grupo de recursos "TestRG1".

	$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
	Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## Restablecimiento de una instancia de VPN Gateway usando el modelo de implementación clásico

El cmdlet de PowerShell para restablecer Azure VPN Gateway es `Reset-AzureVNetGateway`. En el ejemplo siguiente, se restablece la puerta de enlace de VPN de Azure para la red virtual denominada "ContosoVNet".
 
	Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Resultado:

	Error          :
	HttpStatusCode : OK
	Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
	Status         : Successful
	RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
	StatusCode     : OK


## Pasos siguientes
	
Consulte la [referencia de cmdlets de la administración de servicios de PowerShell ](https://msdn.microsoft.com/library/azure/mt617104.aspx) y la [referencia de cmdlets de PowerShell Resource Manager](http://go.microsoft.com/fwlink/?LinkId=828732) para más información.

<!---HONumber=AcomDC_0928_2016-->