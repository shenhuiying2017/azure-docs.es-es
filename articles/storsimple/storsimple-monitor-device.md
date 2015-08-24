<properties 
   pageTitle="Uso del servicio StorSimple Manager para supervisar el dispositivo StorSimple | Microsoft Azure"
   description="Se describe cómo usar el servicio StorSimple Manager para supervisar el rendimiento de E/S, la utilización de la capacidad, la capacidad de proceso de la red y rendimiento del dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/10/2015"
   ms.author="alkohli" />

# Uso del servicio StorSimple Manager para supervisar el dispositivo StorSimple. 

## Información general

Puede usar el servicio StorSimple Manager para supervisar dispositivos específicos dentro de la solución StorSimple. Puede crear gráficos personalizados basados en el rendimiento de E/S, la utilización de la capacidad, la capacidad de proceso de la red y las métricas de rendimiento del dispositivo.

Para ver la información de supervisión para un dispositivo específico, en el Portal de administración, seleccione el servicio StorSimple Manager, haga clic en la pestaña **Supervisión** y, luego, seleccione en la lista de dispositivos. La página **Monitor** contiene la siguiente información.

## Rendimiento de E/S 

**El rendimiento de E/S** realiza un seguimiento de las métricas relacionadas con el número de operaciones de lectura y escritura entre las interfaces de iniciador iSCSI en el servidor host y el dispositivo, o el dispositivo y la nube. Este rendimiento se puede medir para un volumen específico, un contenedor de volúmenes específico o todos los contenedores de volúmenes.

## Uso de la capacidad 

**La utilización de la capacidad** realiza un seguimiento de las métricas relacionadas con la cantidad de espacio de almacenamiento de datos utilizado por los volúmenes, contenedores de volúmenes o dispositivos. Puede crear informes basados en la utilización de la capacidad del almacenamiento principal, el almacenamiento en la nube o el almacenamiento del dispositivo. La utilización de la capacidad se puede medir para un volumen específico, un contenedor de volúmenes específico o todos los contenedores de volúmenes.

- **La utilización de la capacidad de almacenamiento principal** muestra la cantidad de datos escritos en volúmenes StorSimple antes de que los datos se desdupliquen y se compriman.

- **La utilización de la capacidad de almacenamiento en la nube** muestra la cantidad de almacenamiento en la nube utilizado. Estos datos están desduplicados y comprimidos. Esta cantidad incluye instantáneas en la nube que podrían contener datos que no se reflejan en ningún volumen principal y que se mantienen con fines de retención necesaria o heredada. Puede comparar las cifras de consumo de almacenamiento principal y en la nube para hacerse una idea de la tasa de reducción de datos, si bien el número no será exacto.

- **La utilización de la capacidad de almacenamiento del dispositivo** muestra la utilización total del dispositivo, que será superior a la utilización del almacenamiento principal porque incluye el nivel lineal de SSD. Este nivel contiene una cantidad de datos que también existe en los otros niveles del dispositivo. La capacidad en el nivel lineal de SSD es cíclica de modo que cuando llegan nuevos datos, los datos antiguos se mueven a la nube (momento en el cual están desduplicados y comprimidos).

Con el tiempo, la utilización de la capacidad principal y la utilización de la capacidad del dispositivo aumentarán al mismo tiempo hasta que los datos comiencen a almacenarse en niveles en la nube. En ese momento, la utilización de la capacidad del dispositivo probablemente empezará a estabilizarse, pero aumentará la utilización de la capacidad principal conforme se escriban más datos.

## Capacidad de proceso de la red

**La capacidad de proceso de la red** realiza un seguimiento de las métricas relacionadas con la cantidad de datos transferidos desde las interfaces de red del iniciador iSCSI en el servidor host y el dispositivo, y entre el dispositivo y la nube. Puede supervisar esta métrica para cada una de las interfaces de red iSCSI del dispositivo.

## Rendimiento del dispositivo 

**El rendimiento del dispositivo** realiza un seguimiento de las métricas relacionadas con el rendimiento del dispositivo.

## Pasos siguientes

[Obtenga información sobre cómo usar el panel de dispositivos del servicio StorSimple Manager](storsimple-device-dashboard.md).

<!---HONumber=August15_HO7-->