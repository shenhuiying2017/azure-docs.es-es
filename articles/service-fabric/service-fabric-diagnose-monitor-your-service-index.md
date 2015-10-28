<properties
   pageTitle="Diagnosticar y solucionar problemas de un servicio de Service Fabric"
   description="Tutoriales e información sobre conceptos que le ayudarán a diagnosticar, supervisar y solucionar problemas de un servicio de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2015"
   ms.author="ryanwi"/>

# Diagnosticar y supervisar un servicio de Service Fabric
Acciones como supervisar, detectar, diagnosticar y solucionar problemas permite a los servicios continuar con una interrupción mínima de la experiencia del usuario. Para obtener más información, lea:

- [Cómo supervisar y diagnosticar servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Configuración de Application Insights para su aplicación de Service Fabric](service-fabric-diagnostics-application-insights-setup.md)
- [Solucionar problemas de errores de una actualización de aplicación](service-fabric-application-upgrade-troubleshooting.md)
- [Supervisión de diagnósticos y rendimiento para actores confiables](service-fabric-reliable-actors-diagnostics.md)
- [Supervisión de diagnósticos y rendimiento para servicios de confianza](service-fabric-reliable-services-diagnostics.md)

## Solucionar problemas de un clúster
La siguiente información le ayudará a solucionar problemas de su clúster de desarrollo local:

- [Solucionar problemas de su instalación de clúster de desarrollo local](service-fabric-troubleshoot-local-cluster-setup.md)

## Modelo de estado
Service Fabric presenta un modelo de estado que ofrece una funcionalidad de evaluación e informes enriquecida, flexible y extensible para las entidades de Service Fabric. Los componentes de Service Fabric informan del estado desde el primer momento en todas las entidades. Los servicios de usuario pueden enriquecer los datos de estado con información específica de su lógica, sobre sí mismos u otras entidades del clúster. Para obtener más información, lea:

- [Introducción a la supervisión del estado de Service Fabric](service-fabric-health-introduction.md)
- [Cómo ver los informes de mantenimiento de Service Fabric](service-fabric-view-entities-aggregated-health.md)
- [Uso de informes de mantenimiento del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Adición de informes de mantenimiento de Service Fabric personalizados](service-fabric-report-health.md)

<!---HONumber=Oct15_HO3-->