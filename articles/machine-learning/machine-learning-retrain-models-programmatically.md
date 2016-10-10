<properties
	pageTitle="Volver a entrenar modelos de aprendizaje automático mediante programación | Microsoft Azure"
	description="Obtenga información sobre cómo reciclar un modelo y actualizar el servicio web mediante programación para utilizar el modelo recién entrenado en Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2016"
	ms.author="raymondl;garye;v-donglo"/>


#Volver a entrenar modelos de aprendizaje automático mediante programación  

Como parte del proceso de operacionalización de modelos de Aprendizaje automático de Azure, el modelo se debe entrenar y guardar. Posteriormente, podrá usarlo para crear un servicio web predicativo. A continuación, el servicio web se puede consumir en sitios web, paneles y aplicaciones móviles.

Los modelos que crea mediante Aprendizaje automático no suelen ser estáticos. Cuando hay nuevos datos disponibles o cuando el consumidor de la API tiene sus propios datos, el modelo debe volver a entrenarse. O bien, puede que necesite aplicar filtros para obtener un subconjunto de los datos y volver a entrenar el modelo.

El reentrenamiento puede producirse con frecuencia. Con la característica de la API de reentrenamiento mediante programación, puede reciclar el modelo mediante programación con las API de reentrenamiento y actualizar el servicio web con el modelo recién entrenado.

Este documento describe el proceso anterior y muestra cómo usar las API de reentrenamiento.

## Por qué volver a entrenar: definición del problema  

Como parte del proceso de entrenamiento de aprendizaje automático, un modelo se entrena usando un conjunto de datos. Los modelos que crea mediante Aprendizaje automático no suelen ser estáticos. Cuando hay nuevos datos disponibles o cuando el consumidor de la API tiene sus propios datos, el modelo debe volver a entrenarse. Otros escenarios adicionales pueden obligarle a aplicar filtros para obtener un subconjunto de los datos y volver a entrenar el modelo.

En estos casos, una API de programación proporciona una manera cómoda de permitirle a usted o al consumidor de sus API crear un cliente que pueda, en un momento determinado o de forma habitual, volver a entrenar un modelo usando sus propios datos. A continuación, puede evaluar los resultados del reentrenamiento y actualizar la API del servicio web para utilizar el modelo recién entrenado.

##Cómo volver a entrenar: el proceso completo  

Para empezar, el proceso engloba los siguientes componentes: un experimento de entrenamiento y un experimento predictivo publicados como un servicio web. Para habilitar el reentrenamiento de un modelo entrenado, el experimento de entrenamiento debe publicarse como servicio web con el resultado de un modelo entrenado. Esto permite a la API obtener acceso al modelo para el nuevo entrenamiento.

El proceso de configuración de reentrenamiento de un servicio web clásico implica los pasos siguientes:

![Descripción del proceso de reentrenamiento][1]

Diagrama 1: Información general sobre el proceso de reciclaje para un servicio web clásico

El proceso de configuración de reciclaje de un servicio web nuevo implica los pasos siguientes:

![Descripción del proceso de reentrenamiento][7]

Diagrama 2: Información general sobre el proceso de reciclaje para un servicio web nuevo

## Creación de un experimento de entrenamiento
 
En este ejemplo, usará "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" (Ejemplo 5: Entrenar, probar, evaluar para clasificación binaria: Conjunto de datos para adultos) de entre los ejemplos de Aprendizaje automático de Microsoft Azure.
	
Para crear el experimento:

1.	Inicie sesión en Estudio de aprendizaje automático de Microsoft Azure.
2.	En la esquina inferior derecha del panel, haga clic en **Nuevo**.
3.	En los ejemplos de Microsoft, seleccione el ejemplo 5.
4.	Para cambiar el nombre del experimento, en la parte superior del lienzo del experimento, seleccione el nombre del experimento, en este caso "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset".
5.	Escriba Census Model (Modelo de censo).
6.	En la parte inferior del lienzo del experimento, haga clic en **Ejecutar**.
7.	Haga clic en **Set Up Web Service** (Configurar servicio web) y seleccione **Retraining Web Service** (Reciclaje del servicio web).

 	![Experimento inicial.][2]

