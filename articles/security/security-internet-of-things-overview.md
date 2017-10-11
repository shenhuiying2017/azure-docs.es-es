---
title: "Protección de Internet de las cosas (IoT) | Microsoft Docs"
description: " Los servicios de Internet de las cosas (IoT) de Azure ofrecen una amplia gama de funcionalidades. Este artículo le permite comprender cómo proteger sus soluciones de IoT en Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2017
ms.author: terrylan
ms.openlocfilehash: 3793f5453b74b6c06d9e58b426d89099298e1288
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="internet-of-things-security-overview"></a>Información general sobre seguridad de Internet de las cosas
Los servicios de Internet de las cosas (IoT) de Azure ofrecen una amplia gama de funcionalidades. Estos servicios de nivel empresarial le permiten:

* Recopilar datos de dispositivos
* Analizar flujos de datos en movimiento
* Almacenar y consultar grandes conjuntos de datos
* Visualizar datos tanto históricos como en tiempo real
* Integración con sistemas del área de operaciones

Con el fin de ofrecer estas funcionalidades, el Conjunto de aplicaciones de IoT de Azure ofrece paquetes de múltiples servicios de Azure con extensiones personalizadas como soluciones preconfiguradas. Estas soluciones preconfiguradas son implementaciones base de patrones comunes de soluciones de IoT que le ayudan a reducir el tiempo que dedica a entregar sus soluciones de IoT. Con los kits de desarrollo de software de IoT, puede personalizar y extender estas soluciones para satisfacer sus requisitos. También puede usar estas soluciones como ejemplos o plantillas al desarrollar nuevas soluciones de IoT.

El Conjunto de aplicaciones de IoT de Azure es una solución eficaz para sus necesidades de IoT. No obstante, es fundamental que las soluciones de IoT se diseñen pensando en la seguridad desde un primer momento. Debido al gran número de dispositivos de IoT, cualquier incidente de seguridad puede convertirse rápidamente en un problema generalizado con importantes consecuencias.

Para ayudarle a entender cómo proteger sus soluciones de IoT, puede consultar la siguiente información.

## <a name="security-architecture"></a>Arquitectura de seguridad
Cuando se diseña un sistema, es importante conocer las posibles amenazas a las que puede estar expuesto y agregar las defensas adecuadas según corresponda durante su diseño y arquitectura. Es importante diseñar el producto desde el principio teniendo en cuenta la seguridad, ya que conocer la forma en que un atacante podría poner en peligro un sistema ayuda a tomar las medidas pertinentes desde el principio.

Puede consultar [Arquitectura de seguridad de Internet de las cosas](../iot-suite/iot-security-architecture.md)para obtener información sobre la arquitectura de seguridad de IoT.

En este artículo se tratan los temas siguientes:

* [La seguridad comienza con un modelo de riesgos](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
* [Seguridad de IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
* [Modelado de riesgos de la arquitectura de referencia de IoT de Azure](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Seguridad total
IoT plantea desafíos únicos para la seguridad, la privacidad y el cumplimiento a empresas de todo el mundo. A diferencia de la tecnología cibernética tradicional donde estos problemas giran en torno al software y cómo se implementa, a IoT le preocupa lo que sucede cuando los mundos cibernético y físico convergen. Proteger las soluciones IoT requiere garantizar el aprovisionamiento seguro de los dispositivos, proteger la conectividad entre estos dispositivos y la nube y garantizar la protección de los datos en la nube durante su procesamiento y almacenamiento. Sin embargo, trabajar con esta funcionalidad tiene sus inconvenientes: la limitación de recursos de los dispositivos, la distribución geográfica de las implementaciones y la existencia de muchos dispositivos dentro de una solución.

Puede consultar [Seguridad de Internet de las cosas desde el principio](../iot-suite/securing-iot-ground-up.md)para obtener información acerca de cómo controlar la seguridad en estas áreas.

En este artículo se tratan los temas siguientes:

* [Infraestructura segura desde el principio](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure: infraestructura de IoT segura para su negocio](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Prácticas recomendadas
La protección de una infraestructura IoT requiere una estrategia de seguridad rigurosa y detallada. A partir de la protección de datos en la nube, pasando por la protección de la integridad de los datos en tránsito a través de la red pública de Internet y el ofrecimiento de la posibilidad de aprovisionar dispositivos de forma segura, cada capa crea una mayor garantía de seguridad en la infraestructura general.

Puede consultar [Procedimientos recomendados de seguridad de Internet de las cosas](../iot-suite/iot-security-best-practices.md)para conocer estos procedimientos recomendados.

En este artículo se tratan los temas siguientes:

* [Integrador/fabricante de hardware IoT](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [Desarrollador de soluciones de IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
* [Implementador de soluciones de IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
* [Operador de soluciones de IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
