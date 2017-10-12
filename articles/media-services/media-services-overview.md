---
title: "Introducción a Azure Media Services | Microsoft Docs"
description: "En este tema se proporciona información general de Azure Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/04/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 2a175aed40b9775d9a4de6877eb3467b43819568
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-overview"></a>Introducción a Azure Media Services 

Microsoft Azure Media Services es una plataforma extensible basada en la nube que permite a los desarrolladores crear aplicaciones de administración y entrega de contenido multimedia escalables. Media Services se basa en las API de REST, que permiten cargar, almacenar, codificar y empaquetar de forma segura contenido de audio o vídeo para la entrega bajo demanda y de streaming en vivo a varios clientes (por ejemplo, televisión, PC y dispositivos móviles).

Puede crear flujos de trabajo de un extremo a otro usando solamente Media Services. También puede usar componentes de terceros para algunas partes del flujo de trabajo. Por ejemplo, codifique mediante un codificador de terceros. A continuación, cargue, proteja, empaquete y entregue con Media Services.

Puede elegir entre transmitir en secuencias el contenido en directo o entregar el contenido a petición. El tema también incluye vínculos a otros temas de interés.

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
Puede ver las rutas de aprendizaje de Azure Media Services aquí:

* [Flujo de trabajo de streaming en vivo de Azure Media Services](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [Flujo de trabajo de streaming a petición de AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Requisitos previos

Para empezar a usar Azure Media Services, debe tener lo siguiente:

* Una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com).
* Una cuenta de Azure Media Services. Para obtener más información, consulte [Creación de una cuenta](media-services-portal-create-account.md).
* (Opcional) Configurar el entorno de desarrollo. Elija .NET o API de REST para el entorno de desarrollo. Para obtener más información, consulte [Configuración del entorno](media-services-dotnet-how-to-use.md).

    Además, aprenda a [conectarse mediante programación a la API de AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Un punto de conexión de streaming estándar o premium en estado iniciado.  Para más información, consulte [Administración de puntos de conexión de streaming](media-services-portal-manage-streaming-endpoints.md).

## <a name="sdks-and-tools"></a>SDK y herramientas

Para compilar soluciones de Media Services, puede usar:

* [API de REST de Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Uno de los SDK de cliente disponibles:
    * [SDK de Servicios multimedia de Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services)
    * [SDK de Azure para Java](https://github.com/Azure/azure-sdk-for-java)
    * [SDK de Azure para PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Azure Media Services para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (es decir, una versión de un SDK de Node.js que no sea de Microsoft. Su mantenimiento corre a cargo de una comunidad y actualmente no tiene una cobertura del 100 % de las API de AMS).
* Herramientas existentes:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (El Explorador de Azure Media Services (AMSE) es una aplicación Winforms/C# para Windows)

## <a name="concepts-and-overview"></a>Introducción y conceptos
Para conocer los conceptos de Azure Media Services, consulte [Conceptos](media-services-concepts.md).

Para una serie de procedimientos en que se presentan todos los componentes principales de Azure Media Services, consulte los [tutoriales paso a paso de Azure Media Services](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Esta serie tiene una excelente introducción de conceptos y utiliza la herramienta AMSE para demostrar las tareas AMS. La herramienta AMSE es una herramienta de Windows. Esta herramienta admite la mayoría de las tareas que puede lograr mediante programación con el [SDK de AMS para .NET](https://github.com/Azure/azure-sdk-for-media-services), el [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java) o el [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Escenarios admitidos y disponibilidad de Media Services en centros de datos

Para más información, consulte [Scenarios and availability of Media Services features across datacenters](scenarios-and-availability.md) (Escenarios y disponibilidad de Media Services en centros de datos).

## <a name="service-level-agreement-sla"></a>Contrato de nivel de servicio (SLA)

* Garantizamos la disponibilidad del 99,9% de las transacciones de API de REST para la codificación de Media Services.
* Para el streaming, atenderemos correctamente las solicitudes de servicio con una garantía de disponibilidad del 99,9 % para el contenido multimedia existente cuando se adquiera un punto de conexión de streaming estándar o premium.
* Para los canales en directo, garantizamos que los canales en ejecución tendrán una conectividad externa como mínimo el 99,9 % del tiempo.
* Para la protección de contenido, garantizamos que procesaremos correctamente las solicitudes clave como mínimo el 99,9% del tiempo.
* Para el indizador, atenderemos correctamente las solicitudes de tarea de indizador procesadas con una unidad reservada de codificación el 99,9% del tiempo.

Para obtener más información, consulte el [Contrato de nivel de servicio (SLA) de Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

Para obtener información acerca de la disponibilidad en los centros de datos, consulte la sección [Disponibilidad](scenarios-and-availability.md#availability).

## <a name="support"></a>Soporte técnico

[Soporte técnico de Azure](https://azure.microsoft.com/support/options/) proporciona opciones de soporte técnico para Azure, incluido Media Services.

## <a name="provide-feedback"></a>Envío de comentarios

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
