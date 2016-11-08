---
title: Creación de un equilibrador de carga interno mediante la CLI de Azure en Resource Manager | Microsoft Docs
description: Obtenga información sobre cómo crear un equilibrador de carga interno mediante la CLI de Azure en Resource Manager
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2016
ms.author: sewhee

---
# Creación de un equilibrador de carga interno mediante la CLI de Azure
[!INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[classic deployment model](load-balancer-get-started-ilb-classic-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## Implementación de la solución mediante la CLI de Azure
Los pasos siguientes muestran cómo crear un equilibrador de carga orientado a Internet mediante Azure Resource Manager con la CLI. Con Azure Resource Manager, cada recurso se crea y configura de forma individual, y luego se juntan para crear un recurso.

Para implementar un equilibrador de carga, debe crear y configurar los objetos siguientes:

* **Configuración de direcciones IP de front-end**: contiene direcciones IP públicas para el tráfico de red entrante
* **Grupo de direcciones de back-end**: contiene interfaces de red (NIC) que permiten a las máquinas virtuales recibir tráfico de red del equilibrador de carga
* **Reglas de equilibrio de carga**: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto del grupo de direcciones de back-end
* **Reglas NAT de entrada**: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto de una máquina virtual específica en el grupo de direcciones de back-end
* **Sondeos**: contiene los sondeos de estado que se usan para comprobar la disponibilidad de las instancias de las máquinas virtuales del grupo de direcciones de back-end

Para más información, consulte [Compatibilidad de Azure Resource Manager con el equilibrador de carga](load-balancer-arm.md).

## Configuración de la CLI para utilizar Resource Manager
1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](../xplat-cli-install.md). Siga las instrucciones hasta el punto de seleccionar su cuenta y su suscripción a Azure.
2. Ejecute el comando **azure config mode** para cambiar al modo de Resource Manager, como se muestra a continuación:
   
        azure config mode arm
   
    Resultado esperado:
   
        info:    New mode is arm

## Creación de un equilibrador de carga interno paso a paso
1. Inicie sesión en Azure.
   
        azure login
   
    Cuando se le solicite, escriba sus credenciales de Azure.
2. Cambie las herramientas de comandos al modo de Azure Resource Manager.
   
        azure config mode arm

## Crear un grupo de recursos
Todos los recursos de Azure Resource Manager están asociados a un grupo de recursos. Si todavía no lo ha hecho, cree un grupo de recursos.

    azure group create <resource group name> <location>

## Crear un conjunto de equilibrador de carga interno
1. Creación de un conjunto de equilibrador de carga interno
   
    En el siguiente escenario, se crea un grupo de recursos denominado nrprg en la región Este de EE. UU.
   
        azure network lb create -n nrprg -l eastus
   
   > [!NOTE]
   > Todos los recursos de un equilibrador de carga interno, como redes virtuales y subredes de la red virtual, deben estar en el mismo grupo de recursos y en la misma región.
   > 
   > 
2. Cree una dirección IP de front-end para el equilibrador de carga interno.
   
    La dirección IP que usa debe estar dentro del intervalo de subred de la red virtual.
   
        azure network lb frontend-ip create -g nrprg -l ilbset -n feilb -a 10.0.0.7 -e nrpvnetsubnet -m nrpvnet
   
    Parámetros usados:
   
   * **-g**: grupo de recursos
   * **-l**: nombre del conjunto de equilibrador de carga interno
   * **-n**: nombre de la dirección IP de front-end
   * **-a**: dirección IP privada dentro del intervalo de subred
   * **-e**: nombre de subred
   * **-m**: nombre de la red virtual
3. Cree el grupo de direcciones de back-end.
   
        azure network lb address-pool create -g nrprg -l ilbset -n beilb
   
    Parámetros usados:
   
   * **-g**: grupo de recursos
   * **-l**: nombre del conjunto de equilibrador de carga interno
   * **-n**: nombre del grupo de direcciones de back-end
     
     Después de definir una dirección IP de front-end y un grupo de direcciones de back-end, puede crear reglas de equilibradores de carga, reglas NAT de entrada y sondeos de estado personalizados.
4. Cree una regla de equilibrador de carga para el equilibrador de carga interno.
   
    Al seguir los pasos anteriores, el comando crea una regla de equilibrador de carga para escuchar al puerto 1433 en el grupo de servidores front-end y enviar tráfico de red con equilibrio de carga al grupo de direcciones de back-end, usando también el puerto 1433.
   
        azure network lb rule create -g nrprg -l ilbset -n ilbrule -p tcp -f 1433 -b 1433 -t feilb -o beilb
   
    Parámetros usados:
   
   * **-g**: grupo de recursos
   * **-l**: nombre del conjunto de equilibrador de carga interno
   * **-n**: nombre de la regla de equilibrador de carga
   * **-p**: protocolo utilizado para la regla
   * **-f**: puerto que escucha el tráfico de red entrante en el front-end del equilibrador de carga
   * **-b**: puerto que recibe el tráfico de red en el grupo de direcciones de back-end
5. Crear reglas NAT de entrada.
   
    Las reglas NAT de entrada se usan para crear puntos de conexión en un equilibrador de carga que se dirijan a una instancia de máquina virtual específica. En los pasos anteriores se crearon dos reglas NAT para escritorio remoto.
   
        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule1 -p TCP -f 5432 -b 3389
   
        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule2 -p TCP -f 5433 -b 3389
   
    Parámetros usados:
   
   * **-g**: grupo de recursos
   * **-l**: nombre del conjunto de equilibrador de carga interno
   * **-n**: nombre de la regla NAT entrante
   * **-p**: protocolo utilizado para la regla
   * **-f**: puerto que escucha el tráfico de red entrante en el front-end del equilibrador de carga
   * **-b**: puerto que recibe el tráfico de red en el grupo de direcciones de back-end
6. Crear sondeos de estado del equilibrador de carga.
   
    Los sondeos de estado comprueban todas las instancias de máquina virtual para asegurarse de que pueden enviar tráfico de red. La instancia de máquina virtual con comprobaciones de sondeo incorrectas se elimina del equilibrador de carga hasta que vuelve a estar en línea y una comprobación de sondeo determina que es correcta.
   
        azure network lb probe create -g nrprg -l ilbset -n ilbprobe -p tcp -i 300 -c 4
   
    Parámetros usados:
   
   * **-g**: grupo de recursos
   * **-l**: nombre del conjunto de equilibrador de carga interno
   * **-n**: nombre del sondeo de estado
   * **-p**: protocolo utilizado por el sondeo de estado
   * **-i**: intervalo de sondeo en segundos
   * **-c**: número de comprobaciones

    >[AZURE.NOTE] La Plataforma Microsoft Azure utiliza una dirección IPv4 estática enrutable públicamente para una variedad de escenarios de administración. La dirección IP es 168.63.129.16. Ningún firewall debe bloquear esta dirección IP, ya que puede causar un comportamiento inesperado. Con respecto al equilibrio de carga interno de Azure, esta dirección IP la usan los sondeos de supervisión del equilibrador de carga para determinar el estado de mantenimiento de las máquinas virtuales en un conjunto con equilibrio de carga. Si se usa un grupo de seguridad de red para restringir el tráfico a máquinas virtuales de Azure en un conjunto de carga equilibrada internamente o se aplica a una subred de la red virtual, asegúrese de agregar una regla de seguridad de red para permitir el tráfico desde 168.63.129.16.

## Cree tarjetas NIC
Debe crear tarjetas NIC (o modificar las existentes) y asociarlas a las reglas NAT, las reglas de equilibrador de carga y los sondeos.

1. Cree una NIC llamada *lb-nic1-be* y asóciela a la regla NAT *rdp1* y el grupo de direcciones de back-end *beilb*.
   
        azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus
   
    Parámetros:
   
   * **-g**: nombre del grupo de recursos
   * **-n**: nombre del recurso de NIC
   * **--subnet-name**: nombre de la subred
   * **--subnet-vnet-name**: nombre de la red virtual
   * **-d**: id. del grupo de recursos de back-end, que empieza por /subscription/{subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool>
   * **-e**: id. de la regla NAT que se asociará al recurso NIC. Empieza por /subscriptions/####################################/resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name>

Resultado esperado:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

1. Cree una NIC llamada *lb-nic2-be* y asóciela a la regla NAT *rdp2* y el grupo de direcciones de back-end *beilb*.
   
        azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus
2. Cree una máquina virtual llamada *DB1* y asóciela a la NIC llamada *lb-nic1-be*. Se crea una cuenta de almacenamiento llamada *web1nrp* antes de ejecutarse el comando que aparece a continuación:
   
        azure vm create --resource-group nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
   
   > [!IMPORTANT]
   > Las máquinas virtuales en un equilibrador de carga deben estar en el mismo conjunto de disponibilidad. Use `azure availset create` para crear un conjunto de disponibilidad.
   > 
   > 
3. Cree una máquina virtual llamada *DB2* y asóciela a la NIC llamada *lb-nic2-be*. Se creó una cuenta de almacenamiento llamada *web1nrp* antes de ejecutar el comando que aparece a continuación.
   
        azure vm create --resource-group nrprg --name DB2 --location eastus --vnet-    name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Eliminar un equilibrador de carga
Para quitar un equilibrador de carga, use el siguiente comando:

    azure network lb delete -g nrprg -n ilbset

En este ejemplo, **nrprg** es el grupo de recursos e **ilbset** es el nombre del equilibrador de carga interno.

## Pasos siguientes
[Configurar un modo de distribución del equilibrador de carga mediante la afinidad IP de origen](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0928_2016-->