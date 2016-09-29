<properties
	pageTitle="Volver a entrenar modelos de aprendizaje automático mediante programación | Microsoft Azure"
	description="Obtenga información acerca de cómo volver a entrenar un modelo y actualizar el servicio web mediante programación para utilizar el modelo recién entrenado en el Aprendizaje automático de Azure."
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="raymondl;garye;v-donglo"/>


#Volver a entrenar modelos de aprendizaje automático mediante programación  

Como parte del proceso de operacionalización de modelos de Aprendizaje automático de Azure, el modelo se debe entrenar y guardar. Posteriormente, podrá usarlo para crear un servicio web predicativo. A continuación, el servicio web se puede consumir en sitios web, paneles y aplicaciones móviles.

Los modelos que crea mediante Aprendizaje automático no suelen ser estáticos. Cuando hay nuevos datos disponibles o cuando el consumidor de la API tiene sus propios datos, el modelo debe volver a entrenarse. O bien, puede que necesite aplicar filtros para obtener un subconjunto de los datos y volver a entrenar el modelo.

El reentrenamiento puede producirse con frecuencia. Con la característica de la API de reentrenamiento mediante programación, puede volver a entrenar el modelo mediante programación con las API de reentrenamiento y actualizar el servicio web con el modelo recién entrenado.

Este documento describe el proceso anterior y muestra cómo usar las API de reentrenamiento.

## Por qué volver a entrenar: definición del problema  

Como parte del proceso de entrenamiento de aprendizaje automático, un modelo se entrena usando un conjunto de datos. Los modelos que crea mediante Aprendizaje automático no suelen ser estáticos. Cuando hay nuevos datos disponibles o cuando el consumidor de la API tiene sus propios datos, el modelo debe volver a entrenarse. Otros escenarios adicionales pueden obligarle a aplicar filtros para obtener un subconjunto de los datos y volver a entrenar el modelo.

En estos casos, una API de programación proporciona una manera cómoda de permitirle a usted o al consumidor de sus API crear un cliente que pueda, en un momento determinado o de forma habitual, volver a entrenar un modelo usando sus propios datos. A continuación, puede evaluar los resultados del reentrenamiento y actualizar la API del servicio web para utilizar el modelo nuevamente entrenado.

##Cómo volver a entrenar: el proceso completo  

Para empezar, el proceso implica a los siguientes componentes: un experimento de entrenamiento y un experimento predictivo publicados como un servicio web. Para habilitar el reentrenamiento de un modelo entrenado, el experimento de entrenamiento debe publicarse como servicio web con el resultado de un modelo entrenado. Esto permite a la API obtener acceso al modelo para el nuevo entrenamiento.

El proceso de configuración de reentrenamiento implica los pasos siguientes:

![Descripción del proceso de reentrenamiento][1]

Diagrama 1: Descripción del proceso de reentrenamiento

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
3.	Una vez que el experimento ha terminado de ejecutarse, haga clic en **Deploy Web Service [Classic]** (Implementar servicio web [clásico]). Esto permite implementar el experimento predicativo como servicio web clásico.

## Implementación del experimento de entrenamiento como un servicio web de entrenamiento

Para volver a entrenar el modelo entrenado, debe implementar el experimento de entrenamiento que creó como un servicio web de reentrenamiento. Este servicio web necesitará un módulo de salida del servicio web conectado al módulo [Entrenar modelo][train-model] para poder generar nuevos modelos entrenados.

1. Para volver al experimento de entrenamiento, haga clic en el icono Experimentos en el panel izquierdo, y luego haga clic en el experimento denominado Census Model (Modelo de censo).
2. En el cuadro de búsqueda Search Experiment Items (Buscar elementos de experimentos), escriba servicio Web.
3. Arrastre un módulo de entrada de servicio web al lienzo de experimentos y conecte la salida al módulo Limpiar datos que faltan.
4. Arrastre dos módulos de *salida de servicio web* al lienzo de experimentos. Conecte la salida del módulo *Entrenar modelo* a uno y la salida del módulo *Evaluar modelo* al otro. La salida del servicio web para Entrenar modelo nos ofrecerá el nuevo modelo entrenado. La salida vinculada a Evaluar modelo devolverá el resultado de la evaluación del modelo de dicho módulo.
5. Haga clic en **Ejecutar**.
6. En la parte inferior del lienzo del experimento, haga clic en **Set Up Web Service** (Configurar servicio web) y seleccione **Retraining Web Service** (Reentrenamiento del servicio web). Esto implementará el experimento de entrenamiento como un servicio web que genera un modelo entrenado y resultados de evaluación del modelo. Aparecerá el **panel** del servicio web con la clave de API y la página de Ayuda de API para la ejecución por lotes. Solo se puede usar el método de ejecución por lotes para crear modelos entrenados.
  
