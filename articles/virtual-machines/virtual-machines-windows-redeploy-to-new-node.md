<properties 
	pageTitle="Nueva implementación de máquinas virtuales Windows | Microsoft Azure" 
	description="Describe cómo volver a implementar máquinas virtuales Windows para solucionar problemas de conexión RDP." 
	services="virtual-machines-windows" 
	documentationCenter="virtual-machines" 
	authors="iainfoulds" 
	manager="timlt"
	tags="azure-resource-manager,top-support-issue" 
/>
	

<tags 
	ms.service="virtual-machines-windows" 
	ms.devlang="na" 
	ms.topic="support-article" 
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure" 
	ms.date="09/19/2016" 
	ms.author="iainfou" 
/>


# Nueva implementación de la máquina virtual en un nuevo nodo de Azure

Si ha tenido dificultades para solucionar problemas con la conexión a Escritorio remoto (RDP) o el acceso de aplicaciones a una maquina virtual de Azure basada en Windows, puede servir de ayuda que repita la implementación de la máquina virtual. Cuando se vuelve a implementar una máquina virtual, se mueve a otro nodo dentro de la infraestructura de Azure y después se vuelve a conectar, conservando todas las opciones de configuración y los recursos asociados. En este artículo se muestra cómo volver a implementar una máquina virtual con Azure PowerShell o el Portal de Azure.

> [AZURE.NOTE] Después de volver a implementar una máquina virtual, se perderá el disco temporal y se actualizarán las direcciones IP dinámicas asociadas con la interfaz de red virtual.

## Uso de Azure PowerShell

Asegúrese de que tiene la versión más reciente de Azure PowerShell 1.x instalada en su equipo. Para más información, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Use este comando de Azure PowerShell para volver a implementar la máquina virtual:

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## Pasos siguientes
Puede encontrar ayuda específica sobre la [solución de problemas de las conexiones RDP](virtual-machines-windows-troubleshoot-rdp-connection.md) o [pasos detallados para solucionar problemas de RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md) si tiene problemas para conectarse a la máquina virtual. También puede leer sobre la [solución de problemas de aplicaciones](virtual-machines-windows-troubleshoot-app-connection.md) si no se puede acceder a una aplicación que se ejecuta en la máquina virtual.

<!---HONumber=AcomDC_0921_2016-->