<properties
	pageTitle="Inicio de sesión en una máquina virtual de Windows Server | Microsoft Azure"
	description="Obtenga información acerca de cómo iniciar sesión en una máquina virtual de Windows Server con el portal de vista previa de Azure y el modelo de implementación del Administrador de recursos."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Inicio de sesión en una máquina virtual con Windows Server 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-log-on-windows-server.md).

Usará el botón **Conectar** en el Portal de vista previa de Azure para iniciar una sesión de Escritorio remoto. En primer lugar, se conectará a la máquina virtual y, a continuación, iniciará sesión.

## Conexión a la máquina virtual

1. Si no lo ha hecho todavía, inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com/).

2.	En el menú Concentrador, haga clic en **Examinar**.

3.	En la hoja de búsqueda, desplácese hacia abajo y haga clic en **Máquinas virtuales**.

	![Búsqueda de máquinas virtuales](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.	Seleccione la máquina virtual en la lista.

5. En la hoja de la máquina virtual, haga clic en **Conectar**.

	![Conexión a la máquina virtual](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## Iniciar sesión en la nueva máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Solución de problemas

Si las sugerencias sobre el inicio de sesión no ayudan o no son lo que necesita, consulte [Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). En este artículo se le guiará a través del diagnóstico y la resolución de problemas comunes.

<!---HONumber=Oct15_HO3-->