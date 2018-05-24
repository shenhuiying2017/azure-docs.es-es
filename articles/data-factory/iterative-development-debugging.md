---
title: Desarrollo y depuración iterativos en Azure Data Factory | Microsoft Docs
description: Obtenga información sobre cómo desarrollar y depurar de manera iterativa las canalizaciones de Data Factory en Azure Portal.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 05/14/2018
ms.topic: article
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: e403afa5c870b2d007a8c5e1d46162cd899ead29
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164829"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Desarrollo y depuración iterativos con Azure Data Factory

Azure Data Factory permite desarrollar y depurar de manera iterativa las canalizaciones de Data Factory.

Si desea una introducción y demostración de ocho minutos de esta característica, vea el siguiente vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Características de la depuración iterativa
Cree canalizaciones y realice series de pruebas con la funcionalidad **Depurar** en el lienzo de la canalización sin escribir ninguna línea de código.

![Funcionalidad de depuración en el lienzo de la canalización](media/iterative-development-debugging/iterative-development-image1.png)

Consulte los resultados de las series de pruebas en la ventana **Salida** del lienzo de la canalización.

![Ventana de salida del lienzo de la canalización](media/iterative-development-debugging/iterative-development-image2.png)

Después de realizar correctamente una serie de pruebas, agregue más actividades a la canalización y continúe con la depuración de manera iterativa. También puede **cancelar** una serie de pruebas mientras está en curso.

![Cancelación de una serie de pruebas](media/iterative-development-debugging/iterative-development-image3.png)

Cuando realiza las series de pruebas, no es necesario que publique los cambios que se realizaron en la factoría de datos antes de seleccionar **Depurar**. Esto resulta útil en escenarios en los que se desea garantizar que los cambios funcionen según lo esperado antes de actualizar el flujo de trabajo de la factoría de datos.

## <a name="more-info-about-debugging"></a>Más información sobre la depuración

1. Las series de pruebas iniciadas con la funcionalidad **Depurar** no están disponibles en la lista de la pestaña **Supervisar**. Solo puede ver las series que se desencadenan con los desencadenadores **Trigger Now** (Desencadenar ahora), **Schedule** (Programar) o **Tumbling Window** (Ventana de saltos de tamaño constante) de la pestaña **Supervisar**. Puede ver la última serie de pruebas iniciada con la funcionalidad **Depurar** en la ventana **Salida** del lienzo de la canalización.

2. Al seleccionar **Depurar**, se ejecuta la canalización. Así, por ejemplo, si la canalización contiene actividad de copia, la serie de pruebas copia datos del origen al destino. Como resultado, se recomienda usar las carpetas de prueba en las actividades de copia y otras actividades cuando se realice la depuración. Una vez que se depure la canalización, cambie a las carpetas reales que desea usar en las operaciones normales.

## <a name="setting-breakpoints-for-debugging"></a>Establecimiento de puntos de interrupción para la depuración

Data Factory también permite realizar la depuración hasta alcanzar una actividad concreta en el lienzo de la canalización. Simplemente ubique un punto de interrupción en la actividad que indique hasta donde desea probar y seleccione **Depurar**. Data Factory garantiza que las pruebas se ejecutan solo hasta la actividad de punto de interrupción en el lienzo de la canalización. Esta característica *Debug Until* (Depurar hasta) resulta útil cuando no desea probar toda la canalización, sino solo un subconjunto de actividades dentro de la canalización.

![Puntos de interrupción en el lienzo de la canalización](media/iterative-development-debugging/iterative-development-image4.png)

Para establecer un punto de interrupción, seleccione un elemento en el lienzo de la canalización. La opción *Depurar hasta* aparece como un círculo rojo vacío en la esquina superior derecha del elemento.

![Antes de establecer un punto de interrupción en el elemento seleccionado](media/iterative-development-debugging/iterative-development-image5.png)

Tras seleccionar la opción *Depurar hasta* cambia a un círculo rojo lleno para indicar el punto de interrupción está habilitado.

![Después de establecer un punto de interrupción en el elemento seleccionado](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Pasos siguientes
[Integración e implementación continuas en Azure Data Factory](continuous-integration-deployment.md)
