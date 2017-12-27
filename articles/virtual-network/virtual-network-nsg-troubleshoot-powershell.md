---
title: "Solución de problemas de los grupos de seguridad de red - PowerShell | Microsoft Docs"
description: "Obtenga información acerca de cómo solucionar problemas de los grupos de seguridad de red en el modelo de implementación de Azure Resource Manager utilizando Azure PowerShell."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: d556f2d6d37956c3b3bca2a2905b2c947e6be0df
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Solución de problemas de los grupos de seguridad de red utilizando Azure PowerShell
> [!div class="op_single_selector"]
> * 
            [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Si ha configurado grupos de seguridad de red (NSG) en la máquina virtual (VM) y está experimentando problemas de conectividad en la misma, este artículo proporciona información general sobre las funcionalidades de diagnóstico para los NSG que puede ayudarle solucionar el problema.

Los NSG le permiten controlar los tipos de tráfico de flujo de entrada y salida de las máquinas virtuales. Los NSG pueden aplicarse a subredes en Azure Virtual Network (VNet), interfaces de red (NIC) o a ambas. Las reglas vigentes aplicadas a una interfaz de red son una agregación de las reglas que existen en los NSG que se aplican a una NIC y a la subred a la que está conectada. Las reglas en estos NSG a veces pueden entrar en conflicto entre sí y afectar la conectividad de red de la máquina virtual.  

Puede ver todas las reglas de seguridad vigentes de sus NSG, tal y como se aplican a las NIC de la máquina virtual. Este artículo muestra cómo solucionar problemas de conectividad de máquina virtual utilizando estas reglas en el modelo de implementación de Azure Resource Manager. Si no está familiarizado con conceptos de VNet y NSG, lea los artículos de información general sobre [redes virtuales](virtual-networks-overview.md) y [grupos de seguridad de red](virtual-networks-nsg.md).

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Uso de las reglas de seguridad vigentes para solucionar problemas de flujo de tráfico de máquinas virtuales
El siguiente escenario es un ejemplo de un problema de conexión común:

Una máquina virtual denominada *VM1* forma parte de una subred *Subnet1* dentro de una red virtual denominada *WestUS VNet1*. Se produce un error al intentar conectar la máquina virtual mediante RDP a través del puerto TCP 3389. Los NSG se aplican en ambas la NIC *VM1 NIC1* y la subred *subred1*. Se permite el tráfico al puerto TCP 3389 en el NSG asociado con la interfaz de red *VM1-NIC1*; sin embargo, el ping TCP al puerto 3389 de VM1 produce un error.

Aunque este ejemplo utiliza el puerto TCP 3389, los pasos siguientes pueden utilizarse para determinar errores de conexión entrante y saliente a través de cualquier puerto.

## <a name="detailed-troubleshooting-steps"></a>Pasos de la solución de problemas detallada
Complete los pasos siguientes para solucionar problemas de los NSG para una máquina virtual:

1. Inicie una sesión de Azure PowerShell e inicie sesión en Azure. Si no está familiarizado con el uso de Azure PowerShell, lea el artículo [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview) . La cuenta debe tener asignada la operación *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* para la interfaz de red. Para más información sobre cómo asignar operaciones a las cuentas, consulte [Creación de roles personalizados para el control de acceso basado en rol de Azure](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Escriba el siguiente comando para devolver todas las reglas de NSG que se aplican a una NIC denominada *VM1-NIC1* en el grupo de recursos *RG1*:
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Si no conoce el nombre de una NIC, escriba el siguiente comando para recuperar los nombres de todas las NIC en un grupo de recursos: 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    El texto siguiente es un ejemplo del resultado de reglas vigentes devuelto para la NIC *VM1 NIC1* :
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    Tenga en cuenta la siguiente información en el resultado:
   
   * Hay dos secciones **NetworkSecurityGroup**: una está asociada a una subred (*Subnet1*) y otra está asociada a una NIC (*VM1- NIC1*). En este ejemplo, se ha aplicado un NSG a cada una.
   * **Asociación** muestra el recurso (subred o NIC) con el que está asociado un NSG determinado. Si el recurso GSN se mueve o disocia inmediatamente antes de ejecutar este comando, puede que tenga que esperar unos segundos para que los cambios se vean reflejados en los resultados del comando. 
   * Los nombres de regla que vienen precedidos por *defaultSecurityRules*: cuando se crea un NSG, se crean varias reglas de seguridad predeterminadas dentro de él. Las reglas predeterminadas no se pueden quitar, pero pueden reemplazarse con reglas de prioridad más alta.
     Para más información acerca de las reglas de seguridad predeterminadas de NSG, consulte el artículo de [información general sobre los grupos de seguridad de red](virtual-networks-nsg.md#default-rules) .
   * **ExpandedAddressPrefix** expande los prefijos de dirección para las etiquetas predeterminadas de NSG. Las etiquetas representan varios prefijos de direcciones. La expansión de las etiquetas puede ser útil al solucionar problemas de conectividad de máquinas virtuales con de prefijos de dirección específicos. Por ejemplo, con el emparejamiento de red virtual, la etiqueta VIRTUAL_NETWORK se expande para mostrar prefijos emparejados de red virtual en los resultados anteriores.
     
     > [!NOTE]
     > El comando solo muestra reglas vigentes si un NSG está asociado a una subred, una NIC o ambas. Una máquina virtual puede tener varias NIC con diferentes NSG aplicados. Cuando esté intentando solucionar un problema, ejecute el comando para cada NIC.
     > 
     > 
3. Para facilitar el filtrado de un mayor número de reglas de NSG, escriba los siguientes comandos para seguir solucionando el problema: 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    Se aplica un filtro para el tráfico RDP (puerto TCP 3389) a la vista de cuadrícula, como se muestra en la siguiente imagen:
   
    ![Lista de reglas](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. Como puede ver en la vista de cuadrícula, hay reglas tanto de denegación como de permiso para RDP. El resultado del paso 2 muestra que la regla *DenyRDP* se encuentra en el NSG que se aplica a la subred. Para las reglas de entrada, los NSG que se aplican a la subred se procesan primero. Si se encuentra una coincidencia, el NSG que se aplica a la interfaz de red no se procesa. En este caso, la regla *DenyRDP* de la subred bloquea RDP a la máquina virtual (**VM1**).
   
   > [!NOTE]
   > Una máquina virtual puede tener varias NIC conectadas. Cada una de ellas puede estar conectada a una subred diferente. Dado que los comandos en los pasos anteriores se ejecutan en una NIC, es importante asegurarse de que se especifica la NIC con la que se tiene el error de conectividad. Si no está seguro, siempre puede ejecutar los comandos con cada NIC conectada a la máquina virtual.
   > 
   > 
5. A RDP en VM1, cambiar la regla *Deny RDP (3389)* a *Allow RDP(3389)* en el grupo **Subnet1-NSG**. Compruebe que el puerto TCP 3389 está abierto abriendo una conexión RDP con la máquina virtual o usando la herramienta PsPing. Puede obtener más información sobre PsPing consultando la [página de descarga de PsPing](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    Puede agregar o quitar reglas de un NSG utilizando la información en el resultado del comando siguiente:
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>Consideraciones
Tenga en cuenta los puntos siguientes cuando tenga que solucionar problemas de conectividad:

* Las reglas predeterminadas de NSG bloquean el acceso de entrada desde Internet y solo permiten el tráfico entrante de la red virtual. Las reglas deben agregarse explícitamente para permitir el acceso de entrada desde Internet, según sea necesario.
* Si no hay ninguna regla de seguridad de NSG que sea la causa de un error de conectividad de una red de máquina virtual, el problema puede ser debido a:
  * Software de firewall que se esté ejecutando en el sistema operativo de la máquina virtual
  * Rutas configuradas para dispositivos virtuales o para el tráfico local. El tráfico de Internet puede redirigirse a la red local a través de tunelización forzada. Una conexión RDP o SSH desde Internet a su máquina virtual puede no funcionar con esta configuración, dependiendo de cómo el hardware de red local controle este tráfico. Consulte el artículo sobre [solución de problemas de rutas](virtual-network-routes-troubleshoot-powershell.md) para obtener información sobre cómo diagnosticar los problemas de enrutamiento que pueden estar impidiendo el flujo de tráfico de entrada y salida de la máquina virtual. 
* Si ha emparejado redes virtuales, de forma predeterminada, la etiqueta VIRTUAL_NETWORK se expandirá automáticamente para incluir prefijos para redes virtuales emparejadas. Puede ver estos prefijos en la lista **ExpandedAddressPrefix** para solucionar los problemas relacionados con la conectividad de emparejamiento de rede virtuales. 
* Las reglas de seguridad vigentes solo se muestran si hay un NSG asociado con la NIC o subred de máquina virtual. 
* Si no hay ningún NSG asociado a la NIC o subred y tiene una dirección IP pública asignada a la máquina virtual, todos los puertos estarán abiertos para el acceso entrante y saliente. Si la máquina virtual tiene una dirección IP pública, es muy recomendable aplicar los NSG a la subred o NIC.  

