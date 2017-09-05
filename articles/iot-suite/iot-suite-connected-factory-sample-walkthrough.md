---
title: "Tutorial de la solución de fábrica conectada del conjunto de aplicaciones de IoT de Azure| Microsoft Docs"
description: "Una descripción de la solución de fábrica preconfigurada conectada de Azure IoT y su arquitectura."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 517e908a744734139ed0aeee314a4f3b9eda86cc
ms.contentlocale: es-es
ms.lasthandoff: 08/24/2017

---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>Tutorial de la solución de fábrica preconfigurada conectada

La solución de fábrica [preconfigurada][lnk-preconfigured-solutions] conectada del conjunto de aplicaciones de IoT es una implementación de una solución industrial completa que:

* Se conecta a dispositivos industriales simulados que ejecutan servidores OPC UA en líneas de producción de fábrica simuladas y en dispositivos de servidor OPC UA reales. Para más información acerca de OPC UA, consulte las [preguntas frecuentes sobre la fábrica conectada](iot-suite-faq-cf.md).
* Muestra las claves KPI operativas y el OEE de esos servicios y líneas de producción.
* Demuestra cómo se podría usar una aplicación basada en la nube para interactuar con sistemas de servidores OPC UA.
* Le permite conectar sus propios dispositivos de servidor OPC UA.
* Le permite examinar y modificar los datos del servidor OPC UA.
* Se integra con el servicio Azure Time Series Insights (TSI) para proporcionar vistas personalizadas de los datos desde los servidores OPC UA.

Puede usar la solución como punto de partida para su propia implementación, así como [personalizarla][lnk-customize] para cubrir sus requisitos empresariales específicos.

Este artículo le guía a través de algunos de los elementos clave de la solución de fábrica conectada para que pueda entender cómo funciona. Esta información le ayuda a:

* Solucionar problemas de la solución.
* Planear cómo personalizar la solución para satisfacer sus propios requisitos específicos.
* Diseñar una solución de IoT propia que utilice servicios de Azure.

Para más información, consulte las [preguntas frecuentes sobre la fábrica conectada](iot-suite-faq-cf.md).

## <a name="logical-architecture"></a>Arquitectura lógica

El diagrama siguiente describe los componentes lógicos de la solución preconfigurada:

![Arquitectura lógica de fábrica conectada][connected-factory-logical]

## <a name="communication-patterns"></a>Patrones de comunicación

La solución emplea la [especificación de publicación/suscripción de OPC UA](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/) para enviar datos de telemetría de OPC UA a IoT Hub en formato JSON. Para este fin, la solución usa el módulo [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) de IoT Edge.

