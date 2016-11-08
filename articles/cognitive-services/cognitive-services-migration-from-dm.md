
---
title: Migración de Recommendations API de Azure Cognitive Services desde Recommendations API de DataMarket | Microsoft Docs
description: Recomendaciones de Azure Machine Learning - Migración a recomendaciones de Cognitive Services
services: cognitive-services
documentationcenter: ''
author: luiscabrer
manager: jhubbard
editor: cgronlun

ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: luisca

---
# Migración a Recommendations API de Azure Cognitive Services desde Recommendations API de DataMarket
En este artículo se explica cómo migrar desde [Recommendations API de Microsoft DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) a [Recommendations API de Microsoft Azure Cognitive Services](https://www.microsoft.com/cognitive-services/es-ES/recommendations-api).

Recommendations API de DataMarket dejará de aceptar nuevos clientes hasta el 31 de diciembre de 2016 y dejará de utilizarse el 28 de febrero de 2017.

## ¿Cómo empiezo a utilizar Recommendations API de Azure Cognitive Services?
Para migrar Recommendations API de Cognitive Services, realice lo siguiente:

1. Si aún no tiene una suscripción de Azure, [regístrese](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) para crear una.
2. Obtenga instrucciones paso a paso de la [Guía de inicio rápido](cognitive-services-recommendations-quick-start.md) para registrarse en Recommendations API de Cognitive Services, a fin de poder utilizar esta API mediante programación.
3. Vaya a la [página de aterrizaje de Recommendations API de Cognitive Services](https://www.microsoft.com/cognitive-services/es-ES/recommendations-api) para obtener información sobre el servicio y encontrar documentación.

## He usado la interfaz de usuario de Recommendations para generar los modelos. ¿Hay alguna herramienta similar para Recommendations API de Cognitive Services?
Por supuesto. La dirección URL para la nueva [interfaz de usuario de Recommendations](http://recommendations-portal.azurewebsites.net/) es http://recommendations-portal.azurewebsites.net.

> [!NOTE]
> Las credenciales de DataMarket no funcionan aquí. Regístrese para obtener una suscripción a Azure Portal y obtener la clave de cuenta necesaria para utilizar la nueva [interfaz de usuario de Recommendations](http://recommendations-portal.azurewebsites.net/). Si no tiene una clave de cuenta, vea la tarea 1 de la [Guía de inicio rápido](cognitive-services-recommendations-quick-start.md).
> 
> 

## ¿El formato de la nueva API es el mismo que el de Recommendations API de DataMarket?
La API admite los mismos escenarios y los flujos de proceso que los escenarios admitidos en la versión de DataMarket, pero se ha actualizado la interfaz real de la API para que se ajuste a las [directrices de la API de REST de Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Las API son más coherentes y funcionan mejor con las herramientas que admiten Swagger.

Para comprender cada una de las API, consulte el [explorador de API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db). Utilice el botón *Probarlo* para probar una llamada API. El formato de archivos utilizado por Recommendations API (archivos de catálogo y uso) no ha cambiado, por lo que puede seguir usando los mismos archivos o la infraestructura creada para generar esos archivos.

## ¿Cuáles son algunas características nuevas de Recommendations API de Cognitive Services?
Durante los últimos dos meses, se han publicado nuevas funcionalidades para Recommendations API de Cognitive Services:

* Interfaz de usuario de Recommendations para modelos de aprendizaje y pruebas sin tener que escribir una sola línea de código
* Puntuación por lotes para proporcionar miles de recomendaciones a la vez
* Compatibilidad de las métricas de compilación para consultar la calidad de los modelos de recomendaciones
* Compatibilidad con las reglas de negocio
* Capacidad para enumerar y descargar archivos de catálogo y uso
* Compatibilidad de generación de clasificación para consultar las características de calidad de elementos en un modelo de recomendaciones
* Se ha agregado funcionalidad para buscar un producto en el catálogo

## ¿Cuándo Microsoft deja de admitir Recommendations API de DataMarket?
[Recommendations API de DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) deja de aceptar nuevos clientes después del 31 de diciembre de 2016, y el 28 de febrero de 2017 se dejará de utilizar por completo.

## ¿Qué ocurre si no tengo los datos necesarios para volver a crear los modelos en Recommendations API de Cognitive Services?
Se pretende realizar esta transición lo antes posible. Podemos ayudarle a mover los modelos antiguos de su cuenta de DataMarket a la nueva suscripción de Recommendations API de Azure Cognitive Services. Póngase en contacto con nosotros en [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Se le pedirá que proporcione el identificador de suscripción de DataMarket y el identificador de suscripción de Cognitive Services, antes de asociar los modelos con la nueva cuenta.

<!---HONumber=AcomDC_0914_2016-->