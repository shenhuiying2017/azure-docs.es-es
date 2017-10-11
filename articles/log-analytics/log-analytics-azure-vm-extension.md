---
title: "Conexión de máquinas virtuales de Azure a Log Analytics | Microsoft Docs"
description: "Para máquinas virtuales de Windows y Linux que se ejecutan en Azure, se recomienda instalar la extensión de máquina virtual de Azure de Log Analytics para recopilar registros y métricas. Puede usar Azure Portal o PowerShell para instalar la extensión de máquina virtual de Log Analytics en VM de Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: ewinner
editor: 
ms.assetid: ca39e586-a6af-42fe-862e-80978a58d9b1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: richrund
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cdae291b546fef4d7fdb8b067c8e4f4c9708d43f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="connect-azure-virtual-machines-to-log-analytics-with-a-log-analytics-agent"></a>Conexión de máquinas virtuales de Azure al agente de Log Analytics

En equipos Windows y Linux, para recopilar registros y métricas se recomienda instalar el agente de Log Analytics.

Para instalar al agente de Log Analytics en máquinas virtuales de Azure, lo más sencillo es utilizar la extensión de máquina virtual de Log Analytics.  El uso de una extensión simplifica el proceso de instalación y configura automáticamente el agente para enviar datos al área de trabajo de Log Analytics que especifique. El agente también se actualiza automáticamente, garantizando así que disponga de las características y correcciones más recientes.

Para máquinas virtuales Windows, debe habilitar la extensión de máquina virtual *Microsoft Monitoring Agent*.
Para máquinas virtuales Linux, debe habilitar la extensión de máquina virtual *Agente de OMS para Linux*.

