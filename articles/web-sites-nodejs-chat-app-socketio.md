<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Creación de una aplicación de chat Node.js con Socket.IO en un sitio web de Azure

Socket.IO proporciona comunicación en tiempo real entre su servidor node.js y los clientes con WebSockets. También es compatible con la reserva a otros transportes (como el sondeo largo) que funcionan con otros exploradores. Este tutorial le llevara por el hospedaje de una aplicación de chat basada en Socket.IO como un sitio web de Azure. Para obtener más información sobre Socket.IO, consulte [][]<http://socket.io/></a>.

> [WACOM.NOTE] Los procedimientos de esta tarea se aplican a Sitios web Azure; para Servicios en la nube, consulte [Creación de una aplicación de chat Node.js con Socket.IO en un servicio en la nube de Azure][].

## <span id="Download"></span></a>Descarga del ejemplo de chat

Para este proyecto, usaremos el ejemplo de chat del [repositorio de
Socket.IO GitHub][] (en inglés). Realice los siguientes pasos para descargar el ejemplo
y agréguelo al proyecto que creó anteriormente.

1.  Descargue una [versión archivada ZIP o GZ][] del proyecto Socket.IO (la versión 1.0.6 se ha utilizado para este documento)

2.  Extraiga el archivo y la copia del directorio **examples\\chat**
     a una ubicación nueva. Por ejemplo
    **\\node\\chat**.

## <span id="Modify"></span></a>Modificación de App.js e instalación de módulos

Antes de implementar la aplicación en Azure,
debemos realizar algunas modificaciones menores.

1.  Vuelva a nombrar el archivo **index.js** a **app.js**. Esto permite a Azure detectar que se trata de una aplicación Node.js.

2.  Abra el archivo **app.js** en el Bloc de notas u otro editor de texto.

3.  Busque la sección **Module dependencies** al comienzo del archivo app.js y cambie la línea que contiene `var io = require('../..')(server);` por `var io = require('socket.io')(server);` como se muestra a continuación:

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

Después de guardar los cambios en app.js, use los siguientes pasos
para instalar los módulos necesarios:

1.  Desde la línea de comandos, cambie los directorios al directorio **\\node\\chat** y use el siguiente comando para instalar los módulos requeridos por esta aplicación:

        npm install

    Esta acción instalará los módulos que se mencionan en el archivo package.json. Después
    de que el comando se completa, debería ver un resultado similar al
    siguiente:

        express@3.4.8 node_modules\express
        ├── methods@0.1.0
        ├── merge-descriptors@0.0.1
        ├── debug@0.8.1
        ├── cookie-signature@1.0.1
        ├── range-parser@0.0.4
        ├── fresh@0.2.0
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── mkdirp@0.3.5
        ├── commander@1.3.2 (keypress@0.1.0)
        ├── send@0.1.4 (mime@1.2.11)
        └── connect@2.12.0 (uid2@0.0.3, pause@0.0.1, qs@0.6.6, bytes@0.2.1, raw-body@1.1.2, batch@0.5.0, negotiator@0.3.0, multiparty@2.2.0)

2.  Debido a que este ejemplo era originalmente parte del repositorio de Socket.IO GitHub y
    hacía referencia directa a la biblioteca de Socket.IO mediante la ruta relativa,
    no se hacía referencia al Socket.IO en el archivo package.json, por lo que
    debemos instalarlo ejecutando el siguiente comando:

        npm install socket.io@1.0.6 -save

    > [WACOM.NOTE] Aunque versiones más recientes de Socket.IO pueden funcionar con los pasos incluidos en este artículo, realmente se comprobaron con la versión 1.0.6.

## <span id="Publish"></span></a>Creación de un sitio web de Azure

Siga estos pasos para crear un sitio web de Azure, habilite la publicación con Git y, a continuación, habilite la compatibilidad de WebSocket para el sitio web.

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][].

1.  Desde la línea de comandos, cambie los directorios al directorio **\\node\\chat** y use el siguiente comando para crear un nuevo sitio web de Azure y habilitar un repositorio de Git para el sitio web y el directorio local. De esta manera se creará también un Git remoto llamado "azure".

        azure site create mysitename --git

    Debe reemplazar 'mysitename' por un nombre único para su sitio web.

2.  Confirme los archivos existentes en el repositorio local usando los siguientes comandos:

        git add .
        git commit -m "Initial commit"

3.  Inserte los archivos en el repositorio del sitio web de Azure con el siguiente comando:

        git push azure master

    Recibirá mensajes de estado a medida que los módulos se importen en el servidor. Después de que se haya completado este proceso, la aplicación se hospedará en su sitio web de Azure.

    > [WACOM.NOTE] Durante la instalación de módulos, es posible que aparezcan errores del tipo "The imported project ... was not found". Se pueden ignorar con seguridad.

