
---
title: Calcular el uso del ancho de banda de red de Azure RemoteApp | Microsoft Docs
description: Obtenga información acerca de los requisitos del ancho de banda de red para sus colecciones y aplicaciones de Azure RemoteApp.
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Calcular el uso del ancho de banda de red de Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp va a dejar de estar disponible. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Azure RemoteApp usa el Protocolo de escritorio remoto (RDP) para comunicarse entre las aplicaciones que se ejecutan en la nube de Azure y sus usuarios. En este artículo se proporcionan algunas directrices básicas que puede usar para calcular el uso de la red y evaluar potencialmente el uso del ancho de banda de red por cada usuario de Azure RemoteApp.

Calcular el uso del ancho de banda por usuario es muy complejo y requiere la ejecución de varias aplicaciones simultáneamente en escenarios multitarea donde las aplicaciones podrían influir en el rendimiento de la otra basándose en la demanda del ancho de banda de red. Incluso el tipo de cliente de escritorio remoto (por ejemplo, un cliente de Mac frente a un cliente de HTML5) puede provocar resultados diferentes de ancho de banda. Para ayudarle a trabajar a través de estas dificultades, dividiremos los escenarios de uso en varias categorías comunes para replicar los escenarios del mundo real. (Donde el escenario del mundo real es, por supuesto, una combinación de categorías y varía según el usuario).

Antes de seguir avanzando, tenga en cuenta que asumimos que RDP proporciona una experiencia entre buena y excelente para la mayoría de escenarios de uso en redes con una latencia inferior a 120 ms y un ancho de banda superior a los 5 MB; esto se basa en la capacidad de RDP para ajustarse de forma dinámica con el ancho de banda de red disponible y con las necesidades estimadas del ancho de banda de la aplicación. Este artículo va más allá de la "mayoría de escenarios de uso" para situarse en el límite, donde los escenarios empiezan a desenredarse y la experiencia de usuario comienza a degradarse.

Ahora consulte los artículos siguientes para obtener más información, incluidos los factores a considerar, las recomendaciones de línea base y lo que no se incluyó en nuestras estimaciones.

* [¿Cómo funcionan el ancho de banda de red y la calidad de la experiencia conjuntamente?](remoteapp-bandwidthexperience.md)
* [Probar su uso de ancho de banda de red con algunos escenarios comunes](remoteapp-bandwidthtests.md)
* [Directrices rápidas si no tiene el tiempo ni la capacidad de realizar pruebas](remoteapp-bandwidthguidelines.md)

## ¿Qué no incluimos?
Cuando revise las pruebas propuestas y nuestras recomendaciones generales (y hay que reconocer que genéricas), tenga en cuenta que hay varios factores que no tuvimos en cuenta. Por ejemplo, las dificultades de la experiencia de usuario proporcionadas por la naturaleza asimétrica de la carga frente a la descarga del ancho de banda. Además, la naturaleza asimétrica de la mayoría de las redes Wi-Fi afectará el rendimiento y la percepción de la experiencia de usuario. Para escenarios interactivos, puede darse menor prioridad al tráfico de bajada que al de subida, que puede aumentar el número de fotogramas de vídeo o audio perdidos y, por lo tanto, influir en la percepción del usuario de la experiencia de descarga. Puede ejecutar sus propios experimentos para ver qué es adecuado para su red y caso de uso específico.

Aunque trataremos el redireccionamiento de dispositivos, no tendremos en cuenta el impacto del ancho de banda en el tráfico de red provocado por los dispositivos adjuntos, como los de almacenamiento, impresoras, escáneres, cámaras web y otros dispositivos USB. Normalmente, el efecto de dichos dispositivos aumenta las necesidades del ancho de banda de manera temporal, y desaparece cuando se completa la tarea. Pero si se realiza con frecuencia, la demanda de ancho de banda puede ser bastante notable.

Tampoco analizamos cómo un usuario puede afectar a otros usuarios dentro de la misma red. Por ejemplo, un usuario que consume un vídeo de 4K en una red de 100 MB/s puede afectar significativamente a otros usuarios de esa misma red que intenten realizar la misma tarea. Lamentablemente, cada vez es más difícil determinar el impacto del uso simultáneo para proporcionar una recomendación común o integral acerca de cómo funciona el sistema a nivel global. Todo lo que podemos decir es que la tecnología de protocolo subyacente hará el mejor uso del ancho de banda de red disponible, pero tiene sus limitaciones.

<!---HONumber=AcomDC_0817_2016-->