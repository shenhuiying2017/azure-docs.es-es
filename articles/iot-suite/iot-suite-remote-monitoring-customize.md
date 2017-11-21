---
title: "Personalización de la solución de supervisión remota: Azure | Microsoft Docs"
description: "En este artículo se proporciona información sobre cómo puede acceder al código fuente de la solución preconfigurada de supervisión remota."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 886a4412ac348869563a03d697f4363cb3dea8f9
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>Personalización de la solución preconfigurada de supervisión remota

En este artículo se proporciona información sobre cómo puede acceder al código fuente y personalizar la solución preconfigurada de supervisión remota. El artículo describe:

* Los repositorios de GitHub que contienen el código fuente y los recursos para los microservicios que conforman la solución preconfigurada.
* Escenarios comunes de personalización, como agregar un tipo de dispositivo nuevo.

## <a name="project-overview"></a>Información general del proyecto

### <a name="implementations"></a>Implementaciones

La solución de supervisión remota tiene implementaciones .NET y Java. Ambas implementaciones proporcionan una funcionalidad similar y se basan en los mismos servicios de Azure subyacentes. Aquí puede encontrar los repositorios de GitHub de nivel superior:

* [Solución .NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Solución Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>Microservicios

Si está interesado en una característica específica de la solución, puede acceder a los repositorios de GitHub para cada microservicio individual. Cada microservicio implementa una parte distinta de la funcionalidad de la solución. Para más información sobre la arquitectura global, consulte [Arquitectura de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-sample-walkthrough.md).

En esta tabla se resume la disponibilidad actual de cada microservicio para cada lenguaje:

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| Microservicio      | Descripción | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| UI Web            | Aplicación web para la solución de supervisión remota. Implementa la UI mediante el marco React.js. | [N/D(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [N/D(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| Administrador de IoT Hub   | Controla la comunicación con IoT Hub.        | [Disponible](https://github.com/Azure/iothub-manager-java) | [Disponible](https://github.com/Azure/iothub-manager-dotnet)   |
| Autenticación    |  Administra la integración de Azure Active Directory.  | No disponible todavía | [Disponible](https://github.com/Azure/pcs-auth-dotnet)   |
| Simulación de dispositivos | Administra un grupo de dispositivos simulados. | No disponible todavía | [Disponible](https://github.com/Azure/device-simulation-dotnet)   |
| Telemetría         | Pone la telemetría de dispositivo a disposición de la UI. | [Disponible](https://github.com/Azure/device-telemetry-java) | [Disponible](https://github.com/Azure/device-telemetry-dotnet)   |
| Agente de telemetría   | Analiza el flujo de telemetría, almacena mensajes desde Azure IoT Hub y genera alertas de acuerdo con las reglas definidas.  | [Disponible](https://github.com/Azure/telemetry-agent-java) | [Disponible](https://github.com/Azure/telemetry-agent-dotnet)   |
| Configuración de UI         | Administra los datos de configuración de la UI. | [Disponible](https://github.com/azure/pcs-ui-config-java) | [Disponible](https://github.com/azure/pcs-ui-config-dotnet)   |
| Adaptador de almacenamiento   |  Administra las interacciones con el servicio de almacenamiento.   | [Disponible](https://github.com/azure/pcs-storage-adapter-java) | [Disponible](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| Proxy inverso     | Expone los recursos privados de manera administrada mediante un punto de conexión único. | No disponible todavía | [Disponible](https://github.com/Azure/reverse-proxy-dotnet)   |

La solución Java actualmente usa los microservicios del proxy inverso, la simulación y la autenticación .NET. Estos microservicios se reemplazarán por versiones de Java en cuanto estén disponibles.

## <a name="presentation-and-visualization"></a>Presentación y visualización

En las secciones siguientes se describen las opciones para personalizar la capa de presentación y visualizaciones en la solución de supervisión remota:

### <a name="change-the-logo-in-the-ui"></a>Cambio de logotipo en la UI

La implementación predeterminada usa el logotipo y el nombre de la empresa Contoso en la UI. Para cambiar estos elementos de la UI y mostrar el logotipo y el nombre de su empresa:

1. Use el comando siguiente para clonar el repositorio de UI Web:

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. Para cambiar el nombre de la empresa, abra el archivo `src/common/lang.js` en un editor de texto.

1. Ubique la siguiente línea en el archivo:

    ```js
    CONTOSO: 'Contoso',
    ```

1. Reemplace `Contoso` por el nombre de la empresa. Por ejemplo:

    ```js
    CONTOSO: 'YourCo',
    ```

1. Guarde el archivo .

1. Para actualizar el logotipo, agregue un archivo SVG nuevo en la carpeta `assets/icons`. El logotipo existente es el archivo `assets/icons/Contoso.svg`.

1. Abra el archivo `src/components/layout/leftNav/leftNav.js` en un editor de texto.

1. Ubique la siguiente línea en el archivo:

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. Reemplace `Contoso.svg` por el nombre del archivo del logotipo. Por ejemplo:

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. Ubique la siguiente línea en el archivo:

    ```js
    alt="ContosoIcon"
    ```

1. Reemplace `ContosoIcon` por el texto `alt`. Por ejemplo:

    ```js
    alt="YourCoIcon"
    ```

1. Guarde el archivo .

1. Para probar los cambios, puede ejecutar el elemento `webui` actualizado en la máquina local. Para información sobre cómo compilar y ejecutar localmente la solución `webui`, consulte la sección de [compilación, ejecución y prueba locales](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally) en el archivo Léame del repositorio `webui` de GitHub.

1. Para implementar los cambios, consulte la [Guía de referencia para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide).

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>Personalización del mapa

Consulte la página de [personalización del mapa](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) en GitHub para detalles de los componentes del mapa en la solución.

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>Otras opciones de personalización

Para modificar aún más la capa de presentación y visualizaciones en la solución de supervisión remota, puede editar el código. Los repositorios GitHub pertinentes son:

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [WebUI de supervisión remota PCS de Azure](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="device-connectivity-and-streaming"></a>Streaming y conectividad de dispositivos

En las secciones siguientes se describen las opciones para personalizar la capa de streaming y conectividad de dispositivos en la solución de supervisión remota. Los [modelos de dispositivo](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models) describen los tipos de dispositivo y la telemetría de la solución. Puede usar modelos de dispositivo para los dispositivos simulados y los físicos.

Para un ejemplo de la implementación de un dispositivo físico, consulte [Conectar el dispositivo a la solución preconfigurada de supervisión remota](iot-suite-connecting-devices-node.md).

Si usa un _dispositivo físico_, debe proporcionar la aplicación cliente con un modelo de dispositivo que contiene la especificación de la telemetría y los metadatos del dispositivo.

En las secciones siguientes se analiza el uso de los modelos de dispositivo con dispositivos simulados:

### <a name="add-a-telemetry-type"></a>Adición de un tipo de datos de telemetría

Los tipos de dispositivo en la solución de demostración de Contoso especifican la telemetría que envía cada tipo de dispositivo. Para especificar los tipos de telemetría adicionales, un dispositivo puede enviar las definiciones de telemetría como metadatos a la solución. Si usa este formato, el panel consume la telemetría de dispositivo y los métodos disponibles de manera dinámica y no es necesario modificar la UI. De manera alternativa, puede modificar la definición de tipo de dispositivo en la solución.

Para información sobre cómo agregar la telemetría personalizada en el microservicio del _simulador de dispositivos_, consulte [Prueba de la solución con dispositivos simulados](iot-suite-remote-monitoring-test.md).

### <a name="add-a-device-type"></a>Incorporación de un tipo de dispositivo

La solución de demostración de Contoso define algunos tipos de dispositivo de ejemplo. La solución permite definir los tipos de dispositivo personalizados para cumplir con los requisitos específicos de la aplicación. Por ejemplo, es posible que la empresa use una puerta de enlace industrial como el dispositivo primario conectado a la solución.

Para crear una representación precisa del dispositivo, debe modificar la aplicación que se ejecuta en el dispositivo para cumplir con los requisitos del dispositivo.

Para información sobre cómo agregar un tipo de dispositivo nuevo en el microservicio del _simulador de dispositivos_, consulte [Prueba de la solución con dispositivos simulados](iot-suite-remote-monitoring-test.md).

### <a name="define-custom-methods-for-simulated-devices"></a>Definición de los métodos personalizados para los dispositivos simulados

Para información sobre cómo definir los métodos personalizados para los dispositivos simulados en la solución de supervisión remota, consulte los [modelos de dispositivos](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models) en el repositorio GitHub.

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>Uso de un dispositivo físico

Para implementar métodos y trabajos en los dispositivos físicos, consulte los artículos siguientes sobre IoT Hub:

* [Conocimiento e invocación de los métodos directos de IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).
* [Programación de trabajos en varios dispositivos](../iot-hub/iot-hub-devguide-jobs.md).

### <a name="other-customization-options"></a>Otras opciones de personalización

Para modificar aún más la capa de streaming y conectividad de dispositivo en la solución de supervisión remota, puede editar el código. Los repositorios GitHub pertinentes son:

* [Telemetría de dispositivo (.NET)](https://github.com/Azure/device-telemetry-dotnet)
* [Telemetría de dispositivo (Java)](https://github.com/Azure/device-telemetry-java)
* [Agente de dispositivo (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [Agente de dispositivo (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="data-processing-and-analytics"></a>Procesamiento de datos y análisis

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

Para modificar la capa de análisis y procesamiento de datos en la solución de supervisión remota, puede editar el código. Los repositorios GitHub pertinentes son:

* [Agente de dispositivo (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [Agente de dispositivo (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="infrastructure"></a>Infraestructura

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

Para modificar la infraestructura en la solución de supervisión remota, puede editar el código. Los repositorios GitHub pertinentes son:

* [Administrador de IoTHub (.NET)](https://github.com/Azure/iothub-manager-dotnet)
* [Administrador de IoTHub (Java)](https://github.com/Azure/iothub-manager-java)
* [Adaptador de almacenamiento (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet)
* [Adaptador de almacenamiento (Java)](https://github.com/Azure/pcs-storage-adapter-java)

## <a name="next-steps"></a>Pasos siguientes

En este artículo, aprendió sobre los recursos que tiene a su disposición para ayudarlo a personalizar la solución preconfigurada.

Para información más conceptual sobre la solución preconfigurada de supervisión remota, consulte [Arquitectura de supervisión remota](iot-suite-remote-monitoring-sample-walkthrough.md)

Para más información sobre cómo personalizar la solución de supervisión remota, consulte:

* [Guía de referencia para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guía de solución de problemas para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->