Una vez que el experimento ha finalizado su ejecución, el flujo de trabajo resultante debe ser el siguiente:

![Flujo de trabajo resultante después de la ejecución.][4]

Diagrama 3: Flujo de trabajo resultante después de la ejecución.

## Adición de un nuevo punto de conexión
 
El servicio web predictivo que implementó es el punto de conexión de puntuación predeterminado. Los puntos de conexión predeterminados se mantienen sincronizados con los experimentos de entrenamiento y puntuación originales y, por tanto, el modelo entrenado de un punto de conexión predeterminado no se puede reemplazar.

Para crear un nuevo punto de conexión de puntuación, en el servicio web predictivo que se puede actualizar con el modelo entrenado:

>[AZURE.NOTE] Asegúrese de agregar el punto de conexión al servicio web predictivo y no al de entrenamiento. Si ha implementado correctamente un servicio web predictivo y otro de entrenamiento, debería ver dos servicios web independientes. El servicio web predictivo debe terminar con "[predictive exp.]".

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Aprendizaje automático** en el menú izquierdo.
3. En Nombre, haga clic en el área de trabajo y, a continuación, haga clic en **Servicios web**.
4. En Nombre, haga clic en **Census Model [predictive exp.]** (Modelo de censo [exp. predictivo]).
5. Haga clic en **Agregar extremo** en la parte inferior de la página. Para más información sobre la incorporación de puntos de conexión, consulte [Creación de puntos de conexión](machine-learning-create-endpoint.md).

