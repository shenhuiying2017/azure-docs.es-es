<properties 
	pageTitle="Inicio de sesión en una máquina virtual que ejecuta Windows Server" 
	description="Aprenda a utilizar el Portal de administración de Azure para iniciar sesión en una máquina virtual que ejecuta Windows Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="kathydav"/>


# Inicio de sesión en una máquina virtual con Windows Server#

Usará el botón **Conectar** en el Portal de Azure para iniciar una sesión de escritorio remoto. (Para máquinas virtuales Linux, consulte [Inicio de sesión en una máquina virtual con Linux](virtual-machines-linux-how-to-log-on.md).)

## Cómo iniciar sesión

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Sugerencias de solución de problemas

Existen algunas acciones que se pueden intentar rápidamente:

Si experimenta problemas con la conexión a Escritorio remoto, intente restablecer la configuración desde el portal. Desde el panel de la máquina virtual, en **Vista rápida**, haga clic en **Restablecer configuración remota**.

Si experimenta problemas con la contraseña, intente restablecerla desde el portal. Desde el panel de la máquina virtual, en **Vista rápida**, haga clic en **Restablecer contraseña**.

Si esto no funciona, deberá buscar una solución de los problemas más amplia. Para ver más información, consulte [Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).
 
 

<!---HONumber=July15_HO4-->