---
title: Introducción a Azure Media Services v3 | Microsoft Docs
description: En este artículo se proporciona información general de alto nivel sobre Media Services y vínculos a artículos para obtener más información.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, transmisión, difusión, en vivo, sin conexión
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/27/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: c5c719d484684c0efff87671ba7e012d9bd7699e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="what-is-azure-media-services-v3"></a>¿Qué es Azure Media Services v3?

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Versión 2: disponibilidad general](../previous/media-services-overview.md)
> * [Versión 3: versión preliminar](media-services-overview.md)

> [!NOTE]
> La versión más reciente de Azure Media Services está en versión preliminar y se denomina v3.

Azure Media Services es una plataforma basada en la nube que le permite compilar soluciones que consiguen un streaming de vídeo con calidad de difusión, mejoran la accesibilidad y la distribución, analizan el contenido, etc. Tanto si es un desarrollador de aplicaciones, un centro de llamadas, una autoridad gubernamental o una empresa de entretenimiento, Media Services le ayuda a crear aplicaciones que ofrecen experiencias multimedia de alta calidad a grandes audiencias de los navegadores y dispositivos móviles más populares en la actualidad. 

## <a name="what-can-i-do-with-media-services"></a>¿Qué puedo hacer con Media Services?

Media Services le permite generar una variedad de flujos de trabajo multimedia en la nube. A continuación, se indican algunos ejemplos de lo que se puede lograr con Media Services.  

* Entregue vídeos en varios formatos para que se puedan reproducir en una amplia variedad de navegadores y dispositivos. Para la entrega de streaming en vivo y bajo petición a varios clientes (dispositivos móviles, televisión, equipos, etc.), el contenido de audio y vídeo debe codificarse y comprimirse adecuadamente. Para ver cómo entregar y hacer streaming de este tipo de contenido, consulte [Quickstart: Encode and stream files](stream-files-dotnet-quickstart.md) (Inicio rápido: codificación y streaming de archivos).
* Haga streaming de eventos deportivos para una gran audiencia en línea, como fútbol, béisbol, deportes universitarios o de instituto, etc. 
* Retransmita reuniones y eventos públicos, como plenos de ayuntamientos, juntas municipales y reuniones de órganos legislativos.
* Analice contenido de audio o vídeos grabados. Por ejemplo, para lograr una mayor satisfacción del cliente, las organizaciones pueden extraer texto a voz y crear paneles e índices de búsqueda. A continuación, pueden extraer inteligencia de las quejas habituales, los orígenes de las quejas y otros datos relevantes. 
* Cree un servicio de suscripción de vídeo y haga streaming de contenido protegido por DRM cuando un cliente (por ejemplo, un estudio cinematográfico) necesite restringir el acceso y el uso de una obra con derechos de autor.
* Entregue contenido sin conexión para su reproducción en aviones, trenes y automóviles. Es posible que un cliente tenga que descargar el contenido en su teléfono o tableta para reproducirlo cuando tenga previsto desconectarse de la red.
* Agregue subtítulos y leyendas a los vídeos para llegar a un público más amplio (por ejemplo, personas con discapacidades auditivas o usuarios que quieren leer el contenido en un idioma diferente). 
* Implemente una plataforma de vídeo educativa con Azure Media Services y [Azure Cognitive Services APIs](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) para subtítulos de voz a texto, traducción a varios idiomas, etc.
* Habilite Azure CDN para lograr un gran escalado y mejorar la administración de cargas instantáneas pesadas (por ejemplo, al inicio de un evento de lanzamiento de un producto). 

## <a name="v3-capabilities"></a>Funcionalidades de v3

v3 se basa en una superficie de API unificada que expone la funcionalidad de administración y operaciones compilada en **Azure Resource Manager**. Esta versión ofrece las siguientes funcionalidades:  

* **Transformaciones** que le ayudan a definir flujos de trabajo básicos de tareas de análisis o procesamiento multimedia. La transformación es una receta para procesar archivos de audio y vídeo. A continuación, puede aplicarla varias veces para procesar todos los archivos en la biblioteca de contenido si envía trabajos a la transformación.
* **Trabajos** para procesar (codificar o analizar) vídeos. Se puede especificar contenido de entrada en un trabajo con direcciones URL de HTTP, direcciones URL de SAS o rutas de acceso a archivos ubicados en Azure Blob Storage. 
* **Notificaciones** que supervisan el progreso o estado del trabajo y eventos de error, inicio o detención del canal en vivo. Las notificaciones están integradas en el sistema de notificaciones de Azure Event Grid. Puede suscribirse fácilmente a eventos de varios recursos en Azure Media Services. 
* Las plantillas de **Azure Resource Management** se pueden usar para crear e implementar transformaciones, puntos de conexión de streaming, canales, etc.
* **El control de acceso basado en rol** se puede establecer en recursos, lo que le permite bloquear el acceso a recursos específicos, como transformaciones, canales, etc.
* **SDK de cliente** en varios lenguajes: .NET, .NET Core, Python, Go, Java y Node.js.

## <a name="how-can-i-get-started-with-v3"></a>¿Cómo puedo comenzar con v3?

Como desarrollador, puede usar la [API de REST](https://go.microsoft.com/fwlink/p/?linkid=873030) de Media Services o bibliotecas de cliente que le permitan interactuar con la API de REST para crear, administrar y mantener fácilmente flujos de trabajo multimedia personalizados. Microsoft genera y admite las bibliotecas de cliente siguientes: 

* [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Lenguajes .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)
* [.NET Core](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0) (elija la pestaña **CLI de .NET**)
* Java

  Agregue la siguiente dependencia en el proyecto:
  
  ```
  <dependency>
    <groupId>com.microsoft.azure.media-2018-03-30-preview</groupId>
    <artifactId>azure-mgmt- media</artifactId>
    <version>0.0.1-beta</version>
  </dependency> 
  ```
* Node.js 

  Use el comando siguiente:
  
  ```
  npm install azure-arm-mediaservices
  ```
  
* [Python](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)
* [Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)

Media Services proporciona [archivos de Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) que puede usar para generar SDK para el lenguaje o la tecnología que prefiera.  

## <a name="next-steps"></a>Pasos siguientes

Para ver lo fácil que es empezar a codificar y hacer streaming de archivos de vídeo, consulte [Stream files](stream-files-dotnet-quickstart.md) (Streaming de vídeos). 