También puede agregar puntos de conexión de puntuación mediante el código de ejemplo proporcionado en este [repositorio de github](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

## Reentrenamiento del modelo con nuevos datos mediante BES

Para llamar a las API de reentrenamiento:

1. Cree una nueva aplicación de consola C# en Visual Studio (Nuevo -> Proyecto -> Windows Desktop -> Aplicación de consola).
2. Copie el código C# de ejemplo de la página de Ayuda de API del servicio web de entrenamiento para la ejecución por lotes y lo pegamos en el archivo Program.cs, asegurándonos de que el espacio de nombres permanece intacto.
3. El código de ejemplo tiene comentarios que indican las partes del código que debe actualizar.
4. Al especificar la ubicación de salida en la carga de solicitud, la extensión del archivo especificado en *RelativeLocation* debe cambiarse de csv a ilearner. Consulte el ejemplo siguiente.

		Outputs = new Dictionary<string, AzureBlobDataReference>()
		{
			{
				"output1",
				new AzureBlobDataReference()
				{
					ConnectionString = "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==",
					RelativeLocation = "mycontainer/output1results.ilearner"
				}
			},
		},

>[AZURE.NOTE] Los nombres de las ubicaciones de salida pueden ser diferentes de aquellos que aparecen en este tutorial que se basa en el orden en que se agregaron los módulos de salida del servicio web. Puesto que ha configurado este experimento de entrenamiento con dos salidas, los resultados incluyen información de ubicación de almacenamiento para ambas.

### Actualización de la información de Almacenamiento de Azure

El código de ejemplo de BES carga un archivo desde una unidad local (por ejemplo "C:\\temp\\CensusIpnput.csv") en el Almacenamiento de Azure, lo procesa y escribe los resultados de nuevo en el Almacenamiento de Azure.

Para ello, deberá recuperar el nombre, la clave y la información de contenedor de la cuenta de Almacenamiento desde el Portal de Azure clásico para, después, actualizar los valores correspondientes del código.

También deberá asegurarse de que el archivo de entrada está disponible en la ubicación que especifique en el código.

![Salida de reentrenamiento][6]

Diagrama 4: Salida de entrenamiento.

## Evaluación de los resultados de reentrenamiento
 
Al ejecutar la aplicación, la salida incluye la dirección URL y el token SAS necesarios para tener acceso a los resultados de evaluación.

Mediante la combinación de *BaseLocation*, *RelativeLocation* y *SasBlobToken* de los resultados de salida para *output2* (como se muestra en la imagen de la salida de reentrenamiento anterior), podemos ver los resultados de rendimiento del modelo reentrenado copiando y pegando la dirección URL completa en la barra de direcciones del explorador.

Revise los resultados para determinar si el modelo recientemente entrenado funciona lo suficientemente bien como para reemplazar el existente.

## Actualización del modelo entrenado del punto de conexión agregado

Para completar el proceso de reentrenamiento, debe actualizar el modelo entrenado del nuevo punto de conexión que ha agregado.

* Si ha agregado el nuevo punto de conexión mediante el Portal de Azure, puede hacer clic en su nombre y, luego, en el vínculo **UpdateResource** para obtener la dirección URL que necesitará para actualizar el modelo del punto de conexión.
* Si agregó el punto de conexión mediante el código de ejemplo, esto incluye la ubicación de la dirección URL de ayuda identificada por el valor *HelpLocationURL* de la salida.

Para recuperar la dirección URL de la ruta de acceso:

1. Copie y pegue la URL en el explorador.
2. Haga clic en el vínculo Actualizar recurso.
3. Copie la dirección URL de POST de la solicitud PATCH. Por ejemplo:

		PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Ahora puede usar el modelo entrenado para actualizar el punto de conexión de puntuación que creó anteriormente.

El código de ejemplo siguiente muestra cómo utilizar el *BaseLocation*, *RelativeLocation*, *SasBlobToken* y la dirección URL de PATCH para actualizar el punto de conexión.

	private async Task OverwriteModel()
	{
		var resourceLocations = new
		{
			Resources = new[]
			{
				new
				{
					Name = "Census Model [trained model]",
					Location = new AzureBlobDataReference()
					{
						BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
						RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
						SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
					}
				}
			}
		};

		using (var client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

			using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
			{
				request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
				HttpResponseMessage response = await client.SendAsync(request);

				if (!response.IsSuccessStatusCode)
				{
					await WriteFailedResponse(response);
				}

				// Do what you want with a successful response here.
			}
		}
	}

Se puede obtener *apiKey* y *endpointUrl* para la llamada desde el panel del punto de conexión.

El valor del parámetro *Name* de los *recursos* debe coincidir con el nombre del recurso del modelo entrenado guardado en el experimento predictivo. Para obtener el nombre del recurso:

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Aprendizaje automático** en el menú izquierdo.
3. En Nombre, haga clic en el área de trabajo y, a continuación, haga clic en **Servicios web**.
4. En Nombre, haga clic en **Census Model [predictive exp.]** (Modelo de censo [exp. predictivo]).
5. Haga clic en el nuevo punto de conexión que ha agregado.
6. En el panel del punto de conexión, haga clic en *Actualizar recurso*.
7. En la página de documentación de la API Actualizar recurso para el servicio web, puede encontrar el **nombre del recurso** en **Updatable Resources** (Recursos actualizables).

Si el token SAS expira antes de que termine de actualizar el punto de conexión, deberá realizar una operación GET con el identificador de trabajo para obtener un nuevo token.

Si el código se ha ejecutado correctamente, el nuevo punto de conexión debería comenzar a utilizar el modelo reentrenado en aproximadamente 30 segundos.

##Resumen  
Mediante el uso de las API de reentrenamiento, puede actualizar el modelo entrenado de un servicio web predictivo habilitando escenarios como:

* Reentrenamiento de modelos periódicos con nuevos datos.
* Distribución de un modelo entre los clientes con el fin de permitirles reentrenar el modelo mediante sus propios datos.

## Pasos siguientes
[Solución de problemas del reentrenamiento de un servicio web clásico de Aprendizaje automático de Azure](machine-learning-troubleshooting-retraining-models.md)

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0914_2016-->