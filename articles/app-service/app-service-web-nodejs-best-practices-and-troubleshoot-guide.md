---
title: "Guía de procedimientos recomendados y solución de problemas para aplicaciones Node en Azure Web Apps"
description: "Aprenda los procedimientos recomendados y los pasos de solución de problemas para aplicaciones Node en Azure Web Apps."
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: 
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.openlocfilehash: f7f3186ba93670e566a10f97dde86a977101e8fc
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Guía de procedimientos recomendados y solución de problemas para aplicaciones Node en Azure Web Apps
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

En este artículo, aprenderá los procedimientos recomendados y los pasos de solución de problemas de las [aplicaciones de Node](app-service-web-get-started-nodejs.md) que se ejecutan en Azure Web Apps (con [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Tenga cuidado cuando aplique los pasos para solucionar problemas en el sitio de producción. Se recomienda solucionar los problemas de la aplicación en una configuración que no sea de producción (por ejemplo, su espacio de ensayo) y, una vez corregido el problema, cambiar el espacio de ensayo por el de producción.
> 
> 

## <a name="iisnode-configuration"></a>Configuración de IISNODE
Este [archivo de esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) muestra todas las configuraciones que pueden establecer para iisnode. Algunas de las que serán útiles para su aplicación son:

* nodeProcessCountPerApplication
  
    Esta configuración controla el número de procesos de nodo que se inician por cada aplicación IIS. El valor predeterminado es 1. Puede iniciar tantos procesos node.exe como vCPU posea la máquina virtual si establece esta configuración en 0. El valor recomendado es 0 para la mayoría de las aplicaciones, para que pueda usar todas las vCPU en la máquina. Node.exe es un único subproceso, de modo que un proceso node.exe consume un máximo de una vCPU. Para obtener el máximo rendimiento de la aplicación de nodo, quiere usar todas las vCPU.
* nodeProcessCommandLine
  
    Esta configuración controla la ruta de acceso al proceso node.exe. Puede establecer este valor para que señale a su versión de node.exe.
* maxConcurrentRequestsPerProcess
  
    Esta configuración controla el número máximo de solicitudes simultáneas enviadas por iisnode a cada proceso node.exe. En Azure Web Apps, el valor predeterminado es Infinito. No tendrá que preocuparse por esta configuración. Fuera de Azure Web Apps, el valor predeterminado es 1024. Puede definir esta configuración en función de la cantidad de solicitudes que reciba la aplicación y de la velocidad con que la aplicación procese cada una.
* maxNamedPipeConnectionRetry
  
    Esta configuración controla el número máximo de veces que iisnode volverá a intentar establecer conexión en la canalización con nombre para enviar la solicitud a node.exe. Esta configuración, combinada con namedPipeConnectionRetryDelay, determina el tiempo de espera total de cada solicitud dentro de iisnode. El valor predeterminado es 200 en Azure Web Apps. Tiempo de espera total en segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
* namedPipeConnectionRetryDelay
  
    Esta configuración controla la cantidad de tiempo (en milisegundos) que iisnode esperará entre cada reintento de enviar la solicitud a node.exe a través de la canalización con nombre. El valor predeterminado es 250 ms.
    Tiempo de espera total en segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
  
    De forma predeterminada, el tiempo de espera total de iisnode en Azure Web Apps es de 200 \* 250 ms = 50 segundos.
* logDirectory
  
    Esta configuración controla el directorio donde iisnode registra stdout y stderr. El valor predeterminado es iisnode, que es relativo al directorio de scripts principal (el directorio donde se encuentra el archivo server.js principal).
* debuggerExtensionDll
  
    Esta configuración controla la versión de node-inspector que iisnode usa para depurar su aplicación Node. Actualmente, los únicos dos valores válidos para esta configuración son iisnode-inspector-0.7.3.dll e iisnode-inspector.dll. El valor predeterminado es iisnode-inspector-0.7.3.dll. La versión iisnode-inspector-0.7.3.dll usa node-inspector-0.7.3 y WebSockets. Debe habilitar los WebSockets en su Azure Web App para usar esta versión. Consulte <http://www.ranjithr.com/?p=98> para más información sobre cómo configurar iisnode para que use la nueva versión de node-inspector.
* flushResponse
  
    El comportamiento predeterminado de IIS es almacenar hasta 4 MB de datos de respuesta en el búfer antes de vaciarlo o hasta el final de la respuesta, lo que ocurra primero. iisnode ofrece una opción de configuración que invalida este comportamiento: para vaciar un fragmento del cuerpo de la entidad de respuesta en cuanto iisnode lo recibe de node.exe, es preciso establecer el atributo iisnode/@flushResponse de web.config en "true":
  
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```
  
    La habilitación del vaciado de todos los fragmentos del cuerpo de la entidad de respuesta aumenta la sobrecarga de rendimiento y reduce el rendimiento del sistema aproximadamente en un 5 % (a partir de la versión 0.1.13), por lo que se recomienda definir el ámbito de esta configuración solo en los puntos de conexión que requieran streaming de respuestas (p. ej., mediante el elemento <location> en web.config)
  
    Además, para las aplicaciones de streaming, debe establecer también el atributo responseBufferLimit del controlador de iisnode en 0.
  
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```
* watchedFiles
  
    Se trata de una lista de archivos separados por punto y coma que se inspeccionan para ver si hay cambios. Cuando se produce un cambio en un archivo, se produce el reciclaje de la aplicación. Cada entrada consta de un nombre de directorio opcional más un nombre de archivo necesario, que son relativos al directorio donde se encuentra el punto de entrada de la aplicación principal. Solo se permiten comodines en la parte del nombre de archivo. El valor predeterminado es "\*.js;web.config".
* recycleSignalEnabled
  
    El valor predeterminado es false. Si está habilitado, la aplicación de Node puede conectarse a una canalización con nombre (variable de entorno IISNODE\_CONTROL\_PIPE) y enviar un mensaje de "reciclaje". Esto hace que se recicle w3wp correctamente.
* idlePageOutTimePeriod
  
    El valor predeterminado es 0, lo que significa que esta característica está deshabilitada. Cuando se establece en un valor mayor que 0, iisnode quita páginas de todos sus procesos secundarios cada 'idlePageOutTimePeriod' en milisegundos. Consulte la [documentación](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) para comprender lo que significar quitar páginas. Esta configuración resulta útil para las aplicaciones que consumen mucha memoria y que ocasionalmente desean quitar páginas de memoria en disco para liberar algo de RAM.

> [!WARNING]
> Tenga cuidado cuando habilite las siguientes opciones de configuración en aplicaciones de producción. Se recomienda no habilitarlas en aplicaciones de producción activas.
> 
> 

* debugHeaderEnabled
  
    El valor predeterminado es false. Si se establece en true, iisnode agrega un encabezado de respuesta HTTP iisnode-debug a cada respuesta HTTP que envía; el valor del encabezado iisnode-debug es una dirección URL. Se pueden deducir partes individuales de la información de diagnóstico examinando el fragmento de dirección URL, pero se consigue una mejor visualización si se abre la dirección URL en el explorador.
* loggingEnabled
  
    Esta configuración controla el registro de stdout y stderr por parte de iisnode. Iisnode captura stdout y stderr de los procesos de nodo que inicia y escribe en el directorio especificado en la configuración 'logDirectory'. Una vez que este atributo está habilitado, la aplicación escribe registros en el sistema de archivos y, según la cantidad de entradas que registre la aplicación, el rendimiento podría verse afectado.
* devErrorsEnabled
  
   El valor predeterminado es false. Cuando está establecido en true, iisnode muestra el código de estado HTTP y el código de error Win32 en el explorador. El código win32 resulta útil al depurar determinados tipos de problemas.
* debuggingEnabled (no se debe habilitar en el sitio de producción)
  
    Esta configuración controla la característica de depuración. Iisnode está integrado con node-inspector. Si habilita esta configuración, se habilitará la depuración de la aplicación Node. Una vez habilitada esta opción, iisnode preparará los archivos de node-inspector necesarios en el directorio 'debuggerVirtualDir' con la primera solicitud de depuración enviada a la aplicación Node. Para cargar node-inspector, envíe una solicitud a http://yoursite/server.js/debug. Puede controlar el segmento de dirección URL de depuración con el valor 'debuggerPathSegment'. De forma predeterminada, debuggerPathSegment es 'debug'. Puede establecerlo en un GUID, por ejemplo, para que resulte más difícil que otros usuarios lo detecten.
  
    Consulte este [vínculo](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para más información sobre la depuración.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Escenarios, recomendaciones y solución de problemas
### <a name="my-node-application-is-making-too-many-outbound-calls"></a>Mi aplicación Node realiza demasiadas llamadas salientes.
Muchas aplicaciones van a establecer conexiones salientes como parte de su funcionamiento normal. Por ejemplo, cuando llega una solicitud, la aplicación Node va a establecer contacto con una API de REST en otro lugar y así obtener información para procesar la solicitud. Debería utilizar un agente de conexión persistente al realizar llamadas HTTP o HTTPS. Por ejemplo, podría utilizar el módulo agentKeepAlive como agente de conexión persistente cuando realice estas llamadas salientes. Esto garantiza que se reutilicen los sockets en su máquina virtual de Azure Web App, por lo que se reducirá la sobrecarga que supone crear sockets para cada solicitud saliente. Además, garantiza que se usen menos sockets para realizar muchas solicitudes salientes y, por tanto, no se supere el valor maxSockets asignado para cada máquina virtual. La recomendación sobre Azure Web Apps es establecer el valor maxSockets de agentKeepAlive en un total de 160 sockets por máquina virtual. Esto significa que, si tiene cuatro procesos node.exe en ejecución en la máquina virtual, debe establecer el valor maxSockets de agentKeepAlive en 40 por node.exe, es decir, un total de 160 por máquina virtual.

Ejemplo de configuración de [agentKeepALive](https://www.npmjs.com/package/agentkeepalive):

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

En este ejemplo se da por supuesto que tiene cuatro procesos node.exe en ejecución en la máquina virtual. Si tiene un número diferente de procesos node.exe ejecutándose en la máquina virtual, tiene que modificar el valor maxSockets en consecuencia.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>Mi aplicación Node consume demasiada CPU.
Probablemente recibirá una recomendación de Azure Web Apps en el portal sobre el elevado consumo de CPU. También puede configurar monitores para inspeccionar determinadas [métricas](web-sites-monitor.md). Al comprobar el uso de la CPU en el [panel de Azure Portal](../application-insights/app-insights-web-monitor-performance.md), compruebe los valores MAX de la CPU para que no pase por alto los valores máximos.
En aquellos casos en que crea que su aplicación consume demasiada CPU y no pueda explicar por qué, debe generar un perfil de la aplicación Node para averiguarlo.

### 
#### <a name="profiling-your-node-application-on-azure-web-apps-with-v8-profiler"></a>Generación del perfil de su aplicación Node en Azure Web Apps con V8-Profiler
Por ejemplo, supongamos que tiene una aplicación "Hola mundo" cuyo perfil desea generar, como se muestra a continuación:

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Vaya a su sitio de SCM https://yoursite.scm.azurewebsites.net/DebugConsole

Aparece un símbolo del sistema, como se muestra a continuación. Vaya al directorio /wwwroot del sitio.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Ejecute el comando "npm install v8-profiler".

Así se deberían instalar v8-profiler en el directorio node\_modules y todas sus dependencias.
Ahora, edite el archivo server.js para generar el perfil de la aplicación.

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Con el código anterior, se genera el perfil de la función WriteConsoleLog y después se escribe la salida de perfil en el archivo 'profile.cpuprofile' en el directorio wwwroot del sitio. Envíe una solicitud a la aplicación. Puede ver que se crea un archivo 'profile.cpuprofile' en el directorio wwwroot del sitio.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Descargue este archivo y ábralo con las herramientas F12 de Chrome. Presione F12 en Chrome y seleccione la pestaña **Profiles** (Perfiles). Elija el botón **Cargar**. Seleccione el archivo profile.cpuprofile que acaba de descargar. Haga clic en el perfil recién cargado.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Puede ver que el 95 % del tiempo lo consumió la función WriteConsoleLog. También se muestran los números de línea y los archivos de código fuente exactos que causan el problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Mi aplicación Node consume demasiada memoria.
Si la aplicación está consumiendo demasiada memoria, aparece un aviso de Azure Web Apps en el portal que indica un consumo elevado de memoria. Puede configurar monitores para inspeccionar determinadas [métricas](web-sites-monitor.md). Al comprobar el uso de la memoria en el [panel de Azure Portal](../application-insights/app-insights-web-monitor-performance.md), asegúrese de comprobar los valores MAX de la memoria, para que no pase por alto los valores máximos.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>Detección de fugas y comparación de montones para node.js
Podría usar [node-memwatch](https://github.com/lloyd/node-memwatch) como ayuda para identificar fugas de memoria.
Puede instalar memwatch igual que v8-profiler y editar su código para que capture y compare montones a fin de identificar las fugas de memoria en la aplicación.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Los procesos node.exe se terminan de forma aleatoria
Existen varias razones para que suceda esto:

1. La aplicación inicia excepciones no detectadas. Compruebe el archivo d:\\home\\LogFiles\\Application\\logging-errors.txt para ver detalles sobre la excepción iniciada. Este archivo contiene el seguimiento de la pila, así que puede corregir la aplicación en función de esto.
2. La aplicación consume demasiada memoria, lo cual afecta al inicio de otros procesos. Si la memoria total de la máquina virtual se acerca al 100 %, es posible que el Administrador de procesos termine su proceso node.exe para permitir que otros procesos lleguen a funcionar. Para solucionar este problema, asegúrese de que la aplicación no tenga una fuga de memoria; o bien, si la aplicación realmente necesita usar una gran cantidad de memoria, escale verticalmente a una máquina virtual más grande con mucha más memoria RAM.

### <a name="my-node-application-does-not-start"></a>Mi aplicación Node no se inicia
Si la aplicación devuelve errores 500 durante el inicio, pueden existir varias razones:

1. Node.exe no se encuentra en la ubicación correcta. Compruebe la configuración nodeProcessCommandLine.
2. El archivo de script principal no se encuentra en la ubicación correcta. Compruebe web.config y asegúrese de que el nombre del archivo de script principal en la sección de controladores coincida con el archivo de script principal.
3. La configuración de web.config no es correcta: compruebe los nombres y los valores de configuración.
4. Arranque en frío: la aplicación tarda demasiado tiempo en iniciarse. Si la aplicación tarda más de (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 segundos, iisnode devuelve un error 500. Aumente los valores de estas configuraciones para que coincidan con el tiempo de inicio de la aplicación, para evitar que se agote el tiempo de espera de iisnode y se devuelva el error 500.

### <a name="my-node-application-crashed"></a>Mi aplicación Node se ha bloqueado
La aplicación inicia excepciones no detectadas. Compruebe el archivo d:\\home\\LogFiles\\Application\\logging-errors.txt para ver detalles sobre la excepción iniciada. Este archivo contiene el seguimiento de la pila, así que puede corregir la aplicación en función de esto.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Mi aplicación Node tarda demasiado tiempo en iniciarse (arranque en frío)
La razón más común para que una aplicación tarde demasiado tiempo en iniciarse es la existencia de un gran número de archivos en node\_modules. La aplicación intenta cargar la mayoría de estos archivos al iniciarse. De forma predeterminada, puesto que los archivos residen en el recurso compartido de red en Azure Web Apps, se puede tardar bastante tiempo en cargar muchos archivos.
Algunas soluciones para que este proceso sea más rápido son:

1. Asegúrese de tener una estructura de dependencias plana y sin dependencias duplicadas; para ello, use npm3 para instalar los módulos.
2. Pruebe a cargar node\_modules en diferido y a no cargar todos los módulos durante el inicio de la aplicación. Esto significa que se debe realizar la llamada a require('módulo') cuando sea realmente necesario dentro de la función que prueba al usar el módulo.
3. Azure Web Apps ofrece una característica denominada caché local. Esta característica copia el contenido del recurso compartido de red en el disco local de la máquina virtual. Como los archivos son locales, el tiempo de carga de node\_modules es mucho menor.

## <a name="iisnode-http-status-and-substatus"></a>Estado y subestado HTTP de IISNODE
Este [archivo de código fuente](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) enumera todas las combinaciones posibles de estado y subestado que iisnode puede devolver en caso de error.

Habilite FREB para la aplicación a fin de ver el código de error win32 (asegúrese de habilitar FREB solo en sitios de no producción, por motivos de rendimiento).

| Estado HTTP | Subestado HTTP | ¿Posible razón? |
| --- | --- | --- |
| 500 |1000 |Se produjo algún problema al enviar la solicitud a IISNODE; compruebe si se inició node.exe. Es posible que node.exe se haya bloqueado durante el inicio. Compruebe si hay errores en la configuración de web.config. |
| 500 |1001 |- Win32Error 0x2: la aplicación no responde a la dirección URL. Compruebe las reglas de reescritura de direcciones URL o si su aplicación rápida tiene definidas las rutas correctas. -Win32Error 0x6d: la canalización con nombre está ocupada; node.exe no acepta solicitudes porque la canalización está ocupada. Compruebe el uso elevado de CPU. -Otros errores: compruebe si se bloqueó node.exe. |
| 500 |1002 |Se bloqueó node.exe: compruebe el seguimiento de la pila en d:\\home\\LogFiles\\logging-errors.txt. |
| 500 |1003 |Problema de configuración de canalización: no debería ver esto nunca pero, si lo ve, la configuración de canalización con nombre es incorrecta. |
| 500 |1004-1018 |Se produjo un error al enviar la solicitud o al procesar la respuesta enviada a node.exe o recibida de él. Compruebe si se bloqueó node.exe. compruebe el seguimiento de la pila en d:\\home\\LogFiles\\logging-errors.txt. |
| 503 |1000 |No hay memoria suficiente para asignar más conexiones de canalización con nombre. Compruebe por qué la aplicación consume tanta memoria. Compruebe el valor de la configuración maxConcurrentRequestsPerProcess. Si no es infinito y tiene gran cantidad de solicitudes, auméntelo para evitar este error. |
| 503 |1001 |No se pudo enviar la solicitud a node.exe porque la aplicación se está reciclando. Una vez reciclada la aplicación, se deberían atender las solicitudes normalmente. |
| 503 |1002 |Compruebe la verdadera razón en el código de error win32; no se pudo enviar la solicitud a un proceso node.exe. |
| 503 |1003 |Canalización con nombre demasiado ocupada; compruebe si el nodo consume una cantidad excesiva de CPU. |

Existe una opción en NODE.exe denominada NODE\_PENDING\_PIPE\_INSTANCES. De forma predeterminada, fuera de Azure Web Apps, este valor es 4. Esto significa que node.exe solo puede aceptar cuatro solicitudes a la vez en la canalización con nombre. En Azure Web Apps, este valor se establece en 5000. Este valor debería bastar para la mayoría de las aplicaciones Node que se ejecutan en Azure Web Apps. No debería ver 503.1003 en Azure Web Apps, ya que el valor de NODE\_PENDING\_PIPE\_INSTANCES es elevado.  |

## <a name="more-resources"></a>Más recursos
Siga estos vínculos para obtener más información acerca de las aplicaciones de node.js en Azure App Service.

* [Introducción a las aplicaciones web Node.js en Azure App Service](app-service-web-get-started-nodejs.md)
* [Cómo depurar una aplicación web de Node.js en Azure App Service](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Uso de módulos Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js (Aplicaciones web del Servicio de aplicaciones de Azure: Node.js)](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro para desarrolladores de Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Exploring the Super Secret Kudu Debug Console (Exploración de la consola de depuración súper secreta de Kudu)](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)

