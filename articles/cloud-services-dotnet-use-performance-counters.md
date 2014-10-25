<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Uso de contadores de rendimiento en Azure

Puede utilizar contadores de rendimiento en una aplicación de Azure para
recopilar datos que ayuden a determinar cuellos de botella en el sistema y a ajustar el
rendimiento de las aplicaciones y los sistemas. Puede recopilar y utilizar los contadores de rendimiento disponibles para Windows Server 2008, Windows Server 2012, IIS y ASP.NET para determinar el estado de la aplicación de Azure.

Puede configurar contadores de rendimiento antes de la implementación o en tiempo de ejecución dentro de Visual Studio 2012 o Visual Studio 2013 con Azure SDK 2.0 o una versión posterior. Para obtener más información, consulte [Configurar Diagnósticos de Azure][]. Para obtener información sobre la configuración manual de los contadores de rendimiento en una aplicación, vea [Configurar contadores de rendimiento][].

Para obtener instrucciones más detalladas acerca de la creación de una estrategia de registro y seguimiento, así como del uso de los diagnósticos y de otras técnicas para solucionar problemas, consulte [Troubleshooting Best Practices for Developing Azure Applications][].

## <a name="nextsteps"> </a>Pasos siguientes

Siga estos vínculos para obtener información acerca de cómo implementar escenarios de solución de problemas más complejos.

-   [Probar el rendimiento de un servicio en la nube][]
-   [Procedimientos recomendados de solución de problemas para desarrollar aplicaciones de Azure][Troubleshooting Best Practices for Developing Azure Applications]
-   [Supervisión de servicios en la nube][]
-   [Uso del bloque de autoescala de la aplicación][]
-   [Creación de aplicaciones de nube elásticas y resistentes][]

## <a name="additional"> </a>Recursos adicionales

-   [Habilitación de Diagnósticos en Azure][]
-   [Recopilación de datos de registro mediante Diagnósticos de Azure][]
-   [Depurar servicios en la nube][]

  [Configurar Diagnósticos de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/dn186185.aspx
  [Configurar contadores de rendimiento]: http://msdn.microsoft.com/es-es/library/azure/dn535595.aspx
  [Troubleshooting Best Practices for Developing Azure Applications]: http://msdn.microsoft.com/es-es/library/windowsazure/hh771389.aspx
  [Probar el rendimiento de un servicio en la nube]: http://msdn.microsoft.com/es-es/library/azure/hh369930.aspx
  [Supervisión de servicios en la nube]: http://azure.microsoft.com/es-es/documentation/articles/cloud-services-how-to-monitor/
  [Uso del bloque de autoescala de la aplicación]: http://azure.microsoft.com/es-es/documentation/articles/cloud-services-dotnet-autoscaling-application-block/
  [Creación de aplicaciones de nube elásticas y resistentes]: http://msdn.microsoft.com/es-es/library/hh680949(PandP.50).aspx
  [Habilitación de Diagnósticos en Azure]: http://azure.microsoft.com/es-es/documentation/articles/cloud-services-dotnet-diagnostics/
  [Recopilación de datos de registro mediante Diagnósticos de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg433048.aspx
  [Depurar servicios en la nube]: http://msdn.microsoft.com/es-es/library/windowsazure/ee405479.aspx
