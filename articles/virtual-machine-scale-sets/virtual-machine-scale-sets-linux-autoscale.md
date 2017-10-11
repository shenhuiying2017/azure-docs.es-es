---
title: "Escalado automático de conjuntos de escalado de máquinas virtuales Linux | Microsoft Docs"
description: "Configuración del escalado automático para un conjunto de escalado de máquinas virtuales Linux mediante la CLI de Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.openlocfilehash: eff4add1cb16fe25022787668dc1d2277845dd95
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="automatically-scale-linux-machines-in-a-virtual-machine-scale-set"></a>Escalado automático de máquinas de Linux en un conjunto de escalado de máquinas virtuales
Los conjuntos de escalado de máquinas virtuales facilitan la implementación y administración de máquinas virtuales idénticas como conjunto. Los conjuntos de escala proporcionan una capa de proceso altamente escalable y personalizable para aplicaciones de gran escala y admiten imágenes de la plataforma Windows, imágenes de la plataforma Linux, imágenes personalizadas y extensiones. Para obtener más información, consulte [Virtual Machine Scale Sets Overview (Información general de conjuntos de escalado de máquinas virtuales)](virtual-machine-scale-sets-overview.md).

En este tutorial se muestra cómo crear un conjunto de escalado de máquinas virtuales de Linux con la versión más reciente de Ubuntu Linux. En el tutorial también se muestra cómo escalar automáticamente las máquinas del conjunto. El conjunto de escalado se crea y la configuración de escalado se configura mediante la creación de una plantilla de Azure Resource Manager que se implementa con la CLI de Azure. Para más información sobre las plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](../azure-resource-manager/resource-group-authoring-templates.md). Para obtener más información sobre el escalado automático de conjuntos de escalado, consulte [Automatic scaling and Virtual Machine Scale Sets (Escalado automático y conjuntos de escalado de máquinas virtuales)](virtual-machine-scale-sets-autoscale-overview.md).

En este tutorial, implemente los recursos y las extensiones siguientes:

* Microsoft.Storage/storageAccounts
* Microsoft.Network/virtualNetworks
* Microsoft.Network/publicIPAddresses
* Microsoft.Network/loadBalancers
* Microsoft.Network/networkInterfaces
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.Insights.VMDiagnosticsSettings
* Microsoft.Insights/autoscaleSettings

Para más información sobre los recursos de Resource Manager, consulte [Implementación mediante Azure Resource Manager frente al modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md).

Antes de empezar con los pasos de este tutorial, [instale la CLI de Azure](../cli-install-nodejs.md).

## <a name="step-1-create-a-resource-group-and-a-storage-account"></a>Paso 1: Creación de un grupo de recursos y una cuenta de almacenamiento

