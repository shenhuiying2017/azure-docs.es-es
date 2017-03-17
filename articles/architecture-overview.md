---
title: Arquitectura de aplicaciones en Microsoft Azure | Microsoft Docs
description: "Información general de arquitectura que cubre patrones de diseño habituales."
services: 
documentationcenter: 
author: rboucher
manager: carmonm
editor: 
ms.assetid: 3a37bbc0-f624-46f3-bc4e-5a10560f9fbf
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: b463c363f131c3d75bbab229fe9f14495b5f3b95
ms.openlocfilehash: d2a13d5c80513a6fe80979bb97368b269a24a883
ms.lasthandoff: 03/01/2017


---
# <a name="application-architecture-on-microsoft-azure"></a>Arquitectura de aplicaciones en Microsoft Azure
En este artículo se describen los recursos para la creación de aplicaciones que usan Microsoft Azure. Se incluyen herramientas para dibujar visualmente y describir sistemas de software.

## <a name="design-patterns-poster"></a>Póster de patrones de diseño
Microsoft Patterns and Practices ha publicado el libro [Cloud Design Patterns](http://msdn.microsoft.com/library/dn568099.aspx), que está disponible en MSDN y para descargar en formato PDF. También hay disponible un póster de gran formato en el que se enumeran todos los patrones.

![Póster de patrones de nube de Patterns and Practices](./media/architecture-overview/PnPPatternPosterThumb.jpg)


## <a name="drawing-symbol-and-icon-sets"></a>Conjuntos de símbolos e iconos de dibujo
[Vea el vídeo de entrenamiento de Visio y de los símbolos](http://aka.ms/CnESymbolsVideo), y [descargue el conjunto de símbolos empresariales y en la nube](http://aka.ms/CnESymbols) para ayudarle a crear materiales técnicos que describen Azure, Windows Server, SQL Server, etc. Puede usar los símbolos en los siguientes elementos, sin ánimo de lucro o para obtener algún beneficio.  
- diagramas de arquitectura 
- materiales de formación 
- presentaciones 
- hojas de datos 
- infografía 
- notas del producto 
- libros de terceros, si el libro entrena a los usuarios en el uso de productos de Microsoft.

Los símbolos no están diseñados para su uso en interfaces de usuario, pero puede solicitar permiso.  El uso de los de Azure es aceptable. Es decir, si lo hace para representar los mismos objetos de la misma manera que en el portal de Azure.  Por ejemplo, el símbolo de Azure Service Bus se debe usar únicamente para representar objetos de Azure Service Bus y acceder a ellos. Los símbolos de terceros no son propiedad de Microsoft. Consulte los sitios web de esas empresas para conocer las reglas sobre el uso de sus iconos.

Los símbolos de CnE están en los formatos Visio, SVG y PNG. En el conjunto se incluyen instrucciones adicionales sobre cómo usar fácilmente los símbolos de PowerPoint. El conjunto de símbolos se envía cada trimestre y se actualiza a medida que se publican nuevos servicios.

Hay símbolos adicionales para Microsoft Office y tecnologías relacionadas disponibles en la [Galería de símbolos de Microsoft Office Visio](http://www.microsoft.com/en-us/download/details.aspx?id=35772), aunque no están optimizados para diagramas de arquitectura, como el conjunto de CnE.   

Puede enviarnos un correo electrónico a [ CnESymbols@microsoft.com ](mailto:CnESymbols@microsoft.com) para preguntas o comentarios específicos. Nos gustaría conocer su opinión, incluidos comentarios constructivos para seguir invirtiendo tiempo en ellos.

![Conjunto de iconos y símbolos empresariales y de nube](./media/architecture-overview/CnESymbols.png)

## <a name="microsoft-architecture-certification-course"></a>Curso de certificación de arquitectura de Microsoft
Microsoft creó un curso de arquitectura compatible en agosto de 2015 como complemento al examen de certificación 70-534 de Microsoft. Está [disponible gratis en EDX.ORG](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x).  Usa la [plantilla de Visio de proyectos&3;D](#3d-blueprint-visio-template).

![Curso de certificación de arquitectura de Microsoft](./media/architecture-overview/EDXCourse.png)

## <a name="microsoft-solutions"></a>Soluciones de Microsoft
Microsoft publica un conjunto de [arquitecturas de soluciones](http://aka.ms/azblueprints) de alto nivel, que muestra cómo crear tipos de sistemas específicos con productos de Microsoft.

Anteriormente, Microsoft publicó un conjunto de proyectos isométricos en 3D con arquitecturas de ejemplo. Esos proyectos se han reemplazado por las arquitecturas de soluciones. El vínculo del proyecto se ha redirigido para que apunte a las soluciones. Si por algún motivo necesita acceso a los materiales de proyectos anteriores, envíe un correo electrónico a [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) con su solicitud.   

Los diagramas de los planos y las arquitecturas de soluciones usan partes del [conjunto de símbolos empresariales y en la nube](http://aka.ms/CnESymbols).   

![Diagrama 3D de Proyectos de arquitectura de Microsoft](./media/architecture-overview/BluePrintThumb.jpg)

## <a name="3d-blueprint-visio-template"></a>plantilla de Visio de proyectos&3;D
Las versiones 3D de los [Proyectos de arquitectura de Microsoft](http://aka.ms/azblueprints) , ahora extintos, se crearon inicialmente en una herramienta no de Microsoft. Una plantilla de Visio 2013 (y versiones posteriores) incluida el 5 de agosto de 2015 como parte de un [Curso de certificación de arquitectura de Microsoft distribuido en EDX.ORG](#microsoft-architecture-certification-course).

La plantilla también está disponible fuera del curso.

* [Ver el vídeo de entrenamiento](http://aka.ms/3dBlueprintTemplateVideo) primero para saber lo que puede hacer   
* Descargar la [plantilla de Visio de proyectos 3D de Microsoft](http://aka.ms/3DBlueprintTemplate)
* Descargue el [conjunto de símbolos empresariales y en la nube](https://www.microsoft.com/en-us/download/details.aspx?id=41937) para usarlos con la plantilla 3D. 

Envíenos un correo electrónico a [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) si tiene preguntas específicas no respondidas en los materiales de entrenamiento o para proporcionarnos sus comentarios. La plantilla ya no está en desarrollo activo. Sin embargo, todavía resulta útil y pertinente puesto que puede usar cualquier PNG o el [conjunto de símbolos de nube y empresa](http://aka.ms/CnESymbols), que se actualizan.  

![plantilla de Visio de proyectos 3D de Microsoft](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)

## <a name="architecture-infographics"></a>Infografías de arquitectura
Microsoft publica varios pósteres e infografías relacionados con la arquitectura. Entre ellos, se encuentran [Creación de aplicación en la nube para el mundo real](https://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) y [Escalación de aplicaciones con Cloud Services](https://azure.microsoft.com/documentation/infographics/cloud-services/).

![Infografías de arquitectura de Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

