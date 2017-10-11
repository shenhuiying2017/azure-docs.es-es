---
title: Preguntas frecuentes sobre Azure Data Catalog | Microsoft Docs
description: "Preguntas más frecuentes sobre el Catálogo de datos de Azure, incluidas las funciones de detección de origen de datos, anotación y administración."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 11e4bc7e71b4a94c3a0eda4275745b1beb44974d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Preguntas frecuentes sobre el Catálogo de datos de Azure
En este artículo se responden algunas de las preguntas más frecuentes relativas al servicio Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>¿Qué es Azure Data Catalog?
Data Catalog es un servicio totalmente administrado, hospedado en Microsoft Azure, que actúa como sistema de registro y detección de orígenes de datos empresariales. Con él, todos los usuarios, desde analistas a científicos de datos o desarrolladores, pueden registrar, detectar, conocer y consumir orígenes de datos.

## <a name="what-customer-challenges-does-it-solve"></a>¿Qué problemas de los clientes soluciona?
Data Catalog soluciona los problemas de detección de orígenes de datos y "datos oscuros", con el fin de que los usuarios puedan detectar y conocer los orígenes de datos empresariales.

## <a name="what-are-its-target-audiences"></a>¿Cuáles son sus audiencias de destino?
Data Catalog está diseñado tanto para usuarios técnicos como para no técnicos, entre los que se incluyen:

* Desarrolladores de datos y profesionales de BI y de análisis: responsables de generar datos y contenido de análisis para que otros lo consuman.
* Administradores de datos: usuarios que tienen conocimientos de los datos, lo que significan y cómo están diseñados para usarse.
* Consumidores de datos: aquellos que necesitan poder detectar, conocer y conectarse fácilmente a los datos necesarios para realizar su trabajo con la herramienta que prefieran.
* TI central: usuarios que necesitan que los usuarios profesionales puedan detectar cientos de orígenes de datos y que necesitan tener siempre una visión general de cómo se usa los datos y quiénes lo hacen.

## <a name="what-is-its-availability-by-region"></a>¿Cuál es su disponibilidad por región?
Actualmente, los servicios de Data Catalog están disponibles en los siguientes centros de datos:

* Oeste de EE. UU.
* Este de EE. UU.
* Europa occidental
* Europa del Norte
* Australia Oriental
* Sudeste asiático

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>¿Cuáles son sus límites en cuanto al número de recursos de datos?
La edición gratuita de Data Catalog está limitada a 5.000 recursos de datos registrados.

