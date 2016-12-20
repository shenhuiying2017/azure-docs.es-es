---
title: "Uso de la configuración de PM2 para Node.js en Web Apps en Linux | Microsoft Docs"
description: "Uso de la configuración de PM2 para Node.js en Web Apps en Linux"
keywords: azure app service, aplic. web, nodejs, pm2, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: fb420f32-6d74-49c7-992f-0ed5616e66e7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a46f52198e956406c2384c45ed4323aff27259a0


---
# <a name="use-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Uso de la configuración de PM2 para Node.js en Web Apps en Linux
Si establece la pila de la aplicación en Node.js para Web Apps en Linux, tiene la opción de establecer un archivo de inicio de Node.js, como se muestra en la siguiente imagen.

![Establecer un archivo de inicio de Node.js][1]

Se puede utilizar para realizar una de las siguientes tareas:

* Especificar el script de inicio de una aplicación de Node.js (por ejemplo: /bin/server.js).
* Especificar el archivo de configuración de PM2 que se va a usar para una aplicación de Node.js (por ejemplo: /foo/process.json).
  
  > [!NOTE]
  > Si desea que los procesos de Node.js se reinicien automáticamente al modificar determinados archivos, utilice la configuración de PM2. En caso contrario, la aplicación no se reiniciará cuando recibe notificaciones de cambio (por ejemplo, cuando cambia el código de aplicación).
  > 
  > 

En la [documentación del archivo de proceso](http://pm2.keymetrics.io/docs/usage/application-declaration/) de Node.js puede consultar todas las opciones, pero a continuación encontrará un ejemplo de lo que puede usar como archivo process.json:

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

En esta configuración debe tenerse en cuenta lo siguiente:

* La propiedad "script" especifica el script de inicio de la aplicación.
* La propiedad "instances" especifica el número de instancias del proceso de nodo que se inician. Si la aplicación se ejecuta en máquinas virtuales mayores, con varios núcleos, se recomienda maximizar los recursos, para lo que hay que establecer un valor mayor aquí.
* La matriz "watch" especifica todos los archivos que se desean para reiniciar el proceso del nodo cuando cambian.
* Para "watch_options", actualmente es preciso especificar "usePolling" como true, debido al modo en que se monta el contenido de la aplicación.

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es App Service en Linux?](app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png



<!--HONumber=Nov16_HO3-->


