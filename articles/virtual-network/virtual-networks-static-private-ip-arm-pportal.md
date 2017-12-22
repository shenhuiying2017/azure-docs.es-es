---
title: "Configuración de direcciones IP privadas para máquinas virtuales (Azure Portal) | Microsoft Docs"
description: "Obtenga información sobre cómo configurar direcciones IP privadas para máquinas virtuales mediante Azure Portal."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 672462fad715758e50680fa5bade4b1f9d50e6e5
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Configuración de direcciones IP privadas para una máquina virtual mediante Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [CLI de Azure](virtual-networks-static-private-ip-arm-cli.md)
> * [Portal de Azure (clásico)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (clásico)](virtual-networks-static-private-ip-classic-ps.md)
> * [CLI de Azure (clásico)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puedes [Administrar la dirección IP privada estática en el modelo de implementación clásica](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

En los siguientes pasos de ejemplo se presupone que ya se ha creado un entorno simple. Si desea ejecutar los pasos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Creación de una VM para probar las direcciones IP privadas estáticas
No se puede establecer una dirección IP privada estática durante la creación de una VM en el modo de implementación del Administrador de recursos mediante el Portal de Azure. En primer lugar debe crear la VM, tehn establece la IP privada como estática.

Para crear una VM denominada *DNS01* en la subred *FrontEnd* de una red virtual denominada *TestVNet*, siga los siguientes pasos.

1. Desde un explorador, vaya a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **NUEVO** > **Proceso** > **Windows Server 2012 R2 Datacenter**. Tenga en cuenta que la lista **Seleccionar un modelo de implementación** ya muestre **Resource Manager** y, después, haga clic en **Crear**, como se muestra en la figura siguiente.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. En la hoja **Básico** , especifique el nombre de la VM que se va a crear (*DNS01* en nuestro escenario), la cuenta de administrador local y la contraseña, como se muestra en la figura siguiente.
   
    ![Hoja Básico](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Asegúrese de que la **Ubicación** seleccionada sea *Centro de EE. UU.* y, después, haga clic en **Seleccionar existente** en **Grupo de recursos**. Después, haga clic de nuevo en **Grupo de recursos**, luego en *TestRG* y, después, en **Aceptar**.
   
    ![Hoja Básico](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. En la hoja **Elegir un tamaño**, seleccione **A1 Estándar** y, después, haga clic en **Seleccionar**.
   
    ![Selección de una hoja de tamaño](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. En la hoja **Configuración**, asegúrese de que se establecen las siguientes propiedades con los siguientes valores y, después, haga clic en **Aceptar**.
   
    -**Cuenta de almacenamiento**: *vnetstorage*
   
   * **Red**: *TestVNet*
   * **Subred**: *FrontEnd*
     
     ![Selección de una hoja de tamaño](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. En la hoja **Resumen**, haga clic en **Aceptar**. Observe el icono siguiente que aparece en el panel.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperación de la información de la dirección IP privada estática para una VM
Para ver la información de la dirección IP privada estática para la VM que creó con los pasos anteriores, ejecute los siguientes pasos.

1. En Azure Portal, haga clic en **EXAMINAR TODO** > **Máquinas virtuales** > **DNS01** > **Toda la configuración** > **Interfaces de red** y, después, haga clic en la única interfaz de red que aparece.
   
    ![Implementación del icono de máquina virtual](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. En la hoja **Interfaz de red**, haga clic en **Toda la configuración** > **Direcciones IP** y vea los valores **Asignación** y **Dirección IP**.
   
    ![Implementación del icono de máquina virtual](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Adición de una dirección IP privada estática a una VM existente
Para agregar una dirección IP privada estática a la VM creada con los pasos anteriores, siga los siguientes pasos:

1. En la hoja **Direcciones IP** que se muestra a continuación, haga clic en **Estática** en **Asignación**.
2. Escriba *192.168.1.101* en **Dirección IP** y, después, haga clic en **Guardar**.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Si después de hacer clic en **Guardar** se da cuenta de que la asignación sigue establecida en **Dinámica**, significa que la dirección IP que ha escrito sigue estando en uso. Pruebe una dirección IP distinta.
> 
> 

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Eliminación de una dirección IP privada estática de una VM
Para quitar la dirección IP privada estática de la máquina virtual creada anteriormente, siga este paso:

En la hoja **Direcciones IP** que se muestra arriba, haga clic en **Dinámica** en **Asignación** y, después, haga clic en **Guardar**.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información acerca de las [direcciones IP públicas reservadas](virtual-networks-reserved-public-ip.md) .
* Obtenga información sobre las [direcciones IP públicas a nivel de instancia (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Consulte las [API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).

