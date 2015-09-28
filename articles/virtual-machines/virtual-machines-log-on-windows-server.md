<properties
	pageTitle="Inicio de sesión en una máquina virtual que ejecuta Windows Server"
	description="Obtenga información acerca de cómo usar el portal de Azure para iniciar sesión en una máquina virtual con Windows Server."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>


# Inicio de sesión en una máquina virtual con Windows Server#

Usará el botón **Conectar** en el Portal de vista previa de Azure para iniciar una sesión de Escritorio remoto. En primer lugar, se conectará a la máquina virtual y, a continuación, iniciará sesión.

¿Desea conectarse a una VM Linux? Consulte [Inicio de sesión en una máquina virtual con Linux](virtual-machines-linux-how-to-log-on.md).

## Conexión a la máquina virtual

A continuación se facilita una guía detallada de los pasos de este tutorial.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]

1. Si no lo ha hecho todavía, inicie sesión en el [Portal de Azure](http://manage.windowsazure.com).

2. Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual correspondiente.

3. En la barra de comandos, haga clic en **Conectar**.

	![Iniciar sesión en la nueva máquina virtual](./media/virtual-machines-log-on-windows-server/connectwindows.png)

## Iniciar sesión en la nueva máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Sugerencias de solución de problemas

Existen algunas acciones que se pueden intentar rápidamente en el portal:

-	Si experimenta problemas con la conexión a Escritorio remoto, intente restablecer la configuración. Desde el panel de la máquina virtual, en **Vista rápida**, haga clic en **Restablecer configuración remota**.
-	Si experimenta problemas con la contraseña, intente restablecerla. Desde el panel de la máquina virtual, en **Vista rápida**, haga clic en **Restablecer contraseña**.

Si esas sugerencias no funcionan o no son lo que necesita, vea [Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). En este artículo se le guiará a través del diagnóstico y la resolución de problemas comunes.

<!---HONumber=Sept15_HO3-->