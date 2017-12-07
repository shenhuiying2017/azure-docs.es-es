---
title: "Introducción a Azure Location Based Services | Microsoft Docs"
description: "Una introducción a Azure Location Based Services (versión preliminar)"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c8ebce06a72bcaf769a11ec954702463d7489aa0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>Una introducción a Azure Location Based Services (versión preliminar)
Azure Location Based Services es una cartera de servicios geoespaciales que incluyen API de servicio para mapas, búsqueda, rutas, tráfico y zonas horarias. La cartera de servicios compatibles con Azure OneAPI le permite usar herramientas de desarrollador conocidas para desarrollar y escalar rápidamente soluciones que integran la información de ubicación en las soluciones de Azure. Azure Location Based Services proporciona a desarrolladores de todos los sectores unas poderosas funcionalidades geoespaciales, integradas con unos nuevos datos de mapas que son imprescindibles para proporcionar contexto geográfico a aplicaciones web y móviles. Azure Location Based Services es un conjunto de API de REST compatible con Azure One API, que viene acompañado de un control JavaScript basado en web que hace que el desarrollo sea sencillo, flexible y portátil entre varios medios. 

Azure Location Based Services consta de cinco servicios principales que refuerzan las aplicaciones de Azure que requieren un contexto geográfico. Cada uno de los servicios se explica a continuación con más detalle.

**Render Service**: Render Service está diseñado para los desarrolladores para la creación de aplicaciones web y móviles basadas en mapas. El servicio emplea imágenes de gráficos de trama de alta calidad, disponibles en 19 niveles de zoom, o imágenes de mapa en formato vectorial totalmente personalizables.

![Azure Location Based Services Map.png](media/about-location-based-services/Introduction_Map.png)

**Route Service**: Route Service se crea con sólidos cálculos de geometría de infraestructura del mundo real y varias direcciones de modo de transporte. El servicio permite a los desarrolladores calcular direcciones en varios modos de desplazamiento como coche, camión, bicicleta o a pie; así como, varias entradas, como condiciones de tráfico, restricciones de peso o transporte de mercancías peligrosas.

![Azure Location Based Services Route.png](media/about-location-based-services/Introduction_Route.png)

**Search Service**: Search Service está diseñado para los desarrolladores para la búsqueda de direcciones, lugares, listados de empresas por nombre o categoría y otra información geográfica. Search Service puede asimismo realizar la [codificación inversa](https://en.wikipedia.org/wiki/Reverse_geocoding) de direcciones y cruces de calles según una longitud y una latitud. 

![Azure Location Based Services Search.png](media/about-location-based-services/Introduction_Search.png)

**Time Zone Service**: Time Zone Service le permite consultar la información de zona horaria actual, histórica y futura mediante pares de latitud-longitud o un [identificador de IANA](http://www.iana.org/). Time Zone Service también permite convertir los identificadores de zona horaria de Microsoft Windows en zonas horarias de IANA, de forma que se captura una compensación de zona horaria a UTC y se obtiene la hora actual en una zona horaria respectiva. Una respuesta JSON típica de una consulta a Time Zone Service se parece a la siguiente:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

**Traffic Service**: Traffic Service es un conjunto de servicios web diseñados para los desarrolladores para la creación de aplicaciones web y móviles que requieren tráfico. La oferta se divide en los siguientes servicios:
1. Traffic Flow: proporciona velocidades observadas en tiempo real y tiempos de desplazamiento de todas las principales carreteras de la red. 
2. Traffic Incidents: proporciona una vista exacta sobre los atascos y las incidencias de tráfico de toda la red de carreteras.

![Azure Location Based Services Traffic](media/about-location-based-services/Introduction_Traffic.png)

Azure Location Based Services se crea para la movilidad y puede potenciar las aplicaciones multiplataforma dado que el modelo de programación es independiente y admite salidas JSON mediante API de REST. Además, Azure LBS ofrece un práctico Control de mapa de JavaScript con un modelo sencillo de programación para el desarrollo fácil y rápido de aplicaciones web y móviles. 

Azure Location Based Services usa un esquema de autenticación basado en claves, donde el acceso a los servicios se realiza mediante la navegación a [Azure Portal](http://portal.azure.com) y la creación de una cuenta de Azure Location Based Services. Su cuenta incluye dos claves que se generan previamente de manera automática. Comience a integrar estas funcionalidades de ubicación directamente en sus aplicaciones mediante el uso de una de sus claves en las solicitudes al servicio Azure Location Based Services.

Suscríbase a una [cuenta de Azure Location Based Services hoy mismo](http://aka.ms/azurelbsportal).

## <a name="next-steps"></a>Pasos siguientes

Ahora ya tiene una visión de conjunto de Azure Location Based Services (versión preliminar). El paso siguiente consiste en probar una aplicación sencilla que muestre el servicio Location Based Services, así como crear un escenario de un extremo a otro en la aplicación web.

> [!div class="nextstepaction"]
> [Launch a demo interactive map search using Azure Location Based Services (preview)](quick-demo-map-app.md)
>  (Inicio de una demostración de una búsqueda de mapa interactiva mediante Azure Location Based Services) (versión preliminar) [Search nearby point of interest using Azure Location Based Services](tutorial-search-location.md) (Búsqueda de un punto cercano de interés mediante Azure Location Based Services)