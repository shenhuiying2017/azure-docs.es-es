---
title: "Configuración de una Azure Virtual Network (clásica): archivo de configuración de red | Microsoft Docs"
description: "Obtenga información sobre cómo crear y modificar redes virtuales (clásicas) mediante la exportación, la modificación y la importación de un archivo de configuración de red."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f1e3ae26b6525f2235a6b0d53546b334dc027b94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Configuración de una red virtual (clásica) mediante un archivo de configuración de red
> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de implementación de Resource Manager.

Puede crear y configurar una red virtual (clásica) con un archivo de configuración de red mediante la interfaz de línea de comandos (CLI) de Azure 1.0 o Azure PowerShell. No puede crear o modificar una red virtual mediante el modelo de implementación de Azure Resource Manager con un archivo de configuración de red. No puede usar el portal de Azure para crear o modificar una red virtual (clásica) con un archivo de configuración de red. Sin embargo, puede usar el portal de Azure para crear una red virtual (clásica) sin utilizar un archivo de configuración de red.

La creación y configuración de una red virtual (clásica) con un archivo de configuración de red requiere la exportación, cambio e importación del archivo.

## <a name="export"></a>Exportación de un archivo de configuración de red

Puede usar PowerShell o la CLI de Azure para exportar un archivo de configuración de red. PowerShell exporta un archivo XML, mientras que la CLI de Azure exporta un archivo json.

### <a name="powershell"></a>PowerShell
 
1. [Instale Azure PowerShell e inicie sesión en Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Cambie el directorio (y asegúrese de que exista) y el nombre de archivo en el siguiente comando según corresponda. A continuación, ejecute el comando para exportar el archivo de configuración de red:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>CLI de Azure 1.0

1. [Instale la CLI de Azure 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Complete los pasos restantes desde un símbolo del sistema de la CLI de Azure 1.0.
2. Inicie sesión en Azure especificando el comando `azure login`.
3. Asegúrese de que está en el modo de asm escribiendo el comando `azure config mode asm`.
4. Cambie el directorio (y asegúrese de que exista) y el nombre de archivo en el siguiente comando según corresponda. A continuación, ejecute el comando para exportar el archivo de configuración de red:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Creación o modificación mediante el archivo de configuración de red

Un archivo de configuración de red es un archivo XML (cuando se usa PowerShell) o un archivo json (cuando se usa la CLI de Azure). Puede editar el archivo en cualquier editor XML/json o de texto. El artículo [Network configuration file schema settings](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Configuración del esquema de archivos de configuración de red) incluye los detalles de todas las configuraciones. Para una explicación adicional de la configuración, vea [Visualización de las redes virtuales y su configuración](virtual-network-manage-network.md#view-vnet). Los cambios que realice en el archivo:

- Debe cumplir con el esquema o se producirá un error en la importación del archivo de configuración de red.
- Sobrescriben cualquier configuración de red existente para su suscripción, por lo que debe tener mucho cuidado al realizar modificaciones. Por ejemplo, consulte los archivos de configuración de red de ejemplo siguientes. Suponga que el archivo original contenía dos instancias **VirtualNetworkSite** y las ha cambiado, tal como se muestra en los ejemplos. Al importar el archivo, Azure elimina la red virtual para la instancia **VirtualNetworkSite** que ha quitado en el archivo. Este escenario simplificado da por supuesto que no había recursos en la red virtual, ya que si los hubiera, la red virtual no podría eliminarse y se produciría un error en la importación.

> [!IMPORTANT]
> Azure considera una subred que tiene algo implementado en ella como **en uso**. Cuando una subred está en uso, no puede modificarse. Antes de modificar la información de subred en un archivo de configuración de red, mueva todo lo que haya implementado en la subred a una subred diferente que no se esté modificando. Consulte [Traslado de una máquina virtual o una instancia de rol a una subred diferente](virtual-networks-move-vm-role-to-subnet.md) para obtener más información.

### <a name="example-xml-for-use-with-powershell"></a>XML de ejemplo para su uso con PowerShell

El siguiente archivo de configuración de red de ejemplo crea una red virtual denominada *myVirtualNetwork* con un espacio de direcciones de *10.0.0.0/16* en la región de Azure del *Este de EE. UU.* La red virtual contiene una subred denominada *mySubnet* con un prefijo de dirección de *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Si el archivo de configuración de red exportado no contiene ningún contenido, puede copiar el archivo XML en el ejemplo anterior y pegarlo en un archivo nuevo.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>JSON de ejemplo para su uso con la CLI de Azure 1.0

El siguiente archivo de configuración de red de ejemplo crea una red virtual denominada *myVirtualNetwork* con un espacio de direcciones de *10.0.0.0/16* en la región de Azure del *Este de EE. UU.* La red virtual contiene una subred denominada *mySubnet* con un prefijo de dirección de *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Si el archivo de configuración de red exportado no contiene ningún contenido, puede copiar el archivo json en el ejemplo anterior y pegarlo en un archivo nuevo.

## <a name="import"></a>Importación de un archivo de configuración de red

Puede usar PowerShell o la CLI de Azure para importar un archivo de configuración de red. PowerShell importa un archivo XML, mientras que la CLI de Azure importa un archivo json. Si se produce un error en la importación, confirme que el archivo cumple con el [esquema de configuración de red](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Instale Azure PowerShell e inicie sesión en Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Cambie el directorio y el nombre de archivo en el siguiente comando según sea necesario. A continuación, ejecute el comando para importar el archivo de configuración de red:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>CLI de Azure 1.0

1. [Instale la CLI de Azure 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Complete los pasos restantes desde un símbolo del sistema de la CLI de Azure 1.0.
2. Inicie sesión en Azure especificando el comando `azure login`.
3. Asegúrese de que está en el modo de asm escribiendo el comando `azure config mode asm`.
4. Cambie el directorio y el nombre de archivo en el siguiente comando según sea necesario. A continuación, ejecute el comando para importar el archivo de configuración de red:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
