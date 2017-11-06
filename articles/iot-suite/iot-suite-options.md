---
title: Opciones de IoT de Microsoft Azure | Microsoft Docs
description: "Elija cómo implementar la solución de IoT de Azure con Conjunto de aplicaciones de IoT, IoT Central o IoT Hub."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.topic: get-started-article
ms.date: 09/21/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd98d42ab391d471d2302066dc2baf2c64f56f55
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="compare-azure-iot-options"></a>Comparación de las opciones de IoT de Azure

El artículo [Azure e Internet de las cosas](iot-suite-what-is-azure-iot.md) describe la arquitectura de una solución de IoT típica con los niveles siguientes:

* La administración y la conectividad de los dispositivos
* Procesamiento de datos y análisis
* La presentación y la integración empresarial

Para implementar esta arquitectura, IoT de Azure ofrece varias opciones, cada una adecuada para distintos conjuntos de requisitos del cliente:

* [Conjunto de aplicaciones de IoT de Azure](index.md) es una colección de nivel empresarial de [soluciones preconfiguradas](iot-suite-what-are-preconfigured-solutions.md) integrado en la plataforma como servicio de Azure que le permite acelerar el desarrollo de soluciones personalizadas de IoT.

* [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions) es una solución de SaaS que usa un enfoque basado en un modelo para que pueda crear soluciones de IoT de nivel empresarial sin necesidad de pasar por la experiencia de desarrollar soluciones en la nube.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub es la plataforma como servicio de Azure que tanto IoT Central como Conjunto de aplicaciones de IoT usan. IoT Hub habilita la comunicación bidireccional confiable y segura entre millones de dispositivos de IoT y una solución en la nube. IoT Hub le ayuda a superar los desafíos de la implementación de IoT como son:

* La administración y la conectividad de los dispositivos de gran volumen.
* La ingesta de telemetría de gran volumen.
* El comando y el control de dispositivos.
* El cumplimiento de la seguridad de los dispositivos.

## <a name="compare-iot-suite-and-iot-central"></a>Comparación de Conjunto de aplicaciones de IoT e IoT Central

La elección de un producto de IoT de Azure es una parte fundamental del planeamiento de la solución de IoT. IoT Hub es un servicio individual de Azure que, por sí mismo, no proporciona una solución de IoT completa. IoT Hub puede usarse como punto de partida para cualquier solución de IoT y no necesita Conjunto de aplicaciones de IoT de Azure ni Microsoft IoT Central para ello. Tanto Conjunto de aplicaciones de IoT como IoT Central usan IoT Hub junto con otros servicios de Azure. En la tabla siguiente se resumen las diferencias principales entre IoT Suite e IoT Central para ayudarle a elegir la opción correcta según sus requisitos:

|                        | IoT Suite | IoT Central |
| ---------------------- | --------- | ----------- |
| Uso principal | Acelerar el desarrollo de una solución de IoT personalizada que necesita la máxima flexibilidad. | Acelerar el tiempo de comercialización para las soluciones de IoT sencillas que no requieren una personalización profunda del servicio. |
| Acceso a los servicios PaaS subyacentes          | Tiene acceso a los servicios de Azure subyacentes para administrarlos o reemplazarlos según sea necesario. | SaaS. Solución totalmente administrada, los servicios subyacentes no se exponen. |
| Flexibilidad            | Alta. El código para los microservicios es abierto y puede modificarlo como considere oportuno. Además, puede personalizar la infraestructura de implementación.| Mediana. Puede usar la experiencia de usuario integrada y basada en el explorador para personalizar el modelo de solución y los aspectos de la interfaz de usuario. La infraestructura no es personalizable porque los distintos componentes no se exponen.|
| Nivel de dificultad                 | Medio alto. Necesita conocimientos de .NET o Java para personalizar la solución back-end. Necesita conocimientos de JavaScript para personalizar la visualización. | Baja. Necesita conocimientos de modelado para personalizar la solución. No se requieren conocimientos de codificación. |
| Experiencia inicial | Las soluciones preconfiguradas implementan escenarios comunes de IoT. Se pueden implementar en minutos. | Las plantillas proporcionan modelos predefinidos. Se pueden implementar en minutos. |
| Precios                | Puede ajustar los servicios para controlar el costo. | Estructura de precios sencilla y predecible. |

La decisión de qué producto desea usar para crear su solución de IoT se determina en última instancia con:

* Los requisitos empresariales.
* El tipo de solución que desea generar
* La variedad de destrezas de la organización para crear y mantener la solución a largo plazo.

## <a name="next-steps"></a>Pasos siguientes

En función del producto y el enfoque elegidos, los pasos siguientes sugeridos son:

* **Conjunto de aplicaciones de IoT**: [¿Qué son las soluciones preconfiguradas de IoT de Azure?](iot-suite-what-are-preconfigured-solutions.md)
* **IoT Central**: [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions).
* **IoT Hub**: [Introducción al servicio Azure IoT Hub](../iot-hub/iot-hub-what-is-iot-hub.md).
