<properties 
   pageTitle="Uso del servicio StorSimple Manager para supervisar el dispositivo StorSimple | Microsoft Azure"
	description="Se describe cómo usar el servicio StorSimple Manager para supervisar el rendimiento de E/S, la utilización de la capacidad, la capacidad de proceso de la red y rendimiento del dispositivo."
	services="storsimple"
	documentationCenter="NA"
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="08/25/2015"
	ms.author="alkohli"/>

# Uso del servicio StorSimple Manager para supervisar el dispositivo StorSimple. 

## Información general

Puede usar el servicio StorSimple Manager para supervisar dispositivos específicos dentro de la solución StorSimple. Puede crear gráficos personalizados basados en el rendimiento de E/S, la utilización de la capacidad, la capacidad de proceso de la red y las métricas de rendimiento del dispositivo.

Para ver la información de supervisión para un dispositivo específico, en el Portal de Azure, seleccione el servicio StorSimple Manager, haga clic en la pestaña **Supervisión** y, luego, seleccione en la lista de dispositivos. La página **Monitor** contiene la siguiente información.

## Rendimiento de E/S 

**El rendimiento de E/S** realiza un seguimiento de las métricas relacionadas con el número de operaciones de lectura y escritura entre las interfaces de iniciador iSCSI en el servidor host y el dispositivo, o el dispositivo y la nube. Este rendimiento se puede medir para un volumen específico, un contenedor de volúmenes específico o todos los contenedores de volúmenes.

El gráfico siguiente muestra las operaciones de E/S del iniciador del dispositivo para todos los volúmenes de un dispositivo de producción. Las métricas trazadas se leen y escriben bytes por segundo, leen y escribir operaciones de E/S por segundo y leen y escribir las latencias.

