---
title: "Ejemplos de configuración de enrutadores de cliente ExpressRoute | Microsoft Docs"
description: "Esta página ofrece ejemplos de configuración de enrutador para enrutadores Cisco y Juniper."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 032e584dc5abf59e9e3e8d80673b402f1fbf721b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Ejemplos de configuración de enrutadores para configurar y administrar enrutamiento
Esta página ofrece ejemplos de configuración de enrutamiento e interfaces para enrutadores Cisco serie IOS-XE y Juniper serie MX. Solo pretenden ser ejemplos de carácter informativo y no se deben usar tal cual. Puede trabajar con el proveedor para elaborar las configuraciones adecuadas para la red. 

> [!IMPORTANT]
> Los ejemplos de esta página pretenden tener un carácter meramente informativo. Debe trabajar con los equipos técnico y de ventas del proveedor y su equipo de red para elaborar las configuraciones adecuadas que satisfagan sus necesidades. Microsoft no dará soporte técnico en problemas relacionados con las configuraciones que aparecen en esta página. Debe ponerse en contacto con el fabricante del dispositivo para problemas de soporte técnico.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Configuración de MSS de MTU y TCP en las interfaces de enrutador
* La MTU de la interfaz de ExpressRoute es 1500, que es la MTU predeterminada típica de una interfaz Ethernet en un enrutador. A menos que el enrutador tenga una MTU diferente de forma predeterminada, no es necesario especificar un valor en la interfaz del enrutador.
* A diferencia de Azure VPN Gateway, no es necesario especificar el MSS de TCP para un circuito de ExpressRoute.

Los ejemplos de configuración de enrutadores siguientes se aplican a todos los emparejamientos. Si desea más información, vea [Emparejamientos de ExpressRoute](expressroute-circuit-peerings.md) y [Requisitos de NAT de ExpressRoute](expressroute-routing.md).


## <a name="cisco-ios-xe-based-routers"></a>Enrutadores basados en Cisco IOS-XE
Los ejemplos en esta sección se aplican a cualquier enrutador que ejecute la familia del SO IOS-XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configuración de interfaces y subinterfaces
Necesitará una subinterfaz por emparejamiento en cada enrutador que conecte a Microsoft. Una subinterfaz puede identificarse con un identificador de VLAN o un par apilado de identificadores de VLAN y una dirección IP.

**Definición de interfaz Dot1Q**

Este ejemplo ofrece la definición de subinterfaz de una subinterfaz con un solo identificador de VLAN. El identificador de VLAN es único para cada emparejamiento. El último octeto de la dirección IPv4 siempre será un número impar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definición de interfaz QinQ**

Este ejemplo ofrece la definición de subinterfaz de una subinterfaz con dos identificadores de VLAN. El identificador de VLAN externo (s-tag), si se usa, es el mismo en todos los emparejamientos. El identificador de VLAN interno (c-tag) es único para cada emparejamiento. El último octeto de la dirección IPv4 siempre será un número impar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Configuración de sesiones eBGP
Debe configurar una sesión BGP con Microsoft para cada emparejamiento. En el siguiente ejemplo permite configurar una sesión BGP con Microsoft. Si la dirección IPv4 usada para la subinterfaz fue a.b.c.d, la dirección IP del vecino BGP (Microsoft) será a.b.c.d+1. El último octeto de la dirección IPv4 del vecino BGP siempre será un número par.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configuración de prefijos para anunciarse a través de la sesión BGP
Puede configurar el enrutador para anunciar prefijos seleccionados a Microsoft. Puede hacerlo usando el ejemplo siguiente.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Asignaciones de ruta
Puede usar asignaciones de ruta y listas de prefijo para filtrar prefijos propagados en la red. Puede usar el ejemplo siguiente para realizar la tarea. Asegúrese de que tiene el programa de instalación de listas de prefijos adecuado.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Enrutadores Juniper serie MX
Los ejemplos en esta sección se aplican a los enrutadores Juniper serie MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configuración de interfaces y subinterfaces

**Definición de interfaz Dot1Q**

Este ejemplo ofrece la definición de subinterfaz de una subinterfaz con un solo identificador de VLAN. El identificador de VLAN es único para cada emparejamiento. El último octeto de la dirección IPv4 siempre será un número impar.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Definición de interfaz QinQ**

Este ejemplo ofrece la definición de subinterfaz de una subinterfaz con dos identificadores de VLAN. El identificador de VLAN externo (s-tag), si se usa, es el mismo en todos los emparejamientos. El identificador de VLAN interno (c-tag) es único para cada emparejamiento. El último octeto de la dirección IPv4 siempre será un número impar.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Configuración de sesiones eBGP
Debe configurar una sesión BGP con Microsoft para cada emparejamiento. En el siguiente ejemplo permite configurar una sesión BGP con Microsoft. Si la dirección IPv4 usada para la subinterfaz fue a.b.c.d, la dirección IP del vecino BGP (Microsoft) será a.b.c.d+1. El último octeto de la dirección IPv4 del vecino BGP siempre será un número par.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configuración de prefijos para anunciarse a través de la sesión BGP
Puede configurar el enrutador para anunciar prefijos seleccionados a Microsoft. Puede hacerlo usando el ejemplo siguiente.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. Asignaciones de ruta
Puede usar asignaciones de ruta y listas de prefijo para filtrar prefijos propagados en la red. Puede usar el ejemplo siguiente para realizar la tarea. Asegúrese de que tiene el programa de instalación de listas de prefijos adecuado.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Pasos siguientes
Consulte [P+F de ExpressRoute](expressroute-faqs.md) para obtener más detalles.

