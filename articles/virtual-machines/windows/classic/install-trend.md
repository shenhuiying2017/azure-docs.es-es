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
ms.date: 03/30/2017
ms.author: iainfou
ms.openlocfilehash: 41b7ecf0d0c71b5c225454cc77ce87d5736c2165
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure
> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

En este artículo se muestra cómo instalar y configurar Trend Micro Deep Security como servicio en una máquina virtual nueva o existente con Windows Server. Deep Security como servicio proporciona protección antimalware, firewall, sistema de prevención contra intrusiones y supervisión de integridad.

El cliente se instala como una extensión de seguridad usando el Agente de máquina virtual. En una nueva máquina virtual, instale el agente de Deep Security, ya que Azure Portal crea automáticamente el agente de máquina virtual.

Es posible que una máquina virtual existente que se creó mediante Azure Portal, la CLI de Azure o PowerShell no tenga un agente de máquina virtual. En una máquina virtual que no tenga el agente de máquina virtual, primero debe descargarlo e instalarlo. Este artículo trata ambas situaciones.

Si tiene una suscripción actual de Trend Micro para una solución local, puede usarla para ayudar a proteger sus máquinas virtuales de Azure. Si todavía no es cliente, puede suscribirse para una prueba. Para obtener más información acerca de esta solución, consulte la publicación del blog de Trend Micro [Microsoft Azure VM Agent Extension For Deep Security (Extensión del agente de máquina virtual de Microsoft Azure para Deep Security)](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalación del Agente de Deep Security en una máquina virtual nueva

[Azure Portal](http://portal.azure.com) le permite instalar la extensión de seguridad de Trend Micro cuando usa una imagen de **Marketplace** para crear la máquina virtual. Si crea una sola máquina virtual, usar el portal es una forma simple para agregar la protección de Trend Micro.

Mediante una entrada de **Marketplace** se abre un asistente que le ayuda a configurar la máquina virtual. Utilice la hoja **Configuración**, el tercer panel del asistente, para instalar la extensión de seguridad de Trend Micro.  Para obtener instrucciones generales, consulte [Creación de una máquina virtual que ejecuta Windows en Azure Portal](tutorial.md).

Cuando se encuentre en la hoja **Configuración** del asistente, realice los pasos siguientes:

1. Haga clic en **Extensiones**, a continuación, haga clic en **Agregar extensión** en el panel siguiente.

   ![Empezar a agregar la extensión][1]

2. Seleccione **Agente de Deep Security** en el panel **Nuevo recurso**. En el panel de Agente de Deep Security, haga clic en **Crear**.

   ![Identificar agente de Deep Security][2]

3. Escriba el **identificador del inquilino** y la **contraseña de activación del inquilino** para la extensión. Opcionalmente, puede especificar un **identificador de directiva de seguridad**. A continuación, haga clic en **Aceptar** para agregar el cliente.

   ![Proporcionar detalles de la extensión][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalación del Agente de Deep Security en una máquina virtual existente
Para instalar el agente en una máquina virtual existente, necesitará los siguientes elementos:

* El módulo Azure PowerShell, con una versión 0.8.2 o posterior, instalado en el equipo local. Puede comprobar la versión de Azure PowerShell que ha instalado con el comando **Get-Module azure | format-table version** . Para obtener instrucciones y un vínculo a la versión más reciente, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview). Inicie sesión en la suscripción de Azure mediante `Add-AzureAccount`.
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

<!-- Image references -->
[1]: ./media/install-trend/new_vm_Blade3.png
[2]: ./media/install-trend/find_SecurityAgent.png
[3]: ./media/install-trend/SecurityAgentDetails.png

<!-- Link references -->
[Inicio de sesión en una máquina virtual con Windows Server]:connect-logon.md
[Características y extensiones de máquina virtual de Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