La edición estándar de Data Catalog admite hasta 100 000 recursos de datos registrados.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>¿Cuáles son los tipos de recursos y orígenes de datos que admite?
Para ver una lista de orígenes de datos admitidos actualmente, consulte los [DSR de Data Catalog](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>¿Cómo solicito soporte técnico para otro origen de datos?
Para enviar solicitudes de características y otros comentarios, vaya al [foro de Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="how-do-i-get-started-with-data-catalog"></a>¿Cómo empiezo a usar Data Catalog?
La mejor manera de empezar a usarlo es ir a [Introducción a Data Catalog](data-catalog-get-started.md). Este artículo es una visión general completa de las funcionalidades del servicio.

## <a name="how-do-i-register-my-data"></a>¿Cómo registro mis datos?
Para registrar datos Data Catalog:
1. En el portal de Azure Data Catalog, en el área **Publicar**, inicie la herramienta de registro de Azure Data Catalog. 
2. En la aplicación de publicación de Data Catalog, inicie sesión con las mismas credenciales que se usan para acceder al portal de Data Catalog.
3. Seleccione el origen de datos y los recursos específicos que desea registrar.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>¿Qué propiedades extrae de los recursos de datos que se registran?
Las propiedades específicas varían de un origen de datos a otro, pero en general, el servicio de publicación de Data Catalog extrae la siguiente información:

* Nombre de recurso
* Tipo de recurso
* Descripción de activos
* Nombres de columna o atributo
* Tipos de datos de columna o atributo
* Descripción de la columna o atributo

> [!IMPORTANT]
> Al registrar recursos de datos en Data Catalog, los datos no se mueven ni se copian a la nube. Al registrar recursos de un origen de datos, los metadatos de dichos recursos se copian en Azure, pero los datos permanecen en la ubicación del origen de datos existente. La excepción a esta regla es si se elige cargar registros de vista previa o un perfil de datos al registrar los recursos. Cuando se incluye una vista previa, se copian hasta 20 registros de cada recurso y se almacenan como una instantánea en Data Catalog. Al incluir un perfil de datos, se calcula la información de agregado y se incluye en los metadatos que se almacenan en el catálogo. La información de agregado puede incluir el tamaño de las tablas, el porcentaje de valores nulos por columna o los valores mínimos, máximos y medios de las columnas. 
>
>

> [!NOTE]
> En el caso de orígenes de datos como SQL Server Analysis Services que tienen una propiedad **Description** de primera clase, la aplicación de publicación de Data Catalog extrae el valor de dicha propiedad. En el caso de las bases de datos relacionales de SQL Server, que no tienen una propiedad **Description** de primera clase, la aplicación de publicación de Data Catalog extrae el valor de la propiedad extendida **ms_description** de los objetos y columnas. Para más información, consulte [Usar propiedades extendidas en objetos de base de datos](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>¿Cuánto tiempo se debe esperar para que los recursos recién registrados aparezcan en el catálogo?
Después de registrar recursos en Data Catalog, es posible que transcurra un período de entre 5 y 10 segundos hasta que aparezcan en el portal de Data Catalog.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>¿Cómo se anotan y enriquecen los metadatos de mis recursos de datos registrados?
La forma más sencilla de proporcionar metadatos a los recursos registrados consiste en seleccionar el recurso en el portal de Data Catalog y, después, especificar los valores en el panel de propiedades o el panel de esquema del objeto seleccionado.

También puede proporcionar algunos metadatos, como etiquetas y expertos durante el proceso de registro. Los valores que se proporcionan en el servicio de publicación de Data Catalog se aplican a todos los recursos que se registran en ese momento. Para ver si los objetos registrados recientemente en el portal tienen anotaciones adicionales, seleccione el botón **View Portal** (Ver portal) en la pantalla final de la aplicación de publicación de Data Catalog.

## <a name="how-do-i-delete-my-registered-data-objects"></a>¿Cómo elimino los objetos de datos registrados?
Para eliminar un objeto de Data Catalog selecciónelo en el portal y haga clic en el botón **Eliminar**. Al eliminar el objeto se quitan sus metadatos de Data Catalog, pero esto no afecta a origen de datos subyacente.

## <a name="what-is-an-expert"></a>¿Qué es un experto?
Un experto es una persona que tiene una perspectiva informada acerca de un objeto de datos. Un objeto puede tener varios expertos. No es necesario que un experto sea el "propietario" de un objeto, puede ser simplemente alguien que sepa cómo se pueden (y deben) utilizar los datos.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>¿Cómo comparto información con el equipo de Data Catalog si surge algún problema?
Para notificar cualquier problema, compartir información y formular preguntas, vaya al [foro de Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>¿Funciona el catálogo con otro origen de datos que me interesa?
Trabajamos activamente para agregar más orígenes de datos a Data Catalog. Si desea que un origen de datos concreto sea compatible, sugiéralo (o repita la sugerencia si ya se ha efectuado) en el [foro de Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>¿Cómo se relaciona Azure Data Catalog con el Catálogo de datos de Power BI para Office 365?
Azure Data Catalog se puede considerar como una evolución del Catálogo de datos en Power BI. A partir de la primavera del 2017, Azure Data Catalog se usa para habilitar el uso compartido y la detección de consultas en Excel 2016 y Power Query para Excel. Las funcionalidades de Data Catalog en Excel están disponibles para los usuarios con licencias de Power BI Pro.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>¿Qué permisos necesito para registrar recursos en Data Catalog?
Para ejecutar la herramienta de registro de Data Catalog necesita permisos en el origen de datos que le permitan leer los metadatos de este. Para incluir también una vista previa, debe tener permisos para leer en los datos desde los objetos que se están registrando.

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>¿Estará Data Catalog disponible también para las implementaciones locales?
Data Catalog es un servicio en la nube que puede funcionar con orígenes de datos tanto locales como en la nube para ofrecer una solución híbrida de detección de orígenes de datos. Actualmente no está prevista la creación de una versión del servicio Data Catalog que se ejecute de forma local.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>¿Se pueden extraer más metadatos, o metadatos más ricos, de los orígenes de datos que se registran?
Trabajamos activamente para ampliar las funcionalidades de Data Catalog. Si desea que se extraigan más metadatos del origen de datos durante el registro, sugiéralo (o vote por ello si ya se ha planteado) en el [foro de Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). En el futuro permitiremos a terceros agregar nuevos tipos de orígenes de datos a través de una API de extensibilidad.

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>¿Cómo se restringe la visibilidad de los recursos de datos registrados para que solo determinadas personas puedan detectarlos?
Seleccione los recursos de datos en Data Catalog y haga clic en el botón **Tomar posesión**. Los propietarios de los recursos de datos de Data Catalog pueden cambiar la configuración de visibilidad para permitir que todos los usuarios detecten los recursos que se poseen o restringir la visibilidad a determinados usuarios.

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>¿Cómo se actualiza el registro de un recurso de datos para que los cambios del origen de datos se reflejen en el catálogo?
Para actualizar los metadatos de los recursos de datos que ya están registrados en el catálogo, solo hay que volver a registrar el origen de datos que contiene los recursos. Los cambios que se produzcan en el origen de datos, como la incorporación o eliminación de columnas de tablas o vistas, se actualizan en el catálogo, pero se mantienen las anotaciones que han realizado los usuarios.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>No encuentro ninguna respuesta a mi pregunta. ¿Dónde puedo encontrarla?
Vaya al [foro de Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Las preguntas formuladas ahí tendrán respuesta aquí.
