---
title: "Creación de una red virtual mediante una plantilla de Azure Resource Manager | Microsoft Docs"
description: Aprenda a crear una red virtual mediante una plantilla de Azure Resource Manager.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81602766848a91331c8d811ea1c8ec3ffae44b96
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Creación de una red virtual mediante una plantilla de Azure Resource Manager

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure cuenta con dos modelos de implementación: Azure Resource Manager y el clásico. Microsoft recomienda crear recursos mediante el modelo de implementación de Resource Manager. Para más información acerca de las diferencias entre los dos modelos, lea el artículo [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Descripción de los modelos de implementación de Azure).
 
En este artículo se describe cómo crear una red virtual con el modelo de implementación de Resource Manager mediante una plantilla de Azure Resource Manager. También puede crear una red virtual mediante Resource Manager con otras herramientas o crear una red virtual a través del modelo de implementación clásica seleccionando una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
- [Portal](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [Plantilla](virtual-networks-create-vnet-arm-template-click.md)
- [Portal (clásico)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (clásico)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (clásico)](virtual-networks-create-vnet-classic-cli.md)

Aprenderá a descargar y modificar una plantilla de ARM existente desde GitHub, así como a implementar la plantilla de GitHub, PowerShell y la CLI de Azure.

Si simplemente va a implementar la plantilla de ARM directamente desde GitHub, sin realizar ningún cambio, vaya a [implementar una plantilla desde github](#deploy-the-arm-template-by-using-click-to-deploy).

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Descarga e información sobre la plantilla del Administrador de recursos de Azure
Puede descargar la plantilla existente para crear una red virtual y dos subredes desde GitHub, realizar los cambios que desee y volver a utilizarla. Para hacerlo, complete los pasos siguientes:

1. Navegue a [la página de la plantilla de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Haga clic en **azuredeploy.json** y luego en **RAW**.
3. Guarde el archivo en un una carpeta local en su equipo.
4. Si está familiarizado con las plantillas, salte al paso 7.
5. Abra el archivo que acaba de guardar y vea el contenido de la línea 5 en **parameters** . Los parámetros de plantilla ARM proporcionan un marcador de posición para los valores que se pueden rellenar durante la implementación.
   
   | Parámetro | Descripción |
   | --- | --- |
   | **ubicación** |Región de Azure donde se creará la red virtual |
   | **vnetName** |Nombre de la red virtual nueva |
   | **addressPrefix** |Espacio de direcciones de la red virtual, en formato CIDR |
   | **subnet1Name** |Nombre de la primera red virtual |
   | **subnet1Prefix** |Bloque CIDR de la primera subred |
   | **subnet2Name** |Nombre de la segunda red virtual |
   | **subnet2Prefix** |Bloque CIDR de la segunda subred |
   
   > [!IMPORTANT]
   > Las plantillas del Administrador de recursos de Azure que se mantienen en GitHub pueden cambiar con el tiempo. Asegúrese de comprobar la plantilla antes de usarla.
   > 
   > 
6. Compruebe el contenido en **resources** y observe lo siguiente:
   
   * **type**. Tipo de recurso que creó la plantilla. En este caso, **Microsoft.Network/virtualNetworks**, que representan una red virtual.
   * **nombre**. Nombre del recurso. Observe el uso de **[parameters('vnetName')]**, lo que significa que será el usuario quien proporcione el nombre como entrada o como archivo de parámetros durante la implementación.
   * **propiedades**. Lista de propiedades para el recurso. Esta plantilla usa las propiedades de espacio de direcciones y subred durante la creación de la red virtual.
7. Vuelva a [la página de la plantilla de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Haga clic en **azuredeploy-paremeters.json** y luego en **RAW**.
9. Guarde el archivo en un una carpeta local en su equipo.
10. Abra el archivo que acaba de guardar y edite los valores de los parámetros. Utilice los siguientes valores para implementar la red virtual que se describe en este escenario:

    ```json
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. Guarde el archivo .


## <a name="deploy-the-template-using-powershell"></a>Implementación de la plantilla mediante PowerShell

Complete los siguientes pasos para implementar la plantilla que descargó con PowerShell:

1. Instale y configure Azure PowerShell completando los pasos del artículo [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview).
2. Ejecute el comando siguiente para crear un nuevo grupo de recursos:

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    El comando crea un grupo de recursos denominado *TestRG* en la región *Centro de EE. UU.* de Azure. Para más información acerca de los grupos de recursos, visite [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    Resultado esperado:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. Ejecute el siguiente comando para implementar la nueva red virtual mediante la plantilla y los archivos de parámetros que descargó y modificó antes:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    Resultado esperado:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. Ejecute el siguiente comando para ver las propiedades de la nueva red virtual:

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    Resultado esperado:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>Implementación de la plantilla por medio de un solo clic para implementar

Puede reutilizar plantillas de Azure Resource Manager predefinidas que se han cargado en un repositorio GitHub que mantiene Microsoft y está abierto a la comunidad. Estas plantillas pueden implementarse directamente desde GitHub o pueden descargarse y modificarse para ajustarlas a sus necesidades. Para implementar una plantilla que crea una red virtual con dos subredes, complete los siguientes pasos:

1. Desde un explorador, vaya a [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Desplácese hacia abajo por la lista de plantillas y haga clic en **101-vnet-two-subnets**. Compruebe el archivo **README.md** , como se muestra a continuación.

    ![Archivo README.md en github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Haga clic en **Implementar en Azure**. Si es necesario, escriba sus credenciales de inicio de sesión de Azure. 
4. En la hoja **Parámetros**, escriba los valores que desee utilizar para crear la red virtual nueva y, a continuación, haga clic en **Aceptar**. En la siguiente ilustración se muestran los valores para el escenario:
   
    ![Parámetros de plantilla ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. Haga clic en **Grupo de recursos** y seleccione un grupo de recursos al que va a agregar la red virtual, o haga clic en **Crear nuevo** para agregar la red virtual a un nuevo grupo de recursos. En la siguiente ilustración se muestra la configuración de grupo de recursos de uno nuevo denominado **TestRG**:

    ![Grupos de recursos](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. Si es necesario, cambie la configuración de la **Suscripción** y la **Ubicación** de la red virtual.
7. Si no desea ver la red virtual como un icono en el **Panel de inicio**, deshabilite **Anclar a panel de inicio**.
8. Haga clic en **Términos legales**, lea los términos y haga clic en **Comprar** para aceptar. 
9. Haga clic en **Crear** para crear la red virtual.
   
    ![Enviar icono de implementación al portal de vista previa](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. Una vez finalizada la implementación, en Azure Portal, haga clic en **Más servicios**, escriba *redes virtuales* en el cuadro de filtro que aparece y haga clic en Redes virtuales para ver la hoja Redes virtuales. En la hoja, haga clic en *TestVNet*. En la hoja *TestVNet*, haga clic en **Subredes** para ver las subredes creadas, como se muestra en la siguiente imagen:
    
     ![Crear red virtual en el portal de vista previa](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>Pasos siguientes

Aprenda a conectar:

- Una máquina virtual (VM) a una red virtual, para lo cual debería leer los artículos [Creación de una máquina virtual Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) o [Creación de una máquina virtual Linux](../virtual-machines/linux/quick-create-portal.md). En lugar de crear una red virtual y la subred en los pasos de los artículos, puede seleccionar una red virtual y una subred a la que conectar una máquina virtual.
- La red virtual a otras redes virtuales. Para ello, lea el artículo [Conexión de redes virtuales](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- La red virtual a una red local mediante una red privada virtual (VPN) de sitio a sitio o un circuito ExpressRoute. Aprenda cómo hacerlo mediante los artículos [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Conexión de una red virtual a una red local mediante una VPN de sitio a sitio) y [Vinculación de una red virtual a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md).
