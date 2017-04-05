---
title: Uso de herramientas de Azure Stream Analytics para Visual Studio | Microsoft Docs
description: "Tutorial de introducción de las herramientas de Azure Stream Analytics para Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sujie
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d0125dda4df69279e49a9fad4dc28dcbf6368322
ms.lasthandoff: 03/29/2017


---
# <a name="use-azure-stream-analytics-tool-for-visual-studio"></a>Uso de herramientas de Azure Stream Analytics para Visual Studio
Las herramientas de Azure Stream Analytics para Visual Studio ahora están disponibles con carácter general. Estas herramientas permiten una experiencia más enriquecedora para el usuario de Stream Analytics a la hora de solucionar problemas, de escribir consultas complejas e, incluso, de escribir consultas localmente. También tendrá la posibilidad de exportar un trabajo de Stream Analytics a un proyecto de Visual Studio.

## <a name="introduction"></a>Introducción
En este tutorial, aprenderá a usar herramientas de Azure Stream Analytics para Visual Studio para crear, probar localmente, administrar y depurar los trabajos de Azure Stream Analytics. 

Después de completar este tutorial, estará capacitado para lo siguiente:
* Familiarícese con las herramientas de Azure Stream Analytics para Visual Studio.
* Configurar e implementar un trabajo de Stream Analytics.
* Probar el trabajo localmente con datos de ejemplo local.
* Usar la supervisión para solucionar problemas.
* Exportar los trabajos existentes a proyectos.

