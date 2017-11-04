---
title: Supervisar dispositivos de la serie StorSimple 8000 | Microsoft Docs
description: "Se describe cómo usar el servicio Administrador de dispositivos de StorSimple para supervisar el uso, el rendimiento de E/S y el uso de la capacidad."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 679c1fc8775ad4481bc99c9aea79fe16e9bcac8f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2017
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Usar el servicio Administrador de dispositivos de StorSimple para supervisar el dispositivo StorSimple

## <a name="overview"></a>Información general
Puede usar el servicio Administrador de dispositivos de StorSimple para supervisar dispositivos específicos dentro de la solución StorSimple. Puede crear gráficos personalizados basados en el rendimiento de E/S, el uso de la capacidad, el rendimiento de la red y las métricas de rendimiento del dispositivo, y anclarlos al panel. Para más información, vaya a [personalizar el panel del portal](../azure-portal/azure-portal-dashboards.md).

Para ver la información de supervisión de un dispositivo específico, en Azure Portal, seleccione el servicio Administrador de dispositivos de StorSimple. En la lista de dispositivos, seleccione el dispositivo y, después, vaya a **Supervisar**. Podrá ver los gráficos **Capacidad**, **Uso** y **Rendimiento** para el dispositivo seleccionado.

## <a name="capacity"></a>Capacity
**Capacidad** realiza un seguimiento del espacio aprovisionado y del espacio restante en el dispositivo. La capacidad restante se muestra después como anclada localmente o en niveles.

La capacidad de aprovisionamiento y la capacidad restante se dividen en más volúmenes anclados localmente y en niveles. Para cada volumen, se muestra la capacidad aprovisionada y la capacidad restante en el dispositivo.

