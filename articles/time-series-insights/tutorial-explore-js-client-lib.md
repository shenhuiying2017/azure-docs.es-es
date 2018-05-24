---
title: Exploración de la biblioteca de cliente JavaScript de Time Series Insights
description: Obtenga información acerca de la biblioteca de cliente JavaScript de Time Series Insights y el modelo de programación relacionado.
documentationcenter: ''
services: time-series-insights
author: BryanLa
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: time-series-insights
ms.workload: na
ms.tgt_pltfrm: ''
ms.devlang: na
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: bryanla
ms.openlocfilehash: 3fbd4f54fb511ae737abf28ae7b1b50750ab5d69
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210484"
---
# <a name="tutorial-explore-the-time-series-insights-javascript-client-library"></a>Tutorial: Exploración de la biblioteca de cliente JavaScript de Time Series Insights

Para ayudar a los desarrolladores a consultar y visualizar datos almacenados en Time Series Insights (TSI), hemos desarrollado una biblioteca de controles basados en D3 de JavaScript que facilita esta tarea. Con una aplicación web de ejemplo, este tutorial le guiará a través de una exploración de la biblioteca de cliente JavaScript de TSI y el modelo de programación relacionado.

Los temas tratados proporcionan oportunidades para experimentar, obtener una descripción más detallada de cómo obtener acceso a datos de TSI y usar controles de gráficos para representar y visualizar los datos. El objetivo es proporcionar suficientes detalles y que pueda usar la biblioteca en su propia aplicación web.

En este tutorial, obtendrá información sobre lo siguiente:

> [!div class="checklist"]
> * Aplicación de ejemplo TSI
> * Biblioteca de cliente JavaScript de TSI
> * Cómo usa la aplicación de ejemplo la biblioteca para visualizar datos de TSI

## <a name="prerequisites"></a>requisitos previos

Este tutorial usa la característica "Herramientas de desarrollo" (también conocida como DevTools o F12), incluida en los exploradores web más modernos como [Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/), etc. Si todavía no está familiarizado, puede explorar esta característica en el explorador antes de continuar.

## <a name="the-time-series-insights-sample-application"></a>Aplicación Time Series Insights de ejemplo

En este tutorial, se usa la aplicación Time Series Insights de ejemplo para explorar el código fuente subyacente de la aplicación, incluido el uso de la biblioteca de cliente JavaScript de TSI. Se trata de una aplicación web de una sola página (SPA), que muestra el uso de la biblioteca para consultar y visualizar datos desde un entorno de TSI de ejemplo.