1. **Inicie sesión en Microsoft Azure**.  
En la interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema), cambie al modo Resource Manager e [inicie sesión con su identificador profesional o educativo](../xplat-cli-connect.md#scenario-1-azure-login-with-interactive-login). Siga las indicaciones para una experiencia de inicio de sesión interactiva a su cuenta de Azure.

    ```cli   
    azure config mode arm

    azure login
    ```
   
    > [!NOTE]
    > Si tiene un identificador profesional o educativo y no tiene habilitada la autenticación en dos fases, use `azure login -u` junto con el identificador para iniciar sesión que no sea interactiva. Si no tiene un identificador profesional o educativo, puede [crear uno desde su cuenta personal de Microsoft](../active-directory/active-directory-users-create-azure-portal.md).
    
2. **Cree un grupo de recursos**  
Todos los recursos se deben implementar en un grupo de recursos. Para este tutorial, asigne el nombre **vmsstest1**al grupo de recursos.
   
    ```cli
    azure group create vmsstestrg1 centralus
    ```

3. **Implemente una cuenta de almacenamiento en el nuevo grupo de recursos**  
La cuenta de almacenamiento es donde se almacena la plantilla. Cree una cuenta de almacenamiento denominada " **vmsstestsa**".
   
    ```cli
    azure storage account create -g vmsstestrg1 -l centralus --kind Storage --sku-name LRS vmsstestsa
    ```

## <a name="step-2-create-the-template"></a>Paso 2: Creación de la plantilla
Las plantillas del Administrador de recursos de Azure le permiten implementar y administrar los distintos recursos de Azure en conjunto mediante una descripción de JSON de los recursos y los parámetros de implementación asociados.

1. En el editor que prefiera, cree el archivo VMSSTemplate.json y agregue la estructura inicial de JSON para admitir la plantilla.

    ```json
    {
      "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      },
      "variables": {
      },
      "resources": [
      ]
    }
    ```

2. No siempre se necesitan parámetros, pero proporcionan una manera de introducir valores al implementar la plantilla. Agregue estos parámetros en el elemento primario de los parámetros que agregó a la plantilla.

    ```json
    "vmName": { "type": "string" },
    "vmSSName": { "type": "string" },
    "instanceCount": { "type": "string" },
    "adminUsername": { "type": "string" },
    "adminPassword": { "type": "securestring" },
    "resourcePrefix": { "type": "string" }
    ```
   
   * Un nombre para la máquina virtual independiente que se utiliza para tener acceso a las máquinas del conjunto de escalado.
   * Un nombre para la cuenta de almacenamiento donde se almacena la plantilla.
   * El número de instancias de máquinas virtuales para crear inicialmente en el conjunto de escala.
   * Un nombre y contraseña de la cuenta de administrador en las máquinas virtuales.
   * Establece un prefijo de nombre de los recursos que se crean para admitir el conjunto de escalado.

3. Pueden usarse variables en una plantilla para especificar los valores que pueden cambiar con frecuencia o los valores que se deben crear a partir de una combinación de valores de parámetro. Agregue estas variables en el elemento primario de las variables que agregó a la plantilla.

    ```json
    "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
    "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
    "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
    "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
    "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
    "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
    "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
    "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor\\PercentProcessorTime\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU percentage guest OS\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
    ```

   * Nombres DNS que usan las interfaces de red.
   * Los nombres de direcciones IP y los prefijos para la red virtual y las subredes.
   * Los nombres y los identificadores de la red virtual, el equilibrador de carga y las interfaces de red.
   * Los nombres de cuentas de almacenamiento para las cuentas asociadas a las máquinas en el conjunto de escala.
   * Configuración de la extensión de diagnósticos que se instala en las máquinas virtuales. Para más información sobre la extensión de diagnósticos, consulte [Crear una máquina virtual de Windows con supervisión y diagnóstico mediante la plantilla de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

4. Agregue el recurso de la cuenta de almacenamiento en el elemento primario de recursos que agregó a la plantilla. Esta plantilla utiliza un bucle para crear las cinco cuentas de almacenamiento recomendadas donde se guardan los discos del sistema operativo y los datos de diagnóstico. Este conjunto de cuentas puede admitir hasta 100 máquinas virtuales en un conjunto de escala, lo cual es el máximo actual. Cada cuenta de almacenamiento se denomina con un indicador de letra, que se definió en las variables, y se combina con el sufijo que proporcionó en los parámetros de la plantilla.
   
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Agregue el recurso de red virtual. Consulte [Proveedor de recursos de red](../virtual-network/resource-groups-networking.md)para más información.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
        "subnets": [
          {
            "name": "subnet1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          }
        ]
      }
    },
    ```

6. Agregue los recursos de dirección IP públicos que usan la interfaz de red y el equilibrador de carga.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP1')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName1')]"
        }
      }
    },
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP2')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName2')]"
        }
      }
    },
    ```

7. Agregue el recurso de equilibrador de carga que usa el conjunto de escala. Para más información, consulte [Compatibilidad del Administrador de recursos de Azure con el equilibrador de carga](../load-balancer/load-balancer-arm.md)

    ```json   
    {
      "apiVersion": "2015-06-15",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
              }
            }
          }
        ],
        "backendAddressPools": [ { "name": "bepool1" } ],
        "inboundNatPools": [
          {
            "name": "natpool1",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPortRangeStart": 50000,
              "frontendPortRangeEnd": 50500,
              "backendPort": 22
            }
          }
        ]
      }
    },
    ```

8. Agregue el recurso de interfaz de red que utiliza la máquina virtual independiente. Dado que las máquinas de un conjunto de escalado no son accesibles mediante una dirección IP pública, se crea una máquina virtual independiente en la misma red virtual para tener acceso remoto a las máquinas.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
              },
              "subnet": {
                "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
              }
            }
          }
        ]
      }
    },
    ```

9. Agregue la máquina virtual en la misma red que el conjunto de escalado.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": { "vmSize": "Standard_A1" },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "14.04.4-LTS",
            "version": "latest"
          },
          "osDisk": {
            "name": "[concat(parameters('resourcePrefix'), 'os1')]",
            "vhd": {
              "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
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
        }
      }
    },
    ```

