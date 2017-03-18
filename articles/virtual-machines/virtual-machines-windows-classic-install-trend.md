---
title: "Instalación de Trend Micro Deep Security en una máquina virtual | Microsoft Docs"
description: "En este artículo se describe cómo instalar y configurar la seguridad de Trend Micro en una máquina virtual creada con el modelo de implementación clásica en Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 3b55595eb59fb085450eb0d82cc1e93ad1c57f77
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

En este artículo se muestra cómo instalar y configurar Trend Micro Deep Security como servicio en una máquina virtual nueva o existente con Windows Server. Deep Security como servicio proporciona protección antimalware, firewall, sistema de prevención contra intrusiones y supervisión de integridad.

El cliente se instala como una extensión de seguridad usando el Agente de máquina virtual. En una nueva máquina virtual, instale el agente de máquina virtual junto con el Agente de Deep Security. En una máquina virtual que no tenga el Agente de máquina virtual, primero debe descargarlo e instalarlo. Este artículo trata ambas situaciones.

Si tiene una suscripción existente de Trend Micro para una solución local, puede usarla para ayudar a proteger sus máquinas virtuales de Azure. Si todavía no es cliente, puede suscribirse para una prueba. Para obtener más información acerca de esta solución, consulte la publicación del blog de Trend Micro [Microsoft Azure VM Agent Extension For Deep Security (Extensión del agente de máquina virtual de Microsoft Azure para Deep Security)](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalación del Agente de Deep Security en una máquina virtual nueva
El [Portal de Azure clásico](http://manage.windowsazure.com) permite instalar el Agente de máquina virtual y la extensión de seguridad de Trend Micro cuando usa la opción **Desde la galería** para crear la máquina virtual. Si crea una sola máquina virtual, usar el portal es una forma simple para agregar la protección de Trend Micro.

La opción **Desde la galería** abre un asistente que le ayuda configurar la máquina virtual. Utilice la última página del asistente para instalar el Agente de máquina virtual y la extensión de seguridad de Trend Micro. Para obtener instrucciones generales, consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure clásico](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Cuando se encuentre en la última página del asistente, realice los pasos siguientes:

1. En **Agente de máquina virtual**, active la casilla **Install VM Agent** (Instalar Agente de VM).
2. En **Extensiones de seguridad**, active **Agente de Trend Micro Deep Security**.
   
   ![Install the VM Agent and the Deep Security Agent](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)
3. Haga clic en la marca de verificación para crear la máquina virtual.

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalación del Agente de Deep Security en una máquina virtual existente
Para instalar el agente en una máquina virtual existente, necesitará lo siguiente:

* El módulo Azure PowerShell, con una versión 0.8.2 o posterior, instalado en el equipo local. Puede comprobar la versión de Azure PowerShell que ha instalado con el comando **Get-Module azure | format-table version** . Para obtener instrucciones y un vínculo a la versión más reciente, consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs). Inicie sesión en la suscripción de Azure mediante `Add-AzureAccount`.
* El agente de máquina virtual instalado en la MV de destino.

En primer lugar, compruebe que el agente de máquina virtual ya está instalado. Introduzca el nombre de servicio de nube y el nombre de la máquina virtual y, a continuación, ejecute los siguientes comandos en un símbolo de sistema de Azure PowerShell con nivel de administrador. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < y >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Si no conoce el nombre del servicio en la nube y de la máquina virtual, ejecute **Get-AzureVM** para mostrar esa información para todas las máquinas virtuales de su suscripción actual.

Si el comando **write-host** devuelve **True**, el agente de máquina virtual está instalado. Si devuelve **False**, consulte las instrucciones y un vínculo a la descarga en la publicación del blog de Azure [VM Agent and Extensions - Part 2 (Agente de máquina virtual extensiones: parte 2)](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Si el agente de máquina virtual está instalado, ejecute estos comandos.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Pasos siguientes
El agente tarda unos minutos en empezar la ejecución cuando se instala. Después de ello, debe activar Deep Security en la máquina virtual de forma que pueda ser administrado por Deep Security Manager. Consulte los siguientes artículos para obtener instrucciones adicionales:

* Artículo de Trend sobre esta solución, [Instant-On Cloud Security for Microsoft Azure (Seguridad en la nube instantánea para Microsoft Azure)](http://go.microsoft.com/fwlink/?LinkId=404101)
* [Script de Windows PowerShell de ejemplo](http://go.microsoft.com/fwlink/?LinkId=404100) para configurar la máquina virtual
* [Instrucciones](http://go.microsoft.com/fwlink/?LinkId=404099) para el ejemplo

## <a name="additional-resources"></a>Recursos adicionales
[Inicio de sesión en una máquina virtual con Windows Server]

[Características y extensiones de máquina virtual de Azure]

<!--Link references-->
[Inicio de sesión en una máquina virtual con Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Características y extensiones de máquina virtual de Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

