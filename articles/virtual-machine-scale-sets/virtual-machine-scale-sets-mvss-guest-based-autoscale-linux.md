---
title: "Uso del escalado automático de Azure con métricas de invitado en una plantilla de conjunto de escalado de Linux | Microsoft Docs"
description: "Obtenga información acerca de cómo usar el escalado automático con métricas de invitado en una plantilla de conjunto de escalado de máquinas virtuales de Linux"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: negat
ms.openlocfilehash: 8e822d83dd3bafabfea60ad50224c87df226bdc6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Escalado automático con métricas de invitado en una plantilla de conjunto de escalado de Linux

Hay dos tipos de métricas en Azure que se obtienen de las máquinas virtuales y los conjuntos de escalado: algunos proceden de la máquina virtual host y otros proceden de la máquina virtual invitada. A nivel general, si usa métricas de CPU, disco y red estándar, las métricas de host probablemente sean adecuadas. Sin embargo, si necesita una selección de métricas más grande, probablemente sea más adecuado contar con métricas de invitado. Analicemos las diferencias entre ambos tipos de métricas:

Las métricas de host son más sencillas y más confiables. No requieren configuración adicional porque las recopila la máquina virtual host, mientras que las métricas de invitado requieren la instalación de la [extensión Azure Diagnostics para Windows](../virtual-machines/windows/extensions-diagnostics-template.md) o [Azure Diagnostics para Linux](../virtual-machines/linux/diagnostic-extension.md) en la máquina virtual invitada. Un motivo habitual para usar las métricas de invitado en lugar de las métricas de host es que las métricas de invitado proporcionan una mayor variedad de métricas que las de host. Un ejemplo es las métricas de consumo de memoria, que solo están disponibles mediante las métricas de invitado. La lista de las métricas de host admitidas se encuentran [aquí](../monitoring-and-diagnostics/monitoring-supported-metrics.md), y las métricas de invitado de uso frecuente se encuentran [aquí](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md). En este artículo se muestra cómo modificar la [plantilla de conjunto de escalado viable mínimo](./virtual-machine-scale-sets-mvss-start.md) para usar reglas de escalado automático en función de las métricas de invitado para conjuntos de escalado de Linux.

## <a name="change-the-template-definition"></a>Cambio de la definición de la plantilla

La plantilla de conjunto de escalado mínimo viable se puede ver [aquí](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), y la plantilla para implementar el conjunto de escalado de Linux con escalado automático basado en métricas de invitado se puede ver [aquí](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Vamos a examinar la diferencia usada para crear esta plantilla (`git diff minimum-viable-scale-set existing-vnet`) paso a paso:

Primero, se van a agregar parámetros para `storageAccountName` y `storageAccountSasToken`. El agente de Diagnostics almacena los datos de las métricas en una [tabla](../cosmos-db/table-storage-how-to-use-dotnet.md), en esta cuenta de almacenamiento. A partir de la versión 3.0 del agente de Diagnostics para Linux ya no admite el uso de una clave de acceso de almacenamiento. En su lugar, use un [token de SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

A continuación, modifique el conjunto de escalado `extensionProfile` para incluir la extensión Diagnostics. En esta configuración, se especifica el identificador de recurso del conjunto de escalado del que se recopilarán las métricas, así como la cuenta de almacenamiento y el token de SAS que se usará para almacenarlas. Especifique con qué frecuencia se agregan las métricas (en este caso, cada minuto) y las métricas para las que desea realizar un seguimiento (en este caso, porcentaje de memoria usado). Para más información sobre esta configuración y las métricas, aparte del porcentaje de memoria usado, consulte [esta documentación](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Por último, agregue un recurso `autoscaleSettings` para configurar el escalado automático en función de estas métricas. Este recurso tiene una cláusula `dependsOn` que hace referencia al conjunto de escalado para asegurarse de que el conjunto de escalado existe antes de intentar realizar el escalado automático. Si se elige una métrica diferente para el escalado automático, se utilizaría el valor de `counterSpecifier` de la configuración de la extensión Diagnostics como valor de `metricName` en la configuración del escalado automático. Para más información sobre la configuración de escalado automático, consulte los [procedimientos recomendados de escalado automático](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md) y la [documentación de referencia de la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931928.aspx).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>pasos siguientes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
