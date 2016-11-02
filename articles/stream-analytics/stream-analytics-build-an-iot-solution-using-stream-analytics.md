<properties
    pageTitle="Compilación de una solución de IoT con Stream Analytics | Microsoft Azure"
    description="Tutorial de introducción a la solución de IoT de Stream Analytics de un escenario de cabinas de peaje."
    keywords="solución de IOT, funciones de ventana"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Compilación de una solución de IoT con Stream Analytics

## <a name="introduction"></a>Introducción

En este tutorial aprenderá a usar Azure Stream Analytics para obtener información de sus datos en tiempo real. Los desarrolladores pueden combinar fácilmente secuencias de datos, como secuencias de clic, registros y eventos generados por el dispositivo, con registros históricos o datos de referencia para obtener información empresarial. Azure Stream Analytics es un servicio de cálculo de transmisiones en tiempo real totalmente administrado y hospedado en Microsoft Azure que ofrece gran resistencia, baja latencia y escalabilidad, para permitirle ponerse a trabajar en cuestión de minutos.

Después de completar este tutorial, estará capacitado para lo siguiente:

-   Familiarizarse con el portal de Azure Stream Analytics.
-   Configurar e implementar un trabajo de streaming.
-   Señalar los problemas reales y solucionarlos usando el lenguaje de consulta de Stream Analytics.
-   Desarrollar soluciones de streaming para los clientes usando el lenguaje de consulta de Stream Analytics.
-   Usar la experiencia de supervisión y registro para solucionar problemas.

## <a name="prerequisites"></a>Requisitos previos

Para realizar este tutorial, deberá cumplir los siguientes requisitos previos:

