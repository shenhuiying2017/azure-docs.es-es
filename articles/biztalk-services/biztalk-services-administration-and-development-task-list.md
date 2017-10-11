---
title: "Lista de tareas de administración y desarrollo de BizTalk Services | Microsoft Docs"
description: "Ayuda de planeación y de trabajo para implementar Servicios de BizTalk de Azure."
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 7d4532daf5e4b8f45de94bbec230633978814a6e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Lista de tareas de administración y desarrollo de los Servicios de BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

## <a name="getting-started"></a>Introducción
Cuando se trabaja con Servicios de BizTalk de Microsoft Azure, hay varios componentes locales y basados en la nube que hay que tener en cuenta. Para empezar, piense en el siguiente flujo de proceso:  

| Paso | Quién es el responsable | Tarea | Vínculos relacionados |
| --- | --- | --- | --- |
| 1. |Administrador |Creación de la suscripción de Microsoft Azure con una cuenta de Microsoft o una cuenta de la organización |[Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885) |
| 2. |Administrador |Cree o aprovisione un servicio de BizTalk. |[Crear un Servicio de BizTalk mediante el portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=302280) |
| 3. |Administrador |Registrarse a usted o registrar la implementación de los Servicios de BizTalk de su empresa |[Registrar y actualizar una implementación del servicio de BizTalk en el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |Administrador |Se aplica si la aplicación usa el servicio de adaptador de BizTalk para conectarse a un sistema de línea de negocio (LOB) local o usa un destino de tema o cola.  Cree el espacio de nombres del Bus de servicio de Azure. Dé los valores de este espacio de nombres, del nombre del emisor del Bus de servicio y de la clave del emisor del Bus de servicio al desarrollador. |[Crear o modificar un espacio de nombres del servicio de Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) y [Obtener valores del nombre de emisor y de la clave de emisor](biztalk-issuer-name-issuer-key.md) |
| 5. |Developer |Instale el SDK y cree el proyecto del Servicio de BizTalk en Visual Studio. |[Instalar SDK de Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689760.aspx) y [Crear puntos de conexión de mensajería enriquecida en Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Developer |Implemente su proyecto del Servicio de BizTalk para su Servicio de BizTalk hospedado en Azure. |[Implementar y actualizar el proyecto de Servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |Administrador |Se aplica si está usando EDI.  Puede agregar partner y crear acuerdos en el Portal de servicios de BizTalk de Microsoft Azure. Al crear un contrato, puede agregar el puente o las transformaciones creadas por el desarrollador a la configuración del acuerdo. |[Configuración de EDI, AS2 y EDIFACT en el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |Administrador |Con el Portal de Azure clásico, supervise el estado de su Servicio de BizTalk, incluidas las métricas de rendimiento. |[Servicios de BizTalk: pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administrador |Mediante el portal de Servicios de BizTalk de Microsoft Azure, administre los artefactos usados por los Servicios de BizTalk y realice un seguimiento de los mensajes a medida que los archivos puente los procesan. |[Uso del portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |Administrador |Cree un plan de copia de seguridad para realizar una copia de seguridad del Servicio de BizTalk. |[Continuidad empresarial y recuperación ante desastres en Servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>Pasos siguientes
[Tutoriales y ejemplos](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Creación del proyecto en Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Instalación del SDK de Servicios de BizTalk de Azure](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Conceptos
[Creación del proyecto en Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[Mensajería EDI, AS2 y EDIFACT (Negocio a negocio)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>Otros recursos
[Agregar puntos de conexión de mensajería de origen, destino y puente](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Obtener información y crear transformaciones y mapas de mensajes](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Uso del servicio del adaptador de BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)

