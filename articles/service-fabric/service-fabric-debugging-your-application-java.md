---
title: "Depuración de la aplicación de Azure Service Fabric en Eclipse | Microsoft Docs"
description: "Mejore la confiabilidad y el rendimiento de los servicios mediante su desarrollo y depuración en Eclipse en un clúster de desarrollo local."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: vturecek;mikhegn
ms.openlocfilehash: f3bcee3794de35005bd387ecfae7e6707f3cb5ee
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Depuración de la aplicación de Service Fabric para Java con Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Inicie un clúster de desarrollo local siguiendo los pasos descritos en [Configurar el entorno de desarrollo de Service Fabric](service-fabric-get-started-linux.md).

2. Actualice el archivo entryPoint.sh del servicio que desea depurar para que comience el proceso de Java con parámetros de depuración remota. Este archivo se puede encontrar en la ubicación siguiente: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``. En este ejemplo, el puerto 8001 está establecido para depuración.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Actualice el manifiesto de aplicación estableciendo el recuento de instancias o el recuento de réplicas para el servicio que está en depuración en 1. Esta configuración evita conflictos en el puerto que se usa para depuración. Por ejemplo, para servicios sin estado, establezca ``InstanceCount="1"`` y, para los servicios con estado, establezca los tamaños del conjunto de réplicas mínimo y de destino en 1 de la manera siguiente: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

4. Implemente la aplicación.

5. En el IDE de Eclipse, seleccione **Run -> Debug Configurations -> Remote Java Application and input connection properties** (Ejecutar -> Configuraciones de depuración -> Aplicación Java remota y propiedades de conexión de entrada) y establezca las propiedades como se muestra a continuación:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Establezca puntos de interrupción en los puntos deseados y depure la aplicación.

Si se bloquea la aplicación, puede que también desee habilitar los volcados de memoria. Ejecute ``ulimit -c`` en un shell y, si devuelve el valor 0, los volcados de memoria no están habilitados. Para habilitar volcados de memoria ilimitados, ejecute el comando siguiente: ``ulimit -c unlimited``. También podemos comprobar el estado con el comando ``ulimit -a``.  Si desea actualizar la ruta de acceso a la generación de volcado de memoria, ejecute ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``. 

### <a name="next-steps"></a>Pasos siguientes

* [Recopilación de registros con Diagnósticos de Azure de Linux](service-fabric-diagnostics-how-to-setup-lad.md).
* [Supervisión y diagnóstico de servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
