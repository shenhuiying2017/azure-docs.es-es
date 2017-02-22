---
title: "Creación de un equilibrador de carga con conexión a Internet: Portal de Azure clásico | Microsoft Docs"
description: "Aprenda a crear un equilibrador de carga accesible desde Internet en el modelo de implementación clásica mediante el Portal de Azure clásico"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: a022154f5eca6de2d2dbfc1b9aa30d2ea0a7d650

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Introducción a la creación de un equilibrador de carga accesible desde Internet (clásico) en el Portal de Azure clásico

> [!div class="op_single_selector"]
> * [Portal de Azure clásico](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [CLI de Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: Azure Resource Manager y el clásico. Asegúrese de que comprende los [modelos de implementación y las herramientas](../azure-classic-rm.md) antes de trabajar con recursos de Azure. Puede ver la documentación de las distintas herramientas haciendo clic en las fichas en la parte superior de este artículo. Este artículo trata sobre el modelo de implementación clásico. También puede [obtener información sobre cómo crear un equilibrador de carga orientado a Internet con el Administrador de recursos de Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configuración de un equilibrador de carga accesible desde Internet para máquinas virtuales

Para equilibrar la carga del tráfico de red de Internet entre las máquinas virtuales de un servicio en la nube, debe crear un conjunto con equilibrio de carga. En este procedimiento se supone que ya ha creado las máquinas virtuales y que están toda dentro del mismo servicio en la nube.

**Para configurar un equilibrio de carga establecido para máquinas virtuales**

1. En el Portal de Azure  clásico, haga clic en **Máquinas virtuales**y luego en el nombre de una máquina virtual del conjunto con equilibrio de carga.
2. Haga clic en **Puntos de conexión** y luego en **Agregar**.
3. En la página **Agregar un extremo a una máquina virtual** , haga clic en la flecha derecha.
4. En la página **Specify the details of the endpoint** :

   * En **Name**, escriba un nombre para el extremo o seleccione uno de la lista de extremos predefinidos para protocolos comunes.
   * En **Protocol**, seleccione el protocolo que requiere el tipo de extremo, TCP o UDP, según sea necesario.
   * En **Public Port y Private Port**, escriba los números de puerto que desee que utilice la máquina virtual, según sea necesario. Puede utilizar el puerto privado y las reglas de firewall en la máquina virtual para redirigir el tráfico de la manera más adecuada para su aplicación. El puerto privado puede ser el mismo que el puerto público. Por ejemplo, para un extremo para tráfico web (HTTP), puede asignar el puerto 80 al puerto público y el privado.

5. Haga clic en **Create a load-balanced set**y, a continuación, en la flecha derecha.
6. En la página **Configure the load-balanced set** , especifique un nombre para el conjunto con equilibrio de carga y, a continuación, asigne los valores para probar el comportamiento del equilibrador de carga de Azure. El equilibrador de carga utiliza pruebas para determinar si las máquinas virtuales del conjunto con equilibrio de carga están disponibles para recibir tráfico entrante.
7. Haga clic en la marca de verificación para crear el extremo con equilibrio de carga. Verá **Sí** en la columna **Nombre de conjunto de carga equilibrada** de la página **Puntos de conexión** de la máquina virtual.
8. En el portal, haga clic en **Máquinas virtuales**, en el nombre de otra máquina virtual del conjunto con equilibrio de carga, en **Puntos de conexión** y, finalmente, en **Agregar**.
9. En la página **Agregar un extremo a una máquina virtual**, haga clic en **Add endpoint to an existing load-balanced set** (Agregar punto de conexión a un conjunto de carga equilibrada existente), seleccione el nombre del conjunto con equilibrio de carga y haga clic en la flecha derecha.
10. En la página **Specify the details of the endpoint** , escriba un nombre para el extremo y, a continuación, haga clic en la marca de verificación.

Para las máquinas virtuales adicionales del conjunto con equilibrio de carga, repita los pasos del 8 al 10.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Jan17_HO4-->


