---
title: Supervisión y diagnóstico local de servicios creados con Service Fabric de Azure | Microsoft Docs
description: Aprenda a supervisar y diagnosticar sus servicios creados con Service Fabric de Microsoft Azure en una máquina de desarrollo local.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2016
ms.author: subramar

---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Supervisión y diagnóstico de servicios en una configuración de desarrollo de máquina local
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Acciones como supervisar, detectar, diagnosticar y solucionar problemas permiten a los servicios continuar con una interrupción mínima de la experiencia del usuario. La supervisión y el diagnóstico resultan fundamentales en un entorno de producción implementado real. La adopción de un modelo similar durante el desarrollo de servicios garantiza que la canalización del diagnóstico funcione cuando pasa a un entorno de producción. Service Fabric facilita a los desarrolladores de servicio la implementación de diagnósticos que puede funcionar sin problemas en configuraciones de desarrollo local de máquina única y en configuraciones de clúster de producción del mundo real.

## <a name="debugging-service-fabric-java-applications"></a>Depuración de aplicaciones Java de Service Fabric
Para aplicaciones Java, hay [varias plataformas de registro](http://en.wikipedia.org/wiki/Java_logging_framework) disponibles. Puesto que `java.util.logging` es la opción predeterminada con JRE, también se usa para los [ejemplos de código en GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  En la siguiente discusión se explica cómo configurar la plataforma `java.util.logging` . 

Con java.util.logging puede redirigir los registros de aplicaciones a la memoria, flujos de salida, archivos de consolas o sockets. Para cada una de estas opciones, hay controladores predeterminados que se proporcionan en la plataforma. Puede crear un archivo `app.properties` para configurar el controlador de archivo para la aplicación a fin de redirigir todos los registros a un archivo local. 

El fragmento de código siguiente contiene una configuración de ejemplo: 

```java 
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

La carpeta señalada por el archivo `app.properties` debe existir. Una vez creado el archivo `app.properties`, debe modificar igualmente el script de punto de entrada, `entrypoint.sh` en la carpeta `<applicationfolder>/<servicePkg>/Code/` para establecer la propiedad `java.util.logging.config.file` en el archivo `app.propertes`. La entrada debería parecerse al siguiente fragmento:

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Esta configuración hace que se recopilen los registros por orden de rotación en `/tmp/servicefabric/logs/`. El **%u** y el **%g** permiten crear más archivos, con nombres de archivo mysfapp0.log, mysfapp1.log, etc. De forma predeterminada, si no hay ningún controlador configurado explícitamente, se registra el controlador de la consola. Se pueden ver los registros de syslog en /var/log/syslog.

Para más información, consulte los [ejemplos de código en GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  

## <a name="next-steps"></a>Pasos siguientes
El mismo código de seguimiento que agregó a la aplicación también funciona con los diagnósticos de la aplicación en un clúster de Azure. Consulte estos artículos que tratan sobre las distintas opciones de las herramientas y describen cómo puede configurarlas.

* [Recopilación de registros con Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-lad.md)

<!--HONumber=Oct16_HO2-->


