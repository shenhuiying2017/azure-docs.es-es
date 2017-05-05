---
title: Alta disponibilidad de Azure Analysis Services | Microsoft Docs
description: Garantizar una alta disponibilidad de Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: c4eb1162edc42baafe96e6c33699805ffc121204
ms.lasthandoff: 04/20/2017


---

# <a name="analysis-services-high-availability"></a>Alta disponibilidad de Azure Analysis Services
En este artículo se describe cómo garantizar la alta disponibilidad de los servidores de Azure Analysis Services. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>Garantizar la alta disponibilidad durante una interrupción del servicio
Aunque es poco habitual, en los centros de datos de Azure pueden producirse interrupciones. Cuando esto ocurre, provoca también una interrupción en el negocio que podría extenderse unos pocos minutos o incluso horas. La alta disponibilidad se suele lograr con la redundancia de servidores. Con Azure Analysis Services, la redundancia se obtiene gracias a la creación de servidores secundarios adicionales en una o varias regiones. Para garantizar que los datos y metadatos de esos servidores redundantes que cree estén sincronizados con el servidor en una región que se ha desconectado, puede:

* Implementar modelos en servidores redundantes en otras regiones. Este método requiere el procesamiento en paralelo de los datos en el servidor principal y en los servidores redundantes, lo que asegura que todos los servidores están sincronizados.

* Realizar copias de seguridad de las bases de datos del servidor principal y restaurarlas en los servidores redundantes. Por ejemplo, se puede automatizar la creación de copias de seguridad en Azure Storage durante la noche y hacer una restauración en los servidores redundantes de otras regiones. 

En cualquier caso, si se produce una interrupción en el servidor principal, se deben cambiar las cadenas de conexión en los clientes de informes para conectarse al servidor en otro centro de datos regional. Este cambio debe considerarse un último recurso y solo si se produce una interrupción catastrófica del centro de datos regional. Es más probable que una interrupción en el centro de datos que hospeda el servidor principal se recupere antes de que se puedan actualizar las conexiones en todos los clientes. 

Al determinar la forma en que la organización controla una interrupción del servicio, tenga en cuenta cómo asegurarse de que los datos se mantienen actualizados y seguros. 


## <a name="related-information"></a>Información relacionada
[Copia de seguridad y restauración](analysis-services-backup.md) 
[Administración de Azure Analysis Services](analysis-services-manage.md) 


