---
title: "Personalización de la factoría conectada del Conjunto de aplicaciones de IoT de Azure | Microsoft Docs"
description: "Descripción de cómo personalizar el comportamiento de la solución preconfigurada de factoría conectada."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 90a6172dbd887ecda5a9f5d9082a4e136092bc10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalización de cómo muestra la solución de factoría conectada los datos de los servidores de OPC UA

## <a name="introduction"></a>Introducción

La solución de factoría conectada agrega y muestra datos de los servidores de OPC UA conectados a la solución. Puede examinar y enviar comandos a los servidores de OPC UA en la solución. Para más información acerca de OPC UA, consulte las [preguntas frecuentes sobre la fábrica conectada](iot-suite-faq-cf.md).

Entre los ejemplos de datos agregados en la solución se pueden mencionar Overall Equipment Efficiency (OEE) (Eficacia general de equipos) y Key Performance Indicators (Indicadores clave de rendimiento) (KPI) que se pueden ver en el panel en los ámbitos de factoría, línea y estación. En las capturas de pantalla siguientes se muestran los valores de OEE y KPI para la estación **Assembly**, en **Production line 1**, en la factoría **Munich**:

![Ejemplo de valores de OEE y KPI en la solución][img-oee-kpi]

La solución le permite ver información detallada de elementos de datos específicos de los servidores de OPC UA, llamados *estaciones*. En la captura de pantalla siguiente se muestran los trazados del número de artículos fabricados desde una estación específica:

![Trazados del número de artículos fabricados][img-manufactured-items]

Si hace clic en uno de los gráficos, puede explorar los datos aún más con Time Series Insights (TSI):

![Exploración de datos con Time Series Insights][img-tsi]

En este artículo se describe:

- Cómo los datos se ponen a disposición de las distintas vistas de la solución.
- Cómo puede personalizar la forma en que la solución muestra los datos.

## <a name="data-sources"></a>Orígenes de datos

La solución de factoría conectada muestra datos de los servidores de OPC UA conectados a la solución. La instalación predeterminada incluye varios servidores de OPC UA que ejecutan una simulación de factoría. Puede agregar sus propios servidores OPC UA que se [conectan a través de una puerta de enlace][lnk-connect-cf] a la solución.

Puede examinar los elementos de datos que un servidor de OPC UA conectado puede enviar a la solución en el panel:

1. Navegue hasta la vista **Select an OPC UA server** (Seleccionar un servidor de OPC UA):

    ![Navegación hasta la vista Select an OPC UA server (Seleccionar un servidor de OPC UA)][img-select-server]

1. Seleccione un servidor y haga clic en **Connect** (Conectar). Haga clic en **Proceed** (Continuar) cuando aparezca la advertencia de seguridad.

    > [!NOTE]
    > Esta advertencia solo aparece una vez para cada servidor y establece una relación de confianza entre el panel de la solución y el servidor.

1. Ahora puede examinar los elementos de datos que el servidor puede enviar a la solución. Los elementos que se envían a la solución tienen una marca de verificación verde:

    ![Elementos publicados][img-published]

1. Si es un *administrador* de la solución, puede publicar un elemento de datos para que esté disponible en la solución de factoría conectada. Como administrador, también puede cambiar el valor de los elementos de datos y llamar a métodos en el servidor de OPC UA.

## <a name="map-the-data"></a>Asignación de datos

La solución de factoría conectada asigna y agrega los elementos de datos publicados desde el servidor de OPC UA a las distintas vistas de la solución. La solución de factoría conectada se implementa en su cuenta de Azure al aprovisionar la solución. Un archivo JSON de la solución de factoría conectada de Visual Studio almacena esta información de asignación. Puede ver y modificar este archivo de configuración de JSON en la solución de Visual Studio de factoría conectada. Puede volver a implementar la solución después de realizar un cambio.

Puede usar el archivo de configuración para:

- Editar las líneas de producción, las estaciones y las factorías simuladas existentes.
- Asignar datos de los servidores de OPC UA reales que se conectan a la solución.

Para clonar una copia de la solución de Visual Studio de factoría conectada, use el siguiente comando de git:

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

El archivo **ContosoTopologyDescription.json** define la asignación entre los elementos de datos del servidor de OPC UA y las vistas del panel de la solución de factoría conectada. Puede encontrar este archivo de configuración en la carpeta **Contoso\Topology** del proyecto **WebApp** de la solución de Visual Studio.