La solución también tiene un cliente de OPC UA integrado en una aplicación web que puede establecer conexiones con servidores de OPC UA locales. El cliente usa un [proxy inverso](https://wikipedia.org/wiki/Reverse_proxy) y recibe ayuda de IoT Hub para realizar la conexión sin necesidad de abrir puertos en el firewall local. Este patrón de comunicación se denomina [comunicación asistida por el servicio](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/). Para este fin, la solución usa el módulo [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy/) de IoT Edge.


## <a name="simulation"></a>Simulation

Las estaciones simuladas y los sistemas de ejecución de fabricación (MES) simulados constituyen una línea de producción de fábrica. Los dispositivos simulados y el módulo publicador de OPC se basan en el [estándar .NET de OPC UA][lnk-OPC-UA-NET-Standard] publicado por el consorcio OPC Foundation.

El proxy de OPC y el publicador de OPC se implementan como módulos basados en [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Cada línea de producción simulada tiene asociada una puerta de enlace designada.

Todos los componentes de simulación se ejecutan en contenedores de Docker hospedados en una máquina virtual Linux de Azure. De forma predeterminada, la simulación está configurada para ejecutar ocho líneas de producción simuladas.

## <a name="simulated-production-line"></a>Línea de producción simulada

Una línea de producción fabrica piezas. La forman distintas estaciones: una estación de ensamblado, una estación de prueba y una estación de empaquetado.

La simulación ejecuta y actualiza los datos que se exponen a través de los nodos de OPC UA. Todas las estaciones de la línea de producción simulada se organizan mediante el MES a través de OPC UA.

## <a name="simulated-manufacturing-execution-system"></a>Sistema de ejecución de fabricación simulado

El MES supervisa cada estación de la línea de producción a través de OPC UA para detectar los cambios en el estado de la estación. Para controlar las estaciones llama a métodos OPC UA y pasa un producto de una estación a la siguiente hasta que se termina.

## <a name="gateway-opc-publisher-module"></a>Módulo publicador de OPC de puerta de enlace

El módulo publicador de OPC se conecta a los servidores OPC UA de la estación y se suscribe a los nodos de OPC que se van a publicar. El módulo convierte los datos del nodo en formato JSON, los cifra y los envía a IoT Hub como mensajes publicados y enviados de OPC UA.

El módulo publicador de OPC solo necesita el puerto HTTPS saliente (443) y puede funcionar con la infraestructura empresarial existente.

## <a name="gateway-opc-proxy-module"></a>Módulo proxy de OPC de puerta de enlace

El módulo proxy de OPC UA de puerta de enlace crea un túnel para los mensajes de control y comando de OPC UA binarios y solo necesita un puerto HTTPS saliente (443). Puede funcionar con la infraestructura empresarial existente, incluidos los proxies web.

Emplea métodos de dispositivo de IoT Hub para transferir paquetes de datos TCP/IP en la capa de aplicación y así garantizar la confianza del punto de conexión, el cifrado de datos y la integridad mediante SSL/TLS.

El protocolo binario de OPC UA transmitido mediante el mismo proxy usa la autenticación y el cifrado de UA.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

El módulo publicador de OCP de puerta de enlace se suscribe a los nodos de servidor OPC UA para detectar los cambios en los valores de datos. Si se detecta un cambio en los datos de uno de los nodos, este módulo envía entonces mensajes a Azure IoT Hub.

IoT Hub proporciona un origen de eventos a Azure TSI. TSI almacena los datos durante 30 días en función de las marcas de tiempo adjuntas a los mensajes. Estos datos incluyen:

* OPC UA ApplicationUri
* OPC UA NodeId
* Valor del nodo
* Marca de tiempo de origen
* OPC UA DisplayName

Actualmente, el TSI no permite que los clientes personalicen el tiempo que desean conservar los datos.

TSI realiza consultas en los datos del nodo mediante SearchSpan (Time.From, Time.To) y los agrega mediante OPC UA ApplicationUri, OPC UA NodeId u OPC UA DisplayName.

Para recuperar los datos de los medidores de OEE y KPI, y los gráficos de la serie temporal, los datos se agregan mediante funciones de recuento de eventos, suma, promedio, mínimo y máximo.

La serie temporal se compila siguiendo un proceso diferente. Los OEE y KPI se calculan a partir de los datos base de la estación y se traspasan para la topología (líneas de producción, fábricas, empresa) de la aplicación.

Además, la serie temporal de la topología de OEE y KPI se calcula en la aplicación, siempre que una marca de tiempo mostrada esté lista. Por ejemplo, la vista de día se actualiza cada hora completa.

La vista de la serie temporal de los datos del nodo viene directamente de TSI mediante una agregación de marca de tiempo.

## <a name="iot-hub"></a>IoT Hub
El [centro de IoT][lnk-IoT Hub] recibe los datos enviados desde el módulo publicador de OPC en la nube y permite que estén disponibles para el servicio de Azure TSI. 

El centro de IoT en la solución también:
- Mantiene un Registro de identidad que almacena los identificadores de todos los módulos publicador y proxy de OPC.
- Se usa como canal de transporte para la comunicación bidireccional del módulo proxy de OPC.

## <a name="azure-storage"></a>Azure Storage
La solución emplea Azure Blob Storage como almacenamiento en disco en la máquina virtual y para almacenar datos de implementación.

## <a name="web-app"></a>Aplicación web
La aplicación web implementada como parte de la solución preconfigurada consta de un cliente de OPC UA integrado, procesamiento de alertas y visualización de telemetría.

## <a name="next-steps"></a>Pasos siguientes

Puede continuar su introducción al Conjunto de aplicaciones de IoT con la lectura de los siguientes artículos:

* [Permisos en el sitio azureiotsuite.com][lnk-permissions]
* [Implementación de una puerta de enlace en Windows o Linux para la solución preconfigurada de fábrica conectada](iot-suite-connected-factory-gateway-deployment.md)

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-permissions.md