Diagrama 2: Experimento inicial.

## Crear un experimento de puntuación y publicarlo como servicio web  

A continuación creará un experimento predicativo.

1.	En la parte inferior del lienzo del experimento, haga clic en **Set Up Web Service** (Configurar servicio web) y seleccione **Predictive Web Service** (Servicio web predictivo). Esto permite guardar el modelo como un modelo entrenado y agrega los módulos de entrada y salida de servicio web.
2.	Haga clic en **Ejecutar**.
3.	Cuando el experimento haya terminado de ejecutarse, haga clic en **Deploy Web Service [Classic]** (Implementar un servicio web [clásico]) y seleccione **Deploy Web Service [New]** (Implementar un servicio web [nuevo]).

## Implementación del experimento de entrenamiento como un servicio web de entrenamiento

Para reciclar el modelo entrenado, debe implementar el experimento de entrenamiento que creó como un servicio web de reciclaje. Este servicio necesita un módulo de *salida del servicio web* conectado al módulo *[Entrenar modelo][train-model]* para poder generar nuevos modelos entrenados.

1. Para volver al experimento de entrenamiento, haga clic en el icono Experimentos en el panel izquierdo, y luego haga clic en el experimento denominado Census Model (Modelo de censo).
2. En el cuadro de búsqueda Search Experiment Items (Buscar elementos de experimentos), escriba servicio web.
3. Arrastre un módulo de *entrada de servicio web* al lienzo de experimentos y conecte la salida al módulo *Limpiar datos que faltan*.
4. Arrastre dos módulos de *salida de servicio web* al lienzo de experimentos. Conecte la salida del módulo *Entrenar modelo* a uno y la salida del módulo *Evaluar modelo* al otro. La salida del servicio web para **Entrenar modelo** nos ofrecerá el nuevo modelo entrenado. La salida vinculada a **Evaluar modelo** devuelve el resultado del módulo.
5. Haga clic en **Ejecutar**.

Después debe implementar el experimento de entrenamiento como un servicio web que genera un modelo entrenado y resultados de evaluación del modelo. Para lograr esto, el siguiente conjunto de acciones depende de si está trabajando con un servicio web clásico o un servicio web nuevo.
  
**Servicio web clásico**

En la parte inferior del lienzo del experimento, haga clic en **Set Up Web Service** (Configurar servicio web) y seleccione **Deploy Web Service [Classic]** (Implementar servicio web [clásico]). Aparecerá el **panel** del servicio web con la clave de API y la página de Ayuda de API para la ejecución por lotes. Solo se puede usar el método de ejecución por lotes para crear modelos entrenados.

**Servicio web nuevo**

En la parte inferior del lienzo del experimento, haga clic en **Set Up Web Service** (Configurar servicio web) y seleccione **Deploy Web Service [New]** (Implementar servicio web [nuevo]). El portal de servicios web de Azure Machine Learning se abre en la página Deploy Web service (Implementar servicio web). Escriba un nombre para el servicio web y elija un plan de pago y después haga clic en **Implementar**. Solo se puede usar el método de ejecución de lotes para crear modelos entrenados.

En cualquier caso, una vez que el experimento ha finalizado su ejecución, el flujo de trabajo resultante debe ser el siguiente:

![Flujo de trabajo resultante después de la ejecución.][4]

Diagrama 3: Flujo de trabajo resultante después de la ejecución.

## Reentrenamiento del modelo con nuevos datos mediante BES

Para llamar a las API de reentrenamiento:

1. Cree una nueva aplicación de consola C# en Visual Studio (Nuevo -> Proyecto -> Windows Desktop -> Aplicación de consola).
2.	Inicie sesión en el portal de servicio web Machine Learning.
3.	Si está trabajando con un servicio web clásico, haga clic en **Classic Web Services** (Servicios web clásicos).
	1.	Haga clic en el servicio web con el que está trabajando.
	2.	Haga clic en el punto de conexión predeterminado.
	3.	Haga clic en **Consume** (Consumo).
	4.	En la parte inferior de la página **Consumir**, en la sección **Código de ejemplo**, haga clic en **Lote**.
	5.	Vaya al paso 5 de este procedimiento.
