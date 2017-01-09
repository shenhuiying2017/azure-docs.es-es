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
ms.date: 10/04/2016
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 76cc9c79851cc040e6a36c5499a77c0d95948154


---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Preguntas frecuentes sobre el Catálogo de datos de Azure
En este artículo se ofrecen respuestas para las preguntas más frecuentes relacionadas con el servicio del **Catálogo de datos de Azure** de Microsoft.

## <a name="q-what-is-azure-data-catalog"></a>P: ¿Qué es el Catálogo de datos de Azure?
R: Catálogo de datos de Microsoft Azure es un servicio completamente administrado hospedado en la nube de Microsoft Azure que actúa como un sistema de registro y sistema de detección para orígenes de datos empresariales. Catálogo de datos de Azure proporciona capacidades que permiten a cualquier usuario (desde analistas a científicos de datos y desarrolladores) registrar, detectar, comprender y consumir orígenes de datos.

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>P: ¿Qué desafíos de los clientes soluciona Catálogo de datos de Azure?
Catálogo de datos de Azure resuelve el desafío de detección del origen de datos y "datos oscuros" permitiendo a los usuarios descubrir y comprender los orígenes de datos empresariales.

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>P: ¿Quien es el público destinatario del Catálogo de datos de Azure?
Catálogo de datos de Azure proporciona capacidades para usuarios técnicos y no técnicos, incluidos:

* Desarrolladores de datos, profesionales de BI y de análisis: ¿quién es responsable de producir el contenido de datos y análisis para que otros usuarios lo consuman?
* Administradores de datos: los usuarios que tengan conocimientos sobre los datos, lo que significan y cómo están diseñados para usarse y con qué propósito
* Consumidores de datos: usuarios que necesitan ser capaces de detectar fácilmente, comprender y conectarse a los datos necesarios para realizar su trabajo con la herramienta de su elección
* TI central: usuarios que necesitan conseguir que miles de orígenes de datos puedan ser detectables para los usuarios profesionales, y que necesitan mantener una visión general sobre cómo se están usando los datos y por parte de quién

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>P: ¿Cuál es la disponibilidad de la región del Catálogo de datos de Azure?
Los servicios del Catálogo de datos de Azure están actualmente disponibles en los siguientes centros de datos:

* Oeste de EE. UU.
* Este de EE. UU.
* Europa occidental
* Europa del Norte
* Australia Oriental
* Sudeste asiático

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>P: ¿Cuáles son los límites del número de activos de datos del Catálogo de datos de Azure?
La edición gratuita de Catálogo de datos de Azure está limitada a 5.000 recursos de datos registrados.

La edición estándar del Catálogo de datos de Azure admite hasta 100.000 recursos de datos registrados.

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>P: ¿Cuáles son los tipos de recursos y orígenes de datos admitidos?
Para ver la lista de orígenes de datos admitidos actualmente, consulte los [DSR del Catálogo de datos](data-catalog-dsr.md) .

