---
title: 'Configuración de direcciones IP privadas para máquinas virtuales (clásicas): Azure Portal | Microsoft Docs'
description: Obtenga información sobre cómo configurar direcciones IP privadas para máquinas virtuales (clásicas) mediante Azure Portal.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b81f0fc1d504a0cb3422e003aa4ad7986396652
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configuración de direcciones IP privadas para una máquina virtual (clásica) mediante Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación clásico. También puede [administrar la dirección IP privada estática en el modelo de implementación del Administrador de recursos](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

En los siguientes pasos de ejemplo se presupone que ya se ha creado un entorno simple. Si desea ejecutar los pasos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Especificación de una dirección IP privada estática al crear una VM
Para crear una máquina virtual denominada *DNS01* en la subred *FrontEnd* de una red virtual denominada *TestVNet* con una dirección IP privada estática de *192.168.1.101*, complete estos pasos:

1. Desde un explorador, vaya a https://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Seleccione **NUEVO** > **Compute** > **Windows Server 2012 R2 Datacenter**. Observe que la lista **Seleccionar un modelo de implementación** ya muestra **Clásico**. Seleccione **Crear**.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. En **Crear máquina virtual**, especifique el nombre de la máquina virtual que se va a crear (*DNS01* en este escenario), la cuenta de administrador local y la contraseña.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Seleccione **Configuración opcional** > **Red** > **Red virtual** y **TestVNet**. Si **TestVNet** no está disponible, asegúrese de que usa la ubicación *Centro de EE. UU.* y que ha creado el entorno de prueba descrito al principio de este artículo.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. En **Red**, asegúrese de que la subred seleccionada actualmente sea *FrontEnd* y seleccione **Direcciones IP**; en **Asignación de dirección IP**, seleccione **Estática** y especifique *192.168.1.101* en **Dirección IP** como se muestra a continuación.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Seleccione **Aceptar** en **Direcciones IP**; **Aceptar**, en **Red**, y **Aceptar**, en **Configuración opcional**.
7. En **Crear máquina virtual**, seleccione **Crear**. Observe el icono siguiente que aparece en el panel:
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperación de la información de la dirección IP privada estática para una VM
Para ver la información de la dirección IP privada estática para la VM que creó con los pasos anteriores, ejecute los siguientes pasos.

1. En Azure Portal, seleccione **EXAMINAR TODO** > **Máquinas virtuales (clásico)** > **DNS01** > **Toda la configuración** > **Direcciones IP** y observe la asignación de dirección IP y la dirección IP que se muestran a continuación.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Eliminación de una dirección IP privada estática de una VM

En **Direcciones IP**, seleccione **Dinámica** a la derecha del **Asignación de dirección IP**, seleccione **Guardar** y **Sí**, tal y como se muestra en la siguiente imagen:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Adición de una dirección IP privada estática a una VM existente

1. En **Direcciones IP** (que se mostró anteriormente), seleccione **Estática** a la derecha de **Asignación de dirección IP**.
2. Escriba *192.168.1.101* como **Dirección IP**, y seleccione **Guardar** y **Sí**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Configuración de direcciones IP en el sistema operativo

Se recomienda no asignar estáticamente la dirección IP privada asignada a la máquina virtual de Azure en el sistema operativo de una máquina virtual, a menos que sea necesario. Al establecer manualmente la dirección IP privada en el sistema operativo, asegúrese de que sea la misma que la dirección IP privada asignada a la máquina virtual de Azure, de lo contrario, perderá la conectividad a la máquina virtual. No asigne manualmente la dirección IP pública asignada a una máquina virtual de Azure en el sistema operativo de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información acerca de las [direcciones IP públicas reservadas](virtual-networks-reserved-public-ip.md) .
* Obtenga información sobre las [direcciones IP públicas a nivel de instancia (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Consulte las [API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).

