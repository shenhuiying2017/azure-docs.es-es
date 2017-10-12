---
title: Escenarios comunes de Azure Data Catalog | Microsoft Docs
description: "Información general de escenarios comunes de Azure Data Catalog, entre los que se incluyen el registro y la detección de orígenes de datos de gran valor, la habilitación de inteligencia empresarial con características de autoservicio y la captura del conocimiento existente acerca de los orígenes de datos y procesos."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: e01e6fa3b9871541bf9573963ced05848a3726e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-catalog-common-scenarios"></a>Escenarios comunes de Azure Data Catalog
En artículo se muestran escenarios comunes en los que Azure Data Catalog puede ayudar a su organización a obtener más valor de sus orígenes de datos existentes.

## <a name="scenario-1-registration-of-central-data-sources"></a>Escenario 1: registro de orígenes de datos centrales
Las organizaciones suelen tener muchos orígenes de datos de gran valor, entre los que se incluyen la línea de negocio, los sistemas de procesamiento de transacciones en línea (OLTP), el almacenamiento de datos y las bases de datos de análisis e inteligencia empresarial. El número de sistemas y la superposición entre ellos suelen aumentar con el tiempo a medida que evolucionan tanto las necesidades de las empresas como las propias empresas, por ejemplo, cuando se realizan fusiones y adquisiciones.

Puede ser difícil que los miembros de una organización en qué partes de estos orígenes de datos deben buscar los datos. Preguntas como las siguientes son muy comunes:

* De los tres sistemas de recursos humanos usados dentro de la empresa, ¿cuál debo usar para crear este tipo de informe?
* ¿Dónde debo acceder para obtener las cifras de ventas certificadas del año fiscal que acaba de terminar?
* ¿A quién debo preguntar o cuál es el proceso que debo seguir para obtener acceso al almacén de datos?
* No sé si estos números son correctos. ¿A quién puedo preguntar para obtener información sobre cómo se deben usar estos datos antes de compartir este panel con mi equipo?

Azure Data Catalog puede responder a estas y otras preguntas. Los orígenes de datos centrales, de alto valor administrados mediante TI que se usan en las organizaciones suelen ser el punto de partida lógico para rellenar el catálogo. Aunque cualquier usuario puede registrar un origen de datos, poner en marcha el catálogo con los orígenes de datos con más probabilidades de proporcionar valor al mayor número de usuarios ayuda a impulsar la adopción y el uso del sistema. 

Si es la primera vez que usa Azure Data Catalog, el primer paso aconsejable puede ser identificar y registrar los orígenes de datos clave que usan los diferentes equipos de consumidores de datos.

Este escenario también presenta una oportunidad para anotar los orígenes de datos de alto valor para que sea más fáciles comprenderlos y tener acceso a ellos. Un aspecto clave de este esfuerzo es incluir información sobre cómo los usuarios pueden solicitar acceso al origen de datos. Con Azure Data Catalog pueden proporcionar la dirección de correo electrónico del usuario o equipo responsables de controlar el acceso al origen de datos, los vínculos a la documentación o a las herramientas existentes, o texto libre que describe el proceso de solicitud de acceso. Esta información ayuda a los usuarios que detectan orígenes de datos registrados, pero que aún no tienen permiso para acceder a ellos, a solicitar fácilmente dicho acceso mediante los procesos definidos y controlados por los propietarios de los orígenes de datos.

## <a name="scenario-2-self-service-business-intelligence"></a>Escenario 2: inteligencia empresarial con características de autoservicio
Aunque las soluciones de inteligencia empresarial corporativas tradicionales siguen siendo una parte inestimable de los entornos de datos de muchas organizaciones, el cambiante ritmo de los negocios ha hecho que la inteligencia empresarial con características de autoservicio cobre cada vez mayor importancia. Con la inteligencia empresarial con características de autoservicio, tanto las personas que trabajan con información como los analistas pueden crear sus propios informes, libros y paneles sin necesidad de usar un equipo de TI central ni estar restringidos por la programación y disponibilidad de dicho equipo.