![Capacidad de E/S](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Uso
**Uso** realiza un seguimiento de las métricas relacionadas con la cantidad de espacio de almacenamiento de datos usado por los volúmenes, contenedores de volúmenes o dispositivos. Puede crear informes basados en la utilización de la capacidad del almacenamiento principal, el almacenamiento en la nube o el almacenamiento del dispositivo. La utilización de la capacidad se puede medir para un volumen específico, un contenedor de volúmenes específico o todos los contenedores de volúmenes.
De forma predeterminada, se notifica el uso de las últimas 24 horas. Puede editar el gráfico para cambiar la duración en la que se notifica el uso mediante la selección de:
* Últimas 24 horas
* Últimos 7 días
* Últimos 30 días
* Últimos 90 días
* Últimos año

Para los gráficos de utilización, se notifican dos métricas clave: el crecimiento y el rango. El rango hace referencia al valor máximo y a los valores mínimos de utilización notificados durante el tiempo seleccionado (por ejemplo, los últimos 7 días).

El crecimiento hace referencia al aumento de la utilización desde el primer día hasta el último día durante el tiempo seleccionado. 

El crecimiento y el rango también se pueden representar mediante las ecuaciones siguientes:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

La capacidad de almacenamiento principal, en la nube y local puede describirse como sigue:

### <a name="primary-storage-usage"></a>Uso del almacenamiento principal
Estos gráficos muestran la cantidad de datos escritos en volúmenes StorSimple antes de que los datos se desdupliquen y se compriman. Puede ver el uso del almacenamiento principal que hacen todos los volúmenes de un contenedor de volúmenes o de un solo volumen. El almacenamiento principal usado se divide a su vez en almacenamiento principal en capas y almacenamiento principal anclado localmente.

En los gráficos siguientes se muestra el almacenamiento principal usado para un dispositivo de StorSimple antes y después de tomar una instantánea de nube. Como esto son solo datos de volumen, la instantánea de nube no debe cambiar el almacenamiento principal. Como se puede observar, el gráfico no muestra ninguna diferencia en el uso del almacenamiento principal en niveles o anclado localmente como resultado de tomar una instantánea de nube. La instantánea de nube se inició aproximadamente a las 23:50 en ese dispositivo.

![Uso de la capacidad principal después de la instantánea en la nube](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Si ejecuta ahora E/S en el host conectado al dispositivo de StorSimple, verá un aumento en el almacenamiento principal en niveles o en el almacenamiento principal anclado localmente usado según los volúmenes (en niveles o anclados localmente) en los que se escriban los datos. Estos son los gráficos de uso del almacenamiento principal para un dispositivo de StorSimple. En este dispositivo, el host de StorSimple empezó a atender operaciones de escritura aproximadamente a las 14:30 en un volumen en capas en el dispositivo. Puede ver el pico en los bytes de escritura por segundos correspondientes a la E/S que se ejecuta en el host.

![Rendimiento cuando se ejecuta E/S en volúmenes en capas](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Si observa el almacenamiento principal en capas usado, verá que ha aumentado mientras que el uso principal anclado localmente permanece sin cambios, dado que no se ha enviado ninguna operación de escritura a los volúmenes anclados localmente en el dispositivo.

![Uso de la capacidad principal cuando se ejecuta E/S en volúmenes en capas](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Si ejecuta Update 3 o posterior, puede desglosar el uso de la capacidad de almacenamiento principal por volumen individual, todos los volúmenes, todos los volúmenes en capas y todos los volúmenes anclados localmente, como se muestra a continuación. Al desglosar por todos los volúmenes anclados localmente, podrá averiguar rápidamente cuánto volumen se utiliza de la capa local.

![Uso de la capacidad principal de todos los volúmenes en capas](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Utilización de la capacidad principal de todos los volúmenes anclados localmente](./media/storsimple-8000-monitor-device/monitor-usage4.png)

También puede hacer clic en cada uno de los volúmenes de la lista y ver el uso correspondiente.

![Utilización de la capacidad principal de todos los volúmenes anclados localmente](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Uso de almacenamiento en la nube
Estos gráficos muestran la cantidad de almacenamiento en la nube que se usa. Estos datos están desduplicados y comprimidos. Esta cantidad incluye instantáneas en la nube que podrían contener datos que no se reflejan en ningún volumen principal y que se mantienen con fines de retención necesaria o heredada. Puede comparar las cifras de consumo de almacenamiento principal y en la nube para hacerse una idea de la tasa de reducción de datos, si bien el número no será exacto.

Los gráficos siguientes muestran el uso del almacenamiento en la nube de un dispositivo StorSimple cuando se tomó una instantánea de nube.

* La instantánea de nube se inició aproximadamente a las 11:50 en ese dispositivo y puede ver que antes de la instantánea de nube no se usó el almacenamiento en la nube. 
* Una vez completada la instantánea de nube, el uso del almacenamiento en la nube capturó 0,89 GB. 
* Mientras se estaba realizando la instantánea de nube, también hay un pico correspondiente en la E/S del dispositivo a la nube.

    ![Uso del almacenamiento en la nube antes de la instantánea de nube](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Uso del almacenamiento en la nube después de la instantánea de nube](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![E/S del dispositivo a la nube durante la instantánea de nube](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Uso del almacenamiento local
En estos gráficos se muestra el uso total del dispositivo, que será superior al uso del almacenamiento principal porque incluye el nivel lineal de SSD. Este nivel contiene una cantidad de datos que también existe en los otros niveles del dispositivo. La capacidad en el nivel lineal de SSD es cíclica de modo que cuando llegan nuevos datos, los datos antiguos se mueven al nivel HDD (momento en el cual están desduplicados y comprimidos) y, posteriormente, a la nube.

Con el tiempo, el uso del almacenamiento principal y del almacenamiento local aumentará al mismo tiempo hasta que los datos comiencen a almacenarse en niveles en la nube. En ese momento, el uso del almacenamiento local probablemente empezará a estabilizarse, pero aumentará el uso del almacenamiento principal conforme se escriban más datos.

En los gráficos siguientes se muestra el almacenamiento principal usado para un dispositivo de StorSimple cuando se tomó una instantánea de nube. La instantánea de nube se inició a las 11:50 y el uso del almacenamiento local comenzó a disminuir en ese momento. El uso del almacenamiento local descendió de 1,445 GB a 1,09 GB. Esto indica que probablemente los datos sin comprimir en la capa SSD lineal se desduplicaron, comprimieron y se movieron en el nivel de unidad de disco duro. Tenga en cuenta que si el dispositivo tiene una gran cantidad de datos en los niveles de la SSD y en la unidad de disco duro, es posible que no vea esta disminución. En este ejemplo, el dispositivo tiene una pequeña cantidad de datos.

![Uso del almacenamiento local después de la instantánea de nube](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Rendimiento
**Rendimiento** realiza un seguimiento de las métricas relacionadas con el número de operaciones de lectura y escritura entre las interfaces de iniciador iSCSI en el servidor host y el dispositivo, o el dispositivo y la nube. Este rendimiento se puede medir para un volumen específico, un contenedor de volúmenes específico o todos los contenedores de volúmenes. El rendimiento también incluye el uso de CPU y el rendimiento de red para las diversas interfaces de red en el dispositivo.

### <a name="io-performance-for-initiator-to-device"></a>Rendimiento de E/S del iniciador al dispositivo
El gráfico siguiente muestra las operaciones de E/S del iniciador del dispositivo para todos los volúmenes de un dispositivo de producción. Las métricas trazadas son de bytes de lectura y escritura por segundo. También se puede representar la E/S de lectura, escritura y pendiente, o las latencias de lectura y escritura.

![Rendimiento de E/S del iniciador al dispositivo](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Rendimiento de E/S del dispositivo a la nube
En el mismo dispositivo, se trazan las operaciones de E/S para los datos del dispositivo a la nube para todos los contenedores de volumen. En este dispositivo, los datos solo están en el nivel lineal y nada ha pasado a la nube. No ocurre ninguna operación de lectura-escritura del dispositivo a la nube. Por lo tanto, los picos del gráfico se muestran en un intervalo de 5 minutos, que corresponde a la frecuencia con la que se comprueba el latido entre el dispositivo y el servicio.

Para el mismo dispositivo, se tomó una instantánea de nube para datos de volumen a partir de las 11:50. Esto da como resultado el flujo de datos del dispositivo a la nube. En esta duración se proporcionaron las operaciones de escritura a la nube. El gráfico de E/S muestra un pico en los bytes de escritura/s, correspondiente a la hora cuando se tomó la instantánea.

![E/S del dispositivo a la nube durante la instantánea de nube](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Rendimiento de la red para las interfaces de red del dispositivo
**La capacidad de proceso de la red** realiza un seguimiento de las métricas relacionadas con la cantidad de datos transferidos desde las interfaces de red del iniciador iSCSI en el servidor host y el dispositivo, y entre el dispositivo y la nube. Puede supervisar esta métrica para cada una de las interfaces de red iSCSI del dispositivo.

Los gráficos siguientes muestran el rendimiento de la red Data 0, 1 1 GbE en el dispositivo, que se habilitó para la nube (valor predeterminado) y para iSCSI. En este dispositivo, el 14 de junio aproximadamente a las 21:00, los datos se almacenaron en niveles en la nube (no se realizaron instantáneas de nube en ese momento, lo que indica que se usaron niveles como mecanismo para mover los datos a la nube), lo que resultó en el envío de E/S a la nube. Hay un pico correspondiente en el gráfico de rendimiento de la red para la misma hora y la mayoría del tráfico de red es de salida hacia la nube.

![Rendimiento de la red para Data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Si observamos el gráfico de rendimiento de la interfaz Data 1, otra interfaz de red 1 GbE habilitada solo para iSCSI, no hubo prácticamente ningún tráfico de red durante este periodo.

![Rendimiento de la red para Data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Uso de CPU para el dispositivo
**Uso de CPU** realiza un seguimiento de las métricas relacionadas con la CPU que se usa en el dispositivo. El gráfico siguiente muestra las estadísticas de uso de CPU para un dispositivo en producción.

![Uso de CPU para el dispositivo](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [usar el panel de dispositivos del servicio Administrador de dispositivos de StorSimple](storsimple-device-dashboard.md).
* Obtenga información sobre cómo [usar el servicio Administrador de dispositivos de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

