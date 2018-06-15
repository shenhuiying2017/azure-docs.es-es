---
title: 'Continuidad empresarial: Microsoft Genomics | Microsoft Docs'
titleSuffix: Azure
description: Aprenda cómo Microsoft Genomics sirve de soporte a la continuidad empresarial
keywords: Continuidad empresarial, recuperación ante desastres
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: cb3825cb89aff386c4f7c3f3b0d771d73fe637b1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31426812"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Introducción a la continuidad empresarial con Microsoft Genomics
En este artículo de introducción se describen las funcionalidades que Microsoft Genomics proporciona a la continuidad empresarial y la recuperación ante desastres. Obtenga información acerca de las opciones para la recuperación de eventos potencialmente perjudiciales, como una interrupción de la región de Azure, que podrían provocar la pérdida de datos. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics presenta esa asistencia a la continuidad empresarial 
Aunque es poco frecuente, un centro de datos de Azure puede tener una interrupción que a su vez puede provocar una interrupción del negocio, que puede durar desde unos minutos a unas pocas horas. Cuando se produce una interrupción, todos los trabajos que se están ejecutando actualmente en el centro de datos darán un error, y el servicio de Microsoft Genomics no volverá a enviar automáticamente los trabajos a una región secundaria. 

* Una opción consiste en esperar a que el centro de datos vuelva a estar en línea cuando termine la interrupción. Si la interrupción es breve, el servicio de Microsoft Genomics detectará automáticamente los trabajos con errores y se reiniciará automáticamente el flujo de trabajo.

* Otra opción es en enviar el flujo de trabajo en otra región de datos de forma proactiva. Microsoft Genomics implementa instancias en varias [regiones de Azure](https://azure.microsoft.com/regions/services/), y cada instancia específica de región es independiente. Si una de las instancias de Microsoft Genomics experimenta un error local en la región, otras regiones que estén ejecutando instancias de Microsoft Genomics continuarán procesando trabajos. Esta transferencia a una región alternativa está bajo el control del usuario y está disponible en cualquier momento.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Flujos de trabajo de conmutación por error de Microsoft Genomics a otra región
En el caso de una interrupción del centro de datos de una región, puede decidir el envío de flujos de trabajo de Microsoft Genomics en una región secundaria, según la soberanía de datos individuales y los requisitos de continuidad empresarial. Para realizar una conmutación por error manual de los flujos de trabajo de Microsoft Genomics, se usaría una cuenta de Genomics específica de una región distinta y se enviaría el trabajo con unas credenciales de cuenta de almacenamiento de Genomics específicas de región.

En concreto, tendrá que:
* Crear una cuenta de Genomics en la región secundaria mediante Azure Portal. 
* Migrar los datos de entrada a una cuenta de almacenamiento en la región secundaria y configurar una carpeta de salida en la región secundaria.
* Enviar el flujo de trabajo en la región secundaria.

Cuando se restaure la región original, el servicio de Microsoft Genomics no migra los datos de la región secundaria a la región original. Puede mover los archivos de entrada y salida de la región secundaria de nuevo a la región original.  Si elige mover los datos, esto está fuera del servicio de Genomic y todos los cargos relacionados con el movimiento de los datos serán su responsabilidad. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Preparación para una posible interrupción específica de región
Si le preocupa la una recuperación rápida en el caso de una interrupción del centro de datos, hay algunos pasos que puede seguir para reducir el tiempo necesario para que pueda reenviar manualmente los flujos de trabajo de Microsoft Genomics a una región secundaria:

* Identifique una región secundaria adecuada y cree proactivamente una cuenta de Genomics en dicha región
* Duplique los datos en la región primaria y secundaria para que los datos están disponibles inmediatamente en la región secundaria. Esto se puede realizar de forma manual o mediante la función de [almacenamiento con redundancia geográfica](https://docs.microsoft.com/azure/storage/common/storage-redundancy) que está disponible en Azure Storage. 

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido acerca de las opciones de continuidad empresarial y recuperación ante desastres cuando se utiliza el servicio Microsoft Genomics. Para más información acerca de la continuidad empresarial y recuperación ante desastres en Azure en general, consulte la [Guía técnica sobre resistencia en Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 