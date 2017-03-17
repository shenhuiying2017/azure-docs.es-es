---
title: Patrones de redes para Azure Service Fabric | Microsoft Docs
description: "En este artículo se describen los patrones de redes comunes de Service Fabric y cómo crear un clúster con las características de red de Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 030114fa1ea9b76c0ed48baeffb8859260fc8e52
ms.openlocfilehash: 78012ae1552c01690b0ad5b1285173ef9faf12bc
ms.lasthandoff: 03/01/2017


---
# <a name="service-fabric-networking-patterns"></a>Patrones de redes de Service Fabric
Una de las preguntas más comunes que hemos visto al crear clústeres de Service Fabric es cómo integrarlos con las diferentes características de red de Azure.  En este artículo se muestra cómo crear clústeres con las siguientes características:

- [Subred y red virtual existentes](#existingvnet)
- [Dirección IP pública estática](#staticpublicip)
- [Equilibrador de carga solo interno](#internallb)
- [Equilibrador de carga interno y externo](#internalexternallb)

Un concepto clave que hay que tener en cuenta es que Service Fabric se ejecuta en un conjunto de escalado de máquinas virtuales estándar, así que todas las funcionalidades que se pueden usar en un conjunto de escalado de máquinas virtuales también pueden emplearse con un clúster de Service Fabric. Las partes de red de la plantilla de Resource Manager son idénticas.  Una vez que realice la implementación en una red virtual existente, le resultará sencillo incorporar otras características de red como ExpressRoute, VPN Gateway, los grupos de seguridad de red (NSG) y el emparejamiento de VNET.

El único aspecto específico de Service Fabric es que[Azure Portal](https://portal.azure.com) usa internamente el proveedor de recursos de Service Fabric (SFRP) para llamar a un clúster con el fin de obtener información de los nodos y las aplicaciones.  El SFRP requiere acceso de entrada público al puerto de la puerta de enlace HTTP (19080 de forma predeterminada) en el punto de conexión de administración, que lo usa el [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md) para administrar el clúster. Este puerto también lo emplea el proveedor de recursos de Service Fabric para consultar información sobre el clúster con el fin de que se muestre en Azure Portal.  Si no se puede acceder a este puerto desde el SFRP, en el Portal de administración se mostrará un mensaje (por ejemplo, indicando que no se han encontrado nodos), y la lista de aplicaciones y nodos aparecerá vacía.  Si desea tener visibilidad del clúster a través de Azure Portal, el equilibrador de carga debe exponer una dirección IP pública y el NSG debe permitir el tráfico entrante en el puerto 19080.  Si no se cumplen estos requisitos, Azure Portal no mostrará el estado actual del clúster.  En caso contrario, no se verá afectado el clúster y podrá usar el [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md) para obtener el estado actual, que puede ser un límite aceptable según los requisitos de red.  Se trata de una limitación temporal que se va a eliminar en una actualización futura; a partir de ese momento, no se podrá acceder al clúster públicamente y no se verá afectada la funcionalidad del Portal de administración.

## <a name="templates"></a>Plantillas

Pueden encontrar todas las plantillas [aquí](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip). Debe poder implementar las plantillas tal como están con los siguientes comandos de PowerShell.  Si implementa la plantilla de red virtual existente o la de IP pública estática, asegúrese de revisar primero la sección [Configuración inicial](#initialsetup).

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Configuración inicial

### <a name="existing-virtual-network"></a>Red virtual existente

Vamos a empezar con una red virtual existente denominada "ExistingRG-vnet" en el grupo de recursos *ExistingRG*, con una subred llamada "default".  Estos recursos son los predeterminados que se crearon al usar Azure Portal para crear una máquina virtual estándar.  También podría crear la red virtual y subred sin necesidad de generar la máquina virtual. El objetivo principal de agregar un clúster a una red virtual existente es proporcionar conectividad de red a otras máquinas virtuales. Al crear la máquina virtual obtendremos un ejemplo concreto de cómo se suele usar.  Si el clúster de Service Fabric solo usa un equilibrador de carga interno sin una dirección IP pública, la máquina virtual con su dirección IP pública también puede emplearse como host administrativo seguro.

### <a name="static-public-ip-address"></a>Dirección IP pública estática

Como una dirección IP pública estática suele ser un recurso dedicado que se administra independientemente de las máquinas virtuales a las que se asigna, se aprovisiona en un grupo de recursos de red dedicado (en lugar de en el grupo de recursos del clúster de Service Fabric).  Cree una dirección IP pública estática con el nombre "staticIP1" en el mismo grupo de recursos *ExistingRG*, ya sea a través de Azure Portal o PowerShell:

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Plantilla de Service Fabric

Vamos a usar el archivo template.json de Service Fabric que puede descargarse del Portal antes de crear un clúster mediante el Asistente para el Portal estándar. También puede emplear una de las plantillas en la [Galería de plantillas](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric), como la del [clúster de cinco nodos de Service Fabric](https://azure.microsoft.com/en-us/documentation/templates/service-fabric-unsecure-cluster-5-node-1-nodetype/).

<a id="existingvnet"></a>
## <a name="existing-virtual-networksubnet"></a>Subred y red virtual existentes

[24 de enero de 2016: hay otro ejemplo de esto fuera del ámbito de Service Fabric en [https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet)]

1. Cambie el parámetro de subred al nombre de la subred existente y agregue dos nuevos parámetros para hacer referencia a la red virtual existente:

    ```
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```


2. Cambie la variable *vnetID* para que apunte a la red virtual existente:

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Quite *Microsoft.Network/virtualnetworks* de Recursos de manera que Azure no cree una red virtual nueva:

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

4. Comente la red virtual del atributo *dependsOn* de *Microsoft.Compute/virtualMachineScaleSets*, para que no tengamos que depender de crear una red virtual:

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. Implemente la plantilla:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Después de realizar la implementación, la red virtual debe incluir las nuevas máquinas virtuales del conjunto de escalados, y el tipo de nodo del conjunto de escalado de máquinas virtuales debe mostrar la subred y la red virtual existentes.  También puede acceder mediante RDP a la máquina virtual existente que ya estaba en la red virtual y hacer ping a las nuevas máquinas virtuales del conjunto de escalado:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Dirección IP pública estática

1. Agregue parámetros para el nombre del grupo de recursos de direcciones IP estáticas existente, el nombre y el FQDN:

    ```
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Quite el parámetro *dnsName*, ya que la dirección IP estática ya tiene uno:

    ```
    /*
    "dnsName": {
        "type": "string"
    }, 
    */
    ```

3. Agregue una variable para hacer referencia a la dirección IP estática existente:

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Quite *Microsoft.Network/publicIPAddresses* de *Recursos* para que Azure no cree una nueva dirección IP:

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Comente la dirección IP del atributo *dependsOn* de *Microsoft.Network/loadBalancers* para que no tengamos que depender de crear una nueva dirección IP:

    ```
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. Cambie el elemento *publicIPAddress* de *frontendIPConfigurations* en el recurso *Microsoft.Network/loadBalancers* para hacer referencia a la dirección de IP estática existente en lugar de a una recién creada:

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. Cambie *managementEndpoint* en el recurso *Microsoft.ServiceFabric/clusters* al FQDN de DNS de la dirección IP estática.  **Si está usando un clúster seguro, asegúrese de cambiar http:// a https://.** (Nota: esta instrucción es solo para los clústeres de Service Fabric.  Si está empleando un conjunto de escalado de máquinas virtuales, omita este paso):

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Implemente la plantilla:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Después de la implementación, verá que el equilibrador de carga está enlazado a la dirección IP estática pública del otro grupo de recursos. El punto de conexión del cliente de Service Fabric y el [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md) apuntan al FQDN de DNS de la dirección IP estática.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Equilibrador de carga solo interno

Este escenario reemplaza el equilibrador de carga externo en la plantilla predeterminada de Service Fabric con un equilibrador de carga solo interno.  Vea la sección anterior para entender las implicaciones del SFRP y Azure Portal.

1. Quite el parámetro *dnsName*, ya que no se necesita:

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Si lo desea, agregue un parámetro de dirección IP estática, si usa el método de asignación estático. En caso afirmativo, no hace falta que siga estos pasos:

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Quite *Microsoft.Network/publicIPAddresses* de Recursos para que Azure no cree una nueva dirección IP:

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Elimine la dirección IP del atributo *dependsOn* de *Microsoft.Network/loadBalancers* para que no tengamos que depender de crear una nueva dirección IP.  Agregue el atributo *dependsOn* de la red virtual, ya que el equilibrador de carga ahora depende de la subred de la red virtual:

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Cambie *frontendIPConfigurations* del equilibrador de carga de usar *publicIPAddress* a utilizar una subred y *privateIPAddress*, que emplea una dirección IP interna estática predefinida.  Podría usar na dirección IP dinámica eliminando el elemento *privateIPAddress* y cambiando el valor de *privateIPAllocationMethod* a "Dinámico".

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. En el recurso *Microsoft.ServiceFabric/clusters*, cambie *managementEndpoint* para que apunte a la dirección del equilibrador de carga interno.  **Si está usando un clúster seguro, asegúrese de cambiar http:// a https://.** (Nota: esta instrucción es solo para los clústeres de Service Fabric.  Si está empleando un conjunto de escalado de máquinas virtuales, omita este paso):

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Implemente la plantilla:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Después de la implementación, el equilibrador de carga usará la dirección IP privada estática 10.0.0.250. Si tiene otra máquina en esa misma red virtual, también puede acceder al punto de conexión interno del [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md) y verá que se conecta a uno de los nodos detrás del equilibrador de carga.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Equilibrador de carga interno y externo

En este escenario se selecciona el equilibrador de carga externo de un solo tipo de nodo y se agrega un equilibrador de carga interno adicional al mismo tipo de nodo.  Un puerto de back-end asociado a un grupo de direcciones de back-end solo puede asignarse a un equilibrador de carga. Por tanto, debe decidir qué equilibrador de carga debe tener los puertos de aplicación y qué equilibrador de carga debe tener los puntos de conexión de administración (puertos 19000 o 19080).  Tenga en cuenta las restricciones del SFRP anteriores si decide colocar los puntos de conexión de administración en el equilibrador de carga interno.  En este ejemplo se mantienen los puntos de conexión de administración en el equilibrador de carga externo, y se agrega un puerto de aplicación 80 y se coloca en el equilibrador de carga interno.

Si quiere un clúster de dos tipos de nodos, con uno en el equilibrador de carga externo y otro en el interno, solo tiene que seleccionar la plantilla de dos tipos de nodos creados mediante el Portal (que se incluye con dos equilibradores de carga) y cambiar el segundo equilibrador de carga a uno interno, tal y como se indica en la sección anterior Equilibrador de carga solo interno.

1. Agregue el parámetro de dirección IP estática de equilibrador de carga interno (vea las notas anteriores si desea direcciones IP dinámicas):

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Agregue el parámetro de puerto 80 de aplicación:

3. Agregue las versiones internas de las variables de red existentes mediante el método de copia y pega agregando "-Int" a la nomenclatura:

    ```
    /* Add internal LB networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* internal LB networking variables end */
    ```

4. Si ha empezado con la plantilla generada mediante el Portal con un puerto 80 de aplicación, la plantilla del Portal predeterminada agrega *AppPort1* (puerto 80) en el equilibrador de carga externo.  En este caso, elimine el parámetro de los sondeos y del elemento *loadBalancingRules* del equilibrador de carga externo para que pueda agregarlo al equilibrador de carga interno:

    ```
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe", 
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,,
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ], 
    "inboundNatPools": [
    ```

5. Agregue un segundo recurso *Microsoft.Network/loadBalancers*, que es muy parecido al equilibrador de carga interno creado en la sección anterior [Equilibrador de carga solo interno](#internallb), solo que usa las variables "-Int" del equilibrador de carga e implementa exclusivamente el puerto 80 de aplicación.  Este procedimiento quita también *inboundNatPools* con el fin de mantener los puntos de conexión RDP en el equilibrador de carga público: si desea acceder mediante RDP al equilibrador de carga interno, mueva *inboundNatPools* del equilibrador de carga externo a este de tipo interno.

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" LB variables */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add '-Internal' to name */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule, making sure to reference the "-Int" versions of the backendAddressPool, frontendIPConfiguration, and probe variables */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. En *networkProfile* del recurso *Microsoft.Compute/virtualMachineScaleSets*, agregue el grupo de direcciones de back-end internos:

    ```
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Implemente la plantilla:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Después de la implementación, verá dos equilibradores de carga en el grupo de recursos. Desplazándose por los equilibradores de carga observará que los puntos de conexión de administración y la dirección IP pública (puertos 19000 y 19080) están asignados a la dirección IP interna estática, y que la dirección IP interna estática y el punto de conexión de aplicación (puerto 80) están asignados al equilibrador de carga interno. Los dos equilibradores de carga usan el mismo grupo de back-end del conjunto de escalado de máquinas virtuales.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha obtenido información sobre cómo integrar los clústeres de Service Fabric con las características de red de Azure, avance y [cree uno](service-fabric-cluster-creation-via-arm.md). 

