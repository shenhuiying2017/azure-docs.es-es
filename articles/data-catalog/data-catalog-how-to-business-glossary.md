---
title: "Configuración del glosario empresarial para el etiquetado regulado | Microsoft Docs"
description: "Artículo de procedimientos que resalta el glosario empresarial en Catálogo de datos de Azure para definir y utilizar un vocabulario empresarial común para etiquetar recursos de datos registrados."
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
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8a528f8bccaeb55851ad550aee1da93bf4876730


---
# <a name="how-to-set-up-the-business-glossary-for-governed-tagging"></a>Configuración del glosario empresarial para el etiquetado regulado
## <a name="introduction"></a>Introducción
Catálogo de datos de Azure ofrece funcionalidades de detección de orígenes de datos, permitiendo a los usuarios detectar y comprender fácilmente los orígenes de datos que necesitan para realizar análisis y tomar decisiones. Estas funcionalidades de detección consiguen el mayor impacto cuando los usuarios puedan buscar y comprender la gama más amplia de orígenes de datos disponibles.

Una característica del Catálogo de datos que promueve una mejor comprensión de los datos de recursos es el etiquetado. El etiquetado permite a los usuarios asociar palabras clave a un recurso o una columna, lo que a su vez facilita detectar el recurso a través de la búsqueda o la exploración, y permite a los usuarios comprender más fácilmente el contexto y la intención del recurso.

Sin embargo, el etiquetado a veces puede causar problemas por sí mismo. Estos son algunos ejemplos de problemas que pueden aparecer debido al etiquetado:

1. Usuarios con abreviaturas en algunos recursos y el texto expandido en otros en el etiquetado. Esta incoherencia dificulta la detección de los recursos, aunque el objetivo sea etiquetar los recursos con la misma etiqueta.
2. Etiquetas que tienen significados diferentes en distintos contextos. Por ejemplo, una etiqueta denominada "Ingresos" en un conjunto de datos de cliente podría significar ingresos por cliente, pero la misma etiqueta en un conjunto de datos de ventas trimestral puede significar ingresos trimestrales para la empresa.  

Para ayudar a solucionar estos y otros desafíos similares, Catálogo de datos incluye un glosario empresarial.

El glosario empresarial de Catálogo de datos permite a las organizaciones documentar términos empresariales clave y sus definiciones para crear un vocabulario empresarial común. Esta regulación permite la coherencia del uso de datos por la organización. Una vez definidos los términos en el glosario empresarial, pueden asignarse a los recursos de datos en el catálogo, utilizando el mismo enfoque que el etiquetado, lo que permite un *etiquetado regulado*.

> [!NOTE]
> La funcionalidad descrita en este artículo solo está disponible en la edición Estándar del Catálogo de datos de Azure. La Edición Gratis no proporciona funcionalidades para el etiquetado regulado o para un glosario empresarial.
>
>

## <a name="glossary-availability-and-privileges"></a>Disponibilidad del glosario y privilegios
*El glosario empresarial solo está disponible en la edición Standard de Azure Data Catalog. La edición gratuita de Data Catalog no incluye un glosario.*

Se puede acceder al glosario empresarial a través de la opción "Glosario" del menú de navegación del portal de Catálogo de datos.  

![Acceso al glosario empresarial](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Los administradores y los miembros del rol de administradores de glosarios de Data Catalog pueden crear, editar y eliminar términos en el glosario empresarial. Todos los usuarios del Catálogo de datos pueden ver las definiciones de términos y pueden etiquetar recursos con los términos del glosario.

![Adición de nuevo término de glosario](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Creación de términos de glosario
Los administradores de Data Catalog y de los glosarios pueden crear nuevos términos del glosario haciendo clic en el botón Nuevo término para crear los términos del glosario con los campos siguientes:

* Una definición empresarial del término
* Una descripción que captura el uso previsto o las reglas de negocios para la columna o recurso
* Una lista de las partes interesadas que más saben del término
* El término primario, que define la jerarquía en el que se organiza el término

## <a name="glossary-term-hierarchies"></a>Jerarquías de términos de glosario
El glosario empresarial del Catálogo de datos proporciona la capacidad de describir su vocabulario empresarial como una jerarquía de términos. Esto permite a las organizaciones crear una clasificación de los términos que mejor representen su taxonomía empresarial.

El nombre de un término debe ser único en un nivel determinado de la jerarquía: no se permiten nombres duplicados. No hay límite en el número de niveles de una jerarquía, pero una jerarquía a menudo se entiende mejor cuando hay tres niveles como máximo.

El uso de jerarquías en el glosario empresarial es opcional. Si se deja el campo del elemento primario en blanco en el glosario de términos, se creará una lista plana de términos (sin jerarquía) en el glosario.  

## <a name="tagging-assets-with-glossary-terms"></a>Etiquetado de recursos con los términos del glosario
Cuando se hayan definido los términos del glosario en el catálogo, se optimiza la experiencia del etiquetado de recursos para buscar el glosario a medida que el usuario escribe su etiqueta. El portal de Catálogo de datos muestra una lista de términos del glosario coincidentes para que el usuario pueda elegir. Si el usuario selecciona un término del glosario en la lista, se agrega al recurso como etiqueta (también conocida como etiqueta del glosario). El usuario también puede crear una nueva etiqueta escribiendo un término que no esté en el glosario (también conocida como etiqueta de usuario).

![Recurso de datos etiquetados con una etiqueta de usuario y dos etiquetas de glosario](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Las etiquetas de usuario son el único tipo de etiqueta que se admite con la Edición Gratis de Catálogo de datos.
>
>

### <a name="hover-behavior-on-tags"></a>Comportamiento al mantener el puntero en las etiquetas
En el portal de Catálogo de datos, los dos tipos de etiquetas son visualmente distintas, con distintos comportamientos al mantener el puntero. Cuando el usuario mantiene el puntero sobre una etiqueta de usuario, podrá ver el texto de la etiqueta así como al usuario o usuarios que hayan agregado la etiqueta. Cuando el usuario se desplaza sobre una etiqueta de glosario, también ven la definición de los términos del glosario y un vínculo para abrir el glosario empresarial para consultar la definición completa del término.

### <a name="search-filters-for-tags"></a>Filtros de búsqueda de etiquetas
Se puede realizar búsquedas en las etiquetas de glosario y en las de usuario y pueden aplicar como filtros en una búsqueda.

## <a name="summary"></a>Resumen
El glosario empresarial del Catálogo de datos de Azure y el etiquetado regulado que habilita permiten que los recursos de datos se identifiquen, administren y se detecten de forma coherente. El glosario empresarial puede promover el aprendizaje del vocabulario empresarial entre usuarios de una organización y admite que se capturen metadatos significativos, que se realice la detección de recursos y se comprendan al instante.

## <a name="see-also"></a>Otras referencias
* [Documentación de API de REST para las operaciones del glosario empresarial](https://msdn.microsoft.com/library/mt708855.aspx)



<!--HONumber=Nov16_HO3-->