El contenido del archivo JSON se organiza como una jerarquía de nodos de estación, línea de producción y factoría. Esta jerarquía define la jerarquía de navegación en el panel de factoría conectada. Los valores de cada nodo de la jerarquía determinan la información mostrada en el panel. Por ejemplo, el archivo JSON contiene los siguientes valores para la factoría de Múnich:

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

El nombre, la descripción y la ubicación aparecen en esta vista en el panel:

![Datos de Múnich en el panel][img-munich]

Cada factoría, línea de producción y estación tiene una propiedad de imagen. Puede encontrar estos archivos JPEG en la carpeta **Content\img** del proyecto **WebApp**. Estos archivos de imagen se muestran en el panel de factoría conectada.

Cada estación incluye varias propiedades detalladas que definen la asignación de los elementos de datos de OPC UA. Estas propiedades se describen en las secciones siguientes:

### <a name="opcuri"></a>OpcUri

El valor de **OpcUri** es el URI de la aplicación de OPC UA que identifica de forma única el servidor de OPC UA. Por ejemplo, el valor **OpcUri** de la estación de ensamblado de la línea de producción 1 de Múnich tiene el siguiente aspecto: **urn:scada2194:ua:munich:productionline0:assemblystation**.

Puede ver los URI de los servidores de OPC UA conectados en el panel de la solución:

![Vista de URI del servidor de OPC UA][img-server-uris]

### <a name="simulation"></a>Simulation

La información del nodo **Simulation** es específica de la simulación de OPC UA que se ejecuta en los servidores de OPC UA aprovisionados de forma predeterminada. No se utiliza para un servidor de OPC UA real.

### <a name="kpi1-and-kpi2"></a>Kpi1 y Kpi2

Estos nodos describen cómo los datos de la estación contribuyen a los dos valores de KPI en el panel. En una implementación predeterminada, estos valores de KPI son unidades por hora y kWh por hora. La solución calcula los valores de KPI en el ámbito de una estación y los agrega a los ámbitos de línea de producción y de fábrica.

Cada KPI tiene un valor mínimo, máximo y de destino. Cada valor de KPI también puede definir acciones de alerta que se van a realizar para la solución de factoría conectada. El fragmento de código siguiente muestra las definiciones de KPI para la estación de montaje de la línea de producción 1 de Múnich:

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

En la siguiente captura de pantalla se muestran los datos de KPI en el panel.

![Información de KPI en el panel][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

Los nodos **OpcNodes** identifican los elementos de datos publicados desde el servidor de OPC UA y especifican cómo procesar esos datos.

El valor de **NodeId** identifica el identificador de nodo de OPC UA específico del servidor de OPC UA. El primer nodo de la estación de montaje para la línea de producción 1 de Múnich tiene un valor de **ns=2;i=385**. A valor de **NodeId** especifica el elemento de datos que se va a leer del servidor de OPC UA y **SymbolicName** proporciona un nombre descriptivo que se usará en el panel para esos datos.

En la tabla siguiente se resumen otros valores asociados a cada nodo:

| Valor | Descripción |
| ----- | ----------- |
| Relevancia  | Los valores de KPI y OEE con los que contribuyen estos datos. |
| OpCode     | Cómo se agregan los datos. |
| Unidades      | Las unidades que se van a usar en el panel.  |
| Visible    | Si se muestra este valor o no en el panel. Algunos valores se utilizan en los cálculos pero no se muestran.  |
| Máxima    | El valor máximo que se desencadena una alerta en el panel. |
| MaximumAlertActions | Una acción que se realizará en respuesta a una alerta. Por ejemplo, enviar un comando a una estación. |
| ConstValue | Un valor constante que se usa en un cálculo. |

## <a name="deploy-the-changes"></a>Implementación de los cambios

Cuando haya terminado de realizar cambios en el archivo **ContosoTopologyDescription.json**, debe volver a implementar la solución de factoría conectada en su cuenta de Azure.

El repositorio **-azure-iot-connected-factory** incluye un script de PowerShell **build.ps1** que puede usar para volver a compilar e implementar la solución.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la solución preconfigurada de factoría conectada, lea los siguientes artículos:

* [Tutorial de la solución preconfigurada de fábrica conectada][lnk-rm-walkthrough]
* [Implementación de una puerta de enlace para una solución de fábrica conectada][lnk-connect-cf]
* [Permisos en el sitio azureiotsuite.com][lnk-permissions]
* [Preguntas más frecuentes sobre fábrica conectada](iot-suite-faq-cf.md)
* [Preguntas más frecuentes][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md