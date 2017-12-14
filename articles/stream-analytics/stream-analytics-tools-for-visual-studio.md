---
title: Uso de las herramientas de Azure Stream Analytics para Visual Studio | Microsoft Docs
description: "Tutorial de introducción de las herramientas de Azure Stream Analytics para Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
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
ms.openlocfilehash: b06eae6b85f2ca41390955cde2499af2531e2e12
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Uso de herramientas de Azure Stream Analytics para Visual Studio
Las herramientas de Azure Stream Analytics para Visual Studio ahora están disponibles con carácter general. Estas herramientas permiten una experiencia más enriquecedora para los usuarios de Stream Analytics a la hora de solucionar problemas, de escribir consultas complejas e, incluso, de escribir consultas localmente. También tendrá la posibilidad de exportar un trabajo de Stream Analytics a un proyecto de Visual Studio.

## <a name="introduction"></a>Introducción
En este tutorial, aprenderá a usar herramientas de Azure Stream Analytics para Visual Studio y así poder crear, probar localmente, administrar y depurar los trabajos de Stream Analytics. 

Después de completar este tutorial, estará capacitado para lo siguiente:

* Familiarícese con las herramientas de Stream Analytics para Visual Studio.
* Configurar e implementar un trabajo de Stream Analytics.
* Probar el trabajo localmente con datos de ejemplo local.
* Usar la supervisión para solucionar problemas.
* Exportar los trabajos existentes a proyectos.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, debe cumplir estos requisitos previos:

