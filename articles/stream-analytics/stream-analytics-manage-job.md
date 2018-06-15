---
title: 'Tutorial: Creación y administración de un trabajo de Stream Analytics mediante Azure Portal | Microsoft Docs'
description: En este tutorial se proporciona una ilustración completa de cómo usar Azure Stream Analytics para analizar las llamadas fraudulentas en una secuencia de llamadas de teléfono.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
manager: kfile
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.openlocfilehash: 524b15747a275c76fec6c529e4f00d0da1b41420
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "32778196"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>Creación de un trabajo de Stream Analytics para analizar los datos de llamadas de teléfono y visualizar los resultados en un panel de Power BI

En este tutorial se muestra cómo usar Azure Stream Analytics para analizar una llamada de teléfono de ejemplo generada mediante una aplicación cliente. Los datos de llamadas de teléfono generados por la aplicación cliente contienen algunas llamadas fraudulentas y, para filtrar este tipo de llamadas, definiremos un trabajo de Stream Analytics.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Generar datos de ejemplo de llamadas de teléfono y enviarlos a Azure Event Hubs  
> * Creación de un trabajo de Stream Analytics   
> * Configurar la entrada y la salida del trabajo  
> * Definir una consulta para filtrar las llamadas fraudulentas  
> * Probar e iniciar el trabajo  
> * Visualizar los resultados en Power BI 

## <a name="prerequisites"></a>requisitos previos

