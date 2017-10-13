---
title: "Direcciones IP públicas a nivel de instancia (clásica) de Azure mediante | Microsoft Docs"
description: "Comprenda las direcciones públicas de nivel de instancia (ILPIP) y cómo administrarlas con PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
ms.openlocfilehash: 773043f2841ec7539b0d49357dec6bcb9f4f78a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="instance-level-public-ip-classic-overview"></a>Introducción a las direcciones IP públicas a nivel de instancia (clásica)
Una IP pública de nivel de instancia (ILPIP) es una dirección IP pública que se puede asignar directamente a la máquina virtual o a la instancia de rol de Cloud Services, en lugar de a un servicio en la nube en el que reside la máquina virtual o la instancia de rol. Un ILPIP no reemplaza a la dirección IP virtual (VIP) que está asignada al servicio en la nube. Es más bien una dirección IP adicional que puede usar para conectarse directamente a la máquina virtual o instancia de rol.

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artículo trata del modelo de implementación clásico. Microsoft recomienda crear máquinas virtuales a través de Resource Manager. Asegúrese de que comprende cómo funcionan las [direcciones IP](virtual-network-ip-addresses-overview-classic.md) en Azure.

![Diferencia entre ILPIP y VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Como se muestra en la Figura 1, se accede al servicio en la nube mediante una VIP, mientras que normalmente se accede a las máquinas virtuales individuales mediante VIP:&lt;número de puerto&gt;. Al asignar una ILPIP a una máquina virtual específica, se puede tener acceso directamente a esa máquina virtual mediante esa dirección IP.

Cuando se crea un servicio en la nube en Azure, los registros de DNS A correspondientes se crean automáticamente para permitir el acceso al servicio mediante un nombre de dominio completo (FQDN) en lugar de usar la VIP real. Se produce el mismo proceso para la ILPIP, lo que permite el acceso a la máquina virtual o instancia de rol mediante el FQDN en lugar de la ILPIP. Por ejemplo, si crea un servicio en la nube denominado *contosoadservice* y configura un rol web denominado *contosoweb* con dos instancias, Azure registra los siguientes registros A para las instancias:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> Solo puede asignar una ILPIP para cada máquina virtual o instancia de rol. Puede usar hasta 5 ILPIP por suscripción. Las ILPIP no se admiten para las máquinas virtuales de varias NIC.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>¿Por qué debo solicitar una ILPIP?
Si desea poder conectarse a la máquina virtual o a la instancia de rol mediante una dirección IP asignada directamente a ella, en lugar de usar la VIP:&lt;número de puerto&gt; del servicio en la nube, solicite una ILPIP para la máquina virtual o la instancia de rol.

* **FTP activo**: mediante la asignación de una ILPIP a una máquina virtual, puede recibir tráfico en cualquier puerto. Los puntos de conexión no son necesarios para que la máquina virtual reciba tráfico.  Consulte la información general que se da en (https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview)[FTP Protocol Overview] sobre el protocolo FTP para conocer los detalles sobre este último.
* **IP de salida**: el tráfico de salida procedente de la máquina virtual se asigna a la ILPIP como origen y, de esta forma, esta última identifica de forma exclusiva la máquina virtual ante entidades externas.

> [!NOTE]
> Antes, a la dirección ILPIP se le conocía como dirección IP pública (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Administración de una ILPIP para una máquina virtual
Las tareas siguientes le permiten crear, asignar y quitar ILPIP de máquinas virtuales:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Solicitud de una ILPIP durante la creación de máquinas virtuales mediante PowerShell
El script de PowerShell siguiente crea un servicio en la nube denominado *FTPService*, recupera una imagen de Azure, crea una máquina virtual denominada *FTPInstance* usando la imagen recuperada, establece la máquina virtual para que use una ILPIP y agrega la máquina virtual al nuevo servicio:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Recuperación de información de ILPIP para una máquina virtual
Para ver la información de ILPIP para la máquina virtual creada con el script anterior, ejecute el siguiente comando de PowerShell y observe los valores de *PublicIPAddress* y *PublicIPName*:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Resultado esperado:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Supresión de una ILPIP de una máquina virtual
Para quitar la ILPIP agregada a la máquina virtual en el script anterior, ejecute el siguiente comando de PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Agregación de una ILPIP a una máquina virtual existente
Para agregar una ILPIP a la máquina virtual creada con el script anterior, ejecute el siguiente comando:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Administración de una ILPIP para una instancia de rol de Cloud Services

Para agregar una ILPIP a una instancia de rol de Cloud Services, complete los pasos siguientes:

1. Descargue el archivo .cscfg para el servicio en la nube siguiendo los pasos descritos en el artículo sobre [cómo configurar Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg).
2. Actualice el archivo .cscfg y agregue el elemento `InstanceAddress`. El ejemplo siguiente agrega una ILPIP denominada *MyPublicIP* a una instancia de rol denominada *WebRole1*: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Cargue el archivo .cscfg para el servicio en la nube siguiendo los pasos descritos en el artículo sobre [cómo configurar Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg).

## <a name="next-steps"></a>Pasos siguientes
* Descubra cómo funcionan las [direcciones IP](virtual-network-ip-addresses-overview-classic.md) en el modelo de implementación clásica.
* Obtenga información sobre las [direcciones IP reservadas](virtual-networks-reserved-public-ip.md).