## <a name="q-how-do-i-request-support-for-another-data-source"></a>P: ¿Cómo puedo solicitar soporte técnico para otro origen de datos?
Pueden enviarse solicitudes de características y otros comentarios al [Foro del Catálogo de datos de Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>P. ¿Cómo puedo comenzar con el Catálogo de datos de Azure?
El mejor lugar para comenzar es seguir las instrucciones de [Introducción al Catálogo de datos](data-catalog-get-started.md). Este artículo es una visión general completa de las funcionalidades del servicio.

## <a name="q-how-do-i-register-my-data"></a>P: ¿Cómo se registran mis datos?
Para registrar sus datos en Catálogo de datos de Azure, inicie la herramienta de registro de Catálogo de datos de Azure desde el área "Publicar" del portal del Catálogo de datos de Azure. En la aplicación de publicación de Catálogo de datos de Azure, inicie sesión con las mismas credenciales que usa para tener acceso al portal de Catálogo de datos de Azure y, a continuación, seleccione el origen de datos y los recursos específicos que desee registrar.

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>P: ¿Qué propiedades se extraen de los recursos de datos que se registran?
Las propiedades específicas variarán en función del origen de datos, pero en general, el servicio de publicación del Catálogo de datos de Azure extraerá la información siguiente:

* Nombre de recurso
* Tipo de recurso
* Descripción de activos
* Nombres de columna o atributo
* Tipos de datos de columna o atributo
* Descripción de la columna o atributo

> [!IMPORTANT]
> Al registrar recursos de datos en el Catálogo de datos de Azure, no se mueven los datos a la nube ni se copian en ella. Registrar recursos desde un origen de datos copiará los metadatos de los recursos en Azure, pero los datos permanecerán en la ubicación del origen de datos existente. La única excepción a esta regla es si un usuario elige cargar registros de vista previa o un perfil de datos al registrar los recursos. Cuando se incluya una vista previa, se copiarán hasta 20 registros de cada recurso y se almacenarán como instantánea en el Catálogo de datos de Azure. Cuando se incluye un perfil de datos, se calculará la información agregada (como el tamaño de las tablas, los valores null de porcentaje por columna y los valores mínimos, máximos y promedios para las columnas) y se incluirá en los metadatos almacenados en el catálogo.
> 
> 

<br/>

> [!NOTE]
> Para los orígenes de datos como SQL Server Analysis Services que tienen una propiedad **Description** de primera clase, la aplicación de publicación del Catálogo de datos de Azure extraerá el valor de esa propiedad. En el caso de las bases de datos relacionales de SQL Server, que no disponen de una propiedad **Description** de primera clase, la aplicación de publicación de Azure Data Catalog extraerá el valor de la propiedad extendida ms_description para los objetos y las columnas. Para obtener más información, vea en TechNet [Usar propiedades extendidas en objetos de base de datos](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
> 
> 

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>P: ¿Cuánto tiempo se debe esperar a que aparezcan los recursos recién registrados en el Catálogo de datos de Azure?
Después de registrar recursos en el Catálogo de datos de Azure, es posible que transcurra un periodo de 5 a 10 segundos hasta que aparezcan en el portal del Catálogo de datos de Azure.

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>P: ¿Cómo se anotan y enriquecen los metadatos de mis recursos de datos registrados?
La manera más sencilla de proporcionar metadatos para recursos registrados consiste en seleccionar el recurso en el portal del Catálogo de datos de Azure y, después, especificar los valores de metadatos en el panel de propiedades o el panel de esquema del objeto seleccionado.

También puede proporcionar algunos metadatos, como etiquetas y expertos durante el proceso de registro. Los valores proporcionados en el servicio de publicación del Catálogo de datos de Azure se aplicarán a todos los recursos que se estén registrando en ese momento. Para ver los objetos registrados recientemente en el portal a fin de efectuar anotaciones adicionales, seleccione el botón **VER PORTAL** en la pantalla final de la aplicación de publicación del Catálogo de datos de Azure.

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>P: ¿Cómo elimino mis objetos de datos registrados?
Puede eliminar un objeto del Catálogo de datos de Azure si lo selecciona en el portal y, después, hace clic en el botón **Eliminar** . Esto quitará los metadatos del objeto del Catálogo de datos de Azure, pero no afectará al origen de datos subyacente.

## <a name="q-what-is-an-expert"></a>P: ¿Qué es un experto?
Un experto es una persona que tiene una perspectiva informada acerca de un objeto de datos. Un objeto puede tener varios expertos. No es necesario que un experto sea el "propietario" de un objeto; el experto es simplemente una persona que sabe cómo se pueden y deben utilizar los datos.

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>P: ¿Cómo puedo compartir información con el equipo del Catálogo de datos de Azure si encuentro problemas?
Use el foro del Catálogo de datos de Azure para informar de problemas, compartir información y formular preguntas. El foro se encuentra en http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

## <a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>P: ¿Funciona el Catálogo de datos de Azure con este otro origen de datos que me interesa?
Estamos trabajando activamente para agregar más orígenes de datos al Catálogo de datos de Azure. Si hay un origen de datos que le gustaría que fuese compatible, sugiéralo (o repita la sugerencia si ya se ha efectuado) en el [Foro del Catálogo de datos de Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>P: ¿Cómo está relacionado el Catálogo de datos de Azure con el Catálogo de datos en Power BI para Office 365?
El Catálogo de datos de Azure se puede considerar como una evolución del Catálogo de datos. El Catálogo de datos de Azure ofrece opciones similares para la publicación y la detección de orígenes de datos, pero se centra en escenarios más amplios que no dependen de Office 365. Poco después de que el Catálogo de datos de Azure esté disponible de manera general, los dos catálogos se combinarán en un único servicio.

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>P: ¿Qué permisos necesita un usuario registrar activos con el Catálogo de datos de Azure?
El usuario que ejecuta la Herramienta de registro para el Catálogo de datos de Azure necesita permisos en el origen de datos que le permitan leer los metadatos de este. Si el usuario selecciona también incluir una vista previa, el usuario también deberá tener permisos para leer en los datos desde los objetos que se están registrando.

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>P: ¿Estará el catálogo de datos de Azure estará disponible para las implementaciones locales también?
El Catálogo de datos de Azure es un servicio en la nube que puede funcionar con orígenes de datos en la nube y locales, por lo que ofrece una solución de detección de orígenes de datos híbrida. Actualmente no está prevista la creación de una versión del servicio Catálogo de datos de Azure que se ejecute de forma local.

## <a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>P: ¿Podemos extraer más metadatos o más ricos de los orígenes de datos que registramos?
Estamos trabajando activamente para ampliar las funciones del Catálogo de datos de Azure. Si durante el registro hay metadatos adicionales que desea extraer del origen de datos, sugiéralo (o vote por ello si ya se ha planteado) en el [Foro del Catálogo de datos de Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). En el futuro permitiremos a terceros agregar nuevos tipos de orígenes de datos a través de una API de extensibilidad.

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>P: ¿Cómo se restringe la visibilidad de los recursos de datos registrados para que solo determinadas personas puedan detectarlos?
R: Seleccionar los recursos de datos en el Catálogo de datos de Azure y haga clic en el botón "Tomar posesión". Los propietarios de los recursos de datos del Catálogo de datos de Azure pueden cambiar la configuración de visibilidad para permitir que todos los usuarios del catálogo descubran los recursos de propiedad, o para restringir la visibilidad a usuarios específicos.

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>P: ¿Cómo se actualiza el registro de un recurso de datos para que los cambios en el origen de datos se reflejen en el catálogo?
R: Para actualizar los metadatos de los recursos de datos que ya están registrados en el catálogo, simplemente vuelva a registrar el origen de datos que contiene los recursos. Los cambios en el origen de datos, como las columnas que se agregan o quitan de tablas o vistas, se actualizará en el catálogo, pero se mantendrá las anotaciones proporcionadas por los usuarios.

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>P: Mi pregunta no está respondida aquí. ¿Qué debo hacer?
Diríjase al [Foro del Catálogo de datos de Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Las preguntas formuladas ahí tendrán respuesta aquí.




<!--HONumber=Nov16_HO3-->