-   La versión más reciente de [Azure PowerShell](../powershell-install-configure.md)
-   Visual Studio 2015 o la versión de [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-    [Una suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Privilegios administrativos en el equipo
-   Descarga de [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) del Centro de descarga de Microsoft.
-   Opcional: código fuente del generador de eventos TollApp de [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction:-“hello,-toll!”"></a>Introducción al escenario: peajes


Una estación de peaje es un fenómeno común. Se encuentra en muchas autopistas, puentes y túneles de todo el mundo. Cada estación de peaje tiene varias cabinas. En cabinas manuales, se detiene y paga el peaje a un operador. En cabinas automatizadas, un sensor situado en la parte superior de cada cabina escanea una tarjeta RFID que está ubicada en el parabrisas del vehículo al pasar la cabina de peaje. Es fácil imaginar el paso de los vehículos a través de estos peajes como si fuera un flujo de eventos sobre los que se pueden realizar operaciones interesantes.

![Imagen de automóviles en cabinas de peaje](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Datos de entrada

Este tutorial funciona con dos secuencias de datos. Los sensores instalados a la entrada y la salida de las estaciones de peaje producen la primera secuencia. La segunda secuencia es un conjunto de datos de búsqueda estática que tiene datos de registro del vehículo.

### <a name="entry-data-stream"></a>Flujo de datos de entrada

El flujo de datos de entrada contiene información sobre los vehículos que entran en las estaciones de peaje.


| TollId | EntryTime               | LicensePlate | Estado | Asegúrese   | Modelo   | VehicleType | VehicleWeight | Toll | Etiqueta       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 10-09-2014 12:01:00.000 | JNB 7001     | NY    | Honda  | CRV     | 1           | 0             | 7    |           |
| 1      | 10-09-2014 12:02:00.000 | YXZ 1001     | NY    | Toyota | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 10-09-2014 12:02:00.000 | ABC 1004     | CT    | Ford   | Taurus  | 1           | 0             | 5    | 456789123 |
| 2      | 10-09-2014 12:03:00.000 | XYZ 1003     | CT    | Toyota | Corolla | 1           | 0             | 4    |           |
| 1      | 10-09-2014 12:03:00.000 | BNJ 1007     | NY    | Honda  | CRV     | 1           | 0             | 5    | 789123456 |
| 2      | 10-09-2014 12:05:00.000 | CDE 1007     | NJ    | Toyota | 4 x 4     | 1           | 0             | 6    | 321987654 |


Breve descripción de las columnas:

| Columna | Description |
|--------------|--------------------------------------------------------------------|
| TollId       | El identificador de la cabina de peaje que identifica de forma única una cabina de peaje.            |
| EntryTime    | Fecha y hora de entrada del vehículo en la cabina de peaje en UTC. |
| LicensePlate | Número de matrícula del vehículo.                            |
| Estado        | Estado de los Estados Unidos.                                           |
| Asegúrese         | Fabricante del automóvil.                                 |
| Modelo        | Número de modelo de vehículo.                                 |
| VehicleType  | 1 para los vehículos de pasajeros o 2 para vehículos comerciales.       |
| WeightType   | Peso del vehículo en toneladas; es 0 para vehículos de pasajeros.                   |
| Toll         | Costo del peaje en USD.                                              |
| Etiqueta          | Etiqueta electrónica del vehículo que automatiza el pago; en blanco cuando el pago se realiza manualmente. |


### <a name="exit-data-stream"></a>Flujo de datos de salida

El flujo de datos de salida contiene información sobre los vehículos que salen de la estación de peaje.


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 10-09-2014 T12:03:00.0000000Z | JNB 7001         |
| 1          | 10-09-2014 T12:03:00.0000000Z | YXZ 1001         |
| 3          | 10-09-2014 T12:04:00.0000000Z | ABC 1004         |
| 2          | 10-09-2014 T12:07:00.0000000Z | XYZ 1003         |
| 1          | 10-09-2014 T12:08:00.0000000Z | BNJ 1007         |
| 2          | 10-09-2014 T12:07:00.0000000Z | CDE 1007         |

Breve descripción de las columnas:


| Columna | Description |
|--------------|-----------------------------------------------------------------|
| TollId       | El identificador de la cabina de peaje que identifica de forma única una cabina de peaje.         |
| ExitTime     | La fecha y hora de salida del vehículo de la cabina de peaje en UTC. |
| LicensePlate | Número de matrícula del vehículo.                         |

### <a name="commercial-vehicle-registration-data"></a>Datos de registro de vehículos comerciales

En el tutorial se usa una instantánea estática de una base de datos de registro de vehículos comerciales.


| LicensePlate | RegistrationId | Expirada |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| BAC 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

Breve descripción de las columnas:


| Columna | Description |
|--------------|-----------------------------------------------------------------|
| LicensePlate | Número de matrícula del vehículo.                         |
| RegistrationId     | Identificador de registro del vehículo. |
| Expirada | El estado de registro del vehículo: 0 si el registro de vehículo está activo, 1 si el registro ha caducado.                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configuración del entorno para Azure Stream Analytics

Para realizar este tutorial, necesita una suscripción de Microsoft Azure. Microsoft ofrece una evaluación gratuita de los servicios de Microsoft Azure.

Si no tiene una cuenta de Azure, puede [solicitar una versión de evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Para suscribirse a una evaluación gratuita, necesita un dispositivo móvil que pueda recibir mensajes de texto y una tarjeta de crédito válida.

Asegúrese de seguir los pasos de la sección "Limpieza de la cuenta de Azure" al final de este ejercicio para que pueda aprovechar al máximo su crédito gratuito de 200 $ de Azure.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Aprovisionamiento de los recursos de Azure necesarios para el tutorial

Este tutorial requiere dos Centros de eventos para recibir los flujos de datos *entry* y *exit*. Azure SQL Database genera los resultados de los trabajos de Stream Analytics. Azure Storage almacena los datos de referencia sobre los registros de vehículos.

Se puede usar el script Setup.ps1 de la carpeta TollApp en GitHub para crear todos los recursos necesarios. Se recomienda su uso para ahorrar tiempo. Para más información sobre la configuración de estos recursos en Azure Portal, consulte el apéndice "Configuración de recursos del tutorial en Azure Portal".

Descargue y guarde el soporte los archivos y carpetas de [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) compatibles.

Abra una ventana de **Microsoft Azure PowerShell**_como administrador_. Si aún no tiene Azure PowerShell, siga estas instrucciones que se indican en [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

Puesto que Windows bloquea automáticamente archivos .ps1, .dll y .exe, debe establecer la directiva de ejecución antes de ejecutar el script. Asegúrese de ejecutar la ventana de Azure PowerShell _como administrador_. Ejecute **Set-ExecutionPolicy unrestricted**. Cuando se le solicite, escriba **Y**.

![Captura de pantalla de "Set-ExecutionPolicy unrestricted" ejecutándose en la ventana de Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Ejecute **Get-ExecutionPolicy** para asegurarse de que el comando funcionó.

![Captura de pantalla de "Get-ExecutionPolicy" ejecutándose en la ventana de Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Vaya al directorio que contiene los scripts y la aplicación del generador.

![Captura de pantalla de "cd .\TollApp\TollApp" ejecutándose en la ventana de Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Escriba **.\\Setup.ps1** para configurar la cuenta de Azure, crear y configurar todos los recursos necesarios y empezar a generar eventos. El script selecciona aleatoriamente una región para crear los recursos. Para especificar explícitamente una región, puede pasar el parámetro **-location** como en el ejemplo siguiente:

**.\\Setup.ps1 -ubicación “centro de EE. UU.”**

![Captura de pantalla de la página de inicio de sesión en Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

El script abre la página de **inicio de sesión** de Microsoft Azure. Escriba los credenciales de la cuenta.

> [AZURE.NOTE] Si la cuenta tiene acceso a varias suscripciones, se le pedirá que escriba el nombre de la suscripción que quiere usar para el tutorial.

El script puede tardar varios minutos en ejecutarse. Cuando finaliza, el resultado será semejante al de la captura de pantalla siguiente.

![Captura de pantalla de salida del script en la ventana de Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

También verá otra ventana similar a la de la captura de pantalla siguiente. Esta aplicación envía eventos a Azure Events Hubs que son necesarios para ejecutar el tutorial. Por lo tanto, no detenga la aplicación ni cierre esta ventana hasta que finalice el tutorial.

![Captura de pantalla "Enviando datos de centro de eventos"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Ahora podrá ver todos los recursos creados en Azure Portal. Vaya a <https://manage.windowsazure.com> e inicie sesión con las credenciales de su cuenta.

### <a name="azure-event-hubs"></a>Centros de eventos de Azure

Haga clic en **SERVICE BUS** en el lado izquierdo de Azure Portal para ver los centros de eventos creados por el script en la sección anterior.

![SERVICE BUS](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Verá todos los espacios de nombres disponibles en la suscripción. Haga clic en uno que empiece por *tolldata* (en nuestro ejemplo será tolldata4637388511). Haga clic en la pestaña **EVENT HUBS** .

![Pestaña Centros de eventos en Azure Portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Verá dos Centros de eventos denominados *entry* y *exit* creados en este espacio de nombres.

![Captura de pantalla de centros de eventos "entry" y "exit" en Azure Portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Contenedor de Almacenamiento de Azure

1. Haga clic en **STORAGE** en el lado izquierdo de Azure Portal para ver el contenedor de Azure Storage que se usa en el tutorial.

    ![Elemento de menú de almacenamiento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. Haga clic en uno que empiece por *tolldata* (en nuestro ejemplo será tolldata4637388511). Haga clic en la pestaña **CONTENEDORES** para ver el contenedor creado.

    ![Pestaña Contenedores de Azure Portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. Haga clic en el contenedor **tolldata** para ver el archivo JSON cargado que tiene los datos de registro de vehículos.

    ![Captura de pantalla del archivo registration.json en el contenedor](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Base de datos SQL de Azure

1. Haga clic en **BASES DE DATOS SQL** en el lado izquierdo de Azure Portal para ver la base de datos SQL que se usará en este tutorial.

    ![BASES DE DATOS SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. Haga clic en **tolldatadb**.

    ![Captura de pantalla de la base de datos SQL creada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. Copie el nombre del servidor sin el número de puerto (*nombredeservidor*.database.windows.net, por ejemplo).

## <a name="connect-to-the-database-from-visual-studio"></a>Conexión a la base de datos desde Visual Studio

Usaremos Visual Studio para acceder a los resultados de consulta de la base de datos de salida.

Conéctese a la base de datos de SQL (el destino) desde Visual Studio:

1. Abra Visual Studio y, a continuación, haga clic en **Herramientas** > **Conectar a base de datos**.

2. Si se le solicita, haga clic en **Microsoft SQL Server** como origen de datos.

    ![Cuadro de diálogo Cambiar origen de datos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. En el campo **Nombre del servidor** , pegue el nombre que copió en la sección anterior de Azure Portal (es decir, *nombredeservidor*.database.windows.net).

4. Haga clic en **Usar autenticación de SQL Server**.

5. Escriba **tolladmin** en el campo **Nombre de usuario** y **123toll!** in the **Contraseña** .

6. Haga clic en **Seleccione o escriba el nombre de la base de datos** y seleccione **TollDataDB** como la base de datos.

    ![Cuadro de diálogo Agregar conexión](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. Haga clic en **Aceptar**.

8. Abra el Explorador de servidores.

    ![Explorador de servidores](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. Vea las cuatro tablas de la base de datos de TollDataDB.

    ![Tablas de la base de datos TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator:-tollapp-sample-project"></a>Generador de eventos: proyecto de ejemplo TollApp

El script de PowerShell comienza a enviar eventos automáticamente mediante el programa de aplicación de ejemplo TollApp. No es necesario realizar pasos adicionales.

Sin embargo, si está interesado en los detalles de implementación, puede encontrar el código fuente de la aplicación TollApp en GitHub [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Captura de pantalla del código de ejemplo que se muestra en Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Análisis de transmisiones

1. Para crear un nuevo trabajo de análisis, vaya a Azure Portal, abra Stream Analytics y haga clic en **NUEVO** en la esquina inferior izquierda de la página.

    ![New button](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. Haga clic en **CREACIÓN RÁPIDA**. Seleccione la misma región donde el script crea los otros recursos.

3. En **CUENTA DE ALMACENAMIENTO DE SUPERVISIÓN REGIONAL**, seleccione **CREAR UNA NUEVA CUENTA DE ALMACENAMIENTO** y asígnele un nombre único. Análisis de transmisiones de Azure usará esta cuenta para almacenar la información de supervisión de todos los futuros trabajos.

4. Haga clic en **CREAR EL TRABAJO DE ANÁLISIS DE TRANSMISIONES** en la parte inferior de la página.

    ![Opción Crear el trabajo de Análisis de transmisiones](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Definición de orígenes de entrada

1. Haga clic en el trabajo de análisis creado en el portal.

    ![Captura de pantalla del trabajo de análisis en el portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. Abra la pestaña **ENTRADAS** para definir el origen de datos.

    ![La pestaña Entradas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. Haga clic en **AGREGAR ENTRADA**.

    ![La opción Agregar una entrada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. En la primera página, seleccione **FLUJO DE DATOS** .

    ![La opción Secuencia de datos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. Seleccione **CENTRO DE EVENTOS** en la segunda página del asistente.

    ![La opción Centro de eventos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. Escriba **EntryStream** como **ALIAS DE ENTRADA**.

7. Haga clic en la lista desplegable **Centro de eventos** y seleccione el que comienza con "TollData" (por ejemplo, TollData9518658221).

8. Seleccione **entry** como nombre del Centro de eventos y **all** como nombre de la directiva de Centro de eventos.

    La configuración se verá así:

    ![Configuración del centro de eventos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. En la siguiente página, seleccione **JSON** para **FORMATO DE SERIALIZACIÓN DE EVENTOS** y **UTF8** para **CODIFICACIÓN**.

    ![Configuración de la serialización](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. Haga clic en **Aceptar** en la parte inferior de la página para finalizar al asistente.

    ![Captura de pantalla de la entrada EntryStream en Azure Portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    Ahora que ha creado la secuencia de entrada, se siguen los mismos pasos para crear la secuencia de salida. En la tercera página del asistente, asegúrese de especificar valores como en la siguiente captura de pantalla.

    ![Configuración para el flujo de salida](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    Ha definido dos flujos de entrada:

    ![Flujos de entrada definidos en Azure Portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    A continuación, agregaremos la entrada de datos de referencia para el archivo de blob que contiene los datos de registro de los vehículos.

11. Haga clic en **AGREGAR ENTRADA** y luego en **DATOS DE REFERENCIA**.

    ![Opciones de "Agregar una entrada" con los datos de referencia seleccionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. En la página siguiente, seleccione la cuenta de almacenamiento que empieza por **tolldata**. El nombre del contenedor debe ser **tolldata** y el nombre del blob en **PATRÓN DE RUTA DE ACCESO** debe ser **registration.json**. Este nombre de archivo distingue mayúsculas de minúsculas, por lo que asegúrese de escribirlo en minúsculas.

    ![Configuración de almacenamiento de blobs](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. Seleccione los valores como se muestra en la siguiente captura de pantalla en la página siguiente y, luego, haga clic en **Aceptar** para finalizar el asistente.

    ![Selección de JSON para "Formato de serialización de eventos" y UTF8 para "Codificación"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Ahora todas las entradas están definidas.

![Captura de pantalla de las tres entradas definidas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Defininición de salida

1. Haga clic en la pestaña **SALIDA** y luego haga clic en **AGREGAR UNA SALIDA**.

    ![La pestaña Salida y la opción "Agregar una salida"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. Haga clic en **Base de datos SQL**.

3. Seleccione el nombre de servidor que se usó en la sección "Conexión a la base de datos desde Visual Studio" de este artículo. El nombre de la base de datos es **TollDataDB**.

4. Escriba **tolladmin** en el campo **NOMBRE DE USUARIO**, **123toll!** en el campo **CONTRASEÑA** y **TollDataRefJoin** en el campo **TABLA**.

    ![Configuración de SQL Database](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Consulta de Análisis de transmisiones de Azure

La pestaña **CONSULTA** contiene una consulta SQL que transforma los datos de entrada.

![Una consulta agregada a la pestaña Consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

En este tutorial intentaremos responder a varias cuestiones empresariales relacionadas con datos de peaje y construiremos consultas de Stream Analytics que puedan usarse en Azure Stream Analytics para dar una respuesta adecuada.

Antes de iniciar el primer trabajo de Stream Analytics, veamos algunos escenarios y la sintaxis de consulta.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introducción al lenguaje de consulta de Azure Stream Analytics
-----------------------------------------------------

Supongamos que necesita contar el número de vehículos que entran en una cabina de peaje. Como se trata de una secuencia continua de eventos, tendrá que definir un "período de tiempo". Vamos a cambiar la pregunta a "¿Cuántos vehículos entran en una cabina de peaje cada tres minutos?". Esto se conoce comúnmente como "tumbling count".

Veamos la consulta de Azure Stream Analytics que responde a esta pregunta:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Como puede ver, Azure Stream Analytics usa un lenguaje de consulta que es similar a SQL y agrega algunas extensiones para especificar aspectos de la consulta relacionados con el tiempo.

Para más información, puede leer sobre las construcciones de [Administración del tiempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) y [Ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx) que se usan en una consulta en MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Pruebas de consultas de Análisis de transmisiones de Azure

Ahora que hemos escrito nuestra primera consulta de Azure Stream Analytics, es momento de probarla usando los archivos de datos de ejemplo ubicados en la carpeta TollApp en la siguiente ruta:

**..\\TollApp\\TollApp\\Datos**

Esta carpeta contiene los archivos siguientes:

- Entry.json
- Exit.json
- registration.json

## <a name="question-1:-number-of-vehicles-entering-a-toll-booth"></a>Pregunta 1: Número de vehículos que entran en una cabina de peaje

1. Abra Azure Portal y vaya a su trabajo creado de Azure Stream Analytics. Abra la pestaña **CONSULTA** y pegue la consulta de la sección anterior.

    ![Consulta pegada en la pestaña Consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. Para validar esta consulta usando datos de ejemplo, haga clic en el botón **Probar** . En el cuadro de diálogo que se abre, vaya a Entry.json, un archivo con datos de ejemplo del flujo de eventos **EntryTime** .

    ![Captura de pantalla del archivo Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. Compruebe que el resultado de la consulta es el esperado:

    ![Resultados de la prueba](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2:-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Pregunta 2: Notificar el tiempo total que necesita cada vehículo para atravesar la cabina de peaje

El tiempo medio que necesita un vehículo para pasar el peaje ayuda a evaluar la eficacia del proceso y la experiencia del cliente.

Para encontrar el tiempo total, debe unir la secuencia EntryTime con la secuencia ExitTime. Las secuencias se unirán en las columnas TollId y LicencePlate. El operador **JOIN** requiere que especifique un margen temporal que describa la diferencia de tiempo aceptable entre los eventos combinados. Usaremos la función **DATEDIFF** para especificar que los eventos no deben durar más de 15 minutos entre sí. También se aplicará la función **DATEDIFF** a las horas de entrada y salida para calcular el tiempo real que pasa un vehículo en la estación de peaje. Tenga en cuenta la diferencia del uso de **DATEDIFF** en una instrucción **SELECT** en comparación con su uso en una condición **JOIN**.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Para probar esta consulta, actualícela en la pestaña **CONSULTA** del trabajo:

    ![Consulta actualizada en la pestaña Consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. Haga clic en **Probar** y especifique los archivos de entrada de ejemplo para EntryTime y ExitTime.

    ![Captura de pantalla de los archivos de entrada seleccionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. Active la casilla para probar la consulta y ver la salida:

    ![Resultado de la prueba](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3:-report-all-commercial-vehicles-with-expired-registration"></a>Pregunta 3: Notificar todos los vehículos comerciales con registro caducado

Análisis de transmisiones de Azure puede usar instantáneas estáticas de datos para combinar con flujos de datos temporales. Para demostrar esta funcionalidad se usará la siguiente pregunta de ejemplo.

Si un vehículo comercial está registrado en la empresa de peaje, puede atravesar la cabina sin detenerse para inspección. Se usará la tabla de búsqueda de registro de vehículos comerciales para identificar todos los vehículos comerciales con registros caducados.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Para probar una consulta mediante datos de referencia, debe definir un origen de entrada para los datos de referencia, lo cual ya ha hecho.

1. Para probar esta consulta, pegue la consulta en la pestaña **CONSULTA**, haga clic en **Probar** y especifique los dos orígenes de entrada:

    ![Captura de pantalla de los archivos de entrada seleccionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. Vea el resultado de la consulta:

    ![Captura de pantalla de la salida de consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Inicio del trabajo de Análisis de transmisiones


Ahora es el momento de finalizar la configuración e iniciar el trabajo. Guarde la consulta de la pregunta 3, que generará un resultado que coincidirá con el esquema de la tabla de salida **TollDataRefJoin** .

Vaya al **PANEL** del trabajo y haga clic en **INICIAR**.

![Captura de pantalla del botón de inicio en el panel de trabajo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

En el cuadro de diálogo que se abre, cambie la hora de **INICIAR SALIDA** por **HORA PERSONALIZADA**. Cambie la hora por una hora anterior a la hora actual. Como los eventos se han comenzado a generar al empezar este tutorial, este cambio garantiza que se procesan todos los eventos del centro de eventos. Ahora haga clic en la marca de verificación para iniciar el trabajo.

![Selección de tiempo personalizado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

El trabajo puede tardar unos minutos en iniciarse. Puede ver el estado en la página de nivel superior de Stream Analytics.

![Captura de pantalla del estado del trabajo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Comprobación de resultados en Visual Studio

1. Abra el Explorador de servidores de Visual Studio y haga clic con el botón derecho en la tabla **TollDataRefJoin** .
2. Seleccione **Mostrar datos de tabla** para ver el resultado del trabajo.

    ![Selección de "Mostrar datos de tabla" en el Explorador de servidores](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Escalado horizontal de trabajos de Azure Stream Analytics

Azure Stream Analytics está diseñado para escalarse elásticamente de forma que pueda gestionar gran cantidad de datos. Las consultas de Análisis de transmisiones puede usar una cláusula **PARTITION BY** para indicar al sistema que este paso se escalará horizontalmente. **PartitionId** es una columna especial que agrega el sistema para que coincida con el identificador de partición de la entrada (centro de eventos).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Detenga el trabajo actual, actualice la consulta en la pestaña **CONSULTA** y abra la pestaña **ESCALA**.

    **UNIDADES DE STREAMING** definen la cantidad de capacidad de proceso que el trabajo puede recibir.

2. Mueva el control deslizante a 6.

    ![Captura de pantalla de selección de 6 unidades de streaming](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. Vaya a la pestaña **SALIDAS** y cambie el nombre de la tabla SQL por **TollDataTumblingCountPartitioned**.

Ahora, si inicia el trabajo, Azure Stream Analytics puede distribuir el trabajo entre más recursos de proceso y lograr un rendimiento mejor. Tenga en cuenta que la aplicación TollApp también envía eventos que particiona TollId.

## <a name="monitor"></a>Supervisión

La pestaña **SUPERVISIÓN** contiene estadísticas sobre el trabajo en ejecución.

![Captura de pantalla de estadísticas sobre la ejecución de trabajos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Puede tener acceso a los **registros de operaciones** desde la pestaña **PANEL**.

![La opción "Registros de operaciones"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Captura de pantalla de registros de operaciones donde puede ver el estado de los trabajos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Para más información sobre un evento determinado, selecciónelo y haga clic en el botón **DETALLES** .

![Captura de pantalla de detalles sobre un evento seleccionado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Conclusión

Este tutorial ha introducido el servicio Azure Stream Analytics. Se ha demostrado cómo configurar entradas y salidas de un trabajo de Stream Analytics. Mediante el escenario de datos de peaje, se han explicado los tipos más comunes de problemas que surgen en el espacio de datos en movimiento y cómo pueden resolverse con simples consultas de tipo SQL en Azure Stream Analytics. Asimismo, se han descrito las construcciones de extensión SQL para trabajar con datos temporales. Se ha mostrado cómo combinar secuencias de datos, cómo enriquecer la secuencia de datos con datos de referencia estáticos y cómo escalar una consulta horizontalmente para lograr un mayor rendimiento.

Aunque este tutorial proporciona una buena introducción, no está completo de en modo alguno. Puede encontrar más patrones de consulta que usan el lenguaje SAQL en [Ejemplos de consulta para patrones de uso comunes de Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
Para más información sobre Azure Stream Analytics, consulte la [documentación en línea](https://azure.microsoft.com/documentation/services/stream-analytics/) .

## <a name="clean-up-your-azure-account"></a>Limpieza de la cuenta de Azure

1. Detenga el trabajo de Stream Analytics desde Azure Portal.

    El script Setup.ps1 crea dos centros de eventos y una base de datos SQL. Las siguientes instrucciones le ayudarán a limpiar los recursos al final del tutorial.

2. En la ventana de PowerShell, escriba **.\\Cleanup.ps1** para iniciar el script que elimina los recursos usados en el tutorial.

    > [AZURE.NOTE] Los recursos se identifican por el nombre. Asegúrese de revisar cuidadosamente cada elemento antes de confirmar la eliminación.

    ![Captura de pantalla del proceso de limpieza](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)



<!--HONumber=Oct16_HO2-->