10. Agregue el recurso de conjunto de escalado de máquinas virtuales y especifique la extensión de diagnóstico instalada en todas las máquinas virtuales del conjunto. Muchos de los valores para este recurso son similares al recurso de máquina virtual. Las principales diferencias son el elemento de capacidad, que especifica el número de máquinas virtuales del conjunto de escalado, y upgradePolicy, que especifica cómo se realizan las actualizaciones en las máquinas virtuales. El conjunto de escalado no se crea hasta que se crean todas las cuentas de almacenamiento como se especifica en el elemento dependsOn.

    ```json
    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "apiVersion": "2016-03-30",
      "name": "[parameters('vmSSName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "sku": {
        "name": "Standard_A1",
        "tier": "Standard",
        "capacity": "[parameters('instanceCount')]"
      },
      "properties": {
        "upgradePolicy": {
          "mode": "Manual"
        },
        "virtualMachineProfile": {
          "storageProfile": {
            "osDisk": {
              "vhdContainers": [
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4],'.blob.core.windows.net/vmss')]"
              ],
              "name": "vmssosdisk",
              "caching": "ReadOnly",
              "createOption": "FromImage"
            },
            "imageReference": {
              "publisher": "Canonical",
              "offer": "UbuntuServer",
              "sku": "14.04.4-LTS",
              "version": "latest"
            }
          },
          "osProfile": {
            "computerNamePrefix": "[parameters('vmSSName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "networkconfig1",
                "properties": {
                  "primary": "true",
                  "ipConfigurations": [
                    {
                      "name": "ip1",
                      "properties": {
                        "subnet": {
                          "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                          }
                        ]
                      }
                    }
                  ]
                }
              }
            ]
          },
          "extensionProfile": {
            "extensions": [
              {
                "name":"LinuxDiagnostic",
                "properties": {
                  "publisher":"Microsoft.OSTCExtensions",
                  "type":"LinuxDiagnostic",
                  "typeHandlerVersion":"2.1",
                  "autoUpgradeMinorVersion":false,
                  "settings": {
                    "xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                    "storageAccount":"[variables('diagnosticsStorageAccountName')]"
                  },
                  "protectedSettings": {
                    "storageAccountName":"[variables('diagnosticsStorageAccountName')]",
                    "storageAccountKey":"[listkeys(variables('accountid'), '2015-06-15').key1]",
                    "storageAccountEndPoint":"https://core.windows.net"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

11. Agregue el recurso autoscaleSettings que define cómo el conjunto de escala se ajusta según el uso del procesador en las máquinas del conjunto.

    ```json
    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Processor\\PercentProcessorTime",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 50.0
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      }
    }
    ```
    
    Para este tutorial, destacan estos valores:
    
    * **metricName**  
    Este valor es el mismo que el contador de rendimiento que definimos en la variable wadperfcounter. Con esta variable, la extensión Diagnósticos recopila los datos del contador **Processor\PercentProcessorTime**.
    
    * **metricResourceUri**  
    Este valor es el identificador de recursos del conjunto de escalado de máquinas virtuales.
    
    * **timeGrain**  
    Este valor es la granularidad de las métricas que se recopilan. En esta plantilla, se establece en un minuto.
    
    * **statistic**  
    Este valor determina cómo se combinan las métricas para dar cabida a la acción de escalado automático. Los valores posibles son: Average, Min y Max. En esta plantilla, se recopila el uso promedio total de CPU de las máquinas virtuales.

    * **timeWindow**  
    Este valor es el intervalo de tiempo durante el cual se recopilan los datos de instancia. Debe estar comprendido entre 5 minutos y 12 horas.
    
    * **timeAggregation**  
    Este valor determina la manera en que se deberían combinar los datos recopilados en el tiempo. El valor predeterminado es Average. Los valores posibles son: Average, Minimum, Maximum, Last, Total, Count.
    
    * **operator**  
    Este valor es el operador que se utiliza para comparar los datos de métrica y el umbral. Los valores posibles son: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    
    * **threshold**  
    Este valor desencadena la acción de escalado. En esta plantilla, los equipos se agregan al conjunto de escala que se establece cuando el uso promedio de CPU entre máquinas del conjunto es más de un 50%.
    
    * **dirección**  
    Este valor determina la acción que se realiza cuando se alcanza el valor de umbral. Los valores posibles son Increase o Decrease. En esta plantilla, se aumenta el número de máquinas virtuales en el conjunto de escala si el umbral es más de un 50% en la ventana de tiempo definido.

    * **type**  
    Este valor es el tipo de acción que debe realizarse y que se debe establecer en ChangeCount.
    
    * **value**  
    Este valor es el número de máquinas virtuales que se agregan o se quitan del conjunto de escalado. Este valor debe ser 1 o un valor superior. El valor predeterminado es 1. En esta plantilla, el número de máquinas en el conjunto de escala aumenta en 1 cuando se alcanza el umbral.

    * **cooldown**  
    Este valor es la cantidad de tiempo de espera desde la última acción de escalado hasta antes de que se produzca la siguiente acción. Debe estar comprendido entre un minuto y una semana.

12. Guarde el archivo de plantilla.    

## <a name="step-3-upload-the-template-to-storage"></a>Paso 3: Carga de la plantilla en el almacenamiento
La plantilla se puede cargar siempre y cuando conozca el nombre de cuenta y la clave principal de la cuenta de almacenamiento que creó en el paso 1.

1. En la interfaz de línea de comandos (Bash, Terminal, símbolo del sistema), ejecute estos comandos para establecer las variables de entorno que se necesitan para acceder a la cuenta de almacenamiento:

    ```cli   
    export AZURE_STORAGE_ACCOUNT={account_name}
    export AZURE_STORAGE_ACCESS_KEY={key}
    ```
    
    Puede obtener la clave haciendo clic en el icono de llave al ver el recurso de la cuenta de almacenamiento en el portal de Azure. Cuando use un símbolo del sistema de Windows, escriba **set** en lugar de export.

2. Cree el contenedor para almacenar la plantilla.
   
    ```cli
    azure storage container create -p Blob templates
    ```

3. Cargue el archivo de plantillas en el nuevo contenedor.
   
    ```cli
    azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json
    ```

## <a name="step-4-deploy-the-template"></a>Paso 4: Implementación de la plantilla
Ahora que ha creado la plantilla, puede empezar a implementar los recursos. Use este comando para iniciar el proceso:

```cli
azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1
```

Cuando presione Entrar, se le pedirá que proporcione valores para las variables que asignó. Proporcione estos valores:

```cli
vmName: vmsstestvm1
vmSSName: vmsstest1
instanceCount: 5
adminUserName: vmadmin1
adminPassword: VMpass1
resourcePrefix: vmsstest
```

Para que todos los recursos se implementen correctamente se tardará unos 15 minutos.

> [!NOTE]
> También puede usar la capacidad del portal para implementar los recursos. Use este vínculo: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>


## <a name="step-5-monitor-resources"></a>Paso 5: Supervisión de recursos
Puede obtener información acerca de los conjuntos de escala de máquinas virtuales mediante estos métodos:

* El portal de Azure: actualmente puede obtener una cantidad limitada de información mediante el portal.

* [Explorador de recursos de Azure](https://resources.azure.com/) : es la mejor herramienta para explorar el estado actual del conjunto de escalado. Siga esta ruta de acceso y debería ver la vista de instancia del conjunto de escala que creó:
  
    ```cli
    subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines
    ```

* CLI de Azure. Use este comando para obtener información:

    ```cli  
    azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1
    ```

* Conéctese a la máquina virtual de jumpbox igual que lo haría con cualquier otra máquina y, a continuación, podrá obtener acceso remoto a las máquinas virtuales del conjunto de escala para supervisar los procesos individuales.

> [!NOTE]
> Una API de REST completa para obtener más información sobre los conjuntos de escala se puede encontrar en [Conjuntos de escala de máquinas virtuales](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-6-remove-the-resources"></a>Paso 6: Eliminación de recursos
Dado que se le cobrará por los recursos utilizados en Azure, siempre es conveniente eliminar los recursos que ya no sean necesarios. No es necesario eliminar por separado cada recurso de un grupo de recursos. Puede eliminar el grupo de recursos para que se eliminen automáticamente todos sus recursos.

```cli
azure group delete vmsstestrg1
```

## <a name="next-steps"></a>Pasos siguientes
* Encuentre ejemplos de las características de supervisión de Azure Monitor en [Ejemplos de inicio rápido de CLI multiplataforma de Azure Monitor](../monitoring-and-diagnostics/insights-cli-samples.md)
* Aprenda sobre las características de notificación en [Uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook en Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
* Aprenda cómo [usar registros de auditoría para enviar notificaciones de alerta por correo electrónico y webhook en Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
* Consulte la plantilla de la [aplicación de demostración de la autoescala en Ubuntu 16.04](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale), en la que se configura una aplicación Python/bottle para practicar la funcionalidad de escalado automático de los conjuntos de escalas de máquina virtual.

