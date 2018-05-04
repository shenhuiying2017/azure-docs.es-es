---
title: 'Creación de grupos de seguridad de red: Azure Portal | Microsoft Docs'
description: Obtenga información sobre cómo crear e implementar grupos de seguridad de red mediante Azure Portal.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Creación de un grupo de seguridad de red con Azure Portal

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [crear grupos de seguridad de red en el modelo de implementación clásica](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>Creación del grupo de seguridad de red NSG-FrontEnd
Para crear el grupo de seguridad de red **NSG-FrontEnd** según este escenario, siga estos pasos:

1. Desde un explorador, vaya a https://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Seleccione **+ Crear un recurso >** > **Grupos de seguridad de red**.
   
    ![Portal de Azure - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. En **Grupos de seguridad de red**, seleccione **Agregar**.
   
    ![Portal de Azure - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. En **Crear grupo de seguridad de red**, cree uno denominado *NSG-FrontEnd* en el grupo de recursos *RG-NSG* y seleccione **Crear**.
   
    ![Portal de Azure - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Creación de reglas en un grupo de seguridad de red existente
Para crear reglas en un grupo de seguridad de red existente desde Azure Portal, siga estos pasos:

1. Seleccione **Todos los servicios** y escriba **Grupos de seguridad de red**. Cuando aparezca **Grupos de seguridad de red**, selecciónelo.
2. En la lista de grupos de seguridad de red, seleccione **NSG-FrontEnd** > **Reglas de seguridad de entrada**
   
    ![Portal de Azure - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. En la lista de **reglas de seguridad de entrada**, seleccione **Agregar**.
   
    ![Portal de Azure - Agregar regla](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. En **Agregar regla de seguridad de entrada**, cree una regla denominada *web-rule* con prioridad de *200* que permita el acceso a través de *TCP* al puerto *80* a cualquier máquina virtual desde cualquier origen y seleccione **Aceptar**. Observe que la mayoría de estas configuraciones son ya valores predeterminados.
   
    ![Portal de Azure - Configuración de la regla](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Después de unos segundos, verá la nueva regla en el grupo de seguridad de red.
   
    ![Portal de Azure - Nueva regla](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Repita los pasos hasta el sexto para crear una regla de entrada denominada *rdp-rule* con una prioridad de *250* que permita el acceso a través de *TCP* al puerto *3389* a cualquier VM desde cualquier origen.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Asociación del grupo de seguridad de red a la subred FrontEnd

1. Seleccione **Todos los servicios >**, escriba **Grupos de recursos**, seleccione **Grupos de recursos** cuando aparezca y, después, **NSG RG**.
2. En **RG-NSG**, seleccione **...** > **TestVNet**.
   
    ![Portal de Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. En **Configuración**, seleccione **Subredes** > **FrontEnd** > **Grupo de seguridad de red** > **NSG-FrontEnd**.
   
    ![Portal de Azure - Configuración de la subred](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. En la hoja **FrontEnd**, seleccione **Guardar**.
   
    ![Portal de Azure - Configuración de la subred](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Creación del grupo de seguridad de red NSG-BackEnd
Para crear el grupo de seguridad de red **NSG-BackEnd** y asociarlo a la subred **BackEnd**, complete los siguientes pasos:

1. Repita los pasos que se describen en *Creación del grupo de seguridad de red NSG-FrontEnd* para crear un NSG llamado [NSG-BackEnd](#Create-the-NSG-FrontEnd-NSG).
2. Para crear las reglas **de entrada** de la tabla siguiente, repita los pasos de [Creación de reglas en un grupo de seguridad de red existente](#Create-rules-in-an-existing-NSG).
   
   | Regla de entrada | Regla de salida |
   | --- | --- |
   | ![Portal de Azure - Regla de entrada](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portal de Azure - Regla de salida](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Para asociar el grupo de seguridad de red **NSG-Backend** a la subred **BackEnd**, repita los pasos de [Asociación del grupo de seguridad de red a la subred FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet).

## <a name="next-steps"></a>Pasos siguientes
* Información sobre cómo [administrar grupos de seguridad de red existentes](manage-network-security-group.md)
* [Habilite el registro](virtual-network-nsg-manage-log.md) para los grupos de seguridad de red.