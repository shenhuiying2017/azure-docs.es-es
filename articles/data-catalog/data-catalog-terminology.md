---
title: "Terminología de Azure Data Catalog | Microsoft Docs"
description: "En este artículo se ofrece una introducción a los conceptos y términos usados en la documentación de Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6fec74d9-4a3c-4b4b-88ba-cad5ad143331
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: b88abd01c2dbc302bfc0e783d1715710c6f8397c
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-data-catalog-terminology"></a>Terminología de Azure Data Catalog
## <a name="catalog"></a>Catálogo
Azure Data Catalog es un repositorio de metadatos basado en la nube en el que se pueden registrar recursos y orígenes de datos. El catálogo actúa como una ubicación de almacenamiento central para metadatos estructurales extraídos de orígenes de datos y para metadatos descriptivos agregados por los usuarios.

## <a name="data-source"></a>Origen de datos
Un origen de datos es un sistema o un contenedor que administra los recursos de datos. Como ejemplos se incluyen las bases de datos de SQL Server, de Oracle, de SQL Server Analysis Services (tabulares o multidimensionales) y servidores de SQL Server Reporting Services.

## <a name="data-asset"></a>Recurso de datos
Los recursos de datos son objetos contenidos dentro de los orígenes de datos que se pueden registrar con el catálogo. Entre algunos ejemplos se incluyen tablas y vistas de SQL Server, tablas y vistas de Oracle, medidas de SQL Server Analysis Services, dimensiones y KPI e informes de SQL Server Reporting Services.

## <a name="data-asset-location"></a>Ubicación del recurso de datos
El catálogo almacena la ubicación de un origen de datos o un recurso de datos, que puede usarse para conectar con el origen mediante una aplicación cliente. El formato y los detalles de la ubicación varían según el tipo de origen de datos. Por ejemplo, una tabla de SQL Server puede identificarse mediante su nombre de cuatro partes (nombre del servidor, nombre de la base de datos, nombre del esquema y nombre de objeto), mientras que un informe de SQL Server Reporting Services se puede identificar mediante su dirección URL.

## <a name="structural-metadata"></a>Metadatos estructurales
Los metadatos estructurales son los metadatos extraídos de un origen de datos que describe la estructura de un recurso de datos. Aquí se incluye la ubicación de los activos, su nombre y tipo de objeto y características específicas del tipo. Por ejemplo, los metadatos estructurales de las tablas y vistas incluyen los nombres y tipos de datos para las columnas del objeto.

