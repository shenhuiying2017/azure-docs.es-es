---
title: "Cómo consumir un servicio web Azure Machine Learning | Microsoft Docs"
description: "Una vez implementado un servicio de aprendizaje automático, se puede consumir el servicio web RESTFul facilitado como servicio de solicitud-respuesta en tiempo real o como servicio de ejecución por lotes."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.author: garye
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: eec9f637b4b2306ab4a888dbd5ef5b9a021bcac5
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017


---
# <a name="how-to-consume-an-azure-machine-learning-web-service"></a>Cómo consumir un servicio web Azure Machine Learning

Una vez que implemente un modelo predictivo de Azure Machine Learning como servicio web, puede usar una API de REST para enviarle datos y obtener predicciones. Puede enviar los datos en tiempo real o por lotes.

Puede encontrar más información sobre cómo crear e implementar un servicio web Machine Learning con Machine Learning Studio aquí:

* Para obtener un tutorial sobre cómo crear un experimento en Estudio de aprendizaje automático, consulte [Creación del primer experimento](machine-learning-create-experiment.md).
* Para obtener detalles sobre cómo implementar un servicio web, vea [Implementación de un servicio web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).
* Para obtener más información sobre Aprendizaje automático, visite el [Centro de documentación de aprendizaje automático](https://azure.microsoft.com/documentation/services/machine-learning/).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Información general
Con el servicio web Azure Machine Learning, una aplicación externa se comunica con un modelo de puntuación de flujo de trabajo de Machine Learning en tiempo real. Una llamada al servicio web Machine Learning devuelve resultados de predicción a una aplicación externa. Para llamar a un servicio web Machine Learning, se pasa una clave de API que se crea cuando se implementa una predicción. El servicio web Machine Learning se basa en REST, una opción popular de arquitectura para proyectos de programación web.

Aprendizaje automático de Azure tiene dos tipos de servicios:

* Servicio de solicitud y respuesta (RRS): servicio de latencia baja altamente escalable que proporciona una interfaz con los modelos sin estado creados e implementados desde el Estudio de aprendizaje automático.
* Servicio de ejecución por lotes (BES): servicio asincrónico que puntúa un lote de registros de datos.

Para más información sobre los servicios web Machine Learning, consulte [Implementación de un servicio web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Obtener una clave de autorización de Aprendizaje automático de Azure
Al implementar el experimento, se generan claves de API para el servicio web. Puede recuperar las claves de varias ubicaciones.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>En el portal Servicios web Microsoft Azure Machine Learning
Inicie sesión en el portal [Servicios web Microsoft Azure Machine Learning](https://services.azureml.net).

Para recuperar la clave de API para un nuevo servicio web Machine Learning:

1. En el portal de servicios web Azure Machine Learning, haga clic en la opción **Servicios web** del menú superior.
2. Haga clic en el servicio web del que quiere recuperar la clave.
3. En el menú superior, haga clic en **Consume**(Consumo).
4. Copie y guarde la **clave principal**.

Para recuperar la clave de API para un servicio web Machine Learning clásico:

1. En el portal Servicios web Azure Machine Learning, haga clic en la opción **Classic Web Services** (Servicios web clásicos) del menú superior.
2. Haga clic en el servicio web que está usando.
3. Haga clic en el punto de conexión del que quiere recuperar la clave.
4. En el menú superior, haga clic en **Consume**(Consumo).
5. Copie y guarde la **clave principal**.

### <a name="classic-web-service"></a>Servicio web clásico
 También puede obtener una clave para un servicio web clásico en Machine Learning Studio o el Portal de Azure clásico.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. En Estudio de aprendizaje automático, haga clic en **SERVICIOS WEB** a la izquierda.
2. Haga clic en un servicio web. La **clave de API** está en la pestaña **PANEL**.

#### <a name="azure-classic-portal"></a>Portal de Azure clásico
1. Haga clic en **APRENDIZAJE AUTOMÁTICO** a la izquierda.
2. Haga clic en el área de trabajo en el que se encuentra el servicio web.
3. Haga clic en **SERVICIOS WEB**.
4. Haga clic en un servicio web.
5. Haga clic en un extremo. La "CLAVE DE API" está inactiva en la parte inferior derecha.

## <a id="connect"></a>Conexión a un servicio web Machine Learning
Puede conectarse a un servicio web Machine Learning mediante cualquier lenguaje de programación que admita la respuesta y la solicitud HTTP. Puede ver ejemplos en C#, Python y R desde una página de ayuda de servicio web Machine Learning.

**Ayuda de la API de Machine Learning** Se crea una página de ayuda de API de Machine Learning al implementar un servicio web. Vea [Tutorial de Aprendizaje automático de Azure: Implementación de un servicio web](machine-learning-walkthrough-5-publish-web-service.md).
La ayuda de la API de Machine Learning contiene información sobre un servicio web de predicción.

1. Haga clic en el servicio web que está usando.
2. Haga clic en el punto de conexión para el que desea ver la página de ayuda de la API.
3. En el menú superior, haga clic en **Consume**(Consumo).
4. Haga clic en **API help page** (Página de ayuda de la API) en los puntos de conexión Solicitud-respuesta o Ejecución de lotes.

**Para ver la ayuda de la API de Machine Learning para un servicio web nuevo**

En el portal Servicios web Azure Machine Learning:

1. Haga clic en la opción de menú **SERVICIOS WEB** del menú superior.
2. Haga clic en el servicio web del que quiere recuperar la clave.

Haga clic en **Consume** (Consumo) para obtener los URI de los servicios de solicitud-respuesta y ejecución por lotes, además del código de ejemplo en C#, R y Python.

Haga clic en **Swagger API** para obtener la documentación basada en Swagger de las API que se invocan desde los URI proporcionados.

### <a name="c-sample"></a>Ejemplo de C#
Para conectarse a un servicio web Machine Learning, use un elemento **HttpClient** que pase ScoreData. ScoreData contiene un FeatureVector, un vector de n dimensiones de características numéricos que representa el ScoreData. Se autentica en el servicio de Aprendizaje automático con una clave API.

Para conectarse a un servicio web Machine Learning, se debe instalar el paquete NuGet **Microsoft.AspNet.WebApi.Client**.

**Instalar Microsoft.AspNet.WebApi.Client NuGet en Visual Studio**

1. Publique el conjunto de datos de descarga de UCI: Servicio web de conjunto de datos de clase de contenido para adultos 2.
2. Haga clic en **Herramientas** > **Administrador de paquetes NuGet** > **Consola del administrador de paquetes**.
3. Elija **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para ejecutar el ejemplo de código**

1. Publique el experimento "Ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2", que forma parte de la colección de ejemplos de Aprendizaje automático.
2. Asigne una clave de API con la clave de un servicio web. Consulte el apartado anterior **Obtener una clave de autorización de Aprendizaje automático de Azure** .
3. Asigne la URI de servicio a la URI de solicitud.

### <a name="python-sample"></a>Ejemplo de Python
Para conectarse a un servicio web Machine Learning, use la biblioteca **urllib2** que pasa ScoreData. ScoreData contiene un FeatureVector, un vector de n dimensiones de características numéricas que representa el ScoreData. Se autentica en el servicio de Aprendizaje automático con una clave API.

**Para ejecutar el ejemplo de código**

1. Publique el experimento "Ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de clase de contenido para adultos 2", que forma parte de la colección de ejemplos de Aprendizaje automático.
2. Asigne una clave de API con la clave de un servicio web. Vea la sección **Obtener una clave de autorización de Azure Machine Learning** casi al principio de este artículo.
3. Asigne la URI de servicio a la URI de solicitud.


