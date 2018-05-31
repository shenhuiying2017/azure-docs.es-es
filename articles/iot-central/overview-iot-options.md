---
title: Opciones de IoT de Microsoft Azure | Microsoft Docs
description: Elija cómo implementar la solución de Azure IoT con Azure IoT Central, Azure IoT Suite o Azure IoT Hub.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 8ed524e87f820f6c1e2e05da0bcbc7241bdd1ec1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201465"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Comparación de las opciones de Azure IoT y Azure IoT Central

Para implementar una solución de IoT, Microsoft Azure IoT Central y Azure IoT ofrecen varias opciones, cada una adecuada para distintos conjuntos de requisitos del cliente:

* [Azure IoT Central](overview-iot-central.md) es una solución de software como servicio (SaaS) que usa un enfoque basado en un modelo para que pueda crear soluciones de IoT de nivel empresarial sin necesidad de contar con experiencia en el desarrollo de soluciones en la nube.

* [Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/) es una colección de nivel empresarial de [soluciones preconfiguradas](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions) integrado en la plataforma como servicio de Azure (PaaS) que permite acelerar el desarrollo de soluciones personalizadas de IoT.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub es el componente PaaS central de Azure que utilizan tanto Azure IoT Central como Azure IoT Suite. IoT Hub habilita una comunicación bidireccional confiable y segura entre millones de dispositivos de IoT y una solución en la nube. IoT Hub le ayuda a superar los desafíos de la implementación de IoT como son:

* La administración y la conectividad de los dispositivos de gran volumen.
* La ingesta de telemetría de gran volumen.
* El comando y el control de dispositivos.
* El cumplimiento de la seguridad de los dispositivos.

## <a name="compare-azure-iot-central-and-azure-iot-suite"></a>Comparación de Azure IoT Central y Azure IoT Suite

La elección de un producto de IoT de Azure es una parte fundamental del planeamiento de la solución de IoT. IoT Hub es un servicio individual de Azure que, por sí mismo, no proporciona una solución de IoT completa. IoT Hub se puede usar como punto de partida de cualquier solución de IoT, y no necesita Azure IoT Suite ni Azure IoT Central para ello. Tanto IoT Suite como Azure IoT Central usan IoT Hub junto con otros servicios de Azure. En la tabla siguiente se resumen las diferencias principales entre Azure IoT Suite y Azure IoT Central para ayudarle a elegir la opción correcta según sus requisitos:

|     | Azure IoT Central | Azure IoT Suite |
| --- | ----------- | --------- |
| Uso principal                      | Acelerar el tiempo de comercialización para las soluciones de IoT sencillas que no requieren una personalización profunda del servicio.                                                    | Acelerar el desarrollo de una solución de IoT personalizada que necesita la máxima flexibilidad.                                                                                                                             |
| Acceso a los servicios PaaS subyacentes | SaaS. Solución totalmente administrada, los servicios subyacentes no se exponen.                                                                                            | Tiene acceso a los servicios de Azure subyacentes para administrarlos o reemplazarlos según sea necesario.                                                                                                                    |
| Flexibilidad                        | Mediana. Puede usar la experiencia de usuario integrada y basada en el explorador para personalizar el modelo de solución y los aspectos de la interfaz de usuario. La infraestructura no es personalizable porque los distintos componentes no se exponen. | Alta. El código para los microservicios es abierto y puede modificarlo como considere oportuno. Además, puede personalizar la infraestructura de implementación.                                               |
| Nivel de dificultad                        | Baja. Necesita conocimientos de modelado para personalizar la solución. No se requieren conocimientos de codificación.                                                                          | Medio alto. Necesita conocimientos de .NET o Java para personalizar la solución back-end. Necesita conocimientos de JavaScript para personalizar la visualización.                                                                       |
| Experiencia inicial             | Tanto las plantillas de aplicaciones como las plantillas de dispositivos proporcionan modelos pregenerados. Se pueden implementar en minutos.                                                                                                  | Las soluciones preconfiguradas implementan escenarios comunes de IoT. Se pueden implementar en minutos.                                                                                                                            |
| Precios                            | Estructura de precios sencilla y predecible.                                                                                                                           | Puede ajustar los servicios para controlar el costo.                                                                                                                                                            |

La decisión de qué producto desea usar para crear su solución de IoT se determina en última instancia con:

* Los requisitos empresariales.
* El tipo de solución que desea generar
* La variedad de destrezas de la organización para crear y mantener la solución a largo plazo.

## <a name="next-steps"></a>Pasos siguientes

En función del producto y el enfoque elegidos, los pasos siguientes sugeridos son:

* **Azure IoT Central**: [Azure IoT Central](overview-iot-central.md).
* **IoT Suite**: [¿Qué son las soluciones preconfiguradas de IoT de Azure?](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions)
* **IoT Hub**: [Introducción al servicio Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub).