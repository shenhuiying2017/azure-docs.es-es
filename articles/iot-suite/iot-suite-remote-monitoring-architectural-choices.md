---
title: 'Opciones de la arquitectura de la solución de supervisión remota: Azure | Microsoft Docs'
description: En este artículo se describen las opciones técnicas y de la arquitectura de la supervisión remota
services: iot-suite
suite: iot-suite
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 192a763c01e60d816ae2046587176627fc9d8736
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="remote-monitoring-architectural-choices"></a>Opciones de arquitectura de supervisión remota

La solución de supervisión remota de Azure IoT es un Solution Accelerator con licencia MIT y de código abierto que presenta los escenarios comunes de IoT, como la conectividad de dispositivos, la administración de dispositivos y el procesamiento de flujos, a fin de que los clientes puedan agilizar el proceso de desarrollo.  RM sigue la arquitectura de referencia recomendada de Azure IoT publicada [aquí](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).  

En este artículo se describen las opciones técnicas y de arquitectura realizadas en cada uno de los subsistemas de la supervisión remota y, además, se tratan las alternativas consideradas.  Es importante tener en cuenta que las opciones técnicas realizadas en la supervisión remota no son la única forma de implementar una solución de IoT de supervisión remota.  La implementación técnica es una línea base para crear una aplicación apropiada y debe modificarse para ajustarse a las cualificaciones, la experiencia y las necesidades de aplicación vertical para la implementación de una solución de cliente.

## <a name="architectural-choices"></a>Opciones de la arquitectura

### <a name="microservices-serverless-and-cloud-native"></a>Microservicios, sin servidor y nativa en la nube

La arquitectura recomendada para aplicaciones IoT es nativa en la nube, basada en microservicios y sin servidor.  Los diferentes subsistemas de una aplicación IoT deben crearse como servicios discretos que se pueden implementar de forma independiente y que se puedan escalar también por separado.  Estos atributos permiten mayor escala, más flexibilidad para actualizar los subsistemas individuales y proporcionan la flexibilidad para elegir la tecnología apropiada por cada subsistema.  Los microservicios pueden implementarse con varias tecnologías. Por ejemplo, usar la tecnología de contenedores como Docker con la tecnología sin servidor, como Azure Functions, o hospedar microservcios en servicios PaaS, como Azure App Services.

## <a name="core-subsystem-technology-choices"></a>Opciones tecnológicas del subsistema principal

En esta sección se detallan las opciones de tecnología realizadas en la supervisión remota para cada subsistema principal.

![Diagrama principal](media/iot-suite-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Puerta de enlace en la nube
Azure IoT Hub se usa como la puerta de enlace en la nube para la supervisión remota.  IoT Hub ofrece una comunicación segura y bidireccional con los dispositivos. Puede obtener más información sobre IoT Hub [aquí](https://azure.microsoft.com/services/iot-hub/). Para la conectividad de dispositivos IoT, se usan los SDK de .NET Core y IoT Hub de Java.  Los SDK ofrecen contenedores en torno a la API de REST de IoT Hub y controlan escenarios como los reintentos. 

### <a name="stream-processing"></a>Procesamiento de flujos
Para el procesamiento de flujos, la supervisión remota usa Azure Stream Analytics para el procesamiento de reglas complejas.  Para los clientes que desean reglas más sencillas, también tenemos un microservicio personalizado con compatibilidad para el procesamiento de reglas sencillas, aunque esta configuración no forma parte de la implementación predefinida. La arquitectura de referencia recomienda usar Azure Functions para el procesamiento de reglas sencillas y Azure Stream Analytics (ASA) para el procesamiento de reglas complejas.  

### <a name="storage"></a>Storage
Para Storage, Cosmos DB se usa para todas las necesidades de almacenamiento: almacenamiento en frío, almacenamiento en caliente, almacenamiento de reglas y alarmas. Actualmente está en curso el proceso de migración a Azure Blob Storage, tal y como se recomienda en la arquitectura de referencia.  Cosmos DB es la solución de almacenamiento en caliente de uso general recomendada para aplicaciones IoT, aunque las soluciones como Azure Time Series Insights y Azure Data Lake son apropiadas para muchos casos de uso.

### <a name="business-integration"></a>Integración de negocios
Integración de negocios en la solución de supervisión remota se limita a la generación de alarmas, que se hospedan en el almacenamiento en caliente. Se pueden realizar más integraciones de negocios mediante la integración de la solución con Azure Logic Apps.

### <a name="user-interface"></a>Interfaz de usuario
La interfaz de usuario web se compila con JavaScript React.  React ofrece un marco de interfaz de usuario web de uso común en el sector y es similar a otros marcos populares como Angular.  

### <a name="runtime-and-orchestration"></a>Automatización y orquestación
El tiempo de ejecución de la aplicación elegido para la implementación de subsistemas en la supervisión remota se corresponde con contenedores de Docker con Kubernetes (K8s) como el orquestador del escalado horizontal.  Esta arquitectura permite la definición de escala individual por subsistema; sin embargo, incurre en costos de DevOps al mantener máquinas virtuales y contenedores actualizados desde una perspectiva de seguridad.  Las alternativas a Docker y K8s abarcan el hospedaje de microservicios en servicios PaaS (por ejemplo, Azure App Service) o el uso de Service Fabric, DCOS, Swarm, etc. como orquestador.

## <a name="next-steps"></a>Pasos siguientes
* Implemente la solución de supervisión remota [aquí](https://www.azureiotsuite.com/).
* Explore el código de GitHub en [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) y [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Obtenga más información sobre la arquitectura de referencia de IoT [aquí](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).
