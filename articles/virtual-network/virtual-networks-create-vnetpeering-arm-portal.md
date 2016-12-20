---
title: Emparejamiento de VNET mediante Azure Portal | Microsoft Docs
description: Aprenda a crear una red virtual mediante el Portal de Azure en Resource Manager.
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayanannamalai;annahar
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 688fed72b32767f33010b9e8f17921b16320072d


---
# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Emparejamiento de red virtual mediante el Portal de Azure
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para emparejar una red virtual según el escenario anterior por medio del Portal de Azure, siga los pasos a continuación.

1. Desde un explorador, vaya a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Para establecer el emparejamiento de VNET, debe crear dos vínculos, una para cada dirección entre dos redes virtuales. Primero puede crear un vínculo de emparejamiento de red virtual de VNET1 a VNET2. En el portal, haga clic en **Examinar** > **elija Redes virtuales**.
   
    ![Emparejamiento de VNet en el Portal de Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)
3. En la hoja Redes virtuales, elija VNET1 y haga clic en Emparejamientos y, después, en Agregar
   
    ![Elija el emparejamiento](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)
4. En la hoja Agregar emparejamiento, asigne a un vínculo de emparejamiento el nombre LinkToVnet2, elija la suscripción y la red virtual VNET2 emparejada, y haga clic en Aceptar.
   
    ![Vínculo a la red virtual](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)
5. Una vez creado este vínculo de emparejamiento de red virtual, verá el estado del vínculo siguiente:
   
    ![Estado del vínculo](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)
6. Después, cree el vínculo de emparejamiento de VNET de VNET2 a VNET1. En la hoja Redes virtuales, elija VNET2 y haga clic en Emparejamientos y, después, en Agregar
   
    ![Emparejar con otra red virtual](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)
7. En la hoja Agregar emparejamiento, asigne a un vínculo de emparejamiento el nombre LinkToVnet1, elija la suscripción y la red virtual emparejada, y haga clic en Aceptar.
   
    ![Icono de Crear red virtual](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)
8. Una vez creado este vínculo de emparejamiento de red virtual, verá el estado del vínculo siguiente:
   
    ![Estado final del vínculo](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)
9. Compruebe el estado de LinkToVnet2, que también cambiará a Conectado.  
   
    ![Estado final del vínculo 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)
   
   > [!NOTE]
   > El emparejamiento de VNET se establece solo si ambos vínculos están conectados.
   > 
   > 

Hay algunas propiedades configurables para cada vínculo:

| Opción | Description | Valor predeterminado |
|:--- |:--- |:--- |
| AllowVirtualNetworkAccess |Si el espacio de direcciones de red virtual emparejada se incluye como parte de la etiqueta Virtual_network. |Sí |
| AllowForwardedTraffic |Si el tráfico que no parte de una red virtual emparejada se acepta o rechaza. |No |
| AllowGatewayTransit |Permite a la red virtual emparejada usar la puerta de enlace de red virtual. |No |
| UseRemoteGateways |Se usa la puerta de enlace de la red virtual emparejada. La red virtual emparejada debe tener configurada una puerta de enlace y AllowGatewayTransit debe estar seleccionado. No se puede utilizar esta opción si tiene configurada una puerta de enlace. |No |

Cada vínculo de emparejamiento de VNET tiene varias de las propiedades anteriores. Desde el portal, haga clic en el vínculo de emparejamiento de red virtual y modifique las opciones disponibles, haga clic en Guardar para que el cambio surta efecto.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Desde un explorador, vaya a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. En este ejemplo utilizaremos dos suscripciones A y B, y dos usuarios UserA y UserB con privilegios en las suscripciones respectivamente.
3. En el portal, haga clic en Examinar y elija Redes virtuales. Haga clic en la red virtual y después en Agregar.
   
    ![Escenario 2 Examinar](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)
