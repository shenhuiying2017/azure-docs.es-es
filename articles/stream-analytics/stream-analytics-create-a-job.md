<properties 
	pageTitle="Crear un trabajo | Microsoft Azure" 
	description="Cree un segmento de ruta de aprendizaje de trabajo."
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/29/2015" 
	ms.author="jeffstok"/>

# Crear un trabajo

El recurso de nivel superior en el Análisis de transmisiones de Azure es un trabajo de análisis de transmisiones. Consta de uno o varios orígenes de datos de entrada, una consulta que expresa la transformación de datos y uno o más destinos de salida en los que se escriben los resultados.

Para empezar usando el Análisis de transmisiones, cree un nuevo trabajo de Análisis de transmisiones. Tenga en cuenta que esta acción no tiene ninguna implicación de facturación hasta que se inicie el trabajo.

1.  Inicie sesión en el [Portal de Microsoft Azure](http://manage.windowsazure.com) en línea o en el Portal de vista previa de Azure.
2.  En el portal de Azure: haga clic en **Nuevo**, en **Servicios de datos** y luego en **Análisis de transmisiones** y **Creación rápida**.

    ![Asistente para la creación rápida](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

    En el Portal de vista previa de Azure: haga clic en Nuevo, en Datos y análisis y luego en Análisis de transmisiones de Azure.

    ![Portal de Azure - Crear trabajo](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)

3.  Especifique la configuración que desee para el trabajo de Análisis de transmisiones.
	- En el cuadro **Nombre del trabajo**, escriba un nombre para identificar el trabajo de Análisis de transmisiones. Cuando la información de **Nombre del trabajo** se valida, aparece una marca de verificación verde en el cuadro Nombre de trabajo. **Nombre del trabajo** solo puede contener caracteres alfanuméricos y el carácter "-", y debe tener entre 3 y 63 caracteres.
	- Utilice **Región** en el Portal de Azure o **Ubicación** en el Portal de vista previa de Azure para especificar la ubicación geográfica en la que desea ejecutar el trabajo.
	- Si utiliza el Portal de Azure, seleccione o cree una cuenta de almacenamiento para usar **Cuenta de almacenamiento de supervisión regional**. Esta cuenta de almacenamiento se usa para almacenar datos de supervisión para todos los trabajos de Análisis de transmisiones que se ejecutan dentro de esta región.
	- Si utiliza el Portal de vista previa de Azure, especifique un **Grupo de recursos** nuevo o existente para hospedar recursos relacionados para la aplicación.

4.  Cuando se configuren las nuevas opciones del trabajo de Análisis de transmisiones, haga clic en **Crear trabajo de Análisis de transmisiones**. La creación del trabajo del Análisis de transmisiones puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el centro de notificaciones.

    ![Centro de notificaciones](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

    ![Portal de vista previa de Azure - Crear trabajo](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)

5.  Se mostrará el nuevo trabajo con el estado **Creado**. Tenga en cuenta que el botón **Iniciar** está deshabilitado. Debe configurar la entrada, la salida y la consulta del trabajo para poder iniciar el trabajo.

    ![Estado del trabajo](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

    ![Portal de vista previa de Azure - Estado del trabajo](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO3-->