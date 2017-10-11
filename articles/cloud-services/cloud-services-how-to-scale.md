---
title: "Escalado automático de un servicio en la nube en el portal clásico | Microsoft Docs"
description: "(clásico) Obtenga información sobre cómo usar el portal clásico para configurar reglas de escalado automático de un rol de trabajo o un rol web de servicio en la nube en Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: eb167d70-4eba-42a4-b157-d8d0688abf4b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 90d55bbac6e113d6add848ace67cf0749e26342b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-classic-portal"></a>Configuración del escalado automático para un servicio en la nube en el portal clásico
> [!div class="op_single_selector"]
> * [Portal de Azure](cloud-services-how-to-scale-portal.md)
> * [Portal de Azure clásico](cloud-services-how-to-scale.md)

En la página Escala del portal de Azure clásico, puede definir la configuración del escalado automático para el rol web o el rol de trabajo. También puede configurar el escalado manual en lugar del escalado automático basado en reglas.

> [!NOTE]
> Este artículo se centra en los roles de trabajo y en los roles web de Servicio en la nube. Al crear una máquina virtual (clásica) directamente, esta se hospeda en un servicio en la nube. Parte de esta información se aplica a estos tipos de máquinas virtuales. Escalar un conjunto de disponibilidad de máquinas virtuales consiste simplemente en encenderlas y apagarlas basándose en las reglas de escalado que configure. Para más información sobre las máquinas virtuales y los conjuntos de disponibilidad, consulte [Administración de la disponibilidad de las máquinas virtuales](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

Debe considerar la siguiente información antes de configurar el escalado para su aplicación:

* El uso de núcleos afecta el escalado.

    Las instancias de rol más grandes usan más núcleos. Solo puede escalar una aplicación dentro del límite de núcleos para su suscripción. Por ejemplo, supongamos que su suscripción tiene un límite de 20 núcleos. Si ejecuta una aplicación con dos servicios en la nube de tamaño mediano (un total de 4 núcleos), solo puede escalar verticalmente otras implementaciones del servicio en la nube en su suscripción con los 16 núcleos restantes. Para obtener más información sobre los tamaños, consulte [Tamaños de los servicios en la nube](cloud-services-sizes-specs.md).

* Debe crear una cola y asociarla con un rol para poder escalar una aplicación según el umbral de un mensaje. Para obtener más información, consulte [Uso del servicio de almacenamiento de colas](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Puede escalar los recursos que están vinculados con su servicio en la nube. Para obtener más información acerca de los recursos de vinculación, consulte [Vinculación de un recurso a un servicio en la nube](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

* Para permitir una alta disponibilidad de la aplicación, debe asegurarse de que se implemente con dos o más instancias de rol. Para obtener más información, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

## <a name="schedule-scaling"></a>Programar escalado
De forma predeterminada, todos los roles no siguen una programación específica. Por lo tanto, cualquier configuración modificada se aplica a cualquier hora y día durante todo el año. Si quiere, puede configurar el escalado automático o manual para uno de los modos siguientes:

* Días laborables
* Fines de semana
* Noches entre semana
* Mañanas entre semana
* Fechas específicas
* Intervalos de fechas específicas

La opción de programación se configura en el [Portal de Azure clásico](https://manage.windowsazure.com/) en la página  
**Cloud Services** > **\[Su servicio en la nube\]** > **Escala** > **\[Producción y almacenamiento provisional\]**.

Haga clic en el botón **Configurar horas de programación** para cada rol que quiera cambiar.

![Escalado automático en un servicio en la nube basado en una programación][scale_schedules]

## <a name="manual-scale"></a>Escala manual
En la página **Escala** , puede aumentar o disminuir manualmente la cantidad de instancias en ejecución en un servicio en la nube. Esta opción se configura para cada programación que haya creado o para todas si no ha creado una programación.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
   
   > [!TIP]
   > Si no ve el servicio en la nube, debe cambiar de **Producción** a **Almacenamiento provisional** o viceversa.

2. Haga clic en **Escalar**.
3. Seleccione la programación en la que quiere cambiar las opciones de escalado. *No hay horas programadas* es el valor predeterminado si no dispone de ninguna programación definida.
4. Busque la sección **Escalar por métrica** y seleccione **NINGUNA**. Esta es la configuración predeterminada de todos los roles.
5. Cada rol en el servicio en la nube tiene un control deslizante para cambiar la cantidad de instancias que se van a usar.
   
    ![Escalar manualmente un rol de servicio en la nube][manual_scale]
   
    Si necesita más instancias, debe cambiar el [tamaño de la máquina virtual del servicio en la nube](cloud-services-sizes-specs.md).
6. Haga clic en **Guardar**.  
   Las instancias de rol se agregan o quitan según las selecciones realizadas.

> [!TIP]
> Siempre que vea ![][tip_icon], mueva el mouse y podrá obtener ayuda acerca de lo que realiza una configuración específica.

## <a name="automatic-scale---cpu"></a>Escala automática: CPU
Este modo escala si el porcentaje medio de uso de la CPU está por encima o por debajo de los umbrales especificados. Las instancias de rol se crean o eliminan cuando esto ocurre.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
   
   > [!TIP]
   > Si no ve el servicio en la nube, debe cambiar de **Producción** a **Almacenamiento provisional** o viceversa.

2. Haga clic en **Escalar**.
3. Seleccione la programación en la que quiere cambiar las opciones de escalado. *No hay horas programadas* es el valor predeterminado si no dispone de ninguna programación definida.
4. Busque la sección **Escalar por métrica** y seleccione **CPU**.
5. Ahora puede configurar un intervalo máximo y mínimo de instancias de rol, el uso de la CPU de destino (para desencadenar una escalada vertical) y el número de instancias para escalar y reducir verticalmente.

![Escalar un rol de servicio en la nube con la carga de la CPU][cpu_scale]

> [!TIP]
> Siempre que vea ![][tip_icon], mueva el mouse y podrá obtener ayuda acerca de lo que realiza una configuración específica.

## <a name="automatic-scale---queue"></a>Escala automática: Cola
Este modo escala automáticamente si el número de mensajes en una cola está por encima o por debajo de un umbral especificado. Las instancias de rol se crean o eliminan cuando esto ocurre.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
   
   > [!TIP]
   > Si no ve el servicio en la nube, debe cambiar de **Producción** a **Almacenamiento provisional** o viceversa.

2. Haga clic en **Escalar**.
3. Busque la sección **Escalar por métrica** y seleccione **COLA**.
4. Ahora puede configurar un intervalo máximo y mínimo de instancias de rol, la cola y el número de mensajes de cola para procesar por cada instancia, y el número de instancias para escalar y reducir verticalmente.

![Escalar un rol de servicio en la nube con una cola de mensajes][queue_scale]

> [!TIP]
> Siempre que vea ![][tip_icon], mueva el mouse y podrá obtener ayuda acerca de lo que realiza una configuración específica.

## <a name="scale-linked-resources"></a>Escalado de recursos vinculados
A menudo, cuando se escala un rol, es beneficioso también escalar la base de datos que la aplicación está usando. Si vincula la base de datos al servicio en la nube, puede acceder a la configuración de escalado de ese recurso haciendo clic en el vínculo apropiado.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
   
   > [!TIP]
   > Si no ve el servicio en la nube, debe cambiar de **Producción** a **Almacenamiento provisional** o viceversa.

2. Haga clic en **Escalar**.
3. Busque la sección **recursos vinculados** y haga clic en **Administrar el escalado de esta base de datos**.
   
   > [!NOTE]
   > Si no ve la sección **recursos vinculados** , probablemente no tenga ningún recurso vinculado.

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