1. Navegue a la [Aplicación Time Series Insights de ejemplo](https://insights.timeseries.azure.com/clientsample). Verá una página similar a la siguiente, que le pedirá que inicie sesión: ![solicitud de inicio de sesión de cliente de TSI de ejemplo](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Haga clic en el botón "Iniciar sesión" y escriba o seleccione las credenciales. Puede usar una cuenta de empresa u organización (Azure Active Directory) o una cuenta personal (cuenta de Microsoft o MSA).

   ![Solicitud de credenciales de ejemplo de cliente de TSI](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Después de iniciar sesión correctamente, verá una página similar a la siguiente, que contiene varios estilos de gráficos de ejemplo, rellenada con datos de TSI. Observe también su cuenta de usuario y el vínculo para "cerrar sesión" de la esquina superior derecha: ![Página principal de ejemplo de cliente de TSI después de iniciar sesión](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Estructura y origen de la página

Primero veamos el código fuente HTML y JavaScript subyacente de la página que se representa en el explorador. No vamos a recorrer todos los elementos, pero verá las secciones principales para hacerse una idea de cómo funciona la página:

1. Abra "Herramientas de desarrollo" en el explorador e inspeccione los elementos HTML que componen la página actual, también conocidos como árbol de DOM o HTML.

2. Expanda los elementos `<head>` y `<body>`, y tenga en cuenta las siguientes secciones:
   - En `<head>`, encontrará elementos que extraen archivos adicionales que contribuyen al funcionamiento de la página:
     - Un `<script>` elemento para hacer referencia a la biblioteca de autenticación de Azure Active Directory (adal.min.js), también denominada ADAL. Se trata de una biblioteca de JavaScript que proporciona autenticación de OAuth 2.0 (inicio de sesión) y adquisición de tokens para acceder a las API.
     - Elementos `<link>` de hojas de estilos (sampleStyles.css, tsiclient.css), también conocidos como CSS, que se usan para controlar los detalles de estilo de página visuales, como colores, fuentes, espaciado, etc.
     - Un `<script>` elemento para hacer referencia a la biblioteca de cliente de TSI (tsiclient.js): una biblioteca JavaScript que se usa en la página para llamar a las API del servicio TSI y representar los controles de gráficos en la página.

     >[!NOTE]
     > El código fuente de la biblioteca JavaScript ADAL está disponible en el [repositorio azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > El código fuente de la biblioteca JavaScript de cliente de TSI está disponible en el [repositorio tsiclient](https://github.com/Microsoft/tsiclient).

   - En `<body>`, encontrará elementos `<div>`, que actúan como contenedores para definir el diseño de los elementos en la página y otro elemento `<script>`:
     - el primer elemento `<div>` especifica el cuadro de diálogo "Iniciar sesión" (`id="loginModal"`).
     - el segundo `<div>` actúa como un elemento primario para:
       - un encabezado `<div>`, que se usa para mensajes de estado e información de inicio de sesión en la parte superior de la página (`class="header"`).
       - un elemento `<div>` para el resto de los elementos del cuerpo de la página, incluidos todos los gráficos (`class="chartsWrapper"`).
       - una sección `<script>`, que contiene todo el código JavaScript usado para controlar la página.

   [![Ejemplo de cliente de TSI con DevTools](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Expanda el elemento `<div class="chartsWrapper">` y encontrará más elementos `<div>` secundarios, que se usan para colocar cada uno de los ejemplos de control de gráficos. Observe que hay varios pares de elementos `<div>`, uno para cada ejemplo de gráfico:
   - El primero (`class="rowOfCardsTitle"`) contiene un título descriptivo para resumir lo que muestran los gráficos. Por ejemplo: "Static Line Charts With Full-Size Legends" (Gráficos de líneas estáticos con leyendas de tamaño completo)
   - El segundo (`class="rowOfCards"`) es un elemento primario, que contiene elementos `<div>` secundarios adicionales que colocan los controles de gráficos reales en una fila.

  ![Visualización de elementos "div" del cuerpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Ahora, expanda el elemento `<script type="text/javascript">`, directamente debajo del elemento `<div class="chartsWrapper">`. Puede ver el principio de la sección de JavaScript de la página, usada para controlar toda la lógica de la página para aspectos como la autenticación, la llamada a las API del servicio de TSI, la representación de los controles de gráficos y mucho más:

  ![Visualización del script del cuerpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-client-javascript-library-concepts"></a>Conceptos de la biblioteca JavaScript de cliente de TSI

Aunque no la revisaremos en profundidad, la biblioteca de cliente de TSI (tsclient.js) proporciona fundamentalmente una abstracción de dos categorías importantes:

- **Métodos de contenedor para llamar a las API de consulta TSI**: API de REST que permiten consultar datos de TSI mediante expresiones de agregado y se organizan en el espacio de nombres `TsiClient.Server` de la biblioteca.
- **Métodos para crear y rellenar varios tipos de controles de gráficos**: se usan para representar los datos agregados de TSI en una página web y se organizan en el espacio de nombres `TsiClient.UX` de la biblioteca.

Los siguientes conceptos son universales y se aplican a las API de biblioteca de cliente de TSI en general.

### <a name="authentication"></a>Autenticación

Como hemos mencionado anteriormente, este ejemplo es una aplicación de una página que usa la compatibilidad con OAuth 2.0 en ADAL para la autenticación de usuario. Aquí se presentan algunos puntos de interés de esta sección del script:

1. El uso de ADAL para la autenticación requiere que la aplicación cliente se registre en el registro de aplicaciones de Azure Active Directory (Azure AD). Como un SPA, esta aplicación está registrada para usar el flujo de concesión de autorizaciones "implícito" de OAuth 2.0. En consecuencia, la aplicación especifica algunas de las propiedades de registro en el runtime, como el GUID de Id. de cliente (`clientId`) y el URI de redireccionamiento (`postLogoutRedirectUri`), para participar en el flujo.

2. Más adelante, la aplicación solicita un "token de acceso " de Azure AD. El token de acceso se emite para un conjunto finito de permisos, para un identificador de servicio o API específico (https://api.timeseries.azure.com), también conocido como el token "público". Los permisos de token se emiten en nombre del usuario con la sesión iniciada. El identificador del servicio o la API es otra propiedad que se incluye en el registro de Azure AD de la aplicación. Una vez ADAL devuelve el token de acceso a la aplicación, se pasa como un "token de portador" al acceder a las API de servicio de TSI.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=145-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>Identificación del control

Como hemos comentado anteriormente, los elementos `<div>` del `<body>` proporcionan el diseño de todos los controles de gráficos que se muestran en la página. Todos ellos especifican las propiedades de posición y atributos visuales del control de gráfico, incluida una propiedad `id`. La propiedad `id` proporciona un identificador único, que se usa en el código JavaScript para la identificación y el enlace a cada control con fines de representación y actualización.

### <a name="aggregate-expressions"></a>Expresiones de agregado

Las API de la biblioteca de cliente de TSI usan de manera intensiva las expresiones de agregado. Una expresión de agregado proporciona la capacidad de crear uno o más "términos de búsqueda". Las API son similares a la manera en que el [explorador de Time Series Insights](https://insights.timeseries.azure.com/demo) usa el intervalo de búsqueda, el predicado WHERE, las medidas y el valor de división por. La mayoría de las API de biblioteca toma una matriz de expresiones de agregado, que el servicio usa para crear una consulta de datos de TSI.

### <a name="call-pattern"></a>Patrón de llamada

Las acciones de rellenar y representar controles de gráfico siguen un patrón general. Este patrón se encuentra en todo el código JavaScript de la página, que crea instancias de los controles de la aplicación de ejemplo de TSI y los carga:

1. Declare una matriz que contenga una o varias expresiones de agregado de TSI.

   ```javascript
   var aes =  [];
   ```

2. Genere objetos de expresión de agregado de 1 a n, y agréguelos a la matriz de expresiones de agregado.

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```
   **parámetros aggregateExpression**

   | . | DESCRIPCIÓN | Ejemplo |
   | --------- | ----------- | ------- |
   | predicateObject | Expresión de filtrado de datos. |`{predicateString: "Factory = 'Factory3'"}` |
   | measureObject   | Nombre de propiedad de la medida que se usa. | `{property: 'Temperature', type: "Double"}` |
   | measureTypes    | Agregaciones deseadas de la propiedad de medida. | `['avg', 'min']` |
   | searchSpan      | Duración y tamaño del intervalo de la expresión de agregado. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | splitByObject   | Propiedad de cadena por la que quiere realizar la división (opcional: puede ser null). | `{property: 'Station', type: 'String'}` |
   | color           | Color de los objetos que quiere representar. | `'pink'` |
   | alias           | Nombre descriptivo para la expresión de agregado. | `'Factory3Temperature'` |
   | contextMenuActions | Matriz de acciones que se enlazará a los objetos de la serie de tiempo en una visualización (opcional). | Consulte los [Menús contextuales emergentes en la sección Características avanzadas.](#popup-context-menus) |

3. Llame a una consulta de TSI mediante las API de `TsiClient.Server` para solicitar los datos de agregado.

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```
   **parámetros getAggregates**

   | . | DESCRIPCIÓN | Ejemplo |
   | --------- | ----------- | ------- |
   | token     | Token de acceso para la API de TSI | `authContext.getTsiToken()` Consulte el apartado [Autenticación](#authentication). |
   | envFQDN     | Nombre de dominio completo del entorno de TSI | De Azure Portal, por ejemplo `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` |
   | aeTsxArray | Matriz de expresiones de consulta de TSI | Use la variable `aes` tal y como se ha descrito anteriormente: `aes.map(function(ae){return ae.toTsx()}` |

4. Transforme el resultado comprimido que ha devuelto la consulta de TSI, en formato JSON para la visualización.

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Cree un control de gráfico mediante las API de `TsiClient.UX` y enlácelo a uno de los elemento `<div>` en la página.

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Rellenar el control de gráfico con los objetos de datos JSON transformados y represéntelo en la página.

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Representación de controles

La biblioteca expone actualmente ocho controles de análisis únicos. Incluyen un gráfico de líneas, un gráfico circular, un gráfico de barras, un mapa térmico, controles de la jerarquía, una cuadrícula accesible, escalas de tiempo de eventos discretos y escalas de tiempo de transición de estado.

### <a name="line-bar-pie-chart-examples"></a>Ejemplos de gráficos de líneas, de barras y circulares

Primero vamos a examinar el código subyacente de algunos controles de gráficos estándar que se muestran en la aplicación, así como el modelo y los patrones de programación para su creación. Específicamente, examinará la sección de HTML bajo el comentario `// Example 3/4/5`, que representa los controles con valores de id. `chart3`, `chart4` y `chart5`.

Recuerde del paso número 3 de la [sección de origen y estructura de la página](#page-source-and-structure), que los controles de gráfico se organizan en filas en la página, cada una de las cuales tiene una fila de título descriptivo. En este ejemplo, los tres gráficos que se rellenan están bajo el elemento `<div>` de título "Varios tipos de gráficos de los mismos datos", enlazado a los tres elementos `<div>` que se encuentran debajo:

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

La siguiente sección del código de JavaScript usa el patrón descrito anteriormente para compilar expresiones de agregado de TSI, usarlos para consultar los datos de TSI y representar los tres gráficos. Observe los tres tipos del espacio de nombres `tsiClient.ux` (`LineChart`, `BarChart` y `PieChart`) usados para crear y representar los gráficos respectivos. Observe también que los tres gráficos pueden usar los mismos datos de expresión de agregado, `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Los tres gráficos aparecen de la siguiente manera cuando se representan:

[![Varios tipos de gráficos de los mismos datos](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Características avanzadas

La biblioteca también expone algunas características avanzadas opcionales que puede aprovechar.

### <a name="states-and-events"></a>Estados y eventos

Un ejemplo de la funcionalidad avanzada proporcionada es la capacidad de agregar transiciones de estado y eventos discretos a los gráficos. Esta característica es útil para resaltar incidentes, alertas y conmutadores de estado como activado/desactivado.

Aquí puede ver el código subyacente de la sección de HTML bajo el comentario `// Example 10`. El código representa un control de línea bajo el título "Gráficos de líneas con varios tipos de series", que lo enlaza al elemento `<div>` con un valor de identificador `chart10`:

1. Primero, se define una estructura denominada `events4`, que contendrá los elementos de cambio de estado de los que se debe realizar un seguimiento. Contiene:
   - Una clave de cadena denominada `"Component States"`
   - Una matriz de objetos de valor que representan los estados, cada uno de los cuales incluye:
     - Una clave de cadena que contiene una marca de tiempo ISO de JavaScript.
     - Una matriz que contiene las características del estado.
       - Un color.
       - Una descripción.

2. La estructura `events5` se define para `"Incidents"`, que contiene una matriz de los elementos de eventos de los que se va a realizar un seguimiento. La estructura de la matriz tiene la misma forma que la que se describe para `events4`.

3. Por último, se representa el gráfico de líneas. Se pasan las dos estructuras con los parámetros de opción de gráficos: `events:` y `states:`. Observe los demás parámetros de opción, para especificar `tooltip:`, `theme:` o `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visualmente, los marcadores de rombo o los elementos emergentes se utilizan para indicar incidentes, mientras que las barras de color o los elementos emergentes en la escala de tiempo indican cambios de estado:

[![Gráficos de líneas con varios tipos de series](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="popup-context-menus"></a>Menús contextuales emergentes

Otro ejemplo de funcionalidad avanzada son los menús contextuales personalizados (haga clic en los menús emergentes), que son útiles para habilitar acciones y pasos lógicos a seguir dentro del ámbito de la aplicación.

Aquí vemos el código subyacente de HTML bajo `// Example 13/14/15`. Inicialmente, este código representa un gráfico de líneas bajo el título "Gráfico de líneas con menú contextual para crear gráficos circulares o de barras", enlazado al elemento `<div>` con el valor de identificador `chart13`. A través de los menús contextuales, el gráfico de líneas proporciona la capacidad de crear de forma dinámica un gráfico circular y un gráfico de barras, enlazados a los elementos `<div>` con identificadores `chart14` y `chart15`. Además, ambos gráficos circular y de barras también utilizan los menús contextuales para habilitar sus propias características: la capacidad de copiar datos del gráfico circular al gráfico de barras y de imprimir los datos del gráfico de barras en la ventana de consola del explorador, respectivamente.

1. Primero se define una serie de acciones personalizadas. Cada una contiene una matriz con uno o más elementos, donde cada elemento define un único elemento del menú contextual:
   - `barChartActions`: define el menú contextual del gráfico circular, que contiene un elemento para definir un elemento único.
     - `name`: texto utilizado para el elemento de menú, "Parámetros de impresión en la consola".
     - `action`: acción asociada con el elemento de menú, que siempre es una función anónima que toma tres argumentos basados en la expresión de agregado que se usa para crear el gráfico. En este caso, se escriben en la ventana de la consola del explorador:
       - `ae`: matriz de la expresión de agregado
       - `splitBy`: valor de splitBy
       - `timestamp`: marca de tiempo
   - `pieChartActions`: define el menú contextual del gráfico de barras, que contiene un elemento para definir un elemento único. La forma y el esquema coinciden con los del elemento `barChartActions` anterior, pero tenga en cuenta que la función `action` es extremadamente diferente, ya que crea instancias del gráfico de barras y lo representa. Tenga en cuenta también que usa el argumento `ae` para especificar la matriz de la expresión de agregado, pasada en el runtime al mostrarse el elemento de menú emergente. La función también establece la propiedad `ae.contextMenu` con el menú contextual `barChartActions`.
   - `contextMenuActions`: define el menú contextual del gráfico de líneas, que contiene tres elementos para definir tres elementos de menú. La forma y el esquema de cada elemento coinciden con los anteriores. Del mismo modo que `barChartActions`, el primer elemento escribe los tres argumentos de función en la ventana de la consola del explorador. Similar a `pieChartActions`, los segundos dos elementos, respectivamente, crean una instancia de los gráficos circular y de barras, y los representan. Los segundos dos elementos también establecen sus propiedades `ae.contextMenu` con los menús contextuales `pieChartActions` y `barChartActions`, respectivamente.

2. A continuación, se insertan dos expresiones de agregado en la matriz de la expresión de agregado `aes` y se especifica la matriz `contextMenuActions` para cada una de ellas. Estas se usan con el control de gráfico de líneas.

3. Por último, solo el gráfico de líneas se representa inicialmente, desde el cual se pueden representar el gráfico circular y el gráfico de barras en el runtime.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

La captura de pantalla muestra los gráficos, con sus respectivos menús contextuales emergentes. Los gráficos circulares y de barras se crearon de forma dinámica, con las opciones del menú contextual del gráfico de líneas:

[![Gráfico de líneas con menú contextual para crear gráficos circulares o de barras](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pinceles

Los pinceles pueden utilizarse para definir el ámbito de un intervalo de tiempo para definir acciones como las de zoom y exploración.

El código utilizado para mostrar pinceles también se muestra en el ejemplo "Gráfico de líneas con menú contextual para crear gráficos circulares o de barras", donde se explican los [menús contextuales emergentes](#popup-context-menus-section).

1. Las acciones de pincel son muy similares a las de un menú contextual y definen una serie de acciones personalizadas para el pincel. Cada una contiene una matriz con uno o más elementos, donde cada elemento define un único elemento del menú contextual:
   - `name`: texto utilizado para el elemento de menú, "Parámetros de impresión en la consola".
   - `action`: acción asociada con el elemento de menú, que siempre es una función anónima que toma dos argumentos. En este caso, se escriben en la ventana de la consola del explorador:
      - `fromTime`: marca de tiempo "desde" de la selección de pincel
      - `toTime`: marca de tiempo "hasta" de la selección de pincel

2. Las acciones de pincel se agregan como otra propiedad de la opción de gráfico. Observe la propiedad `brushContextMenuActions: brushActions` que se pasa a la llamada `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Gráfico de líneas con menú contextual para crear gráficos circulares o de barras mediante pinceles](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Iniciar sesión en la aplicación de ejemplo de TSI y su origen, y explorarlos.
> * Usar las API de la biblioteca de cliente JavaScript de TSI.
> * Usar JavaScript para crear y rellenar los controles de gráfico con los datos de TSI.

Tal y como hemos explicado, la aplicación de ejemplo de TSI usa un conjunto de datos de demostración. Para obtener más información sobre cómo crear un entorno de TSI y un conjunto de datos propios, pase al siguiente artículo:

> [!div class="nextstepaction"]
> [Planee el entorno de Azure Time Series Insights](time-series-insights-environment-planning.md)


