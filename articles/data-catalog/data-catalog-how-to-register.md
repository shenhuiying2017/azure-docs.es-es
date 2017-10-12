---
title: "Registro de orígenes de datos en Azure Data Catalog | Microsoft Docs"
description: "Este artículo destaca cómo registrar orígenes de datos en Azure Data Catalog, incluidos los campos de metadatos que se extraen durante el registro."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 30166823b33669dda88b41a4aee2dfc34f01466f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registro de orígenes de datos en Azure Data Catalog
## <a name="introduction"></a>Introducción
Azure Data Catalog es un servicio en la nube totalmente administrado que actúa como sistema de registro y de detección para orígenes de datos empresariales. En otras palabras, Data Catalog ayuda a los usuarios a detectar, comprender y usar orígenes de datos, así como a las organizaciones a obtener un mayor valor de sus datos. El primer paso para crear un origen de datos que se pueda detectar mediante Data Catalog es registrar ese origen de datos.

## <a name="register-data-sources"></a>Registrar orígenes de datos
El registro es el proceso de extraer metadatos del origen de datos y copiarlos en el servicio Data Catalog. Los datos permanecen donde residen en ese momento y quedan bajo el control de los administradores y las directivas del sistema actual.

Para registrar un origen de datos, haga lo siguiente:
1. En el portal de Azure Data Catalog, inicie la herramienta de registro de origen de datos. 
2. Inicie sesión con su cuenta profesional o educativa con las mismas credenciales de Azure Active Directory que usa para iniciar sesión en el portal.
3. Seleccione el origen de datos que desee registrar.

Consulte el tutorial [Introducción a Azure Data Catalog](data-catalog-get-started.md) para obtener las instrucciones paso a paso.

Una vez registrado el origen de datos, el catálogo realiza un seguimiento de su ubicación e indexa sus metadatos. Los usuarios pueden buscar, examinar, y detectar el origen de datos y, a continuación, usar su ubicación para conectarse a él mediante el uso de la aplicación o herramienta de su elección.

## <a name="supported-data-sources"></a>Orígenes de datos admitidos
Para ver una lista de orígenes de datos admitidos actualmente, consulte los [DSR de Data Catalog](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadatos estructurales
Al registrar un origen de datos, la herramienta de registro extrae información sobre la estructura de los objetos seleccionados. Esta información se conoce como metadatos estructurales.

En todos los objetos, estos metadatos estructurales incluirán la ubicación del objeto, de modo que los usuarios que detecten los datos pueden usar esa información para conectar con el objeto en las herramientas de cliente de su elección. Entre otros metadatos estructurales se incluyen el nombre y tipo del objeto, así como el nombre de atributo/columna y el tipo de datos.

## <a name="descriptive-metadata"></a>Metadatos descriptivos
Además de los metadatos estructurales de núcleo extraídos del origen de datos, la herramienta de registro de orígenes de datos extrae metadatos descriptivos. En el caso de SQL Server Analysis Services y SQL Server Reporting Services, estos metadatos proceden de las propiedades de descripción expuestas por estos servicios. En SQL Server, se extraerán los valores especificados mediante la propiedad extendida ms\_description. En el caso de Oracle Database, la herramienta de registro de orígenes de datos extraerá la columna COMMENTS de la vista ALL\_TAB\_COMMENTS.

Además de los metadatos descriptivos que se extraen del origen de datos, los usuarios pueden especificar metadatos descriptivos con la herramienta de registro de orígenes de datos. Los usuarios pueden agregar etiquetas e identificar expertos para los objetos que se registran. Todos estos metadatos descriptivos se copian en el servicio Data Catalog junto con los metadatos estructurales.

## <a name="include-previews"></a>Inclusión de vistas previas
De forma predeterminada, solo se extraen metadatos de orígenes de datos y se copian en el servicio Data Catalog, pero la comprensión de un origen de datos suele simplificarse viendo una muestra de los datos que contiene.

La herramienta de registro de orígenes de datos de Data Catalog le permite incluir una vista previa de instantánea de los datos presentes en cada tabla y vista que se registre. Si opta por incluir vistas previas durante el registro, la herramienta de registro incluirá un máximo de 20 registros de cada tabla y vista. Esta instantánea se copia después en el catálogo junto con los metadatos descriptivos y estructurales.

> [!NOTE]
> Las tablas anchas que tienen un gran número de columnas pueden tener menos de 20 registros incluidos en la vista previa.
>
>

## <a name="include-data-profiles"></a>Inclusión de los perfiles de datos
De la misma manera que incluir vistas previas puede proporcionar contexto valioso para los usuarios que buscan orígenes de datos en Data Catalog, incluir un perfil de datos puede facilitar la comprensión de los orígenes de datos detectados.

La herramienta de registro de orígenes de datos de Data Catalog le permite incluir un perfil de datos para cada tabla y vista que se registre. Si decide incluir un perfil de datos durante el registro, la herramienta de registro incluirá estadísticas agregadas sobre los datos de cada tabla o vista, incluyendo:

* El número de filas y el tamaño de los datos que contiene el objeto.
* La fecha de la actualización más reciente de los datos y el esquema de objeto.
* El número de registros nulos y valores distintos para las columnas.
* Los valores mínimo, máximo, promedio y de desviación estándar de las columnas.

Estas estadísticas se copian después en el catálogo junto con los metadatos estructurales y descriptivos.

> [!NOTE]
> Las columnas de texto y fecha no incluyen las estadísticas de promedio o desviación estándar en su perfil de datos.
>
>

## <a name="update-registrations"></a>Actualización de registros
El registro de un origen de datos hace que sea detectable en Data Catalog cuando usa los metadatos y la vista previa opcional que se extraen durante el registro. Si el origen de datos debe actualizarse en el catálogo (por ejemplo, si cambia el esquema de un objeto, hay que incluir tablas que se excluyeron originalmente o quiere actualizar los datos incluidos en las vistas previas), puede volver a ejecutarse la herramienta de registro de orígenes de datos.

Al volver a registrar un origen de datos ya registrado se realiza una operación de combinación "upsert": los objetos existentes se actualizan al tiempo que los nuevos objetos se crean. Los metadatos especificados por los usuarios en el portal de Data Catalog se mantendrán.

## <a name="summary"></a>Resumen
Dado que este copia los metadatos estructurales y descriptivos de un origen de datos en el servicio de catálogo, el registro del origen de datos en Data Catalog hace que los datos sean más fáciles de detectar y entender. Después de haber registrado el origen de datos, puede anotarlo, administrarlo y detectarlo a través del portal de Data Catalog.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el registro de orígenes de datos, consulte el tutorial [Introducción a Azure Data Catalog](data-catalog-get-started.md).
