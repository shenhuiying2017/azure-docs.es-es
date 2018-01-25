---
title: "Configuración del glosario empresarial para el etiquetado regulado en Azure Data Catalog| Microsoft Docs"
description: "Artículo de procedimientos que resalta el glosario empresarial en Azure Data Catalog para definir y utilizar un vocabulario empresarial común para etiquetar recursos de datos registrados."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: b3d63dbe-1ae7-499f-bc46-42124e950cd6
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: a80b7fd0c21851a6670431e9b8647ca5cf5f51ec
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Configuración del glosario empresarial para el etiquetado regulado
## <a name="introduction"></a>Introducción
Azure Data Catalog permite la detección de orígenes de datos, con el fin de que pueda detectar y conocer fácilmente los orígenes de datos que necesita para realizar análisis y tomar decisiones. Estas funcionalidades consiguen el mayor impacto cuando se puede buscar y conocer el rango más amplio de orígenes de datos disponibles.

Una característica del Catálogo de datos que promueve una mejor comprensión de los datos de recursos es el etiquetado. Con las etiquetas es posible asociar palabras clave a un recurso o una columna, lo que, a su vez, facilita la detección del recurso mediante la búsqueda o la exploración. El etiquetado también le ayuda a conocer más fácilmente el contexto y el propósito del recurso.

Sin embargo, el etiquetado a veces puede causar problemas por sí mismo. Estos son algunos ejemplos de los problemas que puede presentar el etiquetado:

* El uso de abreviaturas en algunos recursos y texto expandido en otros. Esta incoherencia dificulta la detección de los recursos, aunque el propósito fuera usar la misma etiqueta con los recursos.
* Posibles variaciones de significado en función del contexto. Por ejemplo, una etiqueta denominada *Ingresos* en un conjunto de datos de cliente puede significar ingresos por cliente, pero la misma etiqueta en un conjunto de datos de ventas trimestral puede significar ingresos trimestrales de la empresa.  

Para ayudar a solucionar estos y otros problemas similares, Data Catalog incluye un glosario empresarial.

Con el glosario empresarial de Data Catalog, las organizaciones pueden documentar los términos empresariales clave y sus definiciones para crear un vocabulario empresarial común. Esta regulación permite la coherencia del uso de datos por la organización. Una vez que un término se define un término en el glosario empresarial, se puede asignar a un recurso de datos del catálogo. Este enfoque, el *etiquetado regulado*, es el mismo que el etiquetado.

## <a name="glossary-availability-and-privileges"></a>Disponibilidad del glosario y privilegios
El glosario empresarial solo está disponible en la Edición Estándar de Azure Data Catalog. La edición gratuita de Data Catalog no incluye un glosario y no proporciona funcionalidades para el etiquetado regulado.

Para acceder al glosario empresarial, es preciso usar la opción **Glosario** del menú de navegación del portal de Data Catalog.  

![Acceso al glosario empresarial](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Los administradores de Data Catalog y los miembros del rol de administradores de glosario pueden crear, editar y eliminar términos en el glosario empresarial. Todos los usuarios de Data Catalog pueden ver las definiciones de términos y etiquetar los recursos con los términos del glosario.

![Adición de nuevo término de glosario](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Creación de términos de glosario
Tanto los administradores de Data Catalog como los del glosario pueden crear nuevos términos del glosario haciendo clic en el botón **Nuevo término**. Todos los términos del glosario contienen los siguientes campos:

* Una definición empresarial del término
* Una descripción que captura el uso previsto o las reglas de negocio de la columna o recurso
* Una lista de las partes interesadas que más saben del término
* El término primario, que define la jerarquía en el que se organiza el término

## <a name="glossary-term-hierarchies"></a>Jerarquías de términos de glosario
Mediante el uso del glosario empresarial de Data Catalog, una organización puede describir su vocabulario empresarial como una jerarquía de términos y puede crear la clasificación de los términos que mejor represente la taxonomía de su negocio.

Cada término debe ser único en un nivel determinado de jerarquía. No se permiten nombres duplicados. No hay límite en el número de niveles de una jerarquía, pero una jerarquía a menudo se entiende mejor cuando hay tres niveles como máximo.

El uso de jerarquías en el glosario empresarial es opcional. Si se deja en blanco el campo del elemento primario en el glosario de términos, se crea una lista plana (sin jerarquía) de los términos del glosario.  

## <a name="tagging-assets-with-glossary-terms"></a>Etiquetado de recursos con los términos del glosario
Una vez que se hayan definido los términos del glosario en el catálogo, se optimiza la experiencia del etiquetado para buscar en el glosario a medida que un usuario escribe una etiqueta. El portal de Data Catalog muestra una lista de términos del glosario coincidentes entre los que se puede elegir. Si el usuario selecciona un término del glosario en la lista, este se agrega al recurso en forma de etiqueta (también se denomina etiqueta de glosario). El usuario también puede crear una nueva etiqueta escribiendo un término que no esté en el glosario (también se denomina etiqueta de usuario).

![Recurso de datos etiquetados con una etiqueta de usuario y dos etiquetas de glosario](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Las etiquetas de usuario son el único tipo de etiqueta que admite la edición gratuita de Data Catalog.
>
>

### <a name="hover-behavior-on-tags"></a>Comportamiento al mantener el puntero en las etiquetas
En el portal de Data Catalog, los dos tipos de etiquetas son visualmente distintas y presentan diferentes comportamientos al mantener el puntero sobre ellas. Si se mantiene el puntero sobre una etiqueta de usuario, se puede ver tanto el texto de la etiqueta como al usuario, o usuarios, que han agregado la etiqueta. Cuando el puntero se mantiene sobre una etiqueta de glosario, también se ven la definición del término del glosario y un vínculo para abrir el glosario empresarial para ver la definición completa del término.

### <a name="search-filters-for-tags"></a>Filtros de búsqueda de etiquetas
Se pueden realizar búsquedas tanto en las etiquetas de glosario como en las de usuario y se pueden aplicar como filtros en una búsqueda.

## <a name="summary"></a>Resumen
Mediante el uso del glosario empresarial en Azure Data Catalog y del etiquetado regulado que se puede identificar, administrar y detectar recursos de datos de forma coherente. El glosario empresarial pueden promover que los miembros de la organización aprendan el vocabulario empresarial. El glosario también admite la captura de metadatos descriptivos, lo que simplifica el conocimiento y la detección de recursos.

## <a name="next-steps"></a>pasos siguientes
* [Documentación de API de REST para las operaciones del glosario empresarial](https://msdn.microsoft.com/library/mt708855.aspx)
