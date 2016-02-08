<properties
	pageTitle="Conectarse a un servicio web de Aprendizaje automático | Microsoft Azure"
	description="Mediante C# o Python, conéctese a un servicio web de Aprendizaje automático de Azure mediante una clave de autorización."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015" 
	ms.author="derrickv" />


# Conectarse a un servicio web de Aprendizaje automático de Azure
La experiencia del desarrollador de Aprendizaje automático de Azure es una API de servicio web para realizar predicciones a partir de datos de entrada en tiempo real o en modo por lotes. Use Estudio de aprendizaje automático de Azure para crear predicciones e implementar un servicio web de Aprendizaje automático de Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para obtener información sobre cómo crear e implementar un servicio web de Aprendizaje automático de Azure con Estudio:

- [Implementación de un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md)
- [Introducción a Estudio de aprendizaje automático](https://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Vista previa de Aprendizaje automático de Azure](https://studio.azureml.net/)
- [Centro de documentación de Aprendizaje automático](https://azure.microsoft.com/documentation/services/machine-learning/)

## Servicio web de Aprendizaje automático de Azure ##

Con el servicio web de Aprendizaje automático de Azure (ML), una aplicación externa se comunica con un modelo de puntuación de flujo de trabajo ML en tiempo real. Una llamada al servicio web ML devuelve resultados de predicción a una aplicación externa. Para llamar a un servicio web de ML, se pasa una clave de API que se crea cuando se implementa una predicción. El servicio web de ML se basa en REST, una opción popular de arquitectura para proyectos de programación web.

Aprendizaje automático de Azure tiene dos tipos de servicios:

- Servicio de solicitud y respuesta (RRS): servicio de latencia baja altamente escalable que proporciona una interfaz con los modelos sin estado creados e implementados desde el Estudio de aprendizaje automático.
- Servicio de ejecución por lotes (BES): servicio asincrónico que puntúa un lote de registros de datos.

Para obtener más información sobre los servicios web de Aprendizaje automático de Azure, vea [Implementación de un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md).

## Obtener una clave de autorización de Aprendizaje automático de Azure ##
Obtenga una clave de API de servicio web de un servicio web de ML. Puede obtenerla de Microsoft Azure Machine Learning Studio o del portal de Azure clásico.
### Microsoft Azure Machine Learning Studio ###
1. En Estudio de aprendizaje automático de Microsoft Azure, haga clic en **SERVICIOS WEB** a la izquierda.
2. Haga clic en un servicio web. La "clave de API" está en la pestaña **PANEL**.

### Portal de Azure clásico ###

1. Haga clic en **APRENDIZAJE AUTOMÁTICO** a la izquierda.
2. Haga clic en un área de trabajo.
3. Haga clic en **SERVICIOS WEB**.
4. Haga clic en un servicio web.
5. Haga clic en un extremo. La "CLAVE DE API" está inactiva en la parte inferior derecha.

## <a id="connect"></a>Conexión a un servicio web de Aprendizaje automático de Azure

Puede conectarse a un servicio web de Aprendizaje automático de Azure mediante cualquier lenguaje de programación que admita la respuesta y solicitud HTTP. Puede ver ejemplos en C#, Python y R desde una página de ayuda de servicio web de Azure ML.

### Para ver una página de ayuda de la API de servicios web de Azure ML ###
Se crea una página de ayuda de la API de Azure ML al implementar un servicio web. Vea [Tutorial de Aprendizaje automático de Azure: Implementación de un servicio web](machine-learning-walkthrough-5-publish-web-service.md).


**Para ver una página de ayuda de la API de Aprendizaje automático de Azure**, en Estudio de aprendizaje automático de Microsoft Azure:

1. Elija **SERVICIOS WEB**.
2. Elija un servicio web.
3. Elija **Página de ayuda de la API** - **SOLICITUD-RESPUESTA** o **EJECUCIÓN POR LOTES**.


**Página de ayuda de la API de Aprendizaje automático de Azure**: La página de ayuda de la API de Aprendizaje automático de Azure contiene detalles acerca de un servicio web de predicción.



### Ejemplo de C# ###

Para conectarse a un servicio web de Aprendizaje automático de Azure, use un **HttpClient** que pase ScoreData. ScoreData contiene un FeatureVector, un vector de n dimensiones de características numéricos que representa el ScoreData. Autentíquese en el servicio de Azure ML con una clave de API.

Para conectarse a un servicio web de Aprendizaje automático, se debe instalar el paquete NuGet **Microsoft.AspNet.WebApi.Client**.

**Instalar Microsoft.AspNet.WebApi.Client Nuget en Visual Studio**

1. Publique el conjunto de datos de descarga de UCI: Servicio web de conjunto de datos de clase de contenido para adultos 2.
2. Haga clic en **Herramientas** > **Administrador de paquetes de Nuget** > **Consola del administrador de paquetes**.
2. Elija **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para ejecutar el ejemplo de código**

1. Publique el experimento "Ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2", que forma parte de la colección de ejemplos de Aprendizaje automático de Azure.
2. Asigne una clave de API con la clave de un servicio web. Consulte cómo obtener una clave de autorización de Aprendizaje automático de Azure.
3. Asigne la URI de servicio a la URI de solicitud.


### Ejemplo de Python ###

Para conectarse a un servicio web de Aprendizaje automático de Azure, use la biblioteca **urllib2** que pasa ScoreData. ScoreData contiene un FeatureVector, un vector de n dimensiones de características numéricos que representa el ScoreData. Autentíquese en el servicio de Azure ML con una clave de API.


**Para ejecutar el ejemplo de código**

1. Publique el experimento "Ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2", que forma parte de la colección de ejemplos de Aprendizaje automático de Azure.
2. Asigne una clave de API con la clave de un servicio web. Consulte cómo obtener una clave de autorización de Aprendizaje automático de Azure.
3. Asigne la URI de servicio a la URI de solicitud. Consulte cómo obtener un URI de solicitud.

<!---HONumber=AcomDC_0128_2016-->