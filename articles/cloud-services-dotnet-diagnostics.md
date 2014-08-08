<properties linkid="dev-net-commons-tasks-diagnostics" urlDisplayName="Diagnostics" pageTitle="How to use diagnostics (.NET) - Azure feature guide" metaKeywords="Azure diagnostics monitoring,logs crash dumps C#" description="Learn how to use diagnostic data in Azure for debugging, measuring performance, monitoring, traffic analysis, and more." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Enabling Diagnostics in Azure" authors="ryanwi" solutions="" manager="" editor="" />

Habilitación de Diagnósticos en Azure
=====================================

Diagnósticos de Azure le permite recopilar datos de diagnóstico desde un rol de trabajo o rol web que se ejecute en Azure. Estos datos se pueden usar para depurar y solucionar problemas, medir el rendimiento, supervisar el uso de los recursos, analizar el tráfico y planificar la capacidad y realizar auditorías.

Se pueden configurar los diagnósticos antes de la implementación o en tiempo de ejecución en Visual Studio 2012 o Visual Studio 2013 con Azure SDK 2.0 o posterior. Para obtener más información, consulte [Configurar Diagnósticos de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/dn186185.aspx).

Para obtener instrucciones más detalladas acerca de la creación de una estrategia de registro y seguimiento, así como del uso de los diagnósticos y de otras técnicas para solucionar problemas, consulte [Troubleshooting Best Practices for Developing Azure Applications](http://msdn.microsoft.com/en-us/library/windowsazure/hh771389.aspx).

Para obtener más información acerca de cómo configurar Diagnósticos en la aplicación o cambiar de forma remota la configuración del monitor de Diagnósticos, consulte [Recopilar datos de registro mediante Diagnósticos de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433048.aspx).

Pasos siguientes
----------------

-   [Cambiar de forma remota la configuración del monitor de diagnósticos](http://msdn.microsoft.com/en-us/library/windowsazure/gg432992.aspx) - Cuando se haya implementado la aplicación, se podrá modificar la configuración de Diagnósticos.
-   [Supervisión de servicios en la nube](../cloud-services-how-to-monitor/) - Se pueden supervisar las métricas clave de rendimiento de sus servicios en la nube en el [Portal de administración de Azure](http://manage.windowsazure.com).

Recursos adicionales
--------------------

-   [Recopilar datos de registro mediante Diagnósticos de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433048.aspx)
-   [Depurar servicios en la nube](http://msdn.microsoft.com/en-us/library/windowsazure/ee405479.aspx)
-   [Configurar Diagnósticos de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/dn186185.aspx)


  [Troubleshooting Best Practices for Developing Azure Applications]: http://msdn.microsoft.com/en-us/library/windowsazure/hh771389.aspx
  

  [Using performance counters in Azure]: ../cloud-services-performance-testing-visual-studio-profiler/
  [How to monitor cloud services]: ../cloud-services-how-to-monitor/
  [DiagnosticMonitorTraceListener Class]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx
  [How to Use the Azure Diagnostics Configuration File]: http://msdn.microsoft.com/en-us/library/windowsazure/hh411551.aspx
  [Adding Trace Failed Requests in the IIS 7.0 Configuration Reference]: http://www.iis.net/ConfigReference/system.webServer/tracing/traceFailedRequests/add
  [Using performance counters in Azure]: /en-us/develop/net/common-tasks/performance-profiling/
  [How to Configure Local Storage Resources]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758708.aspx
  [Browsing Storage Resources with Server Explorer]: http://msdn.microsoft.com/en-us/library/windowsazure/ff683677.aspx
  [Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
  [Azure Diagnostics Manager]: http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx
  [Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433048.aspx
  [Debugging an Azure Application]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405479.aspx
  [Use the Azure Diagnostics Configuration File]: http://msdn.microsoft.com/en-us/library/windowsazure/hh411551.aspx
  [LogLevel Enumeration]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.diagnostics.loglevel.aspx
  [OnStart method]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
  [Azure Diagnostics Configuration Schema]: http://msdn.microsoft.com/en-us/library/gg593185.aspx
  [How to use the Table Storage Service]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/table-services/
  [How to use the Azure Blob Storage Service]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/blob-storage/
  [Azure Management Portal]: http://manage.windowsazure.com
  [Remotely Change the Diagnostic Monitor Configuration]: http://msdn.microsoft.com/en-us/library/windowsazure/gg432992.aspx
  [Configuring Azure Diagnostics]: http://msdn.microsoft.com/en-us/library/windowsazure/dn186185.aspx  
   