* Complete todos los pasos hasta llegar al paso "Crear un trabajo de Stream Analytics" del tutorial [Compilación de una solución de IoT con Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Instale Visual Studio 2017, Visual Studio 2015 o Visual Studio 2013 Update 4. Se admiten las ediciones Enterprise (Ultimate y Premium), Professional y Community. No se admite la edición Express. 
* Siga las [instrucciones de instalación](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) para instalar las herramientas de Stream Analytics para Visual Studio.

## <a name="create-a-stream-analytics-project"></a>Creación de un trabajo de Stream Analytics
En Visual Studio, seleccione **Archivo** > **Nuevo proyecto**. En la lista de plantillas de la izquierda, seleccione **Stream Analytics** y **Aplicación de Azure Stream Analytics**.
En la parte inferior de la página, escriba el **nombre**, la **ubicación** y la **solución** del proyecto, tal y como suele hacer en otros proyectos.

![Crear un proyecto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

El proyecto denominado **Toll** se genera en el **Explorador de soluciones**.

![Proyecto Toll del Explorador de soluciones](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Elegir la suscripción correcta
1. En el menú **Ver**, seleccione el **Explorador de servidores** en Visual Studio.

2. Inicie sesión con su cuenta de Azure. 

## <a name="define-input-sources"></a>Definición de orígenes de entrada
1. En el **Explorador de soluciones**, expanda el nodo **Inputs** y cambie el nombre de **Input.json** a **EntryStream.json**. Haga doble clic en **EntryStream.json**.

2. Escriba **EntryStream** como **Alias de entrada**. Recuerde que el alias de entrada se usa en el script de consulta.

3. En **Tipo de origen**, seleccione **Flujo de datos**.

4. En **Origen**, seleccione **Centro de eventos**.

5. En **Espacio de nombres de Service Bus**, seleccione la opción **TollData** de la lista desplegable.

6. En **Nombre del centro de eventos**, seleccione **entrada**.

7. En **Nombre de la directiva del centro de eventos**, seleccione **RootManageSharedAccessKey** (el valor predeterminado).

8. En **Formato de serialización de eventos**, seleccione **JSON** y en **Codificación**, seleccione **UTF8**.
   
   La configuración debe tener el aspecto siguiente:
   
   ![Configuración de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. Haga clic en **Guardar** en la parte inferior de la página para finalizar al asistente. Ahora puede agregar otra fuente de entrada para crear la secuencia de salida. Haga clic con el botón derecho en el nodo **Inputs** y seleccione **Nuevo elemento**.
   
   ![Nuevo elemento](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. En la ventana emergente, elija **Stream Analytics Input (Entrada de Stream Analytics** y cambie el **nombre** a **ExitStream.json**. Seleccione **Agregar**.
   
    ![Agregar un nuevo elemento](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Haga doble clic en **ExitStream.json** en el proyecto y siga los mismos pasos que en el flujo de entrada para rellenar los campos. Asegúrese de especificar **exit** en el **Nombre del centro de eventos**, tal como se muestra en la siguiente captura de pantalla:
   
    ![Configuración de ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Ahora ha definido dos flujos de entrada.
   
   ![Dos flujos de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   A continuación, agregue la entrada de datos de referencia del archivo de blob que contiene los datos de registro de los vehículos.
   
12. Haga clic con el botón derecho en el nodo **Inputs** del proyecto y siga los mismos pasos que realizó para las entradas de secuencias. En **Tipo de origen**, seleccione **Datos de referencia** y, en **Alias de entrada**, escriba **Registro**.
   
    ![Configuración de registro](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Seleccione la cuenta **Almacenamiento** que contenga la opción con **TollData**. El nombre del contenedor debe ser **TollData** y el **Patrón de ruta de acceso** debe ser **registration.json**. Este nombre de archivo distingue mayúsculas de minúsculas, por lo que asegúrese de escribirlo en minúsculas.

14. Seleccione **Guardar** para finalizar el asistente.

Ahora todas las entradas están definidas.

## <a name="define-output"></a>Defininición de salida
1. En el **Explorador de soluciones**, expanda el nodo **Inputs** y haga doble clic en **Output.json**.

2. En **Alias de salida**, escriba **salida**. En **Receptor**, seleccione **SQL Database**.

3. En **Base de datos**, escriba **TollDataDB**.

4. En **Nombre de usuario**, escriba **tolladmin**. En **Contraseña**, escriba **123toll!**. En **Tabla**, escriba **TollDataRefJoin**.

5. Seleccione **Guardar**.

   ![Configuración de salida](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Consulta de Stream Analytics
Este tutorial intenta responder varias preguntas de empresa que están relacionadas con los datos de peaje. Hemos creado consultas que se pueden usar en Stream Analytics para proporcionar respuestas pertinentes. Antes de iniciar el primer trabajo de Stream Analytics, veamos un escenario sencillo y la sintaxis de la consulta.

### <a name="introduction-to-stream-analytics-query-language"></a>Introducción al lenguaje de consulta de Stream Analytics
Supongamos que necesita contar el número de vehículos que entran en una cabina de peaje. Como se trata de una secuencia continua de eventos, tendrá que definir un "período de tiempo". Vamos a cambiar la pregunta a "¿Cuántos vehículos entran en una cabina de peaje cada tres minutos?". Esto se conoce comúnmente como "cuenta de saltos de tamaño constante".

Veamos la consulta de Stream Analytics que responde a esta pregunta:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Como puede ver, Stream Analytics usa un lenguaje de consulta que es similar a SQL. Además, agrega algunas extensiones para especificar aspectos relacionados con el tiempo de la consulta.

Para obtener más información, puede leer sobre las construcciones de [Administración del tiempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) y [Ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx) que se usan en una consulta en MSDN.

Ahora que hemos escrito nuestra primera consulta de Stream Analytics, es momento de probarla usando los archivos de datos de ejemplo ubicados en la carpeta TollApp en la siguiente ruta:

**..\TollApp\TollApp\Data**

Esta carpeta contiene los archivos siguientes:

* Entry.json
* Exit.json
* registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Pregunta: Número de vehículos que entran en una cabina de peaje
En el proyecto, haga doble clic en **Script.asaql** para abrir el script en el editor. Pegue el script de la sección anterior del editor. El editor de consultas admite IntelliSense, colores de sintaxis y marcador de errores.

![Editor de consultas](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Probar las consultas de Stream Analytics localmente
Primero puede compilar la consulta para ver si hay algún error de sintaxis.

1. Para validar esta consulta mediante los datos de ejemplo, puede usar datos de ejemplo locales haciendo clic con el botón derecho en la entrada y seleccionando **Agregar entrada local**.
   
   ![Agregar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. En la ventana emergente, seleccione los datos de ejemplo de la ruta de acceso local. Seleccione **Guardar**.
   
   ![Agregar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Un archivo denominado **local_EntryStream.json** se agrega automáticamente a la carpeta de entradas.
   
   ![Lista de archivos de la carpeta de entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. En el editor de consultas, haga clic en **Ejecutar localmente**. O bien, puede presionar F5.
   
   ![Ejecución en modo local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Puede encontrar la ruta de acceso de salida en la salida de la consola. Presione cualquier tecla para abrir la carpeta de resultados.
   
   ![Ejecución local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Compruebe los resultados en la carpeta local.
   
   ![Resultado de la carpeta local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Entrada de ejemplo
Asimismo, también puede usar los datos de entrada de los orígenes de entrada como ejemplo en un archivo local. Haga clic con el botón derecho en el archivo de configuración de entrada y seleccione **Datos de ejemplo**. 

![Datos de ejemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Tenga en cuenta que por el momento solo puede usar como ejemplo el centro de eventos o IoT Hub. No se admiten otros orígenes de entrada. En el cuadro de diálogo emergente, escriba la ruta de acceso local que se ha usado para guardar los datos de ejemplo. Seleccione **Ejemplo**.

![Configuración de los datos de ejemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Puede ver el progreso en la ventana **Salida**. 

![Salida de los datos de ejemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Enviar una consulta de Stream Analytics a Azure
1. En el **Editor de consultas**, haga clic en **Enviar a Azure** en el editor de scripts.

   ![Enviar a Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Seleccione **Crear un trabajo de Azure Stream Analytics**. En el **nombre del trabajo**, escriba **TollApp**. Elija la **suscripción** correcta de la lista desplegable. Seleccione **Submit** (Enviar).

   ![Enviar el trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>Inicio del trabajo
Ahora que se ha creado el trabajo, se abre automáticamente la vista de trabajo. 
1. Para iniciar el trabajo, seleccione el botón de la flecha verde.

   ![Botón "Iniciar trabajo"](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Elija la configuración predeterminada y haga clic en **Iniciar**.
 
   ![Iniciar trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   Puede ver que el estado del trabajo ha cambiado a **Ejecutándose** y que hay eventos de entrada y salida.

   ![Métricas y resumen del trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Comprobación de resultados en Visual Studio
1. Abra el Explorador de servidores de Visual Studio y haga clic con el botón derecho en la tabla **TollDataRefJoin** .

2. Seleccione **Mostrar datos de tabla** para ver el resultado del trabajo.
   
   ![Mostrar los datos de tabla](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Visualización de métricas de trabajo
En **Métricas de trabajo** se pueden encontrar algunas estadísticas básicas del trabajo. 

![Métricas de trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Mostrar el trabajo en el Explorador de servidores
En el **Explorador de servidores**, haga clic en **Trabajos de Stream Analytics** y seleccione **Actualizar**. El trabajo aparece en **Trabajos de Stream Analytics**.

![Lista de trabajos](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Abrir la vista de trabajo
Expanda el nodo de trabajos y haga doble clic en el nodo **Vista de trabajos** para abrir la vista de trabajos.

![Vista de trabajo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exportación de un trabajo existente a un proyecto
Hay dos maneras de exportar un trabajo existente a un proyecto.
* En el **Explorador de servidores** en el nodo **Trabajos de Stream Analytics**, haga clic con el botón derecho en el nodo del trabajo. Seleccione **Exportar a un nuevo proyecto de Stream Analytics**.
   
   ![Exportar a un nuevo proyecto de Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   El proyecto generado aparecerá en el **Explorador de soluciones**.
   
    ![Trabajo del Explorador de soluciones](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* En la vista de trabajos, seleccione **Generar proyecto**.
   
   ![Generar el proyecto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Problemas conocidos y limitaciones
 
* Las pruebas locales no funcionan si la consulta tiene funciones geoespaciales.
* El editor no admite las opciones de agregar o cambiar funciones definidas por el usuario de JavaScript.
* Las pruebas locales no admiten la opción de guardar la salida en formato JSON. 
* No hay compatibilidad con las salidas de Power BI y de ADLS.



## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-get-started.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


