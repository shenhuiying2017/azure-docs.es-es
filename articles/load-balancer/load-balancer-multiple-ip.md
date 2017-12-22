---
title: Equilibrio de carga en varias configuraciones de IP en Azure | Microsoft Docs
description: Equilibrio de carga entre las configuraciones de IP principales y secundarias.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ecb64aa13b3b08f7b054a0665df3dc0cdb3e09bd
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Equilibrio de carga en varias configuraciones de IP mediante Azure Portal

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

En este artículo, le mostraremos cómo usar Azure Load Balancer con varias direcciones IP en una controladora de interfaz de red secundaria (NIC). En el siguiente diagrama se ilustra nuestro escenario:

![Escenario del equilibrador de carga](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

En nuestro escenario, usamos la siguiente configuración:

- Dos máquinas virtuales (VM) que ejecutan Windows.
- Cada VM tiene una NIC principal y otra secundaria.
- Cada NIC secundaria tiene dos configuraciones IP.
- Cada máquina virtual hospeda dos sitios web: contoso.com y fabrikam.com.
- Cada uno de los sitios web está enlazado a una configuración de IP en la NIC secundaria.
- Azure Load Balancer se utiliza para exponer dos direcciones IP de servidor front-end, una para cada sitio web. Las direcciones de servidor front-end se utilizan para distribuir el tráfico a la configuración IP correspondiente para cada sitio web.
- Se usa el mismo número de puerto para las direcciones IP de servidor front-end y direcciones IP de grupo back-end.

## <a name="prerequisites"></a>Requisitos previos

En nuestro ejemplo de escenario se da por supuesto que tiene un grupo de recursos denominado **contosofabrikam** con la siguiente configuración:

- El grupo de recursos incluye una red virtual denominada **myVNet**.
- La red **myVNet** incluye dos VM con el nombre **VM1** y **VM2**.
- VM1 y VM2 están en el mismo conjunto de disponibilidad denominado **myAvailset**. 
- VM1 y VM2 tienen una NIC principal con los nombres **VM1NIC1** y **VM2NIC1**, respectivamente. 
- VM1 y VM2 tienen una NIC secundaria con los nombres **VM1NIC2** y **VM2NIC2**, respectivamente.

Para más información sobre la creación de máquinas virtuales con varias NIC, vea [Creación de una máquina virtual con varias NIC mediante PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Realización de equilibrio de carga en varias configuraciones de IP

Complete los pasos siguientes para reproducir el escenario que se describe en este artículo.

### <a name="step-1-configure-the-secondary-nics"></a>Paso 1: Configuración de las NIC secundarias

Para cada máquina virtual de la red virtual, agregue la configuración IP para la NIC secundaria:  

1. Vaya a Azure Portal: http://portal.azure.com. Inicie sesión con su cuenta de Azure.

2. En la parte superior izquierda de la pantalla, seleccione el icono **Grupo de recursos**. A continuación, seleccione el grupo de recursos donde se encuentran las VM (por ejemplo, **contosofabrikam**). El panel **Grupos de recursos** se muestran todos los recursos y las NIC para las VM.

3. Agregue a la NIC secundaria de cada VM la configuración de IP:

    1. Seleccione la NIC secundaria que desee configurar.
    
    2. Seleccione **Configuraciones IP**. En el siguiente panel, cerca de la parte superior, seleccione **Agregar**.

    3. En **Agregar configuración IP**, agregue una segunda configuración de IP a la NIC: 

        1. Escriba un nombre para la configuración de IP secundaria. (por ejemplo, para VM1 y VM2, asigne a las configuraciones de IP los nombres **VM1NIC2-ipconfig2** y **VM2NIC2-ipconfig2** respectivamente).

        2. Para **Dirección IP privada**, en la configuración **Asignación**, seleccione **Estática**.

        3. Seleccione **Aceptar**.

Cuando haya finalizado la segunda configuración de IP para la NIC secundaria, se muestra en la configuración **Configuraciones de IP** de la NIC en cuestión.

### <a name="step-2-create-the-load-balancer"></a>Paso 2: Creación del equilibrador de carga

Creación del equilibrador de carga para la configuración:

1. Vaya a Azure Portal: http://portal.azure.com. Inicie sesión con su cuenta de Azure.

2. En la parte superior izquierda de la pantalla, seleccione **Nuevo** > **Redes** > **Load Balancer**. A continuación, seleccione **Crear**.

3. En **Crear equilibrador de carga**, escriba un nombre para el equilibrador de carga. En este escenario, usamos el nombre **mylb**.

4. En **Dirección IP pública**, cree una nueva dirección IP pública denominada **PublicIP1**.

5. En **Grupo de recursos**, seleccione el grupo de recursos existente de sus máquinas virtuales (por ejemplo, **contosofabrikam**). Seleccione la ubicación para implementar el equilibrador de carga y, luego, seleccione **Aceptar**.

El equilibrador de carga comienza a realizar la implementación. La implementación puede tardar unos minutos en completarse. Una vez completada la implementación, el equilibrador de carga se muestra como un recurso en el grupo de recursos.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Paso 3: Configuración del grupo de direcciones IP de servidor front-end

Para cada sitio web (contoso.com y fabrikam.com), configure el grupo de dirección IP de servidor front-end en el equilibrador de carga:

1. En el portal, seleccione **Más servicios**. En el cuadro de filtro, escriba **Dirección IP pública** y, a continuación, seleccione **Direcciones IP públicas**. En el siguiente panel, cerca de la parte superior, seleccione **Agregar**.

2. Configure dos direcciones IP públicas (**PublicIP1** y **PublicIP2**) para ambos sitios web (contoso.com y fabrikam):

    1. Escriba un nombre para la dirección IP de servidor front-end.

    2. En **Grupo de recursos**, seleccione el grupo de recursos existente de sus máquinas virtuales (por ejemplo, **contosofabrikam**).

    3. Para **Ubicación**, seleccione la misma ubicación que la de las máquinas virtuales.

    4. Seleccione **Aceptar**.

    Una vez creadas las direcciones IP públicas, se muestran en las **direcciones IP públicas**.

3. <a name="step3-3"></a>En el portal, seleccione **Más servicios**. En el cuadro de filtro, escriba **load balancer** y, luego, seleccione **Load Balancer**. 

4. Seleccione el equilibrador de carga (**mylb**) al que desea agregar el grupo de direcciones IP de servidor front-end.

5. En **Configuración**, seleccione **Frontend Pools** (Grupos de servidores front-end). En el siguiente panel, cerca de la parte superior, seleccione **Agregar**.

6. Escriba un nombre para la dirección IP de servidor front-end (por ejemplo, **contosofe** o **fabrikamfe**).

7. <a name="step3-7"></a>Seleccione **Dirección IP**. En **Elegir dirección IP pública**, seleccione las direcciones IP para servidor front-end (**PublicIP1** o **PublicIP2**).

8. Cree la segunda dirección IP de servidor front-end repitiendo el <a href="#step3-3">paso 3</a> hasta el <a href="#step3-7">paso 7</a> en esta sección.

Después de configurar el grupo de servidores front-end, las direcciones IP se muestran en la configuración **Grupo IP front-end** del equilibrador de carga. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Paso 4: Configuración del grupo de servidores back-end

Para cada sitio web (contoso.com y fabrikam.com), configure el grupo de direcciones de servidores back-end en el equilibrador de carga:
        
1. En el portal, seleccione **Más servicios**. En el cuadro de filtro, escriba **load balancer** y, luego, seleccione **Load Balancer**.

2. Seleccione el equilibrador de carga (**mylb**) al que va a agregar el grupo de servidores back-end.

3. En **Configuración**, seleccione **Grupos de back-end**. Escriba un nombre para el grupo de servidores back-end (por ejemplo, **contosopool** o **fabrikampool**). En el siguiente panel, cerca de la parte superior, seleccione **Agregar**. 

4. En **Asociado a**, seleccione **Conjunto de disponibilidad**.

5. En **Conjunto de disponibilidad**, seleccione **myAvailset**.

6. Agregue las configuraciones de IP de la red de destino para ambas VM: 

    ![Configuración de grupos de servidores back-end para el equilibrador de carga](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. En **Máquina virtual de destino**, seleccione la máquina virtual que desea agregar al grupo de servidores back-end (por ejemplo, **VM1** o **VM2**).

    2. En **Network IP configuration** (Configuración de IP de red), seleccione la configuración de IP de la NIC secundaria de la VM que seleccionó en el paso anterior (por ejemplo, **VM1NIC2-ipconfig2** o **VM2NIC2-ipconfig2**).

7. Seleccione **Aceptar**.

Después de configurar el grupo de servidores back-end, las direcciones se muestran en la configuración **Grupo back-end** del equilibrador de carga.

### <a name="step-5-configure-the-health-probe"></a>Paso 5: Configuración del sondeo de mantenimiento

Configuración de un sondeo de estado para el equilibrador de carga:

1. En el portal, seleccione **Más servicios**. En el cuadro de filtro, escriba **load balancer** y, luego, seleccione **Load Balancer**.

2. Seleccione el equilibrador de carga (**mylb**) al que desee agregar el sondeo de mantenimiento.

3. En **Configuración**, seleccione **Sondeo de estado**. En el siguiente panel, cerca de la parte superior, seleccione **Agregar**. 

4. Escriba un nombre para el sondeo de estado (por ejemplo, **HTTP**). Seleccione **Aceptar**.

### <a name="step-6-configure-load-balancing-rules"></a>Paso 6: Configuración de las reglas de equilibrio de carga

Para cada sitio web (contoso.com y fabrikam.com), configure las reglas de equilibrio de carga:
    
1. <a name="step6-1"></a>En **Configuración**, seleccione **Sondeo de estado**. En el siguiente panel, cerca de la parte superior, seleccione **Agregar**. 

2. En **Nombre**, escriba un nombre para la regla de equilibrio de carga (por ejemplo, **HTTPc** para contoso.com, o **HTTPf** para fabrikam.com).

3. En **Dirección IP de front-end**, seleccione la dirección IP de servidor front-end creada previamente (por ejemplo **contosofe** o **fabrikamfe**).

4. En **Puerto** y **Puerto back-end**, mantenga el valor predeterminado de **80**.

5. En **IP flotante (Direct Server Return)**, seleccione **Habilitado**.

6. <a name="step6-6"></a>Seleccione **Aceptar**.

7. Cree la segunda regla del equilibrador de carga repitiendo el <a href="#step6-1">paso 1</a> hasta el <a href="#step6-6">paso 6</a> en esta sección.

Una vez configuradas las reglas, se muestran en el equilibrador de carga la configuración de **reglas de equilibrio de carga**.

### <a name="step-7-configure-dns-records"></a>Paso 7: Configuración de los registros DNS

Como último paso, configure los registros de recursos de DNS para que apunten a las direcciones IP de servidor front-end respectivas para el equilibrador de carga. Puede hospedar los dominios en Azure DNS. Para más información sobre el uso de Azure DNS con Load Balancer, consulte [Uso de Azure DNS con otros servicios de Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Pasos siguientes
- Aprenda más sobre cómo combinar servicios de equilibrio de carga en Azure en [Uso de servicios de equilibrio de carga de Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Aprenda a usar diferentes tipos de registros para administrar el equilibrador de carga y solucionar sus problemas en [Log Analytics para Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
