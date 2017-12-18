---
title: "Tutorial de la solución de fábrica conectada: Azure | Microsoft Docs"
description: "Una descripción la solución preconfigurada de Azure IoT Factoría conectada y de su arquitectura."
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
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 88fe50460baf8b7180da113b33a03120f39cf44f
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>Tutorial de la solución de fábrica preconfigurada conectada

La [solución preconfigurada][lnk-preconfigured-solutions] de IoT Suite Factoría conectada es una implementación de una solución industrial de un extremo a otro que:

* Se conecta a dispositivos industriales simulados que ejecutan servidores OPC UA en líneas de producción de fábrica simuladas y en dispositivos de servidor OPC UA reales. Para más información acerca de OPC UA, consulte las [preguntas frecuentes sobre la fábrica conectada](iot-suite-faq-cf.md).
* Muestra las claves KPI operativas y el OEE de esos servicios y líneas de producción.
* Demuestra cómo se podría usar una aplicación basada en la nube para interactuar con sistemas de servidores OPC UA.
* Le permite conectar sus propios dispositivos de servidor OPC UA.
* Le permite examinar y modificar los datos del servidor OPC UA.
* Se integra con el servicio Azure Time Series Insights (TSI) para proporcionar vistas personalizadas de los datos desde los servidores OPC UA.

Puede usar la solución como punto de partida para su propia implementación, así como [personalizarla][lnk-customize] para cubrir sus requisitos empresariales específicos.

Este artículo le guía a través de algunos de los principales elementos de la solución Factoría conectada para que sepa cómo funciona. En el artículo también se describe cómo fluyen los datos a través de la solución. Esta información le ayuda a:

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

TSI realiza consultas en los datos del nodo mediante **SearchSpan** (**Time.From**, **Time.To**) y los agrega mediante **OPC UA ApplicationUri**, **OPC UA NodeId** u **OPC UA DisplayName**.

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

## <a name="telemetry-data-flow"></a>Flujo de datos de telemetría