4. En la hoja Agregar acceso, haga clic en Seleccionar rol y elija Colaborador de la red, haga clic en Agregar usuarios, escriba el nombre de inicio de sesión de UserB y haga clic en Aceptar.
   
    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)
   
    Esto no es un requisito; se puede establecer emparejamiento incluso si los usuarios presentan individualmente solicitudes de emparejamiento de sus redes virtuales, siempre y cuando las solicitudes coincidan. Agregar privilegios al usuario de la otra red virtual como los usuarios de la red virtual local facilita la instalación en el portal.
5. A continuación, inicie sesión en el Portal de Azure con UserB, que es el usuario con privilegios para SuscriptionB. Siga los pasos anteriores para agregar UserA como Colaborador de la red.
   
    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)
   
   > [!NOTE]
   > Puede cerrar e iniciar las dos sesiones del usuario en el explorador para asegurarse de que la autorización se habilita correctamente.
   > 
   > 
6. Inicie sesión en el portal como UserA, vaya a la hoja VNET3, haga clic en Peering (Emparejamiento), marque la casilla "Conozco mi id. de recurso" y escriba el identificador de recurso de VNET5 en el formato siguiente.
   
    /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}
   
    ![Id. de recurso](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)
7. Inicie sesión en el portal como UserB y siga el paso anterior para crear el vínculo de emparejamiento de VNET5 a VNet3.
   
    ![Id. de recurso 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)
8. Se establecerá el emparejamiento y cualquier máquina virtual VNet3 debería poder comunicarse con cualquier máquina virtual en VNet5

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Como primer paso, el emparejamiento de VNET vincula de HubVnet a VNET1. Tenga en cuenta que no está seleccionada la opción Permitir tráfico reenviado para el vínculo.
   
    ![Emparejamiento básico](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)
2. Después se pueden crear vínculos de emparejamiento de VNET1 a HubVnet. Tenga en cuenta que está seleccionada la opción Permitir tráfico reenviado.
   
    ![Emparejamiento básico](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)
3. Después de establecer el emparejamiento, puede consultar este [artículo](virtual-network-create-udr-arm-ps.md) y establecer una ruta definida por el usuario (UDR) para redirigir el tráfico de VNet1 a través de una aplicación virtual para usar sus funcionalidades. Cuando se especifica la dirección Próximo salto en la ruta, puede establecerla en la dirección IP de la aplicación virtual de la red virtual HubVNet emparejada.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Desde un explorador, vaya a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Para establecer la red virtual de emparejamiento en este escenario, debe crear solo un vínculo desde la red virtual de Azure Resource Manager a la de la implementación clásica. Es decir, desde **VNET1** a **VNET2**. En el portal, haga clic en **Examinar** > elija **Redes virtuales**.
3. En la hoja de redes virtuales, elija **VNET1**. Haga clic en **Emparejamientos** y, a continuación, haga clic en **Agregar**.
4. En la hoja Agregar emparejamiento, dé un nombre al vínculo. Aquí se llama **LinkToVNet2**. En Detalles de miembro del mismo nivel, seleccione **Clásico**.
5. A continuación, elija la suscripción y la red virtual del mismo nivel **VNET2**. A continuación, haga clic en Aceptar.
   
    ![Vinculación de Vnet1 con Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)
6. Una vez creado este vínculo de emparejamiento de red virtual, las dos redes virtuales estarán emparejadas y podrá ver lo siguiente:
   
    ![Comprobación de la conexión de emparejamiento](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)

## <a name="remove-vnet-peering"></a>Desemparejamiento de VNET
1. Desde un explorador, vaya a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Vaya a la hoja de la red virtual, haga clic en Peerings (Emparejamientos), en el vínculo que desea quitar y en el botón Eliminar.
   
   ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)
3. Al quitar un vínculo en el emparejamiento de VNET, el estado del vínculo del emparejamiento cambia a desconectado.
   
    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)
4. En este estado, no se puede volver a crear el vínculo hasta que cambie el estado del vínculo de emparejamiento a iniciado. Se recomienda quitar ambos vínculos antes de volver a crear el emparejamiento de VNET.




<!---HONumber=Nov16_HO2-->


