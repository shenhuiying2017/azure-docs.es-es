---
title: Uso de las herramientas de Stream Analytics para Visual Studio para configurar el proceso de integración e implementación continuas | Microsoft Docs
description: Tutorial para el desarrollo de herramientas de Stream Analytics para Visual Studio con el fin de crear y depurar sus trabajos para dispositivos perimetrales de Stream Analytics.
keywords: visual studio, NuGet, DevOps, Edge jobs, Stream analytics
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
editor: ''
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/13/2018
ms.author: sujie
ms.openlocfilehash: 9362b201fbabc9f8f43647dfd8ac62986b5b6790
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="develop-stream-analytics-edge-jobs-by-using-visual-studio-tools"></a>Desarrollo de trabajos para dispositivos perimetrales de Stream Analytics mediante herramientas de Visual Studio

En este tutorial, aprenderá a usar herramientas de Stream Analytics para Visual Studio con el fin de crear y depurar sus trabajos para dispositivos perimetrales de Stream Analytics. Después de crear y probar el trabajo, puede ir a Azure Portal para implementarlo en los dispositivos. 

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, debe cumplir estos requisitos previos:

* Instalar [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) o [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Se admiten las ediciones Enterprise (Ultimate y Premium), Professional y Community. No se admite la edición Express.  

* Siga las [instrucciones de instalación](stream-analytics-tools-for-visual-studio-edge-jobs.md) para instalar las herramientas de Stream Analytics para Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Creación de un proyecto para dispositivos perimetrales de Stream Analytics 

En Visual Studio, seleccione **Archivo** > **Nuevo** > **Proyecto**. Vaya a la lista **Plantillas** de la izquierda y expanda **Azure Stream Analytics** > **Stream Analytics Edge** >  (Stream Analytics para dispositivos perimetrales) **Azure Stream Analytics Edge Application** (Aplicación de Stream Analytics para dispositivos perimetrales). Proporcione un nombre, una ubicación y una solución para el proyecto y seleccione **Aceptar**.

![Nuevo proyecto para dispositivos perimetrales](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

Una vez que se crea el proyecto, vaya al **Explorador de soluciones** para ver la jerarquía de carpetas.

![Vista del Explorador de soluciones](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Elegir la suscripción correcta

1. En el menú **Ver** de Visual Studio, seleccione **Explorador de servidores**.  

2. Haga clic con el botón derecho en **Azure**, seleccione **Connect to Microsoft Azure Subscription** (Conectarse a la suscripción de Microsoft Azure) e inicie sesión con su cuenta de Azure.

## <a name="define-inputs"></a>Definir las entradas

1. En el **Explorador de soluciones**, expanda el nodo **Entradas**; debería ver una entrada llamada **EdgeInput.json**. Haga doble clic para ver su configuración.  

2. Asegúrese de que el tipo de origen es **Flujo de datos**, el origen es **Centro de Microsoft Edge**, el formato de serialización de eventos es **Json** y la codificación es **UTF8**. Opcionalmente, puede cambiar el nombre del **Alias de entrada**, pero en este ejemplo lo dejaremos como está. En caso de cambiar el nombre del alias de entrada, use el nombre que especificó al definir la consulta. Haga clic en **Save** (Guardar) para guardar la configuración.  
   ![Entrada: configuración](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definir las salidas

1. En el **Explorador de soluciones**, expanda el nodo **Salidas**; debería ver una salida llamada **EdgeOutput.json**. Haga doble clic para ver su configuración.  

2. Asegúrese de que el receptor es **Centro de Microsoft Edge**, el formato de serialización es **Json**, la codificación es **UTF8** y el formato es **Matriz**. Opcionalmente, puede cambiar el nombre del **Alias de salida**, pero en este ejemplo lo dejaremos como está. En caso de cambiar el nombre del alias de salida, use el nombre que especificó al definir la consulta. Haga clic en **Save** (Guardar) para guardar la configuración. 
   ![Salida: configuración](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definir la consulta de transformación

Los trabajos de Stream Analytics implementados en entornos de dispositivos perimetrales admiten la mayoría de las [referencias del lenguaje de consulta de Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396); sin embargo, las siguientes operaciones no admiten aún trabajos para dispositivos perimetrales: 


|**Categoría**  | **Comando**  |
|---------|---------|
|Operadores de geoespaciales |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Otros operadores | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>DISTINCT</li><li>Parámetro de expresión en el operador COUNT</li><li>Microsegundos en funciones DATE y TIME</li><li>UDA de JavaScript (esta característica se encuentra aún en versión preliminar para los trabajos implementados en la nube)</li></ul>   |

Cuando se crea un trabajo para dispositivos perimetrales en el portal, el compilador le avisa automáticamente si no está usando un operador compatible.

En Visual Studio, defina la siguiente consulta de transformación en el editor de consultas (**archivo script.asaql**).

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Probar el trabajo localmente

Para probar la consulta localmente, debe cargar los datos de ejemplo. Para obtener datos de ejemplo, descargue los datos de registro del [repositorio de GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) y guárdelos en el equipo local. 

1. Para cargar datos de ejemplo, haga clic con el botón derecho en el archivo **EdgeInput.json** y elija **Agregar entrada local**.  

2. En la ventana emergente, haga clic en **Examinar** para buscar los datos de ejemplo en la ruta de acceso local y seleccione **Guardar**.
   ![Entrada local: configuración](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Un archivo denominado **local_EdgeInput.json** se agrega automáticamente a la carpeta de entradas.  
4. Puede ejecutarlo localmente o enviarlo a Azure. Para probar la consulta, seleccione **Ejecutar localmente**.  
   ![Opciones de ejecución](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. La ventana de símbolo del sistema muestra el estado del trabajo. Cuando el trabajo se ejecuta correctamente, se crea una carpeta similar a "2018-02-23-11-31-42" en la ruta de acceso de la carpeta del proyecto "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Vaya a la ruta de acceso de carpeta para ver los resultados de la carpeta local:

   También puede iniciar sesión en Azure Portal y comprobar que se ha creado el trabajo. 

   ![Carpeta de resultados](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Enviar el trabajo a Azure

1. Antes de enviar el trabajo a Azure, debe conectarse a su suscripción de Azure. Abra el **Explorador de servidores**, haga clic con el botón derecho en **Azure** > **Connect to Microsoft Azure subscription** (Conectarse a suscripción de Microsoft Azure) e inicie sesión en su suscripción de Azure.  

2. Para enviar el trabajo a Azure, vaya al editor de consultas y seleccione **Enviar a Azure**.  

3. Se abre una ventana emergente, donde puede elegir actualizar un trabajo de para dispositivos perimetrales existente o crear uno nuevo. Cuando se actualiza un trabajo existente, se sustituye toda la configuración del trabajo; en este escenario, publicará un nuevo trabajo. Seleccione **Create a New Azure Stream Analytics Job** (Crear un nuevo trabajo de Azure Stream Analytics), escriba un nombre para el trabajo (algo como **MyASAEdgeJob**), elija los valores adecuados de **Suscripción**, **Grupo de recursos** y **Ubicación** y seleccione **Enviar**.

   ![Enviar a Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   Ahora se ha creado el trabajo para dispositivos perimetrales de Stream Analytics; para saber cómo implementarlo en sus dispositivos, puede consultar el [tutorial de ejecución de trabajos en IoT Edge](stream-analytics-edge.md). 

## <a name="manage-the-job"></a>Administrar el trabajo 

Puede ver el estado del trabajo y el diagrama de trabajos desde el Explorador de servidores. En el **Explorador de servidores** > **Stream Analytics**, expanda la suscripción y el grupo de recursos donde implementó el trabajo para dispositivos perimetrales; puede ver que MyASAEdgejob tiene el estado **Creado**. Expanda el nodo de trabajos y haga doble clic en él para abrir la vista de trabajos.

![Opciones del Explorador de servidores](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
La ventana de vista de trabajos proporciona operaciones como actualizar el trabajo, eliminar el trabajo, abrir el trabajo desde Azure Portal, etc.

![Diagrama de trabajos y otras opciones](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Pasos siguientes

* [Más información acerca de Azure IoT Edge](../iot-edge/how-iot-edge-works.md)
* [Tutorial de ASA en IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Enviar comentarios al equipo mediante esta encuesta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
