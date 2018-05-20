---
title: Azure Machine Learning y FPGA
description: Obtenga información sobre cómo acelerar modelos y redes neurales profundas con FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>¿Qué es una matriz de puertas programable por campos (FPGA)?

Las matrices de puertas programables por campos (FPGA) son circuitos integrados que se pueden volver a configurar según sea necesario. Puede cambiar una FPGA según sea necesario para implementar una lógica nueva. Los modelos acelerados de hardware de Azure Machine Learning permiten implementar modelos entrenados en FPGA en la nube de Azure.

Esta funcionalidad cuenta con la tecnología de Project Brainwave, que se encarga de la traducción de redes neurales profundas (DNN) en un programa de FPGA. 

## <a name="why-use-an-fpga"></a>¿Por qué usar una FPGA?

Las FPGA proporcionan una latencia muy baja y son especialmente eficaces para puntuar datos en un tamaño de lote (no existen requisitos para un tamaño de lote grande).  Son rentables y están disponibles en Azure.  Project Brainwave puede paralelizar DNN entrenadas previamente entre estas FPGA para realizar el escalado horizontal del servicio.

## <a name="next-steps"></a>Pasos siguientes

[Implementación de un modelo como un servicio web en una FPGA](how-to-deploy-fpga-web-service.md)

[Más información sobre cómo instalar el SDK de FPGA](reference-fpga-package-overview.md)