4.	Si está trabajando con un servicio web nuevo, haga clic en **Servicios web**.
	1.	Haga clic en el servicio web con el que está trabajando.
	2.	Haga clic en **Consume** (Consumo).
	3.	En la parte inferior de la página Consumir, en la sección **Código de ejemplo**, haga clic en **Lote**.
5.	Copie el código C# de ejemplo para la ejecución por lotes y péguelo en el archivo Program.cs, asegurándose de que el espacio de nombres permanece intacto.

Agregue el paquete NuGet Microsoft.AspNet.WebApi.Client tal como se especifica en los comentarios. Para agregar la referencia a Microsoft.WindowsAzure.Storage.dll, primero debe instalar la biblioteca de cliente para servicios de Microsoft Azure Storage. Para obtener más información, vea [Servicios de almacenamiento de Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### Actualización de la declaración de apikey

Localice la declaración de **apikey**.

	const string apiKey = "abc123"; // Replace this with the API key for the web service

En la sección **Basic consumption info** (Información básica sobre consumo) de la página **Consumir**, localice la clave principal y cópiela en la declaración de **apikey**.

### Actualización de la información de Almacenamiento de Azure

El código de ejemplo de BES carga un archivo desde una unidad local (por ejemplo "C:\\temp\\CensusIpnput.csv") en el Almacenamiento de Azure, lo procesa y escribe los resultados de nuevo en el Almacenamiento de Azure.

Para ello, debe recuperar el nombre, la clave y la información de contenedor de la cuenta de almacenamiento desde el Portal de Azure clásico y actualizar los valores correspondientes del código.

1. Inicie sesión en el Portal de Azure clásico.
1. En la columna de navegación izquierda, haga clic en **Almacenamiento**.
1. En la lista de cuentas de almacenamiento, seleccione una para almacenar el modelo reciclado.
1. En la parte inferior de la página, haga clic en **Administrar claves de acceso**.
1. Copie y guarde la **clave de acceso principal** y cierre el cuadro de diálogo.
1. En la parte superior de la página, haga clic en **Contenedores**.
1. Seleccione un contenedor existente o cree uno nuevo y guarde el nombre.

Busque las declaraciones *StorageAccountName*, *StorageAccountKey* y *StorageContainerName* declaraciones y actualice los valores guardados en Azure Portal.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
También deberá asegurarse de que el archivo de entrada está disponible en la ubicación que especifique en el código.

### Especifique la ubicación de salida.

Al especificar la ubicación de salida en la carga útil de solicitud, la extensión del archivo especificado en *RelativeLocation* debe especificarse como ilearner. Consulte el ejemplo siguiente.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] Los nombres de las ubicaciones de salida pueden ser diferentes de aquellos que aparecen en este tutorial que se basa en el orden en que se agregaron los módulos de salida del servicio web. Puesto que ha configurado este experimento de entrenamiento con dos salidas, los resultados incluyen información de ubicación de almacenamiento para ambas.

![Salida de reentrenamiento][6]

Diagrama 4: Salida de entrenamiento.

## Evaluación de los resultados de reentrenamiento
 
Al ejecutar la aplicación, la salida incluye la dirección URL y el token SAS necesarios para tener acceso a los resultados de evaluación.

Mediante la combinación de *BaseLocation*, *RelativeLocation* y *SasBlobToken* de los resultados de salida para *output2* (como se muestra en la imagen de la salida de reentrenamiento anterior), podemos ver los resultados de rendimiento del modelo reentrenado copiando y pegando la dirección URL completa en la barra de direcciones del explorador.

Revise los resultados para determinar si el modelo recientemente entrenado funciona lo suficientemente bien como para reemplazar el existente.

Copie *BaseLocation*, *RelativeLocation* y *SasBlobToken* de los resultados de salida, ya que se usarán durante el proceso de reciclaje.

## Pasos siguientes

[Reciclaje de un servicio web clásico](machine-learning-retrain-a-classic-web-service.md)

[Reciclaje de un servicio web nuevo mediante los cmdlets de administración de aprendizaje automático](machine-learning-retrain-new-web-service-using-powershell.md)

<!-- Retrain a New Web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0928_2016-->