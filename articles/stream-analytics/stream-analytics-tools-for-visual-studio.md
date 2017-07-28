---
title: Uso de herramientas de Azure Stream Analytics para Visual Studio | Microsoft Docs
description: "Tutorial de introducción de las herramientas de Azure Stream Analytics para Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: 
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 
ms.author: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 618c1055795a75e0ed71dacddba3e076f81f4946
ms.contentlocale: es-es
ms.lasthandoff: 07/04/2017


---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Uso de herramientas de Azure Stream Analytics para Visual Studio
## <a name="introduction"></a>Introducción
En este tutorial, aprenderá a usar herramientas de Azure Stream Analytics para Visual Studio para crear, probar localmente, administrar y depurar los trabajos de Stream Analytics. 

Después de completar este tutorial, estará capacitado para lo siguiente:
* Familiarícese con las herramientas de Stream Analytics para Visual Studio.
* Configurar e implementar un trabajo de Stream Analytics.
* Probar el trabajo localmente con datos de ejemplo local.
* Usar la supervisión para solucionar problemas.
* Exportar los trabajos existentes a proyectos.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, debe cumplir los siguientes requisitos previos:
* Complete los pasos anteriores a "Creación de un trabajo de Stream Analytics" del [tutorial Compilación de una solución de IoT con Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Use Visual Studio 2015, Visual Studio 2013 Update 4 o Visual Studio 2012. Se admiten las ediciones Enterprise (Ultimate y Premium), Professional y Community. No se admite la edición Express. Visual Studio 2017 no se admite. 
* Use Azure SDK para .NET versión 2.7.1 o posterior. Instálelo con el [Instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).
* Instale las [herramientas de Stream Analytics para Visual Studio](http://aka.ms/asatoolsvs).

## <a name="create-a-stream-analytics-project"></a>Creación de un trabajo de Stream Analytics
1. En Visual Studio, haga clic en el menú **Archivo** y seleccione **Nuevo proyecto**. 

2. Seleccione **Stream Analytics** en la lista de plantillas de la izquierda y, después, haga clic en **Azure Stream Analytics Application** (Aplicación de Azure Stream Analytics).

3. Escriba el **Nombre**, **Ubicación** y **Nombre de la solución** del proyecto como lo hace para otros proyectos.

    ![Ventana Nuevo proyecto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

    Un proyecto de **peaje** se ha generado en el **Explorador de soluciones**.

    ![El proyecto de peaje se ha generado en el Explorador de soluciones](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Elegir la suscripción correcta
1. En Visual Studio, haga clic en el menú **Ver** y abra el **Explorador de servidores**.

2. Inicie sesión con su cuenta de Azure. 

## <a name="define-the-input-sources"></a>Definir los orígenes de entrada
1.  En el **Explorador de soluciones**, expanda el nodo **Inputs** y cambie el nombre de **Input.json** a **EntryStream.json**. Haga doble clic en **EntryStream.json**.
2.  Ahora, el **Alias de entrada** es **EntryStream**. El alias de entrada se usa en el script de consulta. 
3.  En **Tipo de origen**, seleccione **Flujo de datos**.
4.  En **Origen**, seleccione **Centro de eventos**.
5.  En **Espacio de nombres de Service Bus**, seleccione la opción **TollData**.
6.  En **Nombre del centro de eventos**, seleccione **entrada**.
7.  En **Nombre de la directiva del centro de eventos**, seleccione **RootManageSharedAccessKey** (el valor predeterminado).
8.  En **Formato de serialización de eventos**, seleccione **Json**. 
9.  En **Codificación**, seleccione **UTF-8**. Su configuración debe ser similar a la siguiente captura de pantalla:

    ![Ventana de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
10. Para finalizar el asistente, haga clic en **Guardar**. Ahora puede agregar otra fuente de entrada para crear la secuencia de salida. Haga clic con el botón derecho en el nodo **Inputs** y seleccione **Nuevo elemento**.

    ![Nuevo elemento](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
11. En la ventana, seleccione **Azure Stream Analytics Input** (Entrada de Azure Stream Analytics) y cambie el **nombre** a **ExitStream.json**. Haga clic en **Agregar**.

    ![Ventana Agregar nuevo elemento](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
12. Haga doble clic en **ExitStream.json** en el proyecto y siga los mismos pasos que para el flujo de entrada. Asegúrese de especificar **exit** para el **Nombre del centro de eventos** como se muestra en la siguiente captura de pantalla:

    ![Ventana ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

    Ahora ha definido dos flujos de entrada:

    ![Flujos de entrada y salida](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
    Después, agregue la entrada de datos de referencia para el archivo de blob que contiene los datos de registro de los vehículos.

13. Haga clic con el botón derecho en el nodo **Inputs** del proyecto y, después, siga los mismos pasos que realizó para las entradas de secuencias. En **Alias de entrada**, escriba **Registro** y en **Tipo de origen**, seleccione **Datos de referencia**.

    ![Ventana Registro](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

14. En **Cuenta de almacenamiento**, seleccione la opción **tolldata**. En **Contenedor**, seleccione **tolldata** y, en **Patrón de ruta de acceso**, escriba **registration.json**. Este nombre de archivo distingue mayúsculas de minúsculas, por lo que asegúrese de escribirlo en minúsculas.
15. Para finalizar el asistente, haga clic en **Guardar**.

Ahora todas las entradas están definidas.

## <a name="define-the-output"></a>Definir la salida
1.  En el **Explorador de soluciones**, expanda el nodo **Inputs** y haga doble clic en **Output.json**.

2.  En **Alias de salida**, escriba **salida**. 
3.  En **Receptor**, seleccione **SQL Database**.
4.  En **Base de datos**, seleccione **TollDataDB**.
5.  En **Nombre de usuario**, escriba **tolladmin**. 
6.  En **Contraseña**, escriba **123toll!**.
7.  En **Tabla**, escriba **TollDataRefJoin**.
8.  Haga clic en **Guardar**.

    ![Ventana de salida](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="create-a-stream-analytics-query"></a>Creación de una consulta de Stream Analytics
Este tutorial intenta responder varias preguntas de empresa que están relacionadas con los datos de peaje. También crea consultas de Stream Analytics que pueden usarse en Stream Analytics para proporcionar respuestas relevantes.
Antes de iniciar el primer trabajo de Stream Analytics, veamos un escenario sencillo y la sintaxis de consulta.

### <a name="introduction-to-the-stream-analytics-query-language"></a>Introducción al lenguaje de consulta de Stream Analytics
Supongamos que necesita contar el número de vehículos que entran en una cabina de peaje. Como este ejemplo es una secuencia continua de eventos, tendrá que definir un período de tiempo. Cambie la pregunta a "¿Cuántos vehículos entran en una cabina de peaje cada tres minutos?". Este modo para contar datos se conoce normalmente como "tumbling count".

Veamos la consulta de Stream Analytics que responde a esta pregunta:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Stream Analytics usa un lenguaje de consulta que es similar a SQL y agrega algunas extensiones para especificar aspectos de la consulta relacionados con el tiempo.

Para obtener más información, vea las construcciones de [Administración del tiempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) y [Ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx) que se usan en una consulta en MSDN.

Ahora que ha escrito su primera consulta de Stream Analytics, es hora de probarla. Use los archivos de datos de ejemplo situados en su carpeta TollApp en la ruta de acceso siguiente:

..\TollApp\TollApp\Data

Esta carpeta contiene los archivos siguientes:
*   Entry.json
*   Exit.json
*   registration.json

## <a name="count-the-number-of-vehicles-entering-a-toll-booth"></a>Recuento del número de vehículos que entran en una cabina de peaje
En el proyecto, haga doble clic en **Script.asaql** para abrir el script en el **Editor de consultas**. Copie y pegue el script de la sección anterior en el editor. El editor de consultas admite IntelliSense, colores para la sintaxis y el marcador de errores.

![Editor de consultas](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Probar las consultas de Stream Analytics localmente

1. Para compilar la consulta para ver si hay un error de sintaxis, haga clic con el botón derecho en el proyecto y seleccione **Compilar**. 

2. Para validar esta consulta con los datos de ejemplo, puede usar datos de ejemplo locales. Haga clic con el botón derecho en la entrada y seleccione **Agregar entrada local**.

    ![Agregar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
3. En la ventana emergente, seleccione los datos de ejemplo de la ruta de acceso local. Haga clic en **Guardar**.

    ![Agregar ventana de entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    Un archivo denominado **local_EntryStream.json** se agrega automáticamente a la carpeta Inputs.

    ![Archivo agregado a la carpeta Inputs](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
4. En el **Editor de consultas**, haga clic en **Ejecutar localmente**. O bien, puede presionar la tecla F5.

    ![Ejecución en modo local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![Resultado de la ejecución local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    Pulse cualquier tecla para ver el resultado en la ventana **Resultado de la ejecución local de ASA** en Visual Studio. 

    ![Ventana Resultado de la ejecución local de ASA](./media/stream-analytics-tools-for-vs/local-testing-output.png)

5. Haga clic en **Abrir carpeta de resultados** para comprobar que los archivos de salida están en formato CSV y JSON.

    ![Resultado de abrir la carpeta de resultados](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### <a name="sample-the-input-data"></a>Usar los datos de entrada como ejemplo
También puede usar los datos de entrada de orígenes de entrada como ejemplo en un archivo local. 
1. Haga clic con el botón derecho en el archivo de configuración de entrada y seleccione **Datos de ejemplo**. 

   ![Datos de ejemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

    Por el momento solo puede usar como ejemplo el centro de eventos o IoT Hub. No se admiten otros orígenes de entrada.

2. En la ventana emergente, escriba la ruta de acceso local que se ha usado para guardar los datos de ejemplo. Haga clic en **Ejemplo**.

    ![Ventana de datos de ejemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
    Puede ver el progreso en la ventana **Salida**. 

    ![Ventana de salida](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Enviar una consulta de Stream Analytics a Azure
1. En el **Editor de consultas**, haga clic en **Enviar a Azure** en el editor de scripts.

    ![Enviar a Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Seleccione **Crear un trabajo de Azure Stream Analytics**. Escriba el **Nombre del trabajo** y seleccione la **Suscripción** correcta. Haga clic en **Enviar**.

    ![Ventana Enviar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-a-job"></a>Iniciar un trabajo
Ahora que se ha creado el trabajo, se abre automáticamente la vista de trabajo. 
1. Para iniciar el trabajo, haga clic en el botón de **flecha verde**.

    ![Iniciar un trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Seleccione la configuración predeterminada y haga clic en **Iniciar**.
 
    ![Ventana Iniciar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

    El **estado** del trabajo cambia a **En ejecución** y aparecen **Eventos de entrada** y **Eventos de salida**.

    ![Estado de ejecución en Resumen del trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-the-results-in-visual-studio"></a>Comprobación de los resultados en Visual Studio
1. En Visual Studio, abra el **Explorador de servidores** y haga clic con el botón derecho en la tabla **TollDataRefJoin**.
2. Seleccione **Mostrar datos de tabla** para ver el resultado del trabajo.
   
    ![Selección de Mostrar datos de tabla en el Explorador de servidores](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### <a name="view-the-job-metrics"></a>Ver las métricas de trabajo
En **Job Metrics** (Métricas de trabajo) se pueden encontrar algunas estadísticas básicas de trabajo. 

![Ventana Métricas de trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Mostrar el trabajo en el Explorador de servidores
En el **Explorador de servidores**, haga clic en **Trabajos de Stream Analytics** y, después, haga clic en **Actualizar**. El trabajo aparece en **Trabajos de Stream Analytics**.

![Los trabajos de Stream Analytics aparecen en el Explorador de servidores](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Abrir la vista de trabajo
Para abrir la vista de trabajos, expanda el nodo de trabajos y haga doble clic en el nodo **Vista de trabajos**.

![Nodo Vista de trabajos](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exportación de un trabajo existente a un proyecto
Hay dos maneras de exportar un trabajo existente a un proyecto.

En el **Explorador de servidores**, haga clic con el botón derecho en el nodo de trabajos en el nodo **Trabajos de Stream Analytics** y seleccione **Exportar a un nuevo proyecto de Stream Analytics**.

![Exportar a un nuevo proyecto de Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

El proyecto se genera en el **Explorador de soluciones**.

![Proyecto generado en el Explorador de soluciones](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
También puede usar la vista de trabajos y hacer clic en **Generar proyecto**.

![Generar el proyecto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="known-issues-and-limitations"></a>Problemas conocidos y limitaciones
 
- No hay ninguna compatibilidad para la salida de Power BI y la salida de Azure Date Lake Store.
- No hay ninguna compatibilidad con el editor para agregar o cambiar las funciones definidas por el usuario de JavaScript.

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

