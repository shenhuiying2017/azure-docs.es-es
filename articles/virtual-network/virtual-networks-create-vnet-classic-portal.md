---
title: "Creación de una red virtual de Azure (clásico) - Portal clásico | Microsoft Docs"
description: "Obtenga información sobre cómo crear una red virtual (clásico) con un archivo netcfg en el Portal de Azure clásico."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 69894a0b-8050-451e-8a25-c513e1bd271e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: d365f7137527d60eb509b4f431295de2218ea706
ms.openlocfilehash: 5ff91cccb711d61ed120e4a4e820d6a5dfc5e4a6
ms.contentlocale: es-es
ms.lasthandoff: 01/31/2017


---

<a id="create-a-virtual-network-classic-with-a-netcfg-file-using-the-azure-classic-portal" class="xliff"></a>

# Creación de una red virtual (clásico) con un archivo netcfg en el Portal de Azure clásico
[!INCLUDE [virtual-networks-create-vnet-selectors-classic-include](../../includes/virtual-networks-create-vnet-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

En este artículo se explica cómo crear una red virtual con un archivo netcfg a través del modelo de implementación clásica en el Portal de Azure clásico. También puede [crear una red virtual a través del modelo de implementación clásica sin utilizar un archivo netcfg](virtual-networks-create-vnet-classic-pportal.md) o [crear una red virtual a través del modelo de implementación de Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) en Azure Portal.

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

<a id="how-to-create-a-vnet-with-a-network-config-file-in-the-microsoft-azure-classic-portal" class="xliff"></a>

## Creación de una red virtual con un archivo de configuración de red en el Portal de Microsoft Azure clásico
Azure utiliza un archivo xml para definir todas las redes virtuales disponibles para una suscripción. Puede descargar este archivo y editarlo para crear redes virtuales a través del modelo de implementación clásica o para modificar o eliminar redes virtuales existentes. En este artículo se explica cómo descargar este archivo, conocido como un archivo de configuración de red (o netcfg), agregarle una red virtual y cargar el archivo para crear la red virtual. Para obtener más información sobre el archivo de configuración de red, revise el [esquema de configuración de red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Para crear una red virtual mediante un archivo netcfg en el Portal de Azure clásico, siga estos pasos:

1. En un explorador, navegue a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Desplácese hacia abajo en la lista de servicios, haga clic en **REDES** y luego en **EXPORTAR**, como se muestra en la imagen siguiente:

    ![Redes virtuales de Azure](./media/virtual-networks-create-vnet-classic-portal/networks.png)
3. En el cuadro de diálogo **Exportar configuración de red**, seleccione la suscripción desde la que desea exportar la configuración de red virtual y después haga clic en el botón de marca de verificación en la esquina inferior derecha del cuadro.
4. Siga las instrucciones del explorador para guardar el archivo **NetworkConfig.xml** . Asegúrese de observar dónde guarda el archivo.
5. Abra el archivo que guardó en el paso 4 anterior con cualquier aplicación de edición de texto o XML y busque el elemento `<VirtualNetworkSites>` dentro del elemento `<VirtualNetworkConfiguration>`. Si tiene redes virtuales existentes, cada una se muestra en su propio elemento `<VirtualNetworkSite>`. Si el archivo no contiene un elemento `<VirtualNetworkSites>` dentro del elemento `<VirtualNetworkConfiguration>`, cree uno.
6. Si el archivo NetworkConfig existente no tiene ninguna red virtual, el archivo NetworkConfig.xml se parecerá al del siguiente ejemplo después de agregarle la red virtual que se describe en este escenario:

    ```xml
    <NetworkConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="TestVNet" Location="Central US">
            <AddressSpace>
              <AddressPrefix>192.168.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>192.168.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>192.168.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    ```
7. Guarde el archivo de configuración de red.
8. En el Portal de Azure clásico, haga clic en **NUEVO**, **NETWORK SERVICES**, **RED VIRTUAL** y haga clic en **IMPORTAR CONFIGURACIÓN** tal como se muestra en la siguiente imagen:

    ![IMPORTAR CONFIGURACIÓN](./media/virtual-networks-create-vnet-classic-portal/import.png)
10. En la página **Importar el archivo de configuración de red**, haga clic en **BUSCAR ARCHIVO...**, navegue hasta la carpeta en la que guardó el archivo en el paso 7, selecciónelo y, después, haga clic en **Abrir**, como se muestra en la imagen siguiente:

    ![Página Importar archivo de configuración de red](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure4.png)

    En la esquina inferior derecha del cuadro, haga clic en el botón de flecha para ir al paso siguiente.

9. En el cuadro de diálogo **Creando su red**, observe la entrada de la nueva red virtual, como se muestra en la siguiente imagen:

    ![Página Construcción de la red](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure5.png)
10. Para crear la red virtual, haga clic en el botón de marca de verificación en la esquina inferior derecha del cuadro de la imagen anterior. Tras unos segundos la red virtual se muestra en la lista de redes virtuales disponibles, como se muestra en la imagen siguiente:

    ![Nueva red virtual](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure6.png)

