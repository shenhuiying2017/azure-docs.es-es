---
title: Cobertura de tráfico en Azure Maps | Microsoft Docs
description: Información sobre la cobertura de tráfico en Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 49e91287d23c35d8cfea858d41c47ba15efd820c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
---
# <a name="azure-maps-traffic-coverage"></a>Cobertura de tráfico en Azure Maps

Azure Maps proporciona información de tráfico enriquecida en forma de **flujo** e **incidentes** de tráfico. Estos datos se pueden visualizar en mapas o se pueden utilizar para generar rutas más inteligentes que se apoyan en las condiciones de conducción reales. 

Sin embargo, Maps no tiene el mismo nivel de información y precisión en todas las regiones. La tabla siguiente proporciona información acerca de qué tipo de información de tráfico puede solicitar en cada región: 

|Region  |Incidentes  |Flujo  |
|---------|:---------:|:---------:|
|Argentina      |         |✓         |
|Australia     |✓         |✓        |
|Austria     |✓         |✓         |
|Bahréin     |         |✓         |
|Bélgica     |✓         |✓         |
|Brasil     |✓         |✓         |
|Bulgaria     |✓         |✓         |
|Canadá     |✓         |✓         |
|Chile     |✓         |✓         |
|Colombia      |         |✓         |
|Croacia     |         |✓         |
|República Checa     |✓         |✓         |
|Dinamarca     |✓         |✓         |
|Egipto     |         |✓         |
|Estonia     |         | ✓        |
|Finlandia     |✓         |✓         |
|+Islas Åland      |✓         |✓         |
|Francia     |✓         |✓         |
|+Mónaco     |✓         |✓         |
|Alemania     |✓         |✓         |
|Grecia     |✓         |✓         |
|RAE de Hong Kong     |✓         |✓         |
|Hungría     |✓         |✓         |
|Islandia     |         |✓         |
|Indonesia     |✓         |✓         |
|Irlanda (República de)     |✓         |✓         |
|Israel     |         |✓         |
|Italia     |✓         |✓        |
|+San Marino     |✓         |✓         |
|Kuwait     |✓         |✓         |
|Letonia     |         |✓         |
|Lesoto     |✓         |✓         |
|Lituania     |         |✓         |
|Luxemburgo     |✓         |✓         |
|RAE de Macao     |         |✓         |
|Malasia     |✓         |✓         |
|Malta     |✓         |✓         |
|México     |✓         |✓         |
|Marruecos     |         |✓         |
|Países Bajos     |✓         |✓         |
|Nueva Zelanda     |✓         |✓         |
|Noruega     |✓         |✓         |
|Omán     |         |✓         |
|Polonia     |✓         |✓         |
|Portugal     |✓         |✓         |
|+Azores y Madeira     |         |✓         |
|Qatar     |         |✓         |
|Rumania     |         |✓         |
|Federación Rusa     |✓         |✓         |
|Arabia Saudí     |✓         |✓         |
|Singapur     |✓         |✓         |
|Eslovaquia     |✓         |✓         |
|Eslovenia     |✓         |✓         |
|Sudáfrica     |✓         |✓         |
|España     |✓         |✓         |
|+Andorra     |✓         |✓         |
|+Islas Baleares     |✓         |✓         |
|+Islas Canarias     |✓         |✓         |
|+Gibraltar     |✓         |✓         |
|Suecia     |✓         |✓         |
|Suiza     |✓         |✓        |
|+Liechtenstein      |✓         |✓         |
|Taiwán     |✓         |✓        |
|Tailandia     |✓         |✓        |
|Turquía     |✓         |✓         |
|Ucrania     |✓         |✓         |
|Emiratos Árabes Unidos     |✓         |✓         |
|Reino Unido     |✓         |✓         |
|(Guernsey y Jersey)     |✓         |✓         |
|Isla de Man     |✓         |✓         |
|Estados Unidos     |✓         |✓        |
|+Puerto Rico     |✓         |✓         |

Para más información acerca de los datos de tráfico de Azure Maps, consulte las páginas de referencia de [Tráfico](https://docs.microsoft.com/rest/api/maps/traffic).