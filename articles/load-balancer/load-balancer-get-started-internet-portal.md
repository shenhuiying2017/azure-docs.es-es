---
title: "Creación de un equilibrador de carga con conexión a Internet: Azure Portal | Microsoft Docs"
description: "Obtenga información sobre cómo crear un equilibrador de carga orientado a Internet en Resource Manager con Azure Portal"
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 922c33f712e160835256ad9ad040e523dfbf76db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="creating-an-internet-facing-load-balancer-using-the-azure-portal"></a>Creación de un equilibrador de carga orientado a Internet mediante Azure Portal

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [CLI de Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Plantilla](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación del Administrador de recursos.

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Aquí se trata la secuencia de tareas individuales que deben realizarse para crear un equilibrador de carga y se explica con detalle qué se hace para lograr el objetivo.

## <a name="what-is-required-to-create-an-internet-facing-load-balancer"></a>¿Qué se necesita para crear un equilibrador de carga orientado a Internet?

Para implementar un equilibrador de carga, debe crear y configurar los objetos siguientes.

* Configuración de direcciones IP de front-end: contiene direcciones IP públicas para el tráfico de red entrante.
* Grupo de direcciones de back-end: contiene interfaces de red (NIC) para que las máquinas virtuales reciban tráfico de red del equilibrador de carga.
* Reglas de equilibrio de carga: contiene reglas que asignan un puerto público en el equilibrador de carga al del grupo de direcciones de back-end.
* Reglas NAT de entrada: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.
* Sondeos: contiene los sondeos de estado que se usan para comprobar la disponibilidad de las instancias de las máquinas virtuales del grupo de direcciones de back-end.

Puede obtener más información sobre los componentes del equilibrador de carga con Azure Resource Manager en [Compatibilidad de Azure Resource Manager con Load Balancer](load-balancer-arm.md).

## <a name="set-up-a-load-balancer-in-azure-portal"></a>Configuración de un equilibrador de carga en Azure Portal

> [!IMPORTANT]
> En este ejemplo se supone que tiene una red virtual denominada **myVNet**. Consulte [Creación de una red virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) para hacerlo. También se supone que hay una subred dentro de **myVNet** denominada **LB-Subnet-BE** y dos máquinas virtuales que se llaman **web1** y **web2** respectivamente en el mismo conjunto de disponibilidad llamado **myAvailSet** en **myVNet**. Consulte [este vínculo](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para crear máquinas virtuales.

1. Desde un explorador, vaya a Azure Portal: [http://portal.azure.com](http://portal.azure.com) e inicie sesión con su cuenta de Azure.
2. En la parte superior izquierda de la pantalla, seleccione **Nuevo** > **Redes** > **Equilibrador de carga.**
3. En la hoja **Crear equilibrador de carga** , escriba un nombre para el equilibrador de carga. En este caso, se llama **myLoadBalancer**.
4. En **Tipo**, seleccione **Público**.
5. En **Dirección IP pública**, cree una nueva dirección IP pública denominada **myPublicIP**.
6. En Grupo de recursos, seleccione **myRG**. A continuación, seleccione una **ubicación** adecuada y haga clic en **Aceptar**. El equilibrador de carga empieza entonces a implementarse y este proceso tarda unos minutos en completarse correctamente.

    ![Actualización de grupo de recursos de equilibrador de carga](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)

## <a name="create-a-back-end-address-pool"></a>Creación del grupo de direcciones de back-end

1. Una vez que se ha implementado correctamente el equilibrador de carga, selecciónelo entre sus recursos. En configuración, seleccione Grupos de back-end. Escriba un nombre para el grupo de back-end. A continuación, haga clic en el botón **Agregar** en la parte superior de la hoja que aparece.
2. Haga clic en **Agregar máquina virtual** en la hoja **Agregar grupo de back-end**.  Seleccione **Elegir un conjunto de disponibilidad** en **Conjunto de disponibilidad** y seleccione **myAvailSet**. A continuación, seleccione **Elegir las máquinas virtuales** en la sección Virtual Machines de la hoja y haga clic en **web1** y **web2**, las dos máquinas virtuales creadas para el equilibrio de carga. Asegúrese de que las dos tienen marcas de verificación azul a la izquierda, como se muestra en la siguiente imagen. A continuación, haga clic en **Seleccionar** en esa hoja y en Aceptar en la hoja **Elegir las máquinas virtuales**, después, haga clic en **Aceptar** en la hoja **Agregar grupo de back-end**.

    ![Incorporación al grupo de direcciones de back-end ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Compruebe que la lista desplegable de notificaciones tiene una actualización respecto a guardar el grupo de back-end del equilibrador de carga además de actualizar la interfaz de red para las máquinas virtuales **web1** y **web2**.

## <a name="create-a-probe-lb-rule-and-nat-rules"></a>Creación de un sondeo, una regla de equilibrador de carga y reglas NAT

1. Cree un sondeo de estado.

    En la sección Configuración del equilibrador de carga, seleccione Sondeos. A continuación, haga clic en **Agregar** en la parte superior de la hoja.

    Hay dos formas de configurar un sondeo: HTTP o TCP. En este ejemplo se muestra HTTP, pero TCP puede configurarse de forma similar.
    Actualice la información necesaria. Como se mencionó, **myLoadBalancer** equilibra la carga del tráfico en el puerto 80. La ruta de acceso seleccionada es HealthProbe.aspx, el intervalo es 15 segundos y el umbral incorrecto es 2. Una vez terminado, haga clic en **Aceptar** para crear el sondeo.

    Mantenga el puntero sobre el icono de "i" para más información acerca de estas configuraciones individuales y cómo se pueden cambiar para satisfacer sus requisitos.

    ![Incorporación de un sondeo](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

2. Cree una regla de equilibrador de carga.

    Haga clic en Reglas de equilibrio de carga en la sección Configuración del equilibrador de carga. En la nueva hoja, haga clic en **Agregar**. Ponga un nombre a la regla. En este caso, HTTP. Elija los puertos de front-end y de back-end. En este caso, se elige 80 para ambos. Elija **LB-backend** como grupo de back-end y el **HealthProbe** creado previamente como Sondeo. Según sus requisitos, se pueden establecer otras configuraciones. A continuación, haga clic en Aceptar para guardar la regla de equilibrio de carga.

    ![Incorporación de una regla de equilibrio de carga](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

3. Creación de reglas NAT de entrada

    Haga clic en Reglas NAT de entrada en la sección Configuración de su equilibrador de carga. En la hoja nueva, haga clic en **Agregar**. Luego ponga un nombre a la regla NAT de entrada. Aquí se llama **inboundNATrule1**. El destino debe ser la dirección IP pública que se creó anteriormente. Seleccione Personalizar en Servicio y seleccione el protocolo que desea utilizar. Aquí se selecciona TCP. Escriba el puerto, 3441, y el puerto de destino, en este caso 3389. A continuación, haga clic en Aceptar para guardar esta regla.

    Una vez creada la primera regla, repita este paso para la segunda regla NAT entrante denominada inboundNATrule2 desde el puerto 3442 al puerto de destino 3389.

    ![Incorporación de una regla NAT entrante](./media/load-balancer-get-started-internet-portal/6-load-balancer-inbound-nat-rules.png)

## <a name="remove-a-load-balancer"></a>Eliminación de un equilibrador de carga

Para eliminar un equilibrador de carga, seleccione el equilibrador de carga que desea quitar. En la hoja *Equilibrador de carga* , haga clic en **Eliminar** en la parte superior de la hoja. A continuación, seleccione **Sí** cuando se le solicite.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