Obtenga más información sobre las [extensiones de máquina virtual de Azure](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) y el [Agente Linux](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Cuando se usa una recopilación basada en agente para los datos de registro, debe configurar [orígenes de datos en Log Analytics](log-analytics-data-sources.md) para especificar los registros y las métricas que desea recopilar.

> [!IMPORTANT]
> Si configura Log Analytics para indexar los datos de registro mediante [Diagnósticos de Azure](log-analytics-azure-storage.md) y configura el agente para recopilar los mismos registros, los registros se recopilan dos veces. Se le cobrará por los dos orígenes de datos. Si tiene instalado el agente, recopile datos de registro solo mediante el agente; no configure Log Analytics para recopilar datos de registro de diagnósticos de Azure.
>
>

Existen tres maneras sencillas de habilitar la extensión de la máquina virtual de Log Analytics:

* Mediante Azure Portal.
* Mediante Azure PowerShell.
* Mediante el uso de una plantilla de Azure Resource Manager.

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Habilitación de la extensión de máquina virtual en Azure Portal
Puede instalar el agente para Log Analytics y conectar la máquina virtual de Azure donde se ejecuta mediante [Azure Portal](https://portal.azure.com).

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Para instalar al agente de Log Analytics y conectar la máquina virtual a un área de trabajo de Log Analytics
1. Inicie sesión en el [Portal de Azure](http://portal.azure.com).
2. Seleccione **Examinar** en el lado izquierdo del portal y, a continuación, vaya a **Log Analytics (OMS)** y selecciónelo.
3. En la lista de áreas de trabajo de Log Analytics, seleccione el área que desea usar con la máquina virtual de Azure.  
   ![Áreas de trabajo de OMS](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4. En **Log analytics management** (Administración de Log Analytics), seleccione **Máquinas virtuales**.  
   ![Máquinas virtuales](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5. En la lista de **máquinas virtuales**, seleccione la máquina virtual donde desea instalar el agente. El **estado de la conexión de OMS** de la máquina virtual indica que **no está conectado**.  
   ![VM no conectada](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6. En los detalles de la máquina virtual, seleccione **Conectar**. El agente se instala y se configura automáticamente para el área de trabajo de Log Analytics. Este proceso tarda unos minutos, durante los que el estado de conexión de OMS indica que se está *conectando*.  
   ![Conectar VM](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7. Cuando el agente esté instalado y conectado, el estado de la **conexión de OMS** se actualizará para mostrar **This workspace** (Esta área de trabajo).  
   ![Conectada](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)

## <a name="enable-the-vm-extension-using-powershell"></a>Habilitación de la extensión de máquina virtual con PowerShell
Al configurar la máquina virtual con PowerShell, es necesario proporcionar el **identificador del área de trabajo** y la **clave del área de trabajo**. Los nombres de propiedad de la configuración de json **distinguen entre mayúsculas y minúsculas**.

Puede encontrar la clave principal y el identificador en la página **Configuración** del portal de OMS. También puede usar PowerShell como se ilustra en el ejemplo anterior.

![Identificador de área de trabajo y clave principal](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

Hay comandos diferentes para máquinas virtuales clásicas de Azure y máquinas virtuales de Resource Manager. A continuación se incluyen ejemplos para máquinas virtuales clásicas y de Resource Manager.

En el caso de las máquinas virtuales clásicas, use el siguiente ejemplo de PowerShell:

```PowerShell
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Para VM con Linux de Resource Manager que usan la CLI siguiente
```azurecli
az vm extension set --resource-group myRGMonitor --vm-name myMonitorVM --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --version 1.3 --protected-settings ‘{"workspaceKey": "<workspace-key>"}’ --settings ‘{"workspaceId": "<workspace-id>"}’ 
```

En el caso de las máquinas virtuales de Resource Manager, use el siguiente ejemplo de PowerShell:

```PowerShell
Login-AzureRMAccount
Select-AzureRMSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```


## <a name="deploy-the-vm-extension-using-a-template"></a>Implementación de la extensión de máquina virtual mediante una plantilla
Con Azure Resource Manager, puede crear una plantilla (en formato JSON) que defina la implementación y configuración de la aplicación. Esta plantilla se conoce como plantilla de Administrador de recursos y proporciona una manera declarativa de definir la implementación. El uso de una plantilla permite implementar la aplicación repetidamente a lo largo del ciclo de vida de esta y tener la seguridad de que los recursos se implementan de forma coherente.

La inclusión del agente de Log Analytics como parte de la plantilla de Resource Manager le permite garantizar que todas las máquinas virtuales estén preconfiguradas para informar al área de trabajo de Log Analytics.

Para más información sobre las plantillas de Resource Manager, consulte [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

A continuación se incluye un ejemplo de una plantilla de Resource Manager que se utiliza para la implementación de una máquina virtual que ejecuta Windows con la extensión Microsoft Monitoring Agent instalada. Se trata de una plantilla de máquina virtual típica, con las siguientes adiciones:

* Parámetros workspaceId y workspaceName
* Sección de extensión de recursos Microsoft.EnterpriseCloud.Monitoring
* Salidas para buscar workspaceId y workspaceSharedKey

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMS workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

Puede implementar una plantilla mediante el siguiente comando de PowerShell:

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-the-log-analytics-vm-extension"></a>Solución de problemas de la extensión de máquina virtual de Log Analytics
Normalmente, cuando algo no funciona bien, recibe un mensaje desde Azure Portal o Azure PowerShell.

1. Inicie sesión en el [Portal de Azure](http://portal.azure.com).
2. Busque la máquina virtual y abra sus detalles.
3. Haga clic en **Extensiones** para comprobar si la extensión OMS está habilitada o no.

   ![Vista de la extensión de máquina virtual](./media/log-analytics-azure-vm-extension/oms-vmview-extensions.png)

4. Haga clic en la extensión *MicrosoftMonitoringAgent* (Windows) o *OmsAgentForLinux* (Linux) y vea los detalles. 

   ![Detalles de la extensión de máquina virtual](./media/log-analytics-azure-vm-extension/oms-vmview-extensiondetails.png)

### <a name="troubleshooting-windows-virtual-machines"></a>Solución de problemas con máquinas virtuales Windows
Si la extensión del agente de VM *Microsoft Monitoring Agent* no se instala o no envía informes, puede realizar los pasos siguientes para solucionar el problema.

1. Siga los pasos de [KB 2965986](https://support.microsoft.com/kb/2965986#mt1) para comprobar que el agente de máquina virtual de Azure está instalado y funciona correctamente.
   * También puede revisar el archivo de registro del agente de máquina virtual `C:\WindowsAzure\logs\WaAppAgent.log`
   * Si el registro no existe, el agente de máquina virtual no estará instalado.
     * [Instale el agente de máquina virtual de Azure en máquinas virtuales de Azure clásicas.](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
2. Confirme que se está ejecutando la tarea de latido de la extensión de Microsoft Monitoring Agent mediante los siguientes pasos:
   * Inicie sesión en la máquina virtual.
   * Abra el programador de tareas y busque la tarea `update_azureoperationalinsight_agent_heartbeat`.
   * Confirme que la tarea está habilitada y se ejecuta cada minuto.
   * Consulte el archivo de registro de latido en `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`.
3. Revise los archivos de registro de la extensión de máquina virtual de Microsoft Monitoring Agent en `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`.
4. Asegúrese de que la máquina virtual puede ejecutar scripts de PowerShell.
5. Asegúrese de que no se hayan modificado los permisos en C:\Windows\temp.
6. Consulte el estado de Microsoft Monitoring Agent, para lo que debe escribir el siguiente comando en una ventana de PowerShell con privilegios elevados en la máquina virtual `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`.
7. Revise los archivos de registro de instalación de Microsoft Monitoring Agent en `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`.

Para más información, consulte [Solución de problemas de la extensión de máquina virtual de Microsoft Azure](../virtual-machines/windows/extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="troubleshooting-linux-virtual-machines"></a>Solución de problemas con máquinas virtuales Linux
Si la extensión del agente de VM *Agente de OMS para Linux* no se instala o no envía informes, puede realizar los pasos siguientes para solucionar el problema.

1. Si el estado de la extensión es *desconocido*, compruebe si el agente de máquina virtual de Azure está instalado y funciona correctamente revisando el archivo de registro del agente de máquina virtual `/var/log/waagent.log`.
   * Si el registro no existe, el agente de máquina virtual no estará instalado.
   * [Instale el agente de máquina virtual de Azure en máquinas virtuales Linux.](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
2. Para otros estados incorrectos, revise los archivos de registro de extensión de máquina virtual del Agente de OMS para Linux en `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` y `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`.
3. Si el estado de la extensión es correcto, pero no se están cargando datos, revise los archivos de registro del Agente de OMS para Linux en `/var/opt/microsoft/omsagent/log/omsagent.log`.

## <a name="next-steps"></a>Pasos siguientes
* Configure [orígenes de datos de Log Analytics](log-analytics-data-sources.md) para especificar los registros y las métricas que desea recopilar.
* Para recopilar datos de máquinas virtuales, [incorpore soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).
* [Recopile datos con Diagnósticos de Azure](log-analytics-azure-storage.md) para otros recursos que se ejecutan en Azure.

Para equipos que no se ejecutan en Azure, puede instalar el agente de Log Analytics mediante los métodos descritos en los siguientes artículos:

* [Conexión de equipos Windows a Log Analytics](log-analytics-windows-agents.md)
* [Conexión de equipos Linux a Log Analytics](log-analytics-linux-agents.md)