Antes de empezar, asegúrese de que dispone de lo siguiente:

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/).  
* Inicie sesión en [Azure Portal](https://portal.azure.com/).  
* Descargue la aplicación de generación de eventos de llamada telefónica [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) del Centro de descarga de Microsoft; o, también puede obtener el código fuente de [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).  

## <a name="create-an-azure-event-hub"></a>Crear un centro de eventos de Azure 

Para que Stream Analytics pueda analizar el flujo de datos de las llamadas fraudulentas, es necesario enviar los datos a Azure. En este tutorial, enviará los datos a Azure mediante [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs). En este tutorial, se crea un centro de eventos y luego se indica a la aplicación de generación de eventos que envíe los datos de llamada a dicho centro. Ejecute los pasos siguientes para crear un centro de eventos:

1. Inicie sesión en el Portal de Azure.  
2. Seleccione **Crear un recurso** > **Internet de las cosas** > **Event Hubs**.  

   ![Búsqueda del centro de eventos](media/stream-analytics-manage-job/find-eh.png)
3. Rellene el panel **Crear espacio de nombres** con los siguientes valores:  

   |**Configuración**  |**Valor sugerido** |**Descripción**  |
   |---------|---------|---------|
   |NOMBRE     | myEventHubNS        |  Un nombre único para identificar el espacio de nombres del centro de eventos.       |
   |Subscription     |   \<Su suscripción\>      |   Seleccione una suscripción de Azure donde quiere crear el centro de eventos.      |
   |Grupos de recursos     |   MyASADemoRG      |  Seleccione **Crear nuevo** y escriba un nuevo nombre de grupo de recursos para la cuenta.       |
   |Ubicación     |   Oeste de EE. UU. 2      |    Ubicación donde se puede implementar el espacio de nombres del centro de eventos.     |

4. Use las opciones predeterminadas en el resto de la configuración y seleccione **Crear**.  

   ![Creación de un espacio de nombres del centro de eventos](media/stream-analytics-manage-job/create-ehns.png)

5. Cuando se haya terminado de implementar el espacio de nombres, vaya a **Todos los recursos**, busque "myEventHubNS" en la lista de recursos de Azure y selecciónelo para abrirlo.  
6. A continuación, seleccione **+Centro de eventos** >  y asígnele el **nombre** "MyEventHub". Puede usar otro nombre. Use las opciones predeterminadas en el resto de la configuración, seleccione **Crear** y espere a que la implementación se realice correctamente.

   ![Creación de un centro de eventos](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Concesión de acceso al centro de eventos y obtención de una cadena de conexión

Para que una aplicación pueda enviar datos a Azure Event Hubs, el centro de eventos debe tener una directiva que permita el acceso adecuado. La directiva de acceso genera una cadena de conexión que incluye la información de autorización.

1. Vaya al **centro de eventos** que creó en el paso anterior, es decir, "MyEventHub", seleccione **Directivas de acceso compartido** en el panel del centro de eventos y seleccione **+Agregar**.  
2. Establezca el nombre de la directiva en **Mypolicy** y seleccione **Administrar** y **Crear**.  

   ![Creación de una directiva de acceso compartido para el centro de eventos](media/stream-analytics-manage-job/create-ehpolicy.png)

3. Una vez que se ha implementado la directiva, selecciónela para abrirla, busque **Cadena de conexión: clave principal** y seleccione **Copiar** junto a la cadena de conexión.  
4. Pegue la cadena de conexión en un editor de texto. Necesitará esta cadena de conexión en la sección siguiente.  

   La cadena de conexión tiene el siguiente aspecto:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   Tenga en cuenta que la cadena de conexión contiene varios pares clave-valor, separados por punto y coma: Endpoint, SharedAccessKeyName, SharedAccessKey y EntityPath.  

5. Quite el par EntityPath de la cadena de conexión (no olvide quitar el punto y coma que va delante).

## <a name="start-the-event-generator-application"></a>Inicio de la aplicación de generación de eventos

Antes de iniciar la aplicación TelcoGenerator, debe configurarla para enviar datos a los centros de eventos de Azure que creó anteriormente.

1. Extraiga el contenido del archivo [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).  
2. Abra el archivo `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` en un editor de texto de su elección (como hay más de un archivo de configuración, asegúrese de abrir el correcto).  

3. Actualice el elemento <appSettings> en el archivo de configuración con los detalles siguientes:

   * Establezca el valor de la clave EventHubName en el valor de EntityPath en la cadena de conexión.  
   * Establezca el valor de la clave Microsoft.ServiceBus.ConnectionString en la cadena de conexión sin el valor de EntityPath, que es el valor obtenido en el paso 5 de la sección anterior.

4. Guarde el archivo.  
5. A continuación, abra una ventana de comandos, cambie a la carpeta donde ha descomprimido la aplicación TelcoGenerator y escriba el siguiente comando:

   ```
   telcodatagen.exe 1000 .2 2
   ```

   Este comando toma los parámetros siguientes:
   * **Número de registros de datos de llamada por hora**.  
   * **Porcentaje de probabilidad de fraude**: es decir, la frecuencia con la que la aplicación debe simular una llamada fraudulenta. El valor .2 indica que aproximadamente el 20 % de los registros de llamada tendrán un aspecto fraudulento.  
   * **Duración en horas**: el número de horas que debe ejecutarse la aplicación. También puede finalizar el proceso (Ctrl+C) en la línea de comandos para detener la aplicación en cualquier momento.

   Después de unos segundos, la aplicación comienza a mostrar los registros de llamada telefónica en la pantalla tal y como los envía al centro de eventos. Los datos de llamada de teléfono contienen los siguientes campos:

   |**Registro**  |**Definición**  |
   |---------|---------|
   |CallrecTime    |  Marca de tiempo para la hora de inicio de la llamada.       |
   |SwitchNum     |  Conmutador de teléfono que se usa para conectar la llamada. En este ejemplo, los conmutadores son cadenas que representan el país de origen (Estados Unidos, China, Reino Unido, Alemania o Australia).       |
   |CallingNum     |  Número de teléfono del autor de la llamada.       |
   |CallingIMSI     |  Identidad del suscriptor móvil internacional (IMSI). Identificador único del autor de la llamada.       |
   |CalledNum     |   Número de teléfono del destinatario de la llamada.      |
   |CalledIMSI     |  Identidad del suscriptor móvil internacional (IMSI). Identificador único del destinatario de la llamada.       |

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics 

Ahora que tiene un flujo de eventos de llamada, puede crear un trabajo de Stream Analytics que lea los datos desde el centro de eventos.

1. Para crear un trabajo de Stream Analytics, vaya a Azure Portal  

2. Haga clic en **Crear un recurso** > **Internet de las cosas** > **Trabajo de Stream Analytics**.  

3. Rellene el panel **Nuevo trabajo de Stream Analytics** con los valores siguientes:  

   |**Configuración**  |**Valor sugerido**  |**Descripción**  |
   |---------|---------|---------|
   |Nombre del trabajo     |  ASATutorial       |   Un nombre único para identificar el espacio de nombres del centro de eventos.      |
   |Subscription    |  \<Su suscripción\>   |   Seleccione una suscripción de Azure donde desea crear el trabajo.       |
   |Grupos de recursos   |   MyASADemoRG      |   Seleccione **Use existing** (Usar existente) y, después, escriba un nuevo nombre de grupo de recursos para la cuenta.      |
   |Ubicación   |    Oeste de EE. UU. 2     |      Ubicación donde se puede implementar el trabajo. Es conveniente colocar el trabajo y el centro de eventos en la misma región para obtener el mejor rendimiento y no pagar por transferir datos de una región a otra.      |
   |Entorno de hospedaje    | Nube        |     Los trabajos de Stream Analytics se pueden implementar en la nube o en dispositivos perimetrales. Si elige la nube, podrá implementar en la nube de Azure y si elige implementar en un dispositivo perimetral, podrá hacerlo en un dispositivo de IoT Edge.    |
   |Unidades de streaming     |    1       |      Las unidades de streaming representan los recursos informáticos que se necesitan para ejecutar un trabajo. De forma predeterminada, este valor se establece en 1. Para información sobre el escalado de unidades de streaming, consulte el artículo [Descripción y ajuste de las unidades de streaming](stream-analytics-streaming-unit-consumption.md).      |

   ![Creación de un trabajo](media/stream-analytics-manage-job/create-a-job.png)   

4. Use las opciones predeterminadas en el resto de la configuración, seleccione **Crear** y espere a que la implementación se realice correctamente.

## <a name="configure-job-input"></a>Configuración de la entrada del trabajo

El siguiente paso consiste en definir un origen de entrada para que el trabajo lea los datos. En este tutorial, usará como entrada el centro de eventos que creó en la sección anterior. Ejecute los pasos siguientes para configurar la entrada a su trabajo:

1. En Azure Portal, abra el panel **Todos los recursos** y busque el trabajo ASATutorial de Stream Analytics.  

2. En la sección **Topología de trabajo** del panel de trabajos de Stream Analytics, seleccione la opción **Entradas**.  

3. Seleccione **+Add stream input** (+Agregar entrada de flujo) (la entrada de referencia se refiere a datos de búsqueda estática, que no se usarán en este tutorial), **Event hub** (Centro de eventos) y rellene el panel con los siguientes valores:  

   |**Configuración**  |**Valor sugerido**  |**Descripción**  |
   |---------|---------|---------|
   |Alias de entrada     |  CallStream       |  Proporcione un nombre descriptivo para identificar la entrada. El alias de entrada debe tener una longitud de entre 3 y 63 caracteres, y solo puede incluir caracteres alfanuméricos, guiones y guiones bajos.       |
   |Subscription    |   \<Su suscripción\>      |   Seleccione la suscripción de Azure en la que creó el centro de eventos. El centro de eventos puede estar en la misma suscripción que el trabajo de Stream Analytics, o en otra diferente.       |
   |Espacio de nombres del centro de eventos    |  MyEventHubNS       |  Seleccione el espacio de nombres del centro de eventos que creó en la sección anterior. Todos los espacios de nombres del centro de eventos disponibles en su suscripción actual se muestran en la lista desplegable.       |
   |Nombre del centro de eventos    |   MyEventHub      |  Seleccione el centro de eventos que creó en la sección anterior. Todos los centros de eventos disponibles en la suscripción actual se muestran en la lista desplegable.       |
   |Nombre de la directiva del centro de eventos   |  Mypolicy       |  Seleccione la directiva de acceso compartido del centro de eventos que creó en la sección anterior. Todas las directivas de los centros de eventos disponibles en la suscripción actual se muestran en la lista desplegable.       |

   ![Configuración de la entrada](media/stream-analytics-manage-job/configure-input.png) 

4. Use las opciones predeterminadas en el resto de la configuración, seleccione **Guardar** y espere a que la implementación se realice correctamente.

## <a name="configure-job-output"></a>Configuración de la salida del trabajo 

El último paso consiste en definir un receptor de salida para el trabajo en el que se puedan escribir los datos transformados. En este tutorial, transmitirá los resultados a Power BI y visualizará la fecha. Ejecute los pasos siguientes para configurar la salida a su trabajo:

1. En Azure Portal, abra el panel **Todos los recursos** y busque el trabajo ASATutorial de Stream Analytics.  

2. En la sección **Topología de trabajo** del panel de trabajos de Stream Analytics, seleccione la opción **Salidas**.  

3. Seleccione **+Agregar** > **Power BI** y rellene el formulario con los siguientes datos (puede proporcionar un nombre descriptivo para identificar el alias de salida, el nombre del conjunto de datos y el nombre de la tabla, como se muestra en la tabla) y seleccione **Autorizar**:  

   |**Configuración**  |**Valor sugerido**  |
   |---------|---------|---------|
   |Alias de salida  |  MyPBIoutput  |
   |Nombre del conjunto de datos  |   ASAdataset  | 
   |Nombre de tabla |  ASATable  | 

   ![Configuración de la salida](media/stream-analytics-manage-job/configure-output.png)  

4. Después de seleccionar **Autorizar**, se abre una ventana emergente y se le pide que proporcione las credenciales para autenticarse en su cuenta de Power BI. Una vez que la autorización se realice correctamente, **guarde** la configuración. 

## <a name="define-a-query-to-analyze-input-data"></a>Definición de una consulta para analizar los datos de entrada

Ya tiene un trabajo de Stream Analytics configurado para leer un flujo de datos de entrada. El siguiente paso consiste en crear una transformación que analice los datos en tiempo real. Definirá la consulta de transformación mediante el [lenguaje de consulta de Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx). En este tutorial, definirá una consulta que detecta llamadas fraudulentas en los datos del teléfono. 

En este ejemplo, se consideran llamadas fraudulentas aquellas que se originan desde el mismo usuario pero en distintas ubicaciones y la duración entre ambas llamadas es de cinco segundos. Por ejemplo, el mismo usuario no puede legítimamente realizar una llamada desde Estados Unidos y Australia al mismo tiempo. Para definir la consulta de transformación para el trabajo de Stream Analytics, ejecute los siguientes pasos:

1. En Azure Portal, abra el panel **Todos los recursos** y abra el trabajo **ASATutorial** de Stream Analytics que creó anteriormente.  

2. En la sección **Topología de trabajo** del panel de trabajos de Stream Analytics, seleccione la opción **Consulta**. En la ventana emergente se muestran las entradas y salidas que están configurados para el trabajo y en ella puede crear una consulta para transformar el flujo de entrada.  

3. A continuación, reemplace la consulta existente en el editor por los siguientes datos. Esta consulta realiza una autocombinación en un intervalo de 5 segundos de datos de llamada:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Para comprobar si hay llamadas fraudulentas, debe combinar automáticamente los datos de streaming según el valor de `CallRecTime`. Luego, puede buscar registros de llamada en los que el valor `CallingIMSI` (el número donde se origina) sea el mismo, pero el valor `SwitchNum` (país de origen) sea distinto. Cuando use una operación JOIN con datos de streaming, la combinación debe proporcionar algunos límites en la diferencia de tiempo que puede haber entre filas coincidentes. Como los datos de streaming no tienen final, los límites de tiempo de la relación se especifican dentro de la cláusula ON de la combinación, mediante la función [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics). 

   Esta consulta es igual que una combinación SQL normal excepto por la función DATEDIFF. La función DATEDIFF que se usa en esta consulta es específica de Stream Analytics y debe aparecer dentro de la cláusula `ON...BETWEEN`.  

4. **Guarde** la consulta.  

   ![definir consulta](media/stream-analytics-manage-job/define-query.png) 

## <a name="test-your-query"></a>Prueba de la consulta

Puede probar una consulta desde el editor de consultas, pero para ello necesita muestrear los datos. En este tutorial, extraerá datos de ejemplo del flujo de llamadas de teléfono que entra en el centro de eventos. Ejecute los siguientes pasos para probar la consulta:

1. Asegúrese de que la aplicación TelcoGenerator se ejecuta y genera registros de llamadas de teléfono.  

2. En el panel **Consulta**, haga clic en los puntos que aparecen junto a la entrada CallStream y seleccione **Sample data from input** (Muestrear datos de entrada). Se abre un panel que le permite especificar cuántos datos de muestra se leerán del flujo de entrada.  

   ![Muestreo de los datos de entrada](media/stream-analytics-manage-job/sample-input-data.png)  

3. Establezca **Minutos** en 3 y seleccione **Aceptar**. Se realiza el muestreo de tres minutos de datos del flujo de entrada y se le notifica cuando los datos de muestra están listos. Puede ver el estado del muestreo en la barra de notificación. 

   Los datos de muestra se almacenan temporalmente y están disponibles mientras tenga abierta la ventana de consulta. Si cierra la ventana de consulta, los datos de muestra se descartan y tendrá que crear un nuevo conjunto de datos de muestra. Como alternativa, puede obtener un archivo .json que contiene datos de muestra en [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/telco.json) y luego cargarlo para usarlo como datos de muestra para la entrada CallStream.  

4. Seleccione **Probar** para probar la consulta; verá los resultados de salida, tal y como se muestra en esta captura de pantalla:  

   ![Prueba de la salida](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>Inicio del trabajo y visualización de la salida

1. Para iniciar el trabajo, vaya al panel **Información general** de su trabajo y seleccione **Iniciar**.  

2. Seleccione **Ahora** como la hora de inicio de la salida del trabajo y seleccione **Iniciar**. El trabajo se inicia al cabo de unos minutos y puede ver el estado en la barra de notificación.  

3. Una vez que el trabajo se inicia correctamente, vaya a [Powerbi.com](https://powerbi.com/) e inicie sesión con su cuenta profesional o educativa. Si la consulta del trabajo de Stream Analytics genera resultados, verá que el conjunto de datos ya se ha creado. Navegue hasta la pestaña **Conjuntos de datos**; verá un conjunto de datos denominado "ASAdataset".  

4. En el área de trabajo, seleccione **+ Crear**. Cree un nuevo panel y asígnele el nombre Llamadas fraudulentas. Agregará dos iconos a este panel: uno se usa para ver el recuento de llamadas fraudulentas en una instancia determinada y el otro icono tiene una visualización de gráfico de líneas.  

5. En la parte superior de la ventana, seleccione **Agregar icono**, seleccione **Datos de streaming personalizados**, haga clic en Siguiente, elija **ASAdataset**, seleccione **Tarjeta** como tipo de visualización y **fraudulentcalls** como campos. Seleccione **Siguiente**, escriba un nombre para el icono y seleccione **Aplicar**.  

   ![Creación de iconos](media/stream-analytics-manage-job/create-tiles.png)

6. Siga de nuevo los 4 pasos, con las siguientes opciones:
   * Cuando llegue a Tipo de visualización, elija Gráfico de líneas.  
   * Agregue un eje y seleccione **windowend**.  
   * Agregue un valor y seleccione **fraudulentcalls**.  
   * En **Período de tiempo para mostrar**, seleccione los últimos 10 minutos.  

7. El panel se parece a la captura de pantalla siguiente después de agregar ambos iconos. Observará que, si se están ejecutando la aplicación de remitente del centro de eventos y la aplicación de Stream Analytics, el panel de PowerBI se actualiza periódicamente a medida que llegan nuevos datos.  

   ![Resultados de Power BI](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>Inserción del panel de PowerBI en una aplicación web

En esta parte del tutorial, usará una aplicación web [ASP.NET](http://asp.net/) de ejemplo que ha creado el equipo de PowerBI para insertar el panel. Para más información sobre cómo insertar paneles, consulte el artículo [Inserción con Power BI](https://docs.microsoft.com/power-bi/developer/embedding).

En este tutorial, se seguirán los pasos del usuario propietario de la aplicación de datos. Para configurar la aplicación, vaya al repositorio de GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) y siga las instrucciones de la sección **User Owns Data** (use las direcciones URL de redireccionamiento y de página principal en la subsección **integrate-dashboard-web-app**). Dado que se está usando el ejemplo de panel, utilice el código de ejemplo integrate-dashboard-web-app que se encuentra en el [repositorio de GitHub](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User Owns Data/integrate-dashboard-web-app).
Cuando esté satisfecho con la aplicación que se ejecuta en el explorador, siga estos pasos para insertar el panel que creó anteriormente en la página web:

1. Seleccione **Sign in to Power BI** (Iniciar sesión en Power BI). Mediante esta acción se concede a la aplicación acceso a los paneles de la cuenta de Power BI.  

2. Seleccione el botón **Get Dashboards** (Obtener paneles), que muestra los paneles de su cuenta en una tabla. Busque el nombre del panel que creó anteriormente (powerbi-embedded-dashboard) y copie el valor de **EmbedUrl** correspondiente.  

3. Por último, pegue el valor de **EmbedUrl** en el campo de texto correspondiente y seleccione **Embed Dashboard** (Insertar panel). Ahora puede ver el mismo panel insertado en una aplicación web.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un trabajo sencillo de Stream Analytics, ha analizado los datos de entrada y ha presentado los resultados en un panel de Power BI. Para más información sobre los trabajos de Stream Analytics, continúe con el tutorial siguiente:

> [!div class="nextstepaction"]
> [Ejecución de Azure Functions dentro de trabajos de Stream Analytics](stream-analytics-with-azure-functions.md)