4.  Socket.IO usa WebSockets, que no están habilitados de manera predeterminada en Azure. Para habilitar los sockets web, use el siguiente comando:

        azure site set -w

    Si se le solicita, escriba el nombre del sitio web.

    > [WACOM.NOTE]
    > El comando 'azure site set -w' solo funcionará con la versión 0.7.4 o posterior de la Interfaz de línea de comandos entre plataformas de Azure. También puede habilitar el soporte para WebSocket usando el Portal de administración de Azure.
    >
    > Para habilitar WebSockets con el [Portal de administración de Azure][], seleccione la página Configure de su sitio web, seleccione ON para la entrada de Web Sockets y, a continuación, haga clic en Save.
    >
    > ![websockets][]

5.  Para ver el sitio web en Azure, use el siguiente comando para iniciar su explorador web y dirigirse hasta el sitio web hospedado:

        azure site browse

Su aplicación se está ejecutando ahora en Azure y puede retransmitir los mensajes
de chat entre los diferentes clientes que usan Socket.IO.

> [WACOM.NOTE] Para fines de simplicidad, este ejemplo se limita a un chat entre los usuarios que están conectados en la misma instancia. Esto significa que si el servicio en la nube crea dos instancias de rol de trabajo, los usuarios solo podrán conversar con otros que estén conectados en la misma instancia de rol de trabajo. Para escalar la aplicación a fin de que funcione con varias instancias de rol, puede usar una tecnología como el Bus de servicio para compartir el estado de almacén de Socket.IO en todas las instancias. Para ver ejemplos, consulte los ejemplos de uso de Colas y Temas del Bus de servicio en el [repositorio de GitHub de SDK de Azure para Node.js][] (en inglés).

## Escalado horizontal

Las aplicaciones Socket.IO se pueden escalar horizontalmente con un **adaptador** para distribuir mensajes y eventos entre varias instancias de aplicaciones. Aunque hay varios adaptadores disponibles, el adaptador [socket.io-redis][] se puede utilizar fácilmente con la característica de caché de Redis de Azure.

> [WACOM.NOTE] Un requisito adicional para el escalado horizontal de una solución Socket.IO es la compatibilidad con sesiones persistentes. Las sesiones persistentes se habilitan de manera predeterminada en los sitios web Azure con el enrutamiento de solicitudes de Azure. Para obtener más información, consulte [Instance Affinity in Azure Web Sites][]

### Crear una caché de Redis

Realice los pases de [Create a cache in Azure Redis Cache][] para crear una caché nueva.

> [WACOM.NOTE] Guarde el **Nombre de host** y la **Clave principal** de la caché, ya que los necesitará en los siguientes pasos.

### Agregue los módulos redis y socket.io-redis

1.  Desde una línea de comandos, cambie al directorio **\\node\\chat** y use el siguiente comando.

        npm install socket.io-redis@0.1.3 redis@0.11.0 --save

    > [WACOM.NOTE] Las versiones indicadas en este comando son la sutilizadas al hacer las pruebas de este artículo.

2.  Modifique el archivo **app.js** para agregar la siguientes líneas inmediatamente después de `var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Sustituya **redishostname** y **rediskey** por el nombre de host y la clave de su caché de Redis.

    Esto creará un cliente de publicación y suscripción a la caché de Redis creada anteriormente. Entonces los clientes se utilizarán con el adaptador para configurar Socket.IO y utilizar la caché de Redis para pasar mensajes y eventos entre instancias de la aplicación.

    > [WACOM.NOTE] Aunque el adaptador **socket.io-redis** se puede comunicar directamente con Redis, la versión actual (a partir del 14/07/2014) no es compatible con la autenticación que requiere la caché de Redis de Azure. Por tanto la conexión inicial se crea con el módulo **redis**, a continuación el cliente se pasa al adaptador **socket.io-redis**.
    >
    > Aunque la caché de Redis de Azure es compatible con las conexiones seguras que utilizan el puerto 6380, los módulos usados en este ejemplo no son compatibles con las conexiones seguras a partir del 14/07/2014. el código anterior utiliza el puerto predeterminado no seguro 6380.

3.  Guarde el **app.js** modificado

### Confirmar cambios y volver a implementar

Use el siguiente comando de la línea de comandos **\\node\\chat** para confirmar los cambios y volver a implementar la aplicación.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Cuando los cambios se hayan enviado al servidor, puede escalar su sitio por varias instancias con el siguiente comando.

    azure site scale instances --instances #

Donde **\#** es el número de instancias que se van a crear.

Puede conectar a su sitio web desde varios exploradores o equipos para verificar que los mensajes se envían correctamente a todos los clientes.

## <span id="tshooting"></span></a> Solución de problemas

### Límites de conexión

Sitios web de Azure está disponible en varios SKU, lo que determina los recursos disponibles en su sitio. Esto incluye el número permitido de conexiones WebSocket. Para obtener más información, consulte [Web Sites Pricing page][].

### No se están enviando los mensajes con WebSockets

Si los exploradores de los clientes siguen recurriendo al sondeo largo en lugar de utilizar WebSockets, tal vez se deba a uno de estos motivos.

-   **Intente limitar el transporte a solo WebSockets**

    Para que Socket.IO utilice WebSockets como transporte de mensajes, tanto el servidor como el cliente deben ser compatibles con WebSockets. Si uno o el otro no lo son, Socket.IO negociará otro transporte, como el sondeo largo. La lista predeterminada de transportes utilizados por Socket.IO es `websocket, htmlfile, xhr-polling, jsonp-polling`. Puede forzarlo a que solo utilice WebSockets si añade el siguiente código al archivo **app.js**, después de la línea que contiene `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [WACOM.NOTE] Tenga en cuenta que los exploradores más viejos que no son compatibles con WebSockets no podrán conectar al sitio, aunque el código anterior esté activo, ya que restringe la comunicación solo a WebSockets.