![Rendimiento de E/S del iniciador al dispositivo](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

En el mismo dispositivo, se trazan las operaciones de E/S para los datos del dispositivo a la nube para todos los contenedores de volumen. En este dispositivo, los datos solo están en el nivel lineal y nada ha pasado a la nube. No ocurre ninguna operación de lectura-escritura del dispositivo a la nube. En consecuencia, los picos del gráfico se muestran en un intervalo de 5 minutos, que corresponde a la frecuencia con la que se comprueba el latido entre el dispositivo y el servicio.

![Rendimiento de E/S del dispositivo a la nube](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


Para el mismo dispositivo, se ha tomado una instantánea en la nube para datos de volumen a partir de las 2:00 p.m.. Esto da como resultado el flujo de datos del dispositivo a la nube. Las lecturas y escrituras se atendieron en la nube en esta duración. El gráfico de E/S muestra un pico en las distintas métricas, correspondiente a la hora cuando se tomó la instantánea.

![Rendimiento de E/S para el dispositivo a la nube después de la instantánea en la nube](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## Uso de la capacidad 

**El uso de la capacidad** realiza un seguimiento de las métricas relacionadas con la cantidad de espacio de almacenamiento de datos usado por los volúmenes, contenedores de volúmenes o dispositivos. Puede crear informes basados en la utilización de la capacidad del almacenamiento principal, el almacenamiento en la nube o el almacenamiento del dispositivo. La utilización de la capacidad se puede medir para un volumen específico, un contenedor de volúmenes específico o todos los contenedores de volúmenes.

La capacidad de almacenamiento principal, en la nube y del dispositivo puede describirse como sigue:

- **Eluso de la capacidad de almacenamiento principal** muestra la cantidad de datos escritos en volúmenes StorSimple antes de que los datos se desdupliquen y se compriman. Los gráficos siguientes muestran el uso de la capacidad de almacenamiento principal de un dispositivo StorSimple, antes y después de cuando se tomó una instantánea de nube. Como esto son solo datos de volumen, la instantánea de nube no debe cambiar el almacenamiento principal. Como se puede observar, el gráfico no muestra ninguna diferencia en el uso de la capacidad principal como resultado de tomar una instantánea en la nube. Tenga en cuenta que la instantánea en la nube se inició aproximadamente a las 2:00 p.m. en ese dispositivo.

	![Uso de la capacidad principal antes de instantánea en la nube](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)
	
	![Uso de la capacidad principal después de la instantánea en la nube](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)


- **El uso de la capacidad de almacenamiento en la nube** muestra la cantidad de almacenamiento en la nube usado. Estos datos están desduplicados y comprimidos. Esta cantidad incluye instantáneas en la nube que podrían contener datos que no se reflejan en ningún volumen principal y que se mantienen con fines de retención necesaria o heredada. Puede comparar las cifras de consumo de almacenamiento principal y en la nube para hacerse una idea de la tasa de reducción de datos, si bien el número no será exacto. Los gráficos siguientes muestran el uso de capacidad de almacenamiento de nube de un dispositivo StorSimple antes y después de cuando se tomó una instantánea de nube. La instantánea de nube se inició alrededor de las 2:00 p.m. en ese dispositivo y puede ver la captura del uso de la capacidad en la nube al mismo tiempo, aumentando de 5,73 MB a 4,04 GB.

	![Uso de la capacidad en la nube antes de la instantánea en la nube](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

	![Uso de la capacidad en la nube después de la instantánea en la nube](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


- **El uso de la capacidad de almacenamiento del dispositivo** muestra el uso total del dispositivo, que será superior al uso del almacenamiento principal porque incluye el nivel lineal de SSD. Este nivel contiene una cantidad de datos que también existe en los otros niveles del dispositivo. La capacidad en el nivel lineal de SSD es cíclica de modo que cuando llegan nuevos datos, los datos antiguos se mueven al nivel HDD (momento en el cual están desduplicados y comprimidos) y, posteriormente, a la nube.

	Con el tiempo, la utilización de la capacidad principal y la utilización de la capacidad del dispositivo aumentarán al mismo tiempo hasta que los datos comiencen a almacenarse en niveles en la nube. En ese momento, la utilización de la capacidad del dispositivo probablemente empezará a estabilizarse, pero aumentará la utilización de la capacidad principal conforme se escriban más datos.

	Los gráficos siguientes muestran el uso de la capacidad de almacenamiento principal de un dispositivo StorSimple, antes y después de cuando se tomó una instantánea de nube. La instantánea en la nube iniciada a las 2:00 p.m. y el uso de la capacidad de dispositivo comenzaron a disminuir en ese momento. El uso de la capacidad de almacenamiento del dispositivo se redujo de 11,58 GB a 7,48 GB. Esto indica que probablemente los datos sin comprimir en la capa SSD lineal se desduplicaron, comprimieron y se movieron en el nivel de unidad de disco duro. Tenga en cuenta que si el dispositivo tiene una gran cantidad de datos en los niveles de la SSD y en la unidad de disco duro, es posible que no vea esta disminución. En este ejemplo, el dispositivo tiene una pequeña cantidad de datos.

	![Uso de la capacidad del dispositivo antes de la instantánea en la nube](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

	![Uso de la capacidad del dispositivo después de la instantánea en la nube](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## Capacidad de proceso de la red

**La capacidad de proceso de la red** realiza un seguimiento de las métricas relacionadas con la cantidad de datos transferidos desde las interfaces de red del iniciador iSCSI en el servidor host y el dispositivo, y entre el dispositivo y la nube. Puede supervisar esta métrica para cada una de las interfaces de red iSCSI del dispositivo.

Los gráficos siguientes muestran el rendimiento de la red para Data 0 y Data 4, ambas interfaces de red de 1 GbE en el dispositivo. En este caso, Data 0 estaba habilitada para la nube mientras que Data 4 estaba habilitada para iSCSI. Puede ver el tráfico entrante y saliente para el dispositivo StorSimple. Tenga en cuenta que la línea recta en el gráfico que comienza desde las 3:24 p.m. se debe al hecho de que se recopilan datos solo cada 5 minutos y deben omitirse.

![Rendimiento de la red para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Rendimiento de la red para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## Rendimiento del dispositivo 

**El rendimiento del dispositivo** realiza un seguimiento de las métricas relacionadas con el rendimiento del dispositivo. El gráfico siguiente muestra las estadísticas de uso de CPU para un dispositivo en producción.

![Uso de CPU para el dispositivo](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## Pasos siguientes

[Obtenga información sobre cómo usar el panel de dispositivos del servicio StorSimple Manager](storsimple-device-dashboard.md).

<!---HONumber=August15_HO9-->