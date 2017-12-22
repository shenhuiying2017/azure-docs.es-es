---
title: "Administración de grupos de seguridad de red con Azure Portal | Microsoft Docs"
description: Aprenda a administrar grupos de seguridad de red existentes con Azure Portal.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5d55679d-57da-457c-97dc-1e1973909ee5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.openlocfilehash: e9bcf8a893ff209337f6a5763b631a22f8514e20
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-nsgs-using-the-portal"></a>Administración de grupos de seguridad de red mediante el portal

> [!div class="op_single_selector"]
> * [Portal](virtual-network-manage-nsg-arm-portal.md)
> * [PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [CLI de Azure](virtual-network-manage-nsg-arm-cli.md)
>

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del modelo de implementación clásica.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Recuperar información
Puede consultar los NSG existentes, recuperar las reglas de un NSG existente y averiguar cuáles son los recursos a los que está asociado un NSG.

### <a name="view-existing-nsgs"></a>Consultar los NSG existentes

Para ver todos los NSG existentes en una suscripción, complete los pasos siguientes:

1. Desde un explorador, vaya a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.

2. Haga clic en **Examinar >** > **Grupos de seguridad de red**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Compruebe la lista de NSG en la hoja **Grupos de seguridad de red** .

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

### <a name="view-nsgs-in-a-resource-group"></a>Visualización de los NSG en un grupo de recursos

Para consultar la lista de NSG del grupo de recursos **RG-NSG**, complete los pasos siguientes:

1. Haga clic en **Grupos de recursos >** > **RG-NSG** > **...**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. En la lista de recursos, busque elementos que incluyan el icono NSG, tal como aparece en la hoja **Recursos** que se muestra a continuación.

    ![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### <a name="list-all-rules-for-an-nsg"></a>Mostrar todas las reglas de un NSG

Para consultar las reglas de un NSG llamado **NSG-FrontEnd**, complete los pasos siguientes:

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que se muestra arriba, haga clic en **NSG-FrontEnd**.

2. En la pestaña **Configuración**, haga clic en **Reglas de seguridad de entrada**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. La hoja **Reglas de seguridad de entrada** aparece como se muestra a continuación.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. En la pestaña **Configuración**, haga clic en **Reglas de seguridad de salida** para ver las reglas de salida.

    > [!NOTE]
    > Para consultar las reglas predeterminadas, haga clic en el icono **Reglas predeterminadas** que se encuentra en la parte superior de la hoja donde aparecen las reglas.
    >

### <a name="view-nsgs-associations"></a>Consultar las asociaciones de NSG

Para consultar cuáles son los recursos con los que está asociado el NSG **NSG-FrontEnd**, complete los pasos siguientes:

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que se muestra arriba, haga clic en **NSG-FrontEnd**.

2. En la pestaña **Configuración**, haga clic en **Subredes** para consultar cuáles son las subredes que están asociadas con el NSG.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. En la pestaña **Configuración**, haga clic en **Interfaces de red** para consultar cuáles son las NIC que están asociadas con el NSG.

## <a name="manage-rules"></a>Administrar las reglas
Puede agregar reglas a un NSG existente, editar reglas existentes y quitar reglas.

### <a name="add-a-rule"></a>Agregar una regla
Para agregar una regla que permita el tráfico **de entrada** al puerto **443** desde cualquier máquina al grupo de seguridad de red **NSG-FrontEnd**, complete los pasos siguientes:

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que se muestra arriba, haga clic en **NSG-FrontEnd**.
2. En la pestaña **Configuración**, haga clic en **Reglas de seguridad de entrada**.
3. En la hoja **Reglas de seguridad de entrada**, haga clic en **Agregar**. Luego, en la hoja **Agregar regla de seguridad de entrada**, rellene los valores como se muestra a continuación y haga clic en **Aceptar**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

    Después de unos segundos, observe la regla nueva en la hoja **Reglas de seguridad de entrada** .

    ![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Cambiar una regla
Para cambiar la regla que creó antes para permitir el tráfico de entrada solo desde **Internet**, complete los pasos siguientes:

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que se muestra arriba, haga clic en **NSG-FrontEnd**.
2. En la pestaña **Configuración** , haga clic en la regla que se creó anteriormente.
3. En la hoja **allow-https**, cambie la propiedad **Origen** como se muestra a continuación y haga clic en **Guardar**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Eliminar una regla

Para eliminar la regla que creó antes, complete los pasos siguientes:

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que se muestra arriba, haga clic en **NSG-FrontEnd**.
2. En la pestaña **Configuración** , haga clic en la regla que se creó anteriormente.
3. En la hoja **allow-https**, haga clic en **Eliminar** y en **Sí**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Administrar las asociaciones
Puede asociar un NSG a subredes y NIC. También puede desasociar un NSG de cualquier recurso al que está asociado.

### <a name="associate-an-nsg-to-a-nic"></a>Asociar un NSG a una NIC
Para asociar el grupo de seguridad de red **NSG-FrontEnd** a la NIC **TestNICWeb1**, complete los pasos siguientes:

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que se muestra arriba, haga clic en **NSG-FrontEnd**.
2. En la pestaña **Configuración**, haga clic en **Interfaces de red** > **Asociar** > **TestNICWeb1**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Desasociar un NSG de una NIC

Para desasociar el grupo de seguridad de red **NSG-FrontEnd** de la NIC **TestNICWeb1**, complete los pasos siguientes:

1. En Azure Portal, haga clic en **Grupos de recursos >** > **RG-NSG** > **...** > **TestNICWeb1**.

2. En la hoja **TestNICWeb1**, haga clic en **Cambiar seguridad...** > **Ninguna**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> También puede usar esta hoja para asociar la NIC a cualquier NSG existente.
>

### <a name="dissociate-an-nsg-from-a-subnet"></a>Desasociar un NSG de una subred

Para desasociar el grupo de seguridad de red **NSG-FrontEnd** de la subred **FrontEnd**, complete los pasos siguientes:

1. En Azure Portal, haga clic en **Grupos de recursos >** > **RG-NSG** > **...** > **TestVNet**.

2. En la hoja **Configuración**, haga clic en **Subredes** > **FrontEnd** > **Grupo de seguridad de red** > **Ninguno**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. En la hoja **FrontEnd**, haga clic en **Guardar**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Asociación de un grupo de seguridad de red a una máquina virtual

Para asociar el grupo de seguridad de red **NSG-FrontEnd** a la subred **FrontEnd**, complete los pasos siguientes:

1. En Azure Portal, haga clic en **Grupos de recursos >** > **RG-NSG** > **...** > **TestVNet**.
2. En la hoja **Configuración**, haga clic en **Subredes** > **FrontEnd** > **Grupo de seguridad de red** > **NSG-FrontEnd**.
3. En la hoja **FrontEnd**, haga clic en **Guardar**.

> [!NOTE]
> También puede asociar un NSG a una subred desde la hoja **Configuración** del NSG.
>

## <a name="delete-an-nsg"></a>Eliminación de un grupo de seguridad de red
Solo se puede eliminar un NSG que no está asociado a ningún recurso. Para eliminar un NSG, complete los pasos siguientes:

1. En Azure Portal, haga clic en **Grupos de recursos >** > **RG-NSG** > **...** > **NSG-FrontEnd**.
2. En la hoja **Configuración**, haga clic en **Interfaces de red**.
3. Si aparece alguna NIC, haga clic en ella y siga el paso 2 de [Desasociar un NSG de una NIC](#Dissociate-an-NSG-from-a-NIC).
4. Repita el paso 3 para cada NIC.
5. En la hoja **Configuración**, haga clic en **Subredes**.
6. Si aparece alguna subred, haga clic en ella y siga los pasos 2 y 3 de [Desasociar un NSG de una subred](#Dissociate-an-NSG-from-a-subnet).
7. Desplácese a la izquierda hasta la hoja **NSG-FrontEnd** y haga clic en **Eliminar** > **Sí**.

    ![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Pasos siguientes
* [Habilite el registro](virtual-network-nsg-manage-log.md) para los NSG.