## <a name="descriptive-metadata"></a>Metadatos descriptivos
Metadatos descriptivos son los metadatos que describen el propósito o el objetivo de un recurso de datos. Normalmente, los metadatos descriptivos son agregados por los usuarios del catálogo mediante el portal de Azure Data Catalog, pero también se pueden extraer del origen de datos durante el registro. Por ejemplo, la herramienta de registro para Azure Data Catalog extraerá las descripciones de la propiedad Description en SQL Server Analysis Services y SQL Server Reporting Services y desde [la propiedad extendida ms_description](https://technet.microsoft.com/library/ms190243.aspx) en bases de datos de SQL Server, si estas propiedades se han rellenado con valores.

## <a name="request-access"></a>Solicitar acceso
Entre los metadatos descriptivos de un activo de datos se puede incluir información sobre cómo solicitar acceso al activo u origen de datos. Esta información se presenta con la ubicación del activo de datos y puede incluir una o más de las siguientes opciones:

* La dirección de correo electrónico del usuario o equipo responsable de la concesión de acceso al origen de datos.
* La URL del proceso documentado que los usuarios deben seguir para obtener acceso al origen de datos.
* La URL de una herramienta de administración de identidades y de accesos (por ejemplo, Microsoft Identity Manager) que puede usarse para tener acceso al origen de datos.
* Una entrada de texto sin formato que describe cómo los usuarios pueden tener acceso al origen de datos.

## <a name="preview"></a>Vista previa
Una vista previa en Azure Data Catalog es una instantánea de hasta 20 registros que se puede extraer del origen de datos durante el registro y almacenar en el catálogo con los metadatos de recursos de datos. La vista previa puede ayudar a los usuarios que descubren un activo de datos a comprender mejor su función y objetivo. En otras palabras, ver datos de ejemplo puede resultar más valioso que ver tan solo los nombres de columna y los tipos de datos.
Las vistas previas solo se admiten para las tablas y vistas y deben seleccionarse explícitamente por el usuario durante el registro.

## <a name="data-profile"></a>Perfil de datos
Un perfil de datos en Azure Data Catalog es una instantánea de metadatos de nivel de tabla y columna sobre un activo de los datos registrados que se puede extraer del origen de datos durante el registro y almacenar en el catálogo con los metadatos de activos de datos. El perfil de datos puede ayudar a los usuarios que descubren un activo de datos a comprender mejor su función y objetivo. De forma similar a las vistas previas, los perfiles de datos deben ser seleccionados explícitamente por el usuario durante el registro.

> [!NOTE]
> Extraer un perfil de datos puede ser una operación costosa en tablas y vistas grandes, y puede aumentar significativamente el tiempo necesario para registrar un origen de datos.
>
>

## <a name="user-perspective"></a>Perspectiva del usuario
En Azure Data Catalog, cualquier usuario puede proporcionar metadatos descriptivos para un recurso de datos registrados. Cada usuario tiene una perspectiva distinta de los datos y su uso. Por ejemplo, el administrador de un servidor puede proporcionar los detalles de su contrato de nivel de servicio (SLA) o windows de respaldo; un administrador de datos puede proporcionar vínculos a la documentación para los procesos de negocio admitidos por los datos; un analista puede proporcionar una descripción en los términos que sean más relevantes para otros analistas y que pueden ser más valiosos para aquellos usuarios que necesitan descubrir y comprender los datos.

Cada una de estas perspectivas son intrínsecamente valiosas, y con Azure Data Catalog, cada usuario puede proporcionar la información que le resulte significativa, mientras que todos los usuarios pueden usar esa información para comprender los datos y su objetivo.

## <a name="expert"></a>Experto
Un experto es un usuario que se ha identificado como poseedor de una perspectiva informada "experta" para un recurso de datos. Cualquier usuario puede agregarse a sí mismo o a otro usuario como experto para un recurso. Ser enumerado como experto no transmite privilegios adicionales en Azure Data Catalog; permite a los usuarios localizar fácilmente las perspectivas que tienen más probabilidades de ser útiles para consultar los metadatos descriptivos de un recurso.

## <a name="owner"></a>Propietario
Un propietario es un usuario que tiene privilegios adicionales para administrar un recurso de datos en Azure Data Catalog. Los usuarios pueden tomar posesión de los recursos de datos registrados y los propietarios pueden agregar a otros usuarios como copropietarios. Para más información, vea [Cómo administrar recursos de datos](data-catalog-how-to-manage.md).  

> [!NOTE]
> Propiedad y administración solo están disponibles en la Standard Edition de Azure Data Catalog.
>
>

## <a name="registration"></a>Registro
El registro es el acto de extraer metadatos de recursos de datos de un origen de datos y copiarla en el servicio de Azure Data Catalog. Es posible anotar y descubrir los recursos de datos que se han registrado.

## <a name="see-also"></a>Otras referencias
* [¿Qué es Azure Data Catalog?](data-catalog-what-is-data-catalog.md) : este artículo proporciona información general sobre el servicio de Azure Data Catalog, el valor que proporciona y los escenarios que admite.
* [Introducción a Azure Data Catalog](data-catalog-get-started.md) : este artículo ofrece un tutorial integral que muestra cómo usar Azure Data Catalog para la detección del orígenes de datos.  
