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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8c0fc8d11a872b99fee2efa3a32a9e1ccce67f3c
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="load-balancing-on-multiple-ip-configurations-using-the-azure-portal"></a>Equilibrio de carga en varias configuraciones de IP mediante el portal de Azure

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

En este artículo se describe cómo usar Azure Load Balancer con varias direcciones IP en una interfaz de red secundaria (NIC). En este escenario, tenemos dos máquinas virtuales que ejecutan Windows, cada una con una NIC principal y otra secundaria. Cada NIC secundario tiene dos configuraciones IP. Cada máquina virtual hospeda dos sitios web: contoso.com y fabrikam.com. Cada uno de los sitios web está enlazado a una de las configuraciones de IP de la NIC secundaria. Usamos Azure Load Balancer para exponer dos direcciones IP front-end, una por cada sitio web, que van a distribuir el tráfico a la configuración de IP correspondiente del sitio web. En este escenario, se utiliza el mismo número de puerto en los dos front-end, así como en las dos direcciones IP del grupo de back-end.

![Imagen del escenario de equilibrio de carga](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

##<a name="prerequisites"></a>Requisitos previos
En este ejemplo se da por supuesto que tiene un grupo de recursos denominado *contosofabrikam* con la siguiente configuración:
 -  Incluye una red virtual denominada *myVNet* y dos máquinas virtuales denominadas *VM1* y *VM2* respectivamente en el mismo conjunto de disponibilidad denominado *myAvailset*. 
 - Cada máquina virtual tiene una NIC principal y otra secundaria. Las NIC principales se denominan *VM1NIC1* y *VM2NIC1* y las NIC secundarias *VM1NIC2* y *VM2NIC2*. Para más información sobre la creación de máquinas virtuales con varias NIC, consulte [Creación de una máquina virtual con varias NIC mediante PowerShell](../virtual-network/virtual-network-deploy-multinic-arm-ps.md).

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Pasos para equilibrar la carga en varias configuraciones de IP

Complete los pasos siguientes para reproducir el escenario que se describe en este artículo:

### <a name="step-1-configure-the-secondary-nics-for-each-vm"></a>PASO 1: Configuración de la NIC secundaria para cada máquina virtual

Para cada máquina virtual de la red virtual, agregue la configuración de IP definida para la NIC secundaria de la manera siguiente:  

1. En un explorador, vaya al portal de Azure: http://portal.azure.com e inicie sesión con su cuenta de Azure.
2. En la parte superior izquierda de la pantalla, haga clic en el icono Grupo de recursos y luego haga clic en el grupo de recursos donde están ubicadas las máquinas virtuales (por ejemplo, *contosofabrikam*). Se muestra ahora la hoja **Grupos de recursos** con todos los recursos, además de las interfaces de red de las máquinas virtuales.
3. Agregue a la NIC secundaria de cada máquina virtual una configuración de IP de la forma siguiente:
    1. Seleccione la interfaz de red a la que se va a agregar la configuración de IP.
    2. En la hoja que aparece para la NIC que ha seleccionado, haga clic en **Configuraciones de IP**. A continuación, haga clic en **Agregar** hacia la parte superior de la hoja que se muestra.
    3. En la hoja **Agregar configuración IP**, agregue una segunda configuración de IP a la NIC de la manera siguiente: 
        1. Escriba un nombre para la configuración de IP secundaria (por ejemplo, para VM1 y VM2, asigne a las configuraciones de IP los nombres *VM1NIC2-ipconfig2* y *VM2NIC2-ipconfig2* respectivamente).
        2. Para **Dirección IP privada**, en **Asignación**, seleccione **Estática**.
        3. Haga clic en **Aceptar**.
        4. Cuando haya finalizado la segunda configuración de IP para la NIC secundaria, se muestra en la hoja **Configuraciones de IP** de la NIC en cuestión.

### <a name="step-2-create-a-load-balancer"></a>PASO 2: Creación de un equilibrador de carga

Cree un equilibrador de carga de la manera siguiente:

1. En un explorador, vaya al portal de Azure: http://portal.azure.com e inicie sesión con su cuenta de Azure.
2. En la parte superior izquierda de la pantalla, haga clic en **Nuevo** > **Redes** > **Equilibrador de carga**. A continuación, haga clic en **Crear**.
3. En la hoja **Crear equilibrador de carga** , escriba un nombre para el equilibrador de carga. Aquí se denomina *mylb*.
4. En Dirección IP pública, cree una nueva dirección IP pública denominada **PublicIP1**.
5. En Grupo de recursos, seleccione el grupo de recursos existente de sus máquinas virtuales (por ejemplo, *contosofabrikam*). A continuación, seleccione una ubicación adecuada y haga clic en **Aceptar**. El equilibrador de carga empieza entonces a implementarse. Este proceso puede tardar unos minutos en completarse correctamente.
6. Una vez implementado, el equilibrador de carga se muestra como un recurso en el grupo de recursos.

### <a name="step-3-configure-the-frontend-ip-pool"></a>PASO 3: Configuración del grupo de direcciones IP de front-end

Configure el grupo de direcciones IP del front-end para cada sitio web (contoso y fabrikam) de la manera siguiente:

1. En el portal, haga clic en **More services** (Más servicios) > escriba la **dirección IP pública** en el cuadro de filtro y luego haga clic en **Direcciones IP públicas**. Haga clic en **Agregar** hacia la parte superior de la hoja que se muestra.
2. Configure dos direcciones IP públicas (*PublicIP1* y *PublicIP2*) para ambos sitios web (contoso y fabrikam), como se indica a continuación:
    1. Escriba un nombre para la dirección IP de front-end.
    2. Para **Grupo de recursos**, seleccione el grupo de recursos de las máquinas virtuales (por ejemplo, *contosofabrikam*).
    3. Para **Ubicación**, seleccione la misma ubicación que la de las máquinas virtuales.
    4. Haga clic en **Aceptar**.
    5. Una vez creadas las dos direcciones IP públicas, se muestran en la hoja **Direcciones IP públicas**.
3. En el portal, haga clic en **More services** (Más servicios) > escriba **equilibrador de carga** en el cuadro de filtro y luego haga clic en **Equilibrador de carga**.  
4. Seleccione el equilibrador de carga (*mylb*) al que desea agregar el grupo de direcciones IP de front-end.
5. En **Configuración**, seleccione **Frontend Pools** (Grupos de servidores front-end). A continuación, haga clic en **Agregar** hacia la parte superior de la hoja que se muestra.
6. Escriba un nombre para la dirección IP de front-end (*farbikamfe* o *contosofe*).
7. Haga clic en **Dirección IP** y en la hoja **Elegir dirección IP pública**, seleccione las direcciones IP de su front-end (*PublicIP1* o *PublicIP2*).
8. Para crear la segunda dirección IP de front-end, repita los pasos del 3 al 7 de esta sección.
9. Cuando se ha completado la configuración del grupo de direcciones IP de front-end, ambas direcciones IP de front-end se muestran en la hoja **Grupo de direcciones IP de front-end** del equilibrador de carga. 
    
### <a name="step-4-configure-the-backend-pool"></a>PASO 4: Configuración del grupo de back-end   
Configure los grupos de direcciones de back-end en el equilibrador de carga para cada sitio web (contoso y fabrikam) de la manera siguiente:
        
1. En el portal, haga clic en **More services** (Más servicios) > escriba equilibrador de carga en el cuadro de filtro y luego haga clic en **Equilibrador de carga**.  
2. Seleccione el equilibrador de carga (*mylb*) al que va a agregar los grupos de back-end.
3. En **Configuración**, seleccione **Grupos de back-end**. Escriba un nombre para el grupo de back-end (por ejemplo, *contosopool* o *fabrikampool*). A continuación, haga clic en el botón **Agregar** hacia la parte superior de la hoja que aparece. 
4. En **Asociado a**, seleccione **Conjunto de disponibilidad**.
5. En **Conjunto de disponibilidad**, seleccione **myAvailset**.
6. Agregue las configuraciones de IP de la red de destino para ambas máquinas virtuales de la manera siguiente (consulte la figura 2):  
    1. En **Máquina virtual de destino**, seleccione la máquina virtual que desea agregar al grupo de back-end (por ejemplo, VM1 o VM2).
    2. En **Network IP configuration** (Configuración de IP de red), seleccione la configuración de IP de la NIC secundaria de esa máquina virtual (por ejemplo, VM1NIC2-ipconfig2 o VM2NIC2-ipconfig2).
    ![Imagen del escenario de equilibrio de carga](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
            
        **Figura 2**: Configuración del equilibrador de carga con grupos de back-end  
7. Haga clic en **Aceptar**.
8. Cuando se ha completado la configuración del grupo de direcciones IP de back-end, ambos grupos de direcciones de back-end se muestran en la hoja **Grupo de back-end** del equilibrador de carga.

### <a name="step-5-configure-a-health-probe-for-your-load-balancer"></a>PASO 5: Configuración de un sondeo de estado para el equilibrador de carga
Configure un sondeo de estado para el equilibrador de carga de la manera siguiente:
    1. En el portal, haga clic en More services (Más servicios) > escriba equilibrador de carga en el cuadro de filtro y luego haga clic en **Equilibrador de carga**.  
    2. Seleccione el equilibrador de carga al que va a agregar los grupos de back-end.
    3. En **Configuración**, seleccione **Sondeo de estado**. A continuación, haga clic en **Agregar** hacia la parte superior de la hoja que se muestra.
    4. Escriba un nombre para el sondeo de estado (por ejemplo, HTTP) y haga clic en **Aceptar**.

### <a name="step-6-configure-load-balancing-rules"></a>PASO 6: Configuración de las reglas de equilibrio de carga
Configure reglas de equilibrio de carga (*HTTPc* y *HTTPf*) para cada sitio web como se indica a continuación:
    
1. En **Configuración**, seleccione **Sondeo de estado**. A continuación, haga clic en **Agregar** hacia la parte superior de la hoja que se muestra.
2. En **Nombre**, escriba un nombre para la regla de equilibrio de carga (por ejemplo, *HTTPc* para contoso, o *HTTPf* para fabrikam).
3. En Dirección IP de front-end, seleccione la dirección IP de front-end (por ejemplo *Contosofe* o *Fabrikamfe*).
4. En **Puerto** y **Puerto back-end**, mantenga el valor predeterminado de **80**.
5. En **IP flotante (Direct Server Return)**, haga clic en **Habilitado**.
6. Haga clic en **Aceptar**.
7. Repita los pasos del 1 al 6 de esta sección para crear la segunda regla del equilibrador de carga.
8. Cuando finalice la configuración de las reglas de equilibrio de carga, ambas reglas ((*HTTPc* y *HTTPf*) se mostrarán en la hoja **Reglas de equilibrio de carga** del equilibrador de carga.

### <a name="step-7-configure-dns-records"></a>PASO 7: Configuración de los registros DNS
Por último, debe configurar los registros de recursos DNS para que apunten a la dirección IP de front-end correspondiente del equilibrador de carga. Puede hospedar los dominios en Azure DNS. Para más información sobre el uso de Azure DNS con Load Balancer, consulte [Uso de Azure DNS con otros servicios de Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Pasos siguientes
- Aprenda más sobre cómo combinar servicios de equilibrio de carga en Azure en [Uso de servicios de equilibrio de carga de Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Aprenda a usar diferentes tipos de registros para administrar el equilibrador de carga y solucionar sus problemas en [Log Analytics para Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).

