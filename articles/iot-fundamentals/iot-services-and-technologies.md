---
title: Tecnologías y soluciones de Azure Internet of Things (IoT)
description: Aquí se describen las tecnologías y soluciones de la colección de PaaS/SaaS que están disponibles para compilar una solución de Azure IoT.
services: iot-hub
documentationcenter: ''
author: BryanLa
manager: timlt
editor: bryanla; v-clay
ms.service: iot
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: bryanla; v-clay
ms.custom: ''
ms.openlocfilehash: 56410b040ac14a2a46927cac8ed41218fc709888
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
---
# <a name="azure-iot-technologies-and-solutions-paas-and-saas"></a>Soluciones y tecnologías de Azure IoT: PaaS y SaaS

Microsoft ha compilado una cartera que satisface las necesidades de todos los clientes, ya que les permite obtener acceso a las ventajas que ofrece la transformación digital. La cartera de productos de Azure IoT se compone de información general acerca las tecnologías y soluciones de PaaS/SaaS disponibles. Muestra las dos rutas de acceso que están disponibles para compilar su propia solución:
- Plataforma como servicio (PaaS): compile su aplicación mediante [aceleradores de soluciones de Azure IoT](https://www.azureiotsuite.com/), que son una colección de soluciones preconfiguradas de clase empresarial que le permiten acelerar el desarrollo de soluciones personalizadas de IoT.
- Software como servicio (SaaS): empiece a trabajar rápidamente con [Azure IoT Central](https://azure.microsoft.com/services/iot-central/), la nueva solución de SaaS para desarrollar aplicaciones de IoT sin tener que preocuparse por la complejidad de la solución de IoT. Si su organización no tiene los recursos necesarios para crear su propia solución de IoT, Azure IoT Central es una solución de IoT sin código que puede crear modelos de dispositivo, paneles y reglas en minutos.

![Soluciones y tecnologías de Azure IoT][img-paas-saas-technologies-solutions]

## <a name="solutions"></a>Soluciones

Póngase a trabajar rápidamente con los aceleradores de soluciones y las ofertas SaaS. Escoja las soluciones preconfiguradas que le permitirán trabajar con escenarios comunes de IoT, tales como la supervisión remota, el mantenimiento predictivo y la fábrica conectada, para poder crear una solución completamente personalizada. Asimismo, también puede usar Azure IoT Central, una solución de un extremo a otro completamente administrada y que habilita escenarios de IoT muy eficaces sin necesidad de tener un gran conocimiento de soluciones en la nube.

### <a name="azure-iot-solution-accelerators-paas"></a>Aceleradores de soluciones de Azure IoT (PaaS)

Los aceleradores de soluciones de Azure IoT son soluciones PaaS personalizables que ofrecen un alto nivel de control sobre la solución de IoT. Si su empresa va a implementar IoT para operaciones conectadas o tiene requisitos de personalización específicos para los productos conectados, los aceleradores de soluciones de Azure IoT le ofrecen el control que necesita. 

Algunos ejemplos de empresas que pueden beneficiarse de los aceleradores de soluciones de IoT son las organizaciones que cuentan con un gran número de dispositivos o modelos de dispositivo y los fabricantes que están interesados en soluciones de fábrica conectadas. Al crear soluciones totalmente personalizables adaptadas a sus necesidades más complejas, los aceleradores de soluciones de IoT le proporcionan: 

- Soluciones precompiladas
    - Supervisión remota
    - Fábrica conectada
    - Mantenimiento predictivo
    - Simulación de dispositivos
- Capacidad de implementar elementos en minutos
- Oportunidad de agilizar la rentabilidad
- Soluciones que le ofrecen un control absoluto 
 
### <a name="azure-iot-central-saas"></a>Azure IoT Central (SaaS)

Azure IoT Central es una solución de SaaS totalmente administrada que le permite empezar a trabajar rápidamente aunque su experiencia con IoT sea mínima. Si su empresa está intentando obtener velocidad en detrimento de la personalización, los modelos de SaaS pueden ser la solución perfecta para sus necesidades de implementación de IoT. 

Las organizaciones con menos modelos de dispositivo, más escenarios predecibles y capacidades de IoT/TI limitadas, pueden aprovecharse de las ventajas que ofrece IoT mediante SaaS. Aquellas empresas que no tenían el tiempo, los recursos ni la experiencia necesarios para desarrollar productos conectados, ya pueden ponerse manos a la obra gracias a Azure IoT Central. Microsoft es el líder del sector cuando se trata de proporcionar una solución de SaaS madura que sea capaz de hacerse cargo de los requisitos de implementación comunes en IoT. 

- SaaS de IoT completamente administrada
- No es necesario tener experiencia en el desarrollo de soluciones en la nube
- Se adapta a sus necesidades
- Ideal para las necesidades directas de IoT

### <a name="compare-azure-iot-solution-accelerators-and-azure-iot-central"></a>Comparación de los aceleradores de soluciones de Azure IoT y Azure IoT Central

Para implementar una [arquitectura de soluciones IoT](/azure/iot-fundamentals/iot-introduction#iot-solution-architecture) típica, Azure IoT ofrece varias opciones: los [aceleradores de soluciones de Azure IoT](/azure/iot-suite) y [Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions), cada una adecuada para diferentes requisitos de los clientes.

[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) es la plataforma como servicio (PaaS) principal de Azure que utilizan tanto Azure IoT Central como los aceleradores de soluciones de Azure IoT. IoT Hub habilita la comunicación bidireccional confiable y segura entre millones de dispositivos de IoT y una solución en la nube. IoT Hub le ayuda a superar los desafíos de la implementación de IoT como son:

* La administración y la conectividad de los dispositivos de gran volumen.
* La ingesta de telemetría de gran volumen.
* El comando y el control de dispositivos.
* El cumplimiento de la seguridad de los dispositivos.

La elección de un producto de IoT de Azure es una parte fundamental del planeamiento de la solución de IoT. IoT Hub es un servicio individual de Azure que, por sí mismo, no proporciona una solución de IoT completa. IoT Hub se puede usar como punto de partida para cualquier solución de IoT y no es preciso emplear aceleradores de soluciones de Azure IoT ni Azure IoT Central para utilizarlo. Tanto los aceleradores de soluciones de Azure IoT como Azure IoT Central usan IoT Hub, junto con otros servicios de Azure. En la tabla siguiente se resumen las principales diferencias entre los aceleradores de soluciones de Azure IoT y Azure IoT Central para ayudarle a elegir la opción correcta en función de sus necesidades:

|                        | Aceleradores de soluciones de IoT de Azure | Azure IoT Central |
| ---------------------- | --------- | ----------- |
| Uso principal | Acelerar el desarrollo de una solución de IoT personalizada que necesita la máxima flexibilidad. | Acelerar el tiempo de comercialización para las soluciones de IoT sencillas que no requieren una personalización profunda del servicio. |
| Acceso a los servicios PaaS subyacentes          | Tiene acceso a los servicios de Azure subyacentes para administrarlos o reemplazarlos según sea necesario. | SaaS. Solución totalmente administrada, los servicios subyacentes no se exponen. |
| Flexibilidad            | Alta. El código para los microservicios es abierto y puede modificarlo como considere oportuno. Además, puede personalizar la infraestructura de implementación.| Mediana. Puede usar la experiencia de usuario integrada y basada en el explorador para personalizar el modelo de solución y los aspectos de la interfaz de usuario. La infraestructura no es personalizable porque los distintos componentes no se exponen.|
| Nivel de dificultad                 | Medio alto. Necesita conocimientos de .NET o Java para personalizar la solución back-end. Necesita conocimientos de JavaScript para personalizar la visualización. | Baja. Necesita conocimientos de modelado para personalizar la solución. No se requieren conocimientos de codificación. |
| Experiencia inicial | Los aceleradores de soluciones implementan escenarios comunes de IoT. Se pueden implementar en minutos. | Tanto las plantillas de aplicaciones como las plantillas de dispositivos proporcionan modelos pregenerados. Se pueden implementar en minutos. |
| Precios                | Puede ajustar los servicios para controlar el costo. | Estructura de precios sencilla y predecible. |

La decisión de qué producto desea usar para crear su solución de IoT se determina en última instancia con:

* Los requisitos empresariales.
* El tipo de solución que desea generar
* La variedad de destrezas de la organización para crear y mantener la solución a largo plazo.

## <a name="technologies-paas"></a>Tecnologías (PaaS)

Como cuentan con la cartera de IoT o los servicios de plataforma más amplios, las tecnologías de Plataforma como servicio (PaaS) que se distribuyen en la plataforma de Azure permiten crear, personalizar y controlar fácilmente todos los aspectos de la solución de IoT. Establezca comunicaciones bidireccionales con miles de millones de dispositivos de IoT y administre los dispositivos de IoT a escala. A continuación, integre los datos de los dispositivos de IoT con otros servicios de plataforma, como Azure Cosmos DB y Azure Time Series Insights, para mejorar la información de la solución. 

### <a name="device-support"></a>Compatibilidad con dispositivos

Comience su proyecto de IoT con confianza, aprovechando nuestros [kits de inicio de Azure IoT](https://catalog.azureiotsuite.com/kits) o eligiendo entre cientos de dispositivos certificados para IoT de nuestro [catálogo de dispositivos](http://catalog.azureiotsuite.com/). Todos los dispositivos son independientes de la plataforma y se ha comprobado que se conectan sin problemas a IoT Hub.
Conecte todos sus dispositivos a Azure IoT con los [SDK de dispositivo](/azure/iot-hub/iot-hub-devguide-sdks) de código abierto. Los SDK admiten varios sistemas operativos, como Linux, Windows y sistemas operativos en tiempo real, además de varios lenguajes de programación, como [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.NET](https://github.com/Azure/azure-iot-sdk-csharp) y [Python](https://github.com/Azure/azure-iot-sdk-python).

### <a name="iot"></a>IoT 
[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) es un servicio totalmente administrado que permite establecer una comunicación bidireccional fiable y segura entre millones de dispositivos IoT y un back-end de soluciones. El servicio Azure IoT Hub Device Provisioning es un servicio auxiliar de IoT Hub que ofrece un aprovisionamiento just-in-time, sin intervención del usuario, de la instancia apropiada de IoT Hub, que permite a los clientes aprovisionar millones de dispositivos de forma segura y escalable.

### <a name="edge"></a>perimetral
[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) es un servicio de Internet de las cosas (IoT). Este servicio está pensado para los clientes que desean analizar datos en dispositivos, situación también conocida como "en el extremo." Al mover partes de la carga de trabajo al extremo, se reduce la latencia y tendrá la opción de usar escenarios sin conexión.


### <a name="data-and-analytics"></a>Datos y análisis
Aproveche las ventajas que ofrece una matriz de datos de Azure y los análisis de PaaS en la solución de IoT; gracias a ellas, podrá llevar la inteligencia de la nube al extremo con Azure Machine Learning, podrá guardar los datos del dispositivo de IoT de forma rentable gracias a Azure Data Lake o podrá visualizar una gran cantidad de datos en los dispositivos de IoT con [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).

### <a name="visualization-and-integration"></a>Visualización e integración
Microsoft Azure le ofrece una solución de nube completa que combina una colección de servicios en la nube integrados y en constante crecimiento, con un compromiso líder en su sector dedicado a la protección y la privacidad de los datos. Obtenga más información acerca de [Microsoft Azure](https://azure.microsoft.com/).





## <a name="next-steps"></a>Pasos siguientes

Visite la sección [Introducción](/azure/iot-hub/iot-hub-get-started) para comenzar a usar las características de IoT rápida y fácilmente. Si quiere obtener una experiencia práctica más detallada, pruebe a realizar uno de los [tutoriales](/azure/iot-edge/tutorial-simulate-device-windows).

[img-paas-saas-technologies-solutions]: media/iot-comparison/paas-saas-technologies-solutions.png

