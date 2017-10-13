---
title: "Establecimiento de una dirección IP privada interna estática: máquina virtual de Azure (clásica)"
description: "Descripción y administración de direcciones IP estáticas internas (DIP)"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
ms.openlocfilehash: cf9ee59ca4e44ed01836c2efb1f4df5f073bf6e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Establecimiento de una dirección IP privada interna estática mediante PowerShell (modelo clásico)
En la mayoría de los casos, no necesitará especificar una dirección IP interna estática para la máquina virtual. Las máquinas virtuales de una red virtual recibirán automáticamente una dirección IP interna dentro de un intervalo que especifique. Pero en algunos casos, tiene sentido especificar una dirección IP estática para una máquina virtual concreta. Por ejemplo, si la máquina virtual va a ejecutar DNS o será un controlador de dominio. Una dirección IP interna estática permanece con la máquina virtual incluso a través de un estado de detención o desaprovisionamiento. 

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el [modelo de Resource Manager](virtual-networks-static-private-ip-arm-ps.md).
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Comprobación de que una dirección IP concreta está disponible
Para comprobar si la dirección IP *10.0.0.7* está disponible en una red virtual denominada *TestVnet*, ejecute el siguiente comando de PowerShell y compruebe el valor de *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Si desea probar el comando anterior en un entorno seguro, siga las instrucciones de [Creación de una red virtual (clásica)](virtual-networks-create-vnet-classic-pportal.md) para crear una red virtual denominada *TestVnet* y asegurarse de que utiliza el espacio de direcciones *10.0.0.0/8*.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Especificación de una dirección IP interna estática al crear una máquina virtual
El siguiente script de PowerShell crea un servicio en la nube denominado *TestService*, recupera una imagen de Azure, crea una máquina virtual denominada *TestVM* en el nuevo servicio en la nube con la imagen recuperada, establece que la máquina virtual esté en una subred llamada *Subnet-1* y establece *10.0.0.7* como dirección IP interna estática para la máquina virtual:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Recuperación de la información de la IP interna estática para una máquina virtual
Para ver la información de la IP interna estática para la máquina virtual creada con este script, ejecute el siguiente comando de PowerShell y observe los valores de *IpAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Eliminación de una dirección IP interna estática de una máquina virtual
Para quitar la dirección IP interna estática agregada a la máquina virtual en el script anterior, ejecute el siguiente comando de PowerShell:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Incorporación de una dirección IP interna estática a una máquina virtual existente
Para agregar una dirección IP interna estática a la máquina virtual creada con el script anterior, ejecute el siguiente comando:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Pasos siguientes
[IP reservada](virtual-networks-reserved-public-ip.md)

[IP pública a nivel de instancia (ILIP)](virtual-networks-instance-level-public-ip.md)

[API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx)

