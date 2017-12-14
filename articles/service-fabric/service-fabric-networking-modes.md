---
title: "Configuración de modos de redes para servicios de contenedor de Azure Service Fabric | Microsoft Docs"
description: Aprenda a configurar los diferentes modos de red que admite Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: f8e3af4e183952aaac5a8320966aab035b90a1a7
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="service-fabric-container-networking-modes"></a>Modos de redes de contenedor de Service Fabric

Un clúster de Azure Service Fabric de servicios de contenedor usa de forma predeterminada el modo de red **nat**. Cuando más de un servicio de contenedor escucha en el mismo puerto y se usa el modo nat, pueden producirse errores de implementación. Para permitir que varios servicios de contenedor escuchen en el mismo puerto, Service Fabric ofrece el modo de red **abierto** (versión 5.7 y posterior). En modo abierto, cada servicio de contenedor tiene una dirección IP interna asignada dinámicamente que admite que varios servicios escuchen en el mismo puerto.  

Si tiene un servicio de contenedor con un punto de conexión estático en el manifiesto del servicio, puede crear y eliminar nuevos servicios mediante el modo abierto sin errores de implementación. El mismo archivo docker-compose.yml se puede usar también con asignaciones de puertos estáticos para crear varios servicios.

Cuando un servicio de contenedor se reinicia o se mueve a otro nodo del clúster, la dirección IP cambia. Por esta razón, no se recomienda usar la dirección IP asignada dinámicamente para detectar servicios de contenedor. Solo se debe usar para ello el servicio de nombres de Service Fabric o el servicio DNS. 

>[!WARNING]
>Azure permite hasta un total de 4096 direcciones IP por red virtual. La suma del número de nodos y el número de instancias de servicio de contenedor (que usan el modo abierto) no puede superar 4096 direcciones IP en una red virtual. En escenarios de alta densidad, se recomienda el modo de red nat.
>

## <a name="set-up-open-networking-mode"></a>Configuración del modo de red abierto

1. Configure la plantilla de Azure Resource Manager. En la sección **fabricSettings**, habilite el servicio DNS y el proveedor de IP: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
                    }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```

2. Configure la sección de perfil de red para permitir la configuración de varias direcciones IP en cada nodo del clúster. En el ejemplo siguiente se configuran cinco direcciones IP por nodo de un clúster de Service Fabric de Windows o Linux. Puede tener cinco instancias de servicio que escuchen en el puerto en cada nodo.

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. Solo en clústeres de Windows, configure una regla de grupo de seguridad de red (NSG) de Azure que abra el puerto UDP/53 para la red virtual con los siguientes valores:

   |Configuración |Valor | |
   | --- | --- | --- |
   |Prioridad |2000 | |
   |Nombre |Custom_Dns  | |
   |Origen |VirtualNetwork | |
   |Destino | VirtualNetwork | |
   |Servicio | DNS (UDP/53) | |
   |Acción | PERMITIR  | |
   | | |

4. Especifique el modo de red en el manifiesto de aplicación para cada servicio `<NetworkConfig NetworkType="Open">`. El modo **abierto** da lugar a que el servicio obtenga una dirección IP dedicada. Si no se especifica un modo, el servicio adopta como valor predeterminado el modo **nat**. En el siguiente ejemplo de manifiesto, los servicios `NodeContainerServicePackage1` y `NodeContainerServicePackage2` pueden escuchar en el mismo puerto (ambos servicios escuchan en `Endpoint1`). Cuando se especifica el modo de red abierto, no se pueden especificar configuraciones de `PortBinding`.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Puede mezclar y combinar diferentes modos de redes en todos los servicios dentro de una aplicación para un clúster de Windows. Algunos servicios pueden usar el modo abierto y otros el modo nat. Cuando un servicio está configurado para usar el modo nat, el puerto en el que escucha el servicio debe ser único.

    >[!NOTE]
    >En clústeres de Linux, no se admite la combinación de modos de red para diferentes servicios. 
    >

## <a name="next-steps"></a>Pasos siguientes
* [Entender el modelo de aplicación de Service Fabric](service-fabric-application-model.md)
* [Aprenda más sobre los recursos del manifiesto de servicio de Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [Implementación de un contenedor de Windows en Service fabric en Windows Server 2016](service-fabric-get-started-containers.md)
* [Implementación de un contenedor de Docker en Service Fabric en Linux](service-fabric-get-started-containers-linux.md)