![Flujo de datos de telemetría](media/iot-suite-connected-factory-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>Pasos del flujo

1. OPC Publisher lee los certificados X509 OPC UA y las credenciales de seguridad de IoT Hub del almacén de certificados local.
    - Si es necesario, OPC Publisher crea y almacena las credenciales o los certificados que falten en el almacén de certificados.

2. OPC Publisher se registra con IoT Hub.
    - Usa el protocolo configurado. Puede utilizar cualquier protocolo admitido de SDK de cliente de IoT Hub. El predeterminado es MQTT.
    - La comunicación del protocolo está protegida con TLS.

3. OPC Publisher lee el archivo de configuración.

4. OPC Publisher crea una sesión de OPC con cada servidor de agente de usuario de OPC configurado.
    - Usa la conexión TCP.
    - OPC Publisher y el servidor de agente de usuario de OPC se autentican entre sí mediante certificados X509.
    - Todo el tráfico de agente de usuario de OPC adicional se cifra mediante el mecanismo de cifrado de agente de usuario de OPC configurado.
    - En la sesión de OPC para cada intervalo de publicación configurada, OPC Publisher crea una suscripción de OPC.
    - Crea elementos de OPC supervisados para que los nodos de OPC publiquen en la suscripción de OPC.

5. Si cambia un valor de nodo de OPC supervisado, el servidor de agente de usuario de OPC envía actualizaciones OPC Publisher.

6. OPC Publisher transcodifica el nuevo valor.
    - Procesa por lotes varios cambios, si esta característica está habilitada.
    - Crea un mensaje de IoT Hub.

7. OPC Publisher envía un mensaje IoT Hub.
    - Usa el protocolo configurado.
    - La comunicación está protegida con TLS.

8. Time Series Insights (TSI) lee los mensajes de IoT Hub.
    - Usa AMQP a través de TCP/TLS.
    - Este paso es interno del centro de datos.

9. Los datos reposan en TSI.

10. La instancia de WebApp conectada de fábrica en Azure AppService solicita los datos que necesita a TSI.
    - Usa comunicación protegida a través de TCP/TLS.
    - Este paso es interno del centro de datos.

11. El explorador web se conecta a la instancia de WebApp de Factoría conectada.
    - Representa el panel de Factoría conectado.
    - Se conecta a través de HTTPS.
    - El acceso a la aplicación Factoría conectada requiere la autenticación del usuario a través de Azure Active Directory.
    - Las llamadas de WebApi a la aplicación Factoría conectada están protegidas con tokens antifalsificación.

12. En las actualizaciones de datos, la instancia de WebApp de fábrica conectada envía los datos actualizados al explorador web.
    - Usa el protocolo de SignalR.
    - Protegido mediante TCP/TLS.

## <a name="browsing-data-flow"></a>Examen del flujo de datos

![Examen del flujo de datos](media/iot-suite-connected-factory-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>Pasos del flujo

1. El proxy de OPC (componente de servidor) se inicia.
    - Lee las claves de acceso compartido de un almacén local.
    - Si es necesario, almacena las claves de acceso que faltan en el almacén.

2. El proxy de OPC (componente de servidor) se registra con IoT Hub.
    - Lee todos sus dispositivos conocidos de IoT Hub.
    - Usa MQTT sobre TLS a través de Socket o Websocket seguro.

3. El explorador web se conecta a la aplicación web Factoría conectada y representa el panel de dicha aplicación.
    - Usa HTTPS.
    - Un usuario selecciona un servidor de agente de usuario de OPC al que conectarse.

4. La instancia de WebApp de fábrica conectada establece una sesión de agente de usuario de OPC para el servidor de agente de usuario de OPC seleccionado.
    - Usa la pila de agente de usuario de OPC.

5. El transporte del proxy de OPC recibe una solicitud de la pila del agente de usuario de OPC de establecer una conexión mediante TCP Socket al servidor de agente de usuario OPC.
    - Solo recupera la carga de TCP y la utiliza sin cambios.
    - Este paso es interno de la instancia de WebApp de Factoría conectada.

6. El proxy de OPC (componente de cliente) busca en el registro de dispositivos de IoT Hub el dispositivo del proxy OPC (componente del servidor). A continuación, llama a un método de dispositivo del dispositivo del proxy de OPC (componente de servidor) en IoT Hub.
    - Usa HTTPS a través de TCP/TLS para buscar el proxy de OPC.
    - Usa HTTPS a través de TCP/TLS para establecer una conexión de TCP Socket en el servidor de agente de usuario de OPC.
    - Este paso es interno del centro de datos.

7. IoT Hub llama a un método de dispositivo del dispositivo del proxy de OPC (componente de servidor).
    - Usa un MQTT establecido sobre TLS a través de la conexión de Socket o Websocket seguro para establecer una conexión de TCP Socket en el servidor del agente de usuario de OPC.

8. El proxy de OPC (componente de servidor) envía la carga TCP a la red de la planta.

9. El servidor del agente de usuario de OPC procesa la carga y devuelve la respuesta.

10. Socket del proxy de OPC (componente de servidor) recibe la respuesta.
    - El proxy de OPC envía los datos como valor devuelto del método de dispositivo a IoT Hub y al proxy de OPC (componente de cliente).
    - Estos datos se entregan a la pila del agente de usuario de OPC de la aplicación Factoría conectada.

11. La instancia de WebApp de fábrica conectada devuelve una experiencia de usuario de explorador de OPC enriquecida con la información específica del agente de usuario de OPC recibida desde el servidor del agente de usuario de OPC en el explorador web para representarla.
    - Mientras navega por el espacio de direcciones de OPC y aplicar funciones a los nodos en él, la parte del cliente de la experiencia de usuario de explorador de OPC usa llamadas AJAX sobre HTTPS protegidas con tokens antifalsificación para obtener datos de la instancia de WebApp de fábrica conectada.
    - Si es necesario, el cliente utiliza la comunicación que se explica en los pasos 4-10 para intercambiar información con el servidor de agente de usuario de OPC.

> [!NOTE]
> EL proxy de OPC (componente de servidor) y el proxy de OPC (componente de cliente) llevan a cabo los pasos 4-10 para todo el tráfico de TCP asociado a la comunicación del agente de usuario de OPC.

> [!NOTE]
> En el caso del servidor del agente de usuario de OPC y de la pila de agente de usuario de OPC de la instancia de WebApp de Factoría conectada, la comunicación con el proxy de OPC es transparente y se aplican todas las características de seguridad de autenticación y cifrado del agente de usuario de OPC.

## <a name="next-steps"></a>Pasos siguientes

Puede continuar su introducción al Conjunto de aplicaciones de IoT con la lectura de los siguientes artículos:

* [Permisos en el sitio azureiotsuite.com][lnk-permissions]
* [Implementación de una puerta de enlace en Windows o Linux para la solución preconfigurada Fábrica conectada](iot-suite-connected-factory-gateway-deployment.md)
* [Implementación de referencia de OPC Publisher](iot-suite-connected-factory-publisher.md).

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-v1-permissions.md
