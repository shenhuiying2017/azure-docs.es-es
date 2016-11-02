<properties
	pageTitle="Conectarse a un servicio web de Aprendizaje automático | Microsoft Azure"
	description="Mediante C# o Python, conéctese a un servicio web de Aprendizaje automático de Azure mediante una clave de autorización."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016" 
	ms.author="garye" />


# Conectarse a un servicio web de Aprendizaje automático de Azure
La experiencia del desarrollador de Aprendizaje automático de Azure es una API de servicio web para realizar predicciones a partir de datos de entrada en tiempo real o en modo por lotes. Use Estudio de aprendizaje automático de Azure para crear predicciones e implementar un servicio web de Aprendizaje automático de Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para obtener información sobre cómo crear e implementar un servicio web de Aprendizaje automático con Estudio de aprendizaje automático:

- Para obtener un tutorial sobre cómo crear un experimento en Estudio de aprendizaje automático, consulte [Creación del primer experimento](machine-learning-create-experiment.md).
- Para obtener detalles sobre cómo implementar un servicio web, consulte [Implementar un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md).
- Para obtener más información sobre Aprendizaje automático, visite el [Centro de documentación de aprendizaje automático](https://azure.microsoft.com/documentation/services/machine-learning/).

## Servicio web de Aprendizaje automático de Azure ##

Con el servicio web de Aprendizaje automático de Azure, una aplicación externa se comunica con un modelo de puntuación de flujo de trabajo de Aprendizaje automático en tiempo real. Una llamada al servicio web de Aprendizaje automático devuelve resultados de predicción a una aplicación externa. Para llamar a un servicio web de Aprendizaje automático, se pasa una clave de API que se crea cuando se implementa una predicción. El servicio web de Aprendizaje automático se basa en REST, una opción popular de arquitectura para proyectos de programación web.

Aprendizaje automático de Azure tiene dos tipos de servicios:

- Servicio de solicitud y respuesta (RRS): servicio de latencia baja altamente escalable que proporciona una interfaz con los modelos sin estado creados e implementados desde el Estudio de aprendizaje automático.
- Servicio de ejecución por lotes (BES): servicio asincrónico que puntúa un lote de registros de datos.

Para obtener más información sobre los servicios web de Aprendizaje automático, vea [Implementar un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md).

## Obtener una clave de autorización de Aprendizaje automático de Azure ##

Al implementar el experimento, se generan claves de API para el servicio web. La ubicación desde donde se recuperarán las claves dependerá de si se implementó el experimento como servicio web clásico o nuevo.

## Servicio web clásico ##

 Puede obtener una clave de Estudio de aprendizaje automático o el Portal de Azure.

### Estudio de aprendizaje automático ###

1. En Estudio de aprendizaje automático, haga clic en **SERVICIOS WEB** a la izquierda.
2. Haga clic en un servicio web. La **clave de API** está en la pestaña **PANEL**.

### Portal de Azure ###

1. Haga clic en **APRENDIZAJE AUTOMÁTICO** a la izquierda.
2. Haga clic en un área de trabajo.
3. Haga clic en **SERVICIOS WEB**.
4. Haga clic en un servicio web.
5. Haga clic en un extremo. La "CLAVE DE API" está inactiva en la parte inferior derecha.


## Servicio web nuevo 

Para recuperar la clave de API de un servicio web nuevo de Aprendizaje automático, debe iniciar sesión en el portal de[servicios web de Aprendizaje automático de Microsoft Azure](https://services.azureml.net/quickstart).

1. En el portal de servicios web de Aprendizaje automático de Azure, haga clic en la opción **SERVICIOS WEB** del menú superior.
2. Haga clic en el servicio web del que quiere recuperar la clave.
3. En el menú superior, haga clic en **Consume** (Consumo).
4. Copie y guarde la **clave principal**.

## <a id="connect"></a>Conexión a un servicio web de Aprendizaje automático

Puede conectarse a un servicio web de Aprendizaje automático mediante cualquier lenguaje de programación que admita la respuesta y la solicitud HTTP. Puede ver ejemplos en C#, Python y R desde una página de ayuda de servicio web de Aprendizaje automático.

**Ayuda de Aprendizaje automático** Se crea una página de ayuda de API de Aprendizaje automático al implementar un servicio web. Vea [Tutorial de Aprendizaje automático de Azure: Implementación de un servicio web](machine-learning-walkthrough-5-publish-web-service.md). La ayuda de la API de Aprendizaje automático contiene información sobre un servicio web de predicción.

**Pasos para ver la ayuda de la API de Aprendizaje automático de un servicio web clásico** En Estudio de aprendizaje automático, siga estos pasos:

1. Haga clic en **SERVICIOS WEB**.
2. Haga clic en un servicio web.
3. Haga clic en **API help page** (Página de ayuda de la API), **REQUEST/RESPONSE** (SOLICITUD-RESPUESTA) o **BATCH EXECUTION** (EJECUCIÓN POR LOTES).

**Pasos para ver la ayuda de la API de Aprendizaje automático de un servicio web nuevo** En el portal de servicios web de Aprendizaje automático de Azure, siga estos pasos:

1. Haga clic en la opción de menú **SERVICIOS WEB** del menú superior.
2. Haga clic en el servicio web del que quiere recuperar la clave.

Haga clic en **Consume** (Consumo) para obtener los URI de los servicios de solicitud-respuesta y ejecución por lotes, además del código de ejemplo en C#, R y Python.

Haga clic en **Swagger API** para obtener la documentación basada en Swagger de las API que se invocan desde los URI proporcionados.

### Ejemplo de C# ###

Para conectarse a un servicio web de Aprendizaje automático, use un elemento **HttpClient** que pase ScoreData. ScoreData contiene un FeatureVector, un vector de n dimensiones de características numéricos que representa el ScoreData. Se autentica en el servicio de Aprendizaje automático con una clave API.

Para conectarse a un servicio web de Aprendizaje automático, se debe instalar el paquete NuGet **Microsoft.AspNet.WebApi.Client**.

**Instalar Microsoft.AspNet.WebApi.Client Nuget en Visual Studio**

1. Publique el conjunto de datos de descarga de UCI: Servicio web de conjunto de datos de clase de contenido para adultos 2.
2. Haga clic en **Herramientas** > **Administrador de paquetes de Nuget** > **Consola del administrador de paquetes**.
2. Elija **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para ejecutar el ejemplo de código**

1. Publique el experimento "Ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2", que forma parte de la colección de ejemplos de Aprendizaje automático.
2. Asigne una clave de API con la clave de un servicio web. Consulte el apartado anterior **Obtener una clave de autorización de Aprendizaje automático de Azure**.
3. Asigne la URI de servicio a la URI de solicitud.


### Ejemplo de Python ###

Para conectarse a un servicio web de Aprendizaje automático, use la biblioteca **urllib2** que pasa ScoreData. ScoreData contiene un FeatureVector, un vector de n dimensiones de características numéricos que representa el ScoreData. Se autentica en el servicio de Aprendizaje automático con una clave API.


**Para ejecutar el ejemplo de código**

1. Publique el experimento "Ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2", que forma parte de la colección de ejemplos de Aprendizaje automático.
2. Asigne una clave de API con la clave de un servicio web. Consulte el apartado anterior **Obtener una clave de autorización de Aprendizaje automático de Azure**.
3. Asigne la URI de servicio a la URI de solicitud.

<!---HONumber=AcomDC_0914_2016-->