---
title: "¿Qué es el Catálogo de datos de Azure? | Microsoft Docs"
description: "En este artículo se proporciona información general del Catálogo de datos de Microsoft Azure, incluidas sus características y los problemas que está diseñado para solucionar. Catálogo de datos proporciona funcionalidades que permiten a cualquier usuario (desde analistas a científicos de datos y desarrolladores) registrar, detectar, comprender y consumir orígenes de datos."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: cc733907-17ec-4153-9f0c-5b3754b2db19
ms.service: data-catalog
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/21/2016
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 97482bc365395e3d9218688bf38ded4a54bb556a


---
# <a name="what-is-azure-data-catalog"></a>¿Qué es el Catálogo de datos de Azure?
Catálogo de datos de Azure es un servicio en la nube totalmente administrado que permite a los usuarios descubrir los orígenes de datos que necesitan y comprender los orígenes de datos que se encuentran, al tiempo que ayuda a las organizaciones a obtener más valor de sus inversiones existentes. Catálogo de datos proporciona funcionalidades que permiten a cualquier usuario (desde analistas a científicos de datos y desarrolladores) detectar, comprender y consumir orígenes de datos. Catálogo de datos incluye un modelo de micromecenazgo de metadatos y anotaciones que permite a todos los usuarios contribuir con sus conocimientos para crear una comunidad y una cultura de datos.

## <a name="discovery-challenges-for-data-consumers"></a>Desafíos de detección para los consumidores de datos
Tradicionalmente, la detección de orígenes de datos empresariales ha sido un proceso orgánico basado en conocimiento tribal. Esto presenta varios desafíos para las compañías que desean sacar el máximo partido de sus recursos de información.

* Los usuarios no son conscientes de que existen orígenes de datos, a menos que entren en contacto con él como parte de otro proceso; no hay ninguna ubicación central en la que se registren los orígenes de datos.
* A menos que un usuario conozca la ubicación de un origen de datos, no se puede conectar a los datos mediante una aplicación de cliente; las experiencias del consumo de datos requieren que los usuarios conozcan la cadena de conexión o la ruta de acceso.
* A menos que un usuario conozca la ubicación de la documentación de un origen de datos, no podrá entender los usos previstos; los orígenes de datos y la documentación residen en lugares diferentes y se consumen a través de diferentes experiencias.
* Si un usuario tiene preguntas acerca de un recurso de información, deberá localizar al experto o a un equipo responsable de los datos y contactar con esos expertos sin conexión; no hay ninguna conexión explícita entre los datos y aquellos con perspectivas expertas en su uso.
* A menos que un usuario comprenda el proceso de solicitar acceso al origen de datos, la detección del origen de datos y su documentación siguen sin permitirle tener acceso a los datos que requiere.

## <a name="discovery-challenges-for-data-producers"></a>Desafíos de detección para los productores de datos
Aunque los consumidores de datos se enfrentan a estos desafíos, los usuarios responsables de producir y mantener recursos de información se enfrentan a los suyos propios.

* Anotar los orígenes de datos con metadatos descriptivos suele ser un esfuerzo perdido; las aplicaciones cliente suelen omitir las descripciones que se almacenan en el origen de datos.
* La creación de documentación para orígenes de datos suele ser un esfuerzo perdido; mantener la documentación en sincronización con el origen de datos es una responsabilidad continuada y los usuarios no tienen confianza en la documentación, ya que a menudo se percibe como obsoleta.
* La restricción del acceso al origen de datos y la garantía de que los consumidores de datos saben cómo solicitar el acceso suponen un desafío continuo.

Crear y mantener la documentación para un origen de datos es complejo y lento. El desafío de conseguir que dicha documentación esté disponible para cualquier persona que use el origen de datos a menudo lo es en mayor medida.

Cuando se combinan, estos desafíos presentan una barrera importante para las empresas que desean para estimular y fomentar el uso y la comprensión de los datos empresariales.

## <a name="azure-data-catalog-can-help"></a>El Catálogo de datos de Azure puede ser de gran ayuda
Catálogo de datos está diseñado para abordar estos problemas y permitir a las empresas sacar el máximo partido a los recursos de información existentes. El Catálogo de datos le ayuda haciendo que los orígenes de datos sean fácilmente reconocibles y comprensibles para los usuarios que necesitan los datos que administran.

El Catálogo de datos proporciona un servicio basado en la nube en el que es posible registrar el origen de los datos. Los datos permanecen en la ubicación existente, pero se agrega una copia de los metadatos al Catálogo de datos, junto con una referencia a la ubicación del origen de datos. Estos metadatos también se indexan para conseguir que cada origen de datos se pueda detectar fácilmente a través de la búsqueda y puedan ser comprensibles para los usuarios que lo detecten.

Una vez que se ha registrado un origen de datos, a continuación sus metadatos se pueden enriquecer, por parte del usuario que realizó el registro o por parte de otros usuarios de la empresa. Cualquier usuario puede anotar un origen de datos proporcionando descripciones, etiquetas u otros metadatos, como la documentación y procesos para solicitar acceso al origen de datos. Estos metadatos descriptivos complementan a los metadatos estructurales (como los nombres de columna y tipos de datos) registrados desde el origen de datos.

El descubrimiento y comprensión de los orígenes de datos y su uso es el propósito principal de registrar los orígenes. Cuando los usuarios empresariales necesitan datos para sus tareas (que pueden ser de inteligencia empresarial, desarrollo de aplicaciones, ciencia de datos o cualquier otra tarea en la que se requieran datos específicos) pueden usar la experiencia de detección del Catálogo de datos para encontrar rápidamente los datos que se ajusten a sus necesidades, comprender los datos para evaluar su adecuación para un propósito y consumir dichos datos abriendo el origen de los mismos en la herramienta que deseen. Al mismo tiempo, el Catálogo de datos permite a los usuarios contribuir al catálogo mediante el etiquetado, documentación y anotación de los orígenes de datos que ya se han registrado, y el registro de nuevos orígenes de datos que la comunidad de usuarios del catálogo puede entonces detectar, comprender y consumir.

![Funcionalidades del Catálogo de datos](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="get-started-with-data-catalog"></a>Introducción a Catálogo de datos
Para empezar a usar el Catálogo de datos ya mismo, visite [www.azuredatacatalog.com](https://www.azuredatacatalog.com).

Hay una guía de introducción disponible [aquí](data-catalog-get-started.md).

## <a name="learn-more-about-data-catalog"></a>Más información acerca del Catálogo de datos
Para más información acerca de las funcionalidades del Catálogo de datos, consulte:

* [Registro de orígenes de datos](data-catalog-how-to-register.md)
* [Detección de orígenes de datos](data-catalog-how-to-discover.md)
* [Anotación de orígenes de datos](data-catalog-how-to-annotate.md)
* [Documentación de los orígenes de datos](data-catalog-how-to-documentation.md)
* [Conexión a orígenes de datos](data-catalog-how-to-connect.md)
* [Trabajo con macrodatos](data-catalog-how-to-big-data.md)
* [Cómo administrar recursos de datos](data-catalog-how-to-manage.md)
* [Configuración del glosario empresarial](data-catalog-how-to-business-glossary.md)
* [Preguntas más frecuentes](data-catalog-frequently-asked-questions.md)




<!--HONumber=Nov16_HO2-->


