---
title: "Guía de Azure | Microsoft Docs"
description: "Procedimientos recomendados y guía de Azure"
services: 
documentationcenter: na
author: bennage
manager: marksou
editor: 
tags: 
ms.assetid: de94c74a-fea7-4815-8484-553e421a7490
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: christb
translationtype: Human Translation
ms.sourcegitcommit: 5f3ced657cf3d6587a63789b3dd3ca41cd2856f0
ms.openlocfilehash: 0061e1ff2ae2d6b8ed7b7c3bb60405e76d4cc91b


---
# <a name="azure-guidance"></a>Guía de Azure
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

El equipo de patrones y prácticas de Microsoft forma parte del equipo de asesoramiento para el cliente de Azure. Nuestro objetivo es ayudar a los desarrolladores, arquitectos y profesionales de TI a tener éxito en la plataforma Microsoft Azure. Desarrollamos un guía para desarrolladores que muestra las prácticas recomendadas para la creación de soluciones en la nube en Azure.

## <a name="checklists"></a>Checklists
Estas listas son una referencia rápida para revisar los aspectos fundamentales de disponibilidad y escalabilidad. 

* [Lista de comprobación de disponibilidad][AvailabilityChecklist] 
  
    Resumen de las prácticas recomendadas para garantizar la disponibilidad.
* [Lista de comprobación de escalabilidad][ScalabilityChecklist]
  
    Resumen de prácticas recomendadas para diseñar e implementar servicios escalables y controlar la administración de datos.

## <a name="best-practices-articles"></a>Artículos sobre prácticas recomendadas
Estos artículos proporcionan una explicación más detallada de conceptos importantes que suelen estar asociadas a la informática en la nube. 

* [Diseño de API][APIDesign] 
  
    Explicación de los problemas de diseño para tener en cuenta al diseñar una API web.
* [Implementación de API][APIImplementation] 
  
    Conjunto de procedimientos recomendados para implementar y publicar una API web.
* [Guía de seguridad de API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    Explicación de los problemas de autenticación y autorización (por ejemplo, tipos de token, protocolos de autorización, flujos de autorización y mitigación de amenazas).
* [Instrucciones de escalado automático][AutoscalingGuidance] 
  
    Resumen de consideraciones para escalar soluciones sin necesidad de intervención manual.
* [Guía sobre los trabajos en segundo plano][BackgroundJobsGuidance] 
  
    Descripción de las opciones disponibles y las prácticas recomendadas para implementar las tareas que deben realizarse en segundo plano, independientemente de cualquier primer plano u operaciones interactivas.
* [Guía sobre Content Delivery Network (CDN)][CDNGuidance] 
  
    Instrucciones generales y práctica recomendada para el uso de la red CDN para minimizar la carga en sus aplicaciones y maximizar la disponibilidad y el rendimiento.
* [Guía sobre el almacenamiento en caché][CachingGuidance] 
  
    Resumen de cómo usar el almacenamiento en caché para mejorar el rendimiento y la escalabilidad de un sistema.
* [Guía para crear particiones de datos][DataPartitioningGuidance]
  
    Estrategias que puede usar para crear particiones de datos para mejorar la escalabilidad, reducir la contención y optimizar el rendimiento.
* [Guía sobre supervisión y diagnóstico][MonitoringandDiagnosticsGuidance] 
  
    Guía sobre cómo comprobar cómo los usuarios usan el sistema, comprobar el uso de los recursos y supervisar en general el estado y el rendimiento del sistema.
* [Convenciones de nomenclatura recomendadas][naming-conventions] 
  
    Convenciones de nomenclatura recomendadas para los recursos de Azure.
* [Guía general sobre reintentos][RetryGeneralGuidance] 
  
    Explicación de los conceptos generales para controlar errores transitorios.
* [Guía sobre reintentos específica del servicio][RetryServiceSpecificGuidance]
  
    Resumen de las características de reintentos para muchos servicios de Azure, incluida información para ayudarle a usar, adaptar o ampliar el mecanismo de reintentos para ese servicio.

## <a name="scenario-guides"></a>Guías sobre escenarios
* [Ejecución de ElasticSearch en Azure][elasticsearch] 
  
    Elasticsearch es una base de datos y un motor de búsqueda de código abierto de gran escalabilidad. Es adecuado para situaciones que requieren un análisis y un descubrimiento rápidos de la información contenida en grandes conjuntos de datos. Esta guía analiza algunos aspectos claves a tener en cuenta al diseñar un clúster de Elasticsearch.
* [Administración de identidades en aplicaciones multiinquilino][identity-multitenant] 
  
    En una arquitectura multiinquilino, varios inquilinos comparten una aplicación pero están aislados entre sí. Esta guía muestra cómo administrar identidades de usuario en una aplicación multiinquilino, usando [Azure Active Directory][AzureAD] para controlar el inicio de sesión y la autenticación.
* [Desarrollo de soluciones de macrodatos](https://msdn.microsoft.com/library/dn749874.aspx)
  
    Esta guía explora el uso de HDInsight para escenarios como la exploración iterativa, como un almacén de datos, para los procesos ETL, y la integración en los sistemas existentes de BI. También incluye instrucciones para comprender los conceptos de los macrodatos, la planeación y el diseño de soluciones de macrodatos y la implementación de estas soluciones.

## <a name="patterns"></a>Patrones
* [Patrones de diseño en la nube: guía de arquitectura preceptiva para las aplicaciones en la nube](https://msdn.microsoft.com/library/dn568099.aspx)
  
    Patrones de diseño en la nube es una biblioteca de patrones de diseño y temas con instrucciones relacionadas. Articula la ventaja de aplicar patrones mostrando cómo cada pieza tiene cabida en arquitecturas de aplicación en la nube.
* [Optimización del rendimiento para aplicaciones en la nube](https://github.com/mspnp/performance-optimization)
  
    Esta guía es una exploración de antipatrones comunes que impiden que las aplicaciones escalen con carga. Incluye ejemplos que muestran ocho antipatrones y un [manual de análisis de rendimiento](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) y una guía para [evaluar el rendimiento frente a las métricas clave](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Arquitecturas de referencia
Nuestras arquitecturas de referencia se organizan por escenario.
Todas las arquitectura individuales ofrecen prácticas recomendadas y pasos preceptivos, así como un componente ejecutable que incluye las recomendaciones.

La biblioteca actual de arquitecturas de referencia está disponible en [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Guía de resistencia
Estos temas describen cómo diseñar aplicaciones que sean resistentes a los errores en un entorno de nube distribuida.   

* [Introducción a la resistencia][ResiliencyOvervew]
  
     Cómo crear aplicaciones en la plataforma Azure que pueden recuperarse de errores y continuar funcionando. Describe un enfoque estructurado para lograr resistencia, desde el diseño hasta la implementación, la distribución y las operaciones.
* [Lista de comprobación de resistencia][resiliency-checklist]
  
    Una lista de comprobación de recomendaciones que le ayudarán a planear una variedad de modos de error que podrían producirse.
* [Análisis del modo de error][resiliency-fma] 
  
    Análisis de errores de modo (FMA) es un proceso de creación de resistencia en un sistema mediante la identificación de posibles puntos de error. Como punto de partida para el proceso FMA, este artículo contiene un catálogo de los modos de error potenciales y sus mitigaciones. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Feb17_HO1-->