En los escenarios de BI con características de autoservicio es habitual que los usuarios combinen datos de varios orígenes, muchos de las cuales puede que no se hayan usado anteriormente para BI y análisis. Aunque es posible que algunos de estos orígenes de datos ya sean conocidos, puede ser todo un desafío detectar qué es lo que hay que hacer para buscar y evaluar los posibles orígenes de datos para una tarea determinada.

Tradicionalmente, este proceso de detección se realiza de forma manual: los analistas usan sus conexiones de red del mismo nivel para identificar a otras personas que trabajan con los datos que se buscan. Una vez que un origen de datos se encuentra y se usa, el proceso se vuelve a repetir en cada esfuerzo de BI con características de autoservicio posterior y varios usuarios realizan un proceso manual redundante de detección.

Con Azure Data Catalog, la organización puede interrumpir este ciclo de esfuerzos. Tras detectar un origen de datos por medios tradicionales, un analista puede registrarlo para que en el futuro otros usuarios puedan detectarlo más fácilmente. Aunque el analista puede agregar más valor mediante la anotación de los recursos de datos registrados, dicha anotación no tiene por qué realizarse al mismo tiempo que el registro. Los usuarios pueden contribuir con el tiempo, a medida que sus programaciones lo permitan, agregando valor gradualmente a los orígenes de datos registrados en el catálogo.

Este crecimiento orgánico del contenido del catálogo es un complemento natural al registro por adelantado de orígenes de datos centrales. Rellenar previamente el catálogo con datos que necesitarán muchos usuarios puede ser un incentivo para el descubrimiento y uso inicial. Permitir a los usuarios registrar y anotar orígenes adicionales puede ser una manera de mantener involucrados a estos y a otro miembros de la organización.

Merece la pena indicar que aunque este escenario se centra específicamente en la inteligencia empresarial con características de autoservicio, los mismos patrones y desafíos se aplican a proyectos de BI corporativos a gran escala. Mediante Data Catalog, su organización puede mejorar cualquier esfuerzo que implique un proceso manual de detección de orígenes de datos.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Escenario 3: captura de conocimientos tribales
¿Cómo sé qué datos necesita para hacer su trabajo y dónde encontrarlos?

Si ha estado en su trabajo durante un tiempo, probablemente lo sepa. Ha realizado un proceso de aprendizaje gradual y con el tiempo ha obtenido información acerca de los orígenes de datos que son clave para su trabajo cotidiano.

Cuando se une un nuevo empleado a su equipo, ¿cómo sabe qué datos se requieren para el trabajo y dónde encontrarlos?

Es probable que el nuevo integrante del equipo le plantee estas preguntas.

Esta transferencia continua de conocimientos tribales forma parte del proceso de detección del origen de los datos tanto en las organizaciones grandes como en las pequeñas. Los miembros del equipo más mayores y experimentados han acumulado conocimientos con los años y los miembros del equipo más recientes y han aprendido a preguntarles si tienen preguntas. La información más esencial a menudo solo existe en las cabezas de algunas personas claves y cuando esas personas están de vacaciones o dejan el equipo, la organización se ve afectada.

Habitualmente, los expertos en datos documentan sus conocimientos y dicha documentación la comparten a través del correo electrónico o en forma de archivos de Word que dejan en un sitio de SharePoint del equipo. Aunque este método puede ser válido, presenta un nuevo problema de detección: ¿cómo se puede saber qué documentación existe y dónde encontrarla?

Con Azure Data Catalog, su organización tiene una única ubicación central para almacenar y compartir estos conocimientos tribales y para facilitar su detección. En Data Catalog, los expertos en datos pueden anotar los recursos de datos directamente y proporcionar vínculos a la documentación existente. Cuando los miembros de la organización usen el catálogo para detectar un origen de datos, no solo encontrarán dicho origen, sino también los conocimientos que antes solo estaban en las mentes de los expertos de la organización.
