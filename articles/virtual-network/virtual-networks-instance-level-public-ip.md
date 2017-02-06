---
title: "Dirección IP pública a nivel de instancia (clásica) mediante PowerShell | Microsoft Docs"
description: "Descripción de ILPIP (PIP) y de cómo administrarlas mediante PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: f1919d84cf912e184d87a5eeb462355e8ee3da07


---
# <a name="instance-level-public-ip-classic-overview"></a>Introducción a las direcciones IP públicas a nivel de instancia (clásica)
Una IP pública de nivel de instancia (ILPIP) es una dirección IP pública que se puede asignar directamente a la máquina virtual o a la instancia de rol, en lugar de a un servicio en la nube en el que reside la máquina virtual o la instancia de rol. Esta no reemplaza a la dirección VIP (IP virtual) que está asignada al servicio en la nube. Es más bien una dirección IP adicional que puede usar para conectarse directamente a la máquina virtual o instancia de rol.

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen Resource Manager. Asegúrese de que comprende cómo funcionan las [direcciones IP](virtual-network-ip-addresses-overview-classic.md) en Azure.

![Diferencia entre ILPIP y VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Como se muestra en la Figura 1, se accede al servicio en la nube mediante una VIP, mientras que normalmente se accede a las máquinas virtuales individuales mediante VIP:&lt;número de puerto&gt;. Al asignar una ILPIP a una máquina virtual específica, se puede tener acceso directamente a esa máquina virtual mediante esa dirección IP.

Cuando se crea un servicio en la nube en Azure, los registros de DNS A correspondientes se crean automáticamente para permitir el acceso al servicio mediante un nombre de dominio completo (FQDN) en lugar de usar la VIP real. Se produce el mismo proceso para la ILPIP, lo que permite el acceso a la máquina virtual o instancia de rol mediante el FQDN en lugar de la ILPIP. Por ejemplo, si crea un servicio en la nube denominado *contosoadservice* y configura un rol web denominado *contosoweb* con dos instancias, Azure registrará los siguientes registros A para las instancias:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> Solo puede asignar una ILPIP para cada máquina virtual o instancia de rol. Puede usar hasta 5 ILPIP por suscripción. Por ahora, no se admiten ILPIP para máquinas virtuales con varias NIC.
> 
> 

## <a name="why-should-i-request-an-ilpip"></a>¿Por qué debo solicitar una ILPIP?
Si desea poder conectarse a la máquina virtual o a la instancia de rol mediante una dirección IP asignada directamente a ella, en lugar de usar la VIP:&lt;número de puerto&gt; del servicio en la nube, solicite una ILPIP para la máquina virtual o la instancia de rol.

* **FTP pasivo** : al tener una ILPIP en la máquina virtual, puede recibir tráfico en cualquier puerto; no tendrá que abrir un punto de conexión para ello. Esto permite escenarios como el FTP pasivo, donde los puertos se seleccionan de forma dinámica.
* **IP de salida** : el tráfico de salida procedente de la máquina virtual sale con la ILPIP como origen y, de esta forma, se identifica de forma única a la máquina virtual ante entidades externas.

> [!NOTE]
> Antes, a la ILPIP se la llamaba "PIP", que significa "dirección IP pública".
> 

## <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Solicitud de una ILPIP durante la creación de máquinas virtuales mediante PowerShell
El script de PowerShell siguiente crea un servicio en la nube denominado *FTPService*, recupera una imagen de Azure y crea una máquina virtual denominada *FTPInstance* usando la imagen recuperada. También establece la máquina virtual para que use una ILPIP y agrega la máquina virtual al nuevo servicio:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Recuperación de información de ILPIP para una máquina virtual
Para ver la información de ILPIP para la máquina virtual creada con este script, ejecute el siguiente comando de PowerShell y observe los valores de *PublicIPAddress* y *PublicIPName*:

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

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>Supresión de una ILPIP de una máquina virtual
Para quitar la ILPIP agregada a la máquina virtual en el script anterior, ejecute el siguiente comando de PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Agregación de una ILPIP a una máquina virtual existente
Para agregar una ILPIP a la máquina virtual creada con el script anterior, ejecute el siguiente comando:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>Asociación de una ILPIP a una máquina virtual mediante un archivo de configuración de servicio
También puede asociar una ILPIP a una máquina virtual mediante un archivo de configuración de servicio (CSCFG). El siguiente xml de ejemplo muestra cómo configurar un servicio en la nube a fin de usar una ILPIP denominada *MyPublicIP* para una instancia de rol: 

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Pasos siguientes
* Descubra cómo funcionan las [direcciones IP](virtual-network-ip-addresses-overview-classic.md) en el modelo de implementación clásica.
* Obtenga información sobre las [direcciones IP reservadas](virtual-networks-reserved-public-ip.md).




<!--HONumber=Jan17_HO1-->