## <a name="prerequisites"></a>Requisitos previos
Para realizar este tutorial, deberá cumplir los siguientes requisitos previos:
* Completar los pasos anteriores a la sección **Creación de un trabajo de Stream Analytics** del tutorial [Compilación de una solución de IoT con Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Visual Studio 2015, Visual Studio 2013 Update 4 o Visual Studio 2012. Se admiten las ediciones Enterprise (Ultimate y Premium), Professional, Community; no se admite la edición Express. Visual Studio 2017 no se admite actualmente. 
* SDK de Microsoft Azure para .NET versión 2.7.1 o posterior.  Instálelo usando el [instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).
* Instalación de las [herramientas de Azure Stream Analytics para Visual Studio](http://aka.ms/asatoolsvs).

## <a name="create-a-stream-analytics-project"></a>Creación de un trabajo de Stream Analytics
En Visual Studio, haga clic en el menú **Archivo** y elija **Nuevo proyecto** . Elija **Stream Analytics** en la lista de plantillas de la izquierda y luego haga clic en **Azure Stream Analytics Application** (Aplicación de Azure Stream Analytics).
Escriba los nombres que estime oportuno en Nombre de proyecto, Ubicación y Nombre de la solución en la parte inferior tal y como hace para otros proyectos.

![Creación de un proyecto de Azure Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Verá un proyecto **Toll** generado en el **Explorador de soluciones**.

![Creación de un proyecto de Azure Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Elección de la suscripción correcta
1. Abra el **Explorador de servidores** en Visual Studio desde el menú **Ver**.
2. Inicie sesión con su cuenta de Azure. 

## <a name="define-input-sources"></a>Definición de orígenes de entrada
1.    En el **Explorador de soluciones**, expanda el nodo **Inputs** y cambie el nombre de **Input.json** a **EntryStream.json**. Haga doble clic en **EntryStream.json**.
2.    Su **ALIAS DE ENTRADA** ahora debe ser **EntryStream**. Tenga en cuenta que el alias de entrada es el que se usará en el script de consulta. 
3.    El tipo de origen es **Flujo de datos**.
4.    El origen es **Centro de eventos**.
5.    El espacio de nombre de Service Bus debe ser **tollData** en la lista desplegable.
6.    El nombre del centro de eventos se debe establecer en **entrada**.
7.    El nombre de la directiva del centro de eventos es **RootManageSharedAccessKey** (el valor predeterminado).
8.    Seleccione **JSON** para **FORMATO DE SERIALIZACIÓN DE EVENTOS** y **UTF8** para **CODIFICACIÓN**.
   
   La configuración se verá así:
   
   ![Definición de orígenes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9.    Haga clic en **Guardar** en la parte inferior de la página para finalizar al asistente. Ahora puede agregar otra fuente de entrada para crear la secuencia de salida. Haga clic con el botón derecho en el nodo Inputs y haga clic en **Nuevo elemento**.
   
   ![Definición de orígenes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10.    En la ventana emergente, elija **Azure Stream Analytics Input** (Entrada de Azure Stream Analytics) y cambie el nombre a **ExitStream.json**. Haga clic en **Agregar**.
   
   ![Definición de orígenes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11.    Haga doble clic en **ExitStream.json** en el proyecto y siga los mismos pasos que para el flujo de entrada para rellenar la información. Asegúrese de especificar los valores para Nombre del centro de eventos tal y como se indica en la siguiente captura de pantalla.
   
   ![Definición de orígenes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Ahora ha definido dos flujos de entrada.
   
   ![Definición de orígenes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   A continuación, agregaremos la entrada de datos de referencia para el archivo de blob que contiene los datos de registro de los vehículos.
   
12.    Haga clic con el botón derecho en el nodo **Inputs** en el proyecto y luego siga el mismo procedimiento para las entradas del flujo, pero seleccione **DATOS DE REFERENCIA** en lugar de Flujo de datos y, para Alias de entrada, elija **Registro**.
   
   ![Definición de orígenes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13.    Seleccione la cuenta de almacenamiento que contiene **tolldata**. El nombre del contenedor debe ser **tolldata** y el **PATRÓN DE RUTA DE ACCESO** debe ser **registration.json**. Este nombre de archivo distingue mayúsculas de minúsculas, por lo que debe asegurarse de escribirlo en minúsculas.
14.    Haga clic en **Guardar** para finalizar el asistente.

Ahora todas las entradas están definidas.

## <a name="define-output"></a>Defininición de salida
1.    En **Explorador de soluciones**, expanda el nodo **Inputs** y haga doble clic en **Output.json**.
2.    Establezca Alias de salida en **salida** y luego Receptor en SQL Database.
2.    Escriba el nombre de base de datos **TollDataDB**.
3.    Escriba **tolladmin** en el campo **NOMBRE DE USUARIO**, **123toll!** en el campo **CONTRASEÑA** y **TollDataRefJoin** en el campo **TABLA**.
4.    Haga clic en **Guardar**.

![Defininición de salida](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="azure-stream-analytics-query"></a>Consulta de Azure Stream Analytics
En este tutorial intentaremos responder a varias cuestiones empresariales relacionadas con datos de peaje y construiremos consultas de Stream Analytics que puedan usarse en Azure Stream Analytics para dar una respuesta adecuada.
Antes de iniciar el primer trabajo de Stream Analytics, veamos unos escenarios sencillos y la sintaxis de consulta.

### <a name="introduction-to-azure-stream-analytics-query-language"></a>Introducción al lenguaje de consulta de Azure Stream Analytics
Supongamos que necesita contar el número de vehículos que entran en una cabina de peaje. Como se trata de una secuencia continua de eventos, tendrá que definir un "período de tiempo". Vamos a cambiar la pregunta a "¿Cuántos vehículos entran en una cabina de peaje cada tres minutos?". Esto se conoce comúnmente como "tumbling count".

Veamos la consulta de Azure Stream Analytics que responde a esta pregunta:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Como puede ver, Azure Stream Analytics usa un lenguaje de consulta que es similar a SQL y agrega algunas extensiones para especificar aspectos de la consulta relacionados con el tiempo.

Para más información, puede leer sobre las construcciones de [Administración del tiempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) y [Ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx) que se usan en una consulta en MSDN.

Ahora que hemos escrito nuestra primera consulta de Azure Stream Analytics, es momento de probarla usando los archivos de datos de ejemplo ubicados en la carpeta TollApp en la siguiente ruta:

<seg>
  **..\TollApp\TollApp\Data**</seg>

Esta carpeta contiene los archivos siguientes: •   Entry.json •   Exit.json •   Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Pregunta: Número de vehículos que entran en una cabina de peaje
En el proyecto, haga doble clic en Script.asaql para abrir el script en el editor y pegue el script de la sección anterior en el editor. El editor de consultas admite Intellisense, colores para la sintaxis y marcador de errores.

![Editar consulta](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="testing-azure-stream-analytics-queries-locally"></a>Pruebas de consultas de Azure Stream Analytics localmente

1. Primero puede compilar la consulta para ver si hay algún error de sintaxis. [TBD]
2. Para validar esta consulta con los datos de ejemplo, puede usar datos de ejemplo local haciendo clic con el botón derecho en la entrada y seleccionando **Agregar entrada local** en el menú contextual.
   
   ![Agregar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
   En la ventana emergente, seleccione los datos de ejemplo de la ruta de acceso local. Haga clic en **Guardar**.
   
   ![Agregar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Un archivo denominado **local_EntryStream.json** se agregará automáticamente a la carpeta Inputs.
   
   ![Agregar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Haga clic en Ejecutar localmente en el editor de consultas. O bien, puede presionar F5.
   
   ![Ejecución local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Puede encontrar la ruta de acceso de salida de la salida de la consola y presionar cualquier tecla para abrir la carpeta de resultados.
   
   ![Ejecución local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Compruebe el resultado en la carpeta local.
   
   ![Ejecución local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Entrada de ejemplo
También puede muestrear los datos de entrada de orígenes de entrada en el archivo local. Haga clic con el botón derecho en el archivo de configuración de entrada y seleccione **Datos de ejemplo**. 

![Datos de ejemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Tenga en cuenta que, por ahora, solo puede muestrear el Centro de eventos o IoT Hub. No se admiten otros orígenes de entrada.  En la ventana emergente, rellene la ruta de acceso local para guardar los datos de ejemplo. Haga clic en **Ejemplo**.

![Datos de ejemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Puede ver el progreso en la ventana Salida. 

![Datos de ejemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-azure-stream-analytics-query-to-azure"></a>Envío de la consulta de Azure Stream Analytics a Azure
En **Editor de consultas**, haga clic en **Submit To Azure** (Enviar a Azure) en el editor de scripts.

![Enviar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
Elija Create a New Azure Stream Analytics Job (Crear un nuevo trabajo de Azure Stream Analytics). Escriba el nombre del trabajo como se indica a continuación. Elija la suscripción correcta. Haga clic en Enviar.

![Enviar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-job"></a>Iniciar trabajo
Ahora se habrá creado el trabajo y la vista de trabajos se abrirá automáticamente. Haga clic en el botón **VERDE** para iniciar el trabajo.

![Iniciar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
Elija la configuración predeterminada y haga clic en **Iniciar**.
 
![Iniciar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

Puede ver que el estado del trabajo ha cambiado a **Ejecutándose** y hay eventos de entrada y salida.

![Iniciar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Comprobación de resultados en Visual Studio
1. Abra el Explorador de servidores de Visual Studio y haga clic con el botón derecho en la tabla **TollDataRefJoin** .
2. Seleccione **Mostrar datos de tabla** para ver el resultado del trabajo.
   
   ![Selección de "Mostrar datos de tabla" en el Explorador de servidores](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Visualización de métricas de trabajo
En **Job Metrics** (Métricas de trabajo) se pueden encontrar algunas estadísticas básicas de trabajo. 

![Métricas de trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-job-in-server-explorer"></a>Enumeración de trabajos en el Explorador de servidores
Haga clic en **Trabajos de Stream Analytics** en el **Explorador de servidores** y haga clic en **Actualizar**. El trabajo debe aparecer en **Trabajos de Stream Analytics**.

![Enumeración de trabajos](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-job-view"></a>Apertura de la vista de trabajos
Expanda el nodo de trabajos y haga doble clic en el nodo **Vista de trabajos** para abrir la vista de trabajos.

![Vista de trabajos](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exportación de un trabajo existente a un proyecto
Hay dos maneras de exportar un trabajo existente a un proyecto.
1. Haga clic con el botón derecho en el nodo de trabajos en el nodo **Trabajos de Stream Analytics** en el **Explorador de servidores**. Haga clic en **Export to New Stream Analytics Project** (Exportar a nuevo proyecto de Stream Analytics) en el menú contextual.
   
   ![Exportar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   Verá el proyecto generado en el **Explorador de soluciones**.
   
   ![Exportar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
2. En la vista de trabajos, haga clic en **Generate Project** (Generar proyecto).
   
   ![Exportar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Problemas y limitaciones conocidos
 
1. Las pruebas locales no funcionan si la consulta tiene funciones geoespaciales. 
2. No hay compatibilidad con el editor para agregar o cambiar funciones definidas por el usuario de JavaScript.
3. Las pruebas locales no admiten el almacenamiento de la salida en formato JSON. 
4. No hay compatibilidad para la salida de Power BI y la salida ADLS.



## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)



