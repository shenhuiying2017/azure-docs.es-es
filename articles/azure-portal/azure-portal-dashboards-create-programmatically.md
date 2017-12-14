---
title: "Creación mediante programación de paneles de Azure | Microsoft Docs"
description: "En este artículo se explica cómo crear mediante programación paneles de Azure."
services: azure-portal
documentationcenter: 
author: adamab
manager: timlt
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: adamab
ms.openlocfilehash: d9acb58791cb1412d5e67479ca6490e1548be2c8
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="programmatically-create-azure-dashboards"></a>Creación mediante programación de paneles de Azure

Este documento le guía a través del proceso de creación y publicación de paneles de Azure mediante programación. Se hace referencia en todo el documento al panel que se muestra a continuación.

![panel de ejemplo](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Información general

Los paneles compartidos en Azure constituyen [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), al igual que las máquinas virtuales y las cuentas de almacenamiento.  Por lo tanto, se pueden administrar mediante programación a través de las [API de REST de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-rest-api), la [CLI de Azure](https://docs.microsoft.com/cli/azure/overview), los [comandos de Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.2.0) y numerosas características de [Azure Portal](https://portal.azure.com) basadas en estas API para facilitar la administración de recursos.  

Cada una de estas API y herramientas ofrece formas de crear, enumerar, recuperar, modificar y eliminar recursos.  Puesto que los paneles son recursos, puede elegir la API o herramienta que prefiera usar.

Independientemente de la herramienta que utilice, debe construir una representación JSON de su objeto dashboard antes de que pueda llamar a cualquier API de creación de recursos. Este objeto contiene información acerca de los elementos (conocidos como iconos) del panel. Incluye tamaños, posiciones, recursos a los que están enlazados y cualquier personalización del usuario.

La manera más práctica de crear este documento JSON es usar el [portal](https://portal.azure.com/) para agregar y colocar los iconos de forma interactiva. A continuación, exporta el documento JSON. Por último, crea una plantilla a partir del resultado para utilizarla posteriormente en scripts, programas y herramientas de implementación.

## <a name="create-a-dashboard"></a>Creación de un panel

Para crear un panel, puede usar el comando Nuevo del panel en la pantalla principal del portal.

![comando nuevo de panel](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Luego, puede utilizar la galería de iconos para buscar y agregar iconos. Los iconos se agregan arrastrándolos y colocándolos. Algunos iconos son compatibles con el cambio de tamaño mediante un controlador de arrastre, mientras que otros admiten tamaños fijos que se pueden ver en el menú contextual.

### <a name="drag-handle"></a>Controlador de arrastre
![controlador de arrastre](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Tamaños fijos mediante el menú contextual
![menú contextual de tamaños](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Uso compartido del panel

Después de haber configurado el panel a su gusto, los siguientes pasos son publicar el panel (mediante el comando Compartir) y utilizar Resource Explorer para capturar la representación JSON.

![comando compartir](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Al hacer clic en el comando Compartir, se muestra un cuadro de diálogo en el que se le pide que elija en qué suscripción y grupo de recursos quiere publicar. Tenga en cuenta que [debe tener acceso de escritura](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) a la suscripción y al grupo de recursos que desee.

![uso compartido y acceso](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Captura de la representación JSON del panel

La publicación solo tarda unos segundos.  Cuando termine, el paso siguiente consiste en ir a [Resource Explorer](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) para capturar la representación JSON.

![examen de resource explorer](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

En Resource Explorer, desplácese hasta la suscripción y el grupo de recursos elegidos. A continuación, haga clic en el recurso de panel recién publicado para ver la representación JSON.

![json de resource explorer](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Creación de una plantilla a partir de JSON

El paso siguiente consiste en crear una plantilla a partir de esta representación JSON para que se pueda reutilizar mediante programación con las API de administración de recursos adecuadas, como las herramientas de línea de comandos, o en el portal.

No es necesario comprender perfectamente la estructura JSON del panel para crear una plantilla. En la mayoría de los casos, desea conservar la estructura y la configuración de los iconos, y parametrizar el conjunto de recursos de Azure a los que señalen. Examine el panel JSON exportado y busque todas las apariciones de los identificadores de recursos de Azure. El panel de ejemplo tiene varios iconos que señalan todos a una única máquina virtual de Azure. Esto se debe a que el panel solo examina este único recurso. Si en la representación JSON de ejemplo (que se incluye al final del documento) busca "/subscriptions", encontrará varias apariciones de este identificador.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Para publicar este panel para cualquier máquina virtual en el futuro, debe parametrizar todas las apariciones de esta cadena en JSON. 

Hay dos tipos de API que crean recursos en Azure. [API imperativas](https://docs.microsoft.com/rest/api/resources/resources) que crean un recurso a la vez y un sistema de [implementación basado en plantillas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) que puede organizar la creación de varios recursos dependientes con una sola llamada API. Esta última admite de forma nativa la parametrización y creación de plantillas, por eso la utilizamos en nuestro ejemplo.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Creación mediante programación de un panel a partir de la plantilla mediante una implementación de plantilla

Azure ofrece la capacidad de organizar la implementación de varios recursos. Puede crear una plantilla de implementación que especifique el conjunto de recursos que se van a implementar, así como las relaciones entre ellos.  El formato JSON de los recursos es el mismo que si estuviera creándolos uno a uno. La diferencia es que el [lenguaje de plantilla](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) agrega algunos conceptos como variables, parámetros, funciones básicas, etc. Esta sintaxis extendida solo se admite en el contexto de una implementación de plantilla y no funciona si se usa con las API imperativas que se han mencionado anteriormente.

Si ha elegido esta última opción, debe realizar la parametrización mediante la sintaxis de parámetros de la plantilla.  Reemplace todas las instancias del identificador del recurso, que hemos visto anteriormente, tal como se muestra aquí.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Ejemplo de propiedad JSON con el identificador de recurso codificado de forma rígida
`id: “/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1”`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Ejemplo de propiedad JSON convertida a una versión con parámetros según los parámetros de plantilla

`id: "[resourceId(parameters('virtualMachineResourceGroup'), ‘Microsoft.Compute/virtualMachines’, parameters('virtualMachineName'))]"`

También debe declarar algunos metadatos de plantilla requeridos y los parámetros de la parte superior de la plantilla JSON del modo siguiente:

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__Vista de la plantilla de trabajo completa al final de este documento.__

Una vez diseñada la plantilla, puede implementarla mediante las [API de REST](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), la [CLI de Azure](https://docs.microsoft.com/cli/azure/group/deployment#az_group_deployment_create) o la [página de implementación de plantillas del portal](https://portal.azure.com/#create/Microsoft.Template).

Estas son dos versiones de nuestro panel de ejemplo JSON. La primera es la versión que hemos exportado desde el portal y que ya estaba enlazada a un recurso. La segunda es la versión de la plantilla que se puede enlazar mediante programación a cualquier máquina virtual e implementar con Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Representación JSON de nuestro panel de ejemplo (antes de la creación de plantillas)

Esto es lo que puede esperar ver si sigue las instrucciones anteriores para capturar la representación JSON de un panel que ya está implementado. Observe los identificadores de recursos codificados de forma rígida que muestran que este panel señala a una máquina virtual de Azure específica.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Representación de la plantilla de nuestro panel de ejemplo

La versión de la plantilla del panel ha definido tres parámetros llamados __virtualMachineName__, __virtualMachineResourceGroup__ y __dashboardName__.  Los parámetros permiten que este panel señale a una máquina virtual de Azure diferente cada vez que realice una implementación. Los identificadores con parámetros aparecen resaltados para mostrar que este panel puede mediante programación configurarse e implementarse para apuntar a cualquier máquina virtual de Azure. La manera más fácil de probar esta característica es copiar la plantilla siguiente y pegarla en la [página de implementación de plantillas de Azure Portal](https://portal.azure.com/#create/Microsoft.Template). 

En este ejemplo se implementa un panel por sí mismo, pero el lenguaje de plantilla le permite implementar varios recursos e incluir uno o más paneles junto a ellos. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```