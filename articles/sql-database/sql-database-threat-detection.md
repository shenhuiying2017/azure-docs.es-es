---
title: "Detección de amenazas de Azure SQL Database| Microsoft Docs"
description: "Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: eadaa3e7a279b6b92da1d0c026c3002297dfd298
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-threat-detection"></a>Detección de amenazas de SQL Database

Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos.  La detección de amenazas se encuentra actualmente en versión preliminar.

## <a name="overview"></a>Información general

Detección de amenazas ofrece un nuevo nivel de seguridad, que permite a los clientes detectar amenazas potenciales y responder a ellas a medida que se producen, gracias a las alertas de seguridad sobre actividades anómalas que se proporcionan.  Los usuarios pueden explorar los eventos sospechosos mediante la [auditoría de SQL Database](sql-database-auditing.md) para determinar si proceden de un intento de acceder a los datos en la base de datos, infringir su seguridad o aprovecharlos.
Detección de amenazas facilita la solución de las posibles amenazas a la base de datos sin necesidad de ser un experto en seguridad ni administrar sistemas de supervisión de seguridad avanzada.

Por ejemplo, Detección de amenazas detecta determinadas actividades anómalas en la base de datos que sugieren posibles intentos de inyección de código SQL. La inyección de código SQL es uno de los problemas de seguridad habituales entre las aplicaciones web en Internet y se usa para atacar aplicaciones controladas por datos. Los atacantes aprovechan las vulnerabilidades de la aplicación para inyectar instrucciones SQL malintencionadas en los campos de entrada de la aplicación, con el fin de infringir la seguridad o modificar datos en la base de datos.

## <a name="next-steps"></a>Pasos siguientes

* Para configurar y administrar la detección de amenazas, consulte [este artículo sobre cómo configurar y administrar la detección de amenazas en Azure Portal](sql-database-threat-detection-portal.md).