-   **Usar SSL**

    WebSockets depende de algunos encabezados HTTP menos utilizados, como el encabezado **Upgrade**. Algunos dispositivos de red intermedios, como los proxy web, pueden quitar estos encabezados. Para evitar este problema, puede establecer la conexión WebSocket por SSL.

    Una manera sencilla de conseguir esto es configurar Socket.IO en `match origin protocol`. Esto envía instrucciones a Socket.IO para asegurar la comunicación de WebSockets al igual que la solicitud HTTP/HTTPS original para la página web. Si un explorador utiliza una URL HTTPS para visitar su sitio web, las comunicaciones consiguientes de WebSocket a través de Socket.IO se asegurarán con SSL.

    Para modificar este ejemplo y habilitar esta configuración, añada el siguiente código al archivo **app.js**, después de la línea que contiene `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

-   **Verifique la configuración de web.config**

    Sitios web Azure que alojan aplicaciones Node.js utilizan el archivo**web.config** para enrutar las solicitudes entrantes a la aplicación Node.js. Para que WebSockets funcione correctamente con aplicaciones Node.js, **web.config** debe contener la siguiente entrada.

        <webSocket enabled="false"/>

    Esto deshabilita el módulo WebSockets de IIS, que incluye su propia implementación de WebSockets y está en conflicto con los módulos WebSocket específicos de Node.js, como por ejemplo Socket.IO. Si esta línea no está presente, o si está ajustada en `true`, esto puede ser el motivo por el que el transporte WebSocket no funciona para su aplicación.

    Normalmente, las aplicaciones Node.js no incluyen un archivo **web.config**, por lo que los Sitios web Azure automáticamente generará uno para las aplicaciones Node.js cuando se implementan. Como este archivo se genera automáticamente en el servidor, debe utilizar la URL FTP o FTPS en su sitio web para ver este archivo. Puede encontrar URL FTP y FTPS para su sitio en el Portal de administración de Azure si selecciona el sitio web y a continuación el vínculo **Dashboard**. Las URL se muestran en la sección **quick glance**.

    > [WACOM.NOTE] Sitios web Azure solo genera el archivo **web.config** si la aplicación no proporciona uno. Sitios web Azure utilizará el archivo **web.config** en la raíz de su proyecto de aplicación si proporciona uno.

    Si la entrada no está presente, o si se ha ajustado en el valor `true`, entonces deberá crear un **web.config** en la raíz de su aplicación Node.js y especificar un valor de `false`. Como referencia a continuación mostramos el **web.config** predeterminado para una aplicación que utiliza **app.js** como punto de entrada.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js site to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js site entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    > [WACOM.NOTE] Si la aplicación utiliza un punto de entrada distinto de **app.js**, deberá sustituir todas las ocurrencias de **app.js** por el punto de entrada correcto. Por ejemplo, sustituyendo **app.js** por **server.js**.

## Pasos siguientes

En este tutorial aprendió a crear una aplicación de chat hospedada en un sitio web de Azure. También puede hospedar esta aplicación como un servicio en la nube de Azure. Para conocer los pasos y así llevarlo a cabo, consulte [Creación de una aplicación de chat Node.js con Socket.IO en un servicio en la nube de Azure][1].

  []: http://socket.io/
  [Creación de una aplicación de chat Node.js con Socket.IO en un servicio en la nube de Azure]: http://www.windowsazure.com/es-es/develop/nodejs/tutorials/app-using-socketio/
  [repositorio de Socket.IO GitHub]: https://github.com/Automattic/socket.io
  [versión archivada ZIP o GZ]: https://github.com/Automattic/socket.io/releases
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A7171371E
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [websockets]: ./media/web-sites-nodejs-chat-app-socketio/websockets.png
  [repositorio de GitHub de SDK de Azure para Node.js]: https://github.com/WindowsAzure/azure-sdk-for-node
  [socket.io-redis]: https://github.com/automattic/socket.io-redis
  [Instance Affinity in Azure Web Sites]: http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
  [Create a cache in Azure Redis Cache]: http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409
  [Web Sites Pricing page]: /es-es/pricing/details/web-sites/
  [1]: /es-es/develop/nodejs/tutorials/app-using-socketio/
