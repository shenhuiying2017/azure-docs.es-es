---
title: "Información general de API de exportación de Mobile Engagement"
description: "Aprender los conceptos básicos sobre la exportación de datos sin procesar generados por los dispositivos del usuario para aprovecharlos en sus propias herramientas."
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: 
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
ms.openlocfilehash: 346e0e480ff84ee849f135a7605d27df9e32f966
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="mobile-engagement-export-api-overview"></a>Información general de API de exportación de Mobile Engagement
## <a name="introduction"></a>Introducción
Con en este documento aprenderá los conceptos básicos sobre la exportación de datos sin procesar generados por los dispositivos del usuario para aprovecharlos en sus propias herramientas.

## <a name="pre-requisites"></a>Requisitos previos
La exportación de datos sin procesar desde Mobile Engagement requiere:

* Configuración de autenticación de API para poder usar las API (consulte la [configuración manual de la autenticación](mobile-engagement-api-authentication-manual.md)),
* Usar las API de REST o el [SDK de .net](mobile-engagement-dotnet-sdk-service-api.md),
* Una cuenta de Almacenamiento de Azure.

> [!NOTE]
> También recomendamos el excelente [Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com/), al menos durante la fase de desarrollo, ya que proporciona una IU fácil de usar para interactuar con Almacenamiento de Azure.
> 
> 

## <a name="what-can-be-exported"></a>¿Qué se puede exportar?
Mobile Engagement permite a sus usuarios recopilar muchos tipos de datos y, por tanto, tiene varios tipos de exportación adecuados para estos tipos de datos diferentes.
Hay 2 tipos esenciales de exportación:

* Instantánea: se usa normalmente para exportar datos que representan un estado y para los que Mobile Engagement no tiene un historial. Esto incluye, por ejemplo, etiquetas (información de la aplicación), tokens o comentarios de campañas de inserción. En consecuencia, estos tipos de exportación no están relacionados con una fecha.
* Históricos: este tipo de exportación se usa para datos que se acumulan a lo largo del tiempo, como eventos o actividades, por ejemplo.

La tabla siguiente describe exhaustivamente todas las exportaciones posibles:

| Tipo de exportación | Tipo de datos | Descripción |
| --- | --- | --- |
| Instantánea |Insertar |Genera una exportación de comentarios de campañas de inserción por identificador de dispositivo o identificador de usuario |
| Instantánea |Etiqueta |Genera una exportación de las etiquetas (información de la aplicación) asociadas a cada dispositivo |
| Instantánea |Dispositivo |Genera una exportación de la mayoría de los datos sobre dispositivos, como los aspectos técnicos (modelo, configuración regional, zona horaria, etc..), las etiquetas, la primera que se ve, etc. |
| Instantánea |Se necesita el cifrado de tokens |Genera una exportación de todos los tokens válidos |
| Históricos |Actividad |Genera una exportación de todas las actividades para cada dispositivo en un período de tiempo determinado |
| Históricos |Evento |Genera una exportación de todas las actividades para cada dispositivo en un período de tiempo determinado |
| Históricos |Trabajo |Genera una exportación de todos los trabajos para cada dispositivo en un período de tiempo determinado |
| Históricos |Error |Genera una exportación de todos los errores para cada dispositivo en un período de tiempo determinado |

## <a name="how-does-it-work"></a>¿Cómo funciona?
Las exportaciones son tareas de larga ejecución que puede producir archivos de datos grandes. Por ese motivo, no se pueden invocar para devolver inmediatamente un archivo para descargar.
Para exportar datos desde Mobile Engagement, tendrá que crear un **trabajo de exportación** a través de la API donde, por lo general, debe especificar:

* El tipo de exportación (instantánea o histórico)
* El tipo de datos,
* El **contenedor de Almacenamiento de Azure** (incluida una SAS válida con acceso de escritura) donde se escribirá el resultado de la exportación.
* Por ejemplo, el parámetro de la dirección URL del contenedor podría ser https://[StorageAccountName].blob.core.windows.net/[ContainerName]?[SASWritePermissionsToken]  

Aquí le mostramos un ejemplo real. https://testazmeexport.blob.core.windows.net/test1234azme?sv=2015-12-11&ss=b&srt=sco&sp=rwdlac&se=2016-12-17T04:59:26Z&st=2016-12-16T20:59:26Z&spr=https&sig=KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q%3D

Tenga en cuenta que el trabajo puede tardar varios minutos en iniciarse y después, su ejecución, puede durar desde unos pocos segundos para las aplicaciones pequeñas hasta varias horas para aplicaciones con una gran cantidad de usuarios o actividades.

Una vez que se crea el trabajo, es posible comprobar su estado para ver si todavía se está ejecutando o si se ha completado.

Cuando el trabajo se complete CORRECTAMENTE, el archivo de datos resultante estará disponible en el contenedor de almacenamiento proporcionado.

