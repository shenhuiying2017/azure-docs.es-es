---
title: "Supervisión de Azure y máquinas virtuales Windows | Microsoft Docs"
description: "Tutorial: Supervisar una máquina virtual Windows con Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 2bf6e3cf2f039a9c92617203a3d4cf2aac8901ce
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

---

# <a name="monitor-a-windows-virtual-machine-with-azure-powershell"></a>Supervisar una máquina virtual Windows con Azure PowerShell

La supervisión de Azure usa agentes para recopilar datos de arranque y de rendimiento de máquinas virtuales de Azure, almacenar estos datos en Azure Storage y permitir el acceso a ellos a través del portal, el módulo de Azure PowerShell y la CLI de Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Habilitar los diagnósticos de arranque en una máquina virtual
> * Ver los diagnósticos de arranque
> * Ver las métricas del host de la máquina virtual
> * Instalar la extensión de Diagnostics
> * Ver las métricas de la máquina virtual
> * Crear una alerta
> * Configurar la supervisión avanzada

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, vea [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

Para completar el ejemplo de este tutorial, debe tener una máquina virtual. Si es necesario, este [script de ejemplo](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) puede crear una. Al trabajar en el tutorial, reemplace el grupo de recursos, el nombre de la máquina virtual y la ubicación cuando proceda.

## <a name="view-boot-diagnostics"></a>Ver los diagnósticos de arranque

Cuando las máquinas virtuales Windows arrancan, el agente de diagnóstico de arranque captura una salida de pantalla que se puede usar para solucionar problemas. Esta funcionalidad está habilitada de forma predeterminada. Las capturas de pantalla se almacenan en una cuenta de Azure Storage, que también se crea de forma predeterminada. 

Puede obtener los datos de diagnóstico de arranque con el comando [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata). En el ejemplo siguiente, el diagnóstico de arranque se descarga en la raíz de la unidad *c:\*. 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Ver las métricas del host

Una máquina virtual Windows tiene una máquina virtual host dedicada en Azure con la que interactúa. Las métricas del host se recopilan automáticamente y se pueden ver en Azure Portal.

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Haga clic en **Métricas** en la hoja de la máquina virtual y seleccione cualquiera de las métricas del host en **Métricas disponibles** para ver el funcionamiento de la máquina virtual host.

    ![Ver las métricas del host](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalar la extensión de Diagnostics

Hay disponibles métricas básicas del host, pero para ver métricas más pormenorizadas específicas de la máquina virtual, es preciso instalar la extensión de Azure Diagnostics en la máquina virtual. La extensión de Azure Diagnostics permite recuperar más datos de supervisión y diagnóstico de la máquina virtual. Puede ver estas métricas de rendimiento y crear alertas basadas en el funcionamiento de la máquina virtual. La extensión de diagnósticos se instala a través de Azure Portal como se indica a continuación:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Haga clic en **Configuración de diagnóstico**. La lista muestra que los *diagnósticos de arranque* ya están habilitados desde la sección anterior. Haga clic en la casilla *Métricas básicas*.
3. Haga clic en el botón **Habilitar supervisión a nivel de invitado**.

    ![Ver métricas de diagnósticos](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Ver las métricas de la máquina virtual

Las métricas de la máquina virtual se pueden ver de la misma manera que las de la máquina virtual host:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Para ver el rendimiento de la máquina virtual, haga clic en **Métricas** en la hoja de la máquina virtual y seleccione cualquiera de las métricas de diagnóstico en **Métricas disponibles**.

    ![Ver las métricas de la máquina virtual](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Creación de alertas

Puede crear alertas basadas en métricas de rendimiento concretas. Se pueden usar alertas para enviar una notificación, por ejemplo, cuando el uso medio de la CPU supera un umbral concreto o cuando el espacio libre en disco disponible cae por debajo de una cantidad determinada. Las alertas se muestran en Azure Portal o se pueden enviar por correo electrónico. También puede desencadenar runbooks de Azure Automation o Azure Logic Apps en respuesta a las alertas que se generan.

En el siguiente ejemplo se crea una alerta para el uso medio de la CPU.

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Haga clic en **Reglas de alertas** en la hoja de la máquina virtual y, después, en **Agregar alerta de métrica** en la parte superior de la hoja de alertas.
4. Especifique un **nombre** para la alerta, como *myAlertRule*.
5. Para desencadenar una alerta cuando el porcentaje de la CPU supera 1,0 durante cinco minutos, deje el resto de valores predeterminados seleccionados.
6. Opcionalmente, active la casilla *Enviar correo electrónico a propietarios, colaboradores y lectores* para enviar una notificación por correo electrónico. La acción predeterminada es presentar una notificación en el portal.
7. Haga clic en el botón **Aceptar**.

## <a name="advanced-monitoring"></a>Supervisión avanzada 

Mediante [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) se puede realizar una supervisión más avanzada de la máquina virtual. Si aún no lo ha hecho, puede suscribirse para disfrutar de una [evaluación gratuita](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) de Operations Management Suite.

Cuando tenga acceso al portal de OMS, puede buscar la clave y el identificador del área de trabajo en la hoja Configuración. Use el comando [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) para agregar la extensión de OMS a la máquina virtual. Actualice los valores de variable del ejemplo siguiente de modo que reflejen la clave y el identificador del área de trabajo de OMS.  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

Después de unos minutos, debería ver la nueva máquina virtual en el área de trabajo de OMS. 

![Hoja OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha configurado y revisado las máquinas virtuales con Azure Security Center. Ha aprendido a:

> [!div class="checklist"]
> * Crear una red virtual
> * Crear un grupo de recursos y una máquina virtual 
> * Habilitar los diagnósticos de arranque en la máquina virtual
> * Ver los diagnósticos de arranque
> * Ver las métricas del host
> * Instalar la extensión de Diagnostics
> * Ver las métricas de la máquina virtual
> * Crear una alerta
> * Configurar la supervisión avanzada

En el siguiente tutorial obtendrá información sobre Azure Security Center.

> [!div class="nextstepaction"]
> [Administración de la seguridad de máquinas virtuales](./tutorial-azure-security.md)
