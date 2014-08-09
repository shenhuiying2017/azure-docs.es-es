<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build a Node.js Chat Application with Socket.IO on an Azure Web Site" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

Creación de una aplicación de chat Node.js con Socket.IO en un sitio web de Azure
=================================================================================

Socket.IO proporciona comunicación en tiempo real entre su servidor node.js y los clientes. Este tutorial le llevara por el hospedaje de una aplicación de chat basada en Socket.IO como un sitio web de Azure. Para obtener más información sobre Socket.IO, consulte <http://socket.io/>.

**Nota:**

Los procedimientos de esta tarea se aplican a Sitios web Azure; para Servicios en la nube, consulte [Creación de una aplicación de chat Node.js con Socket.IO en un servicio en la nube de Azure](http://www.windowsazure.com/es-es/develop/nodejs/tutorials/app-using-socketio/).

A continuación se muestra una captura de pantalla de la aplicación completada:

![Explorador que muestra la aplicación de chat](./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png)

Descarga del ejemplo de chat
----------------------------

Para este proyecto, usaremos el ejemplo de chat del [repositorio de Socket.IO GitHub](https://github.com/LearnBoost/socket.io/tree/0.9.14) (en inglés). Realice los siguientes pasos para descargar el ejemplo y agréguelo al proyecto que creó anteriormente.

1.  Cree una copia local del repositorio con el botón **Clonar**. Puede también usar el botón **ZIP** para descargar el proyecto.

    ![Ventana de explorador con https://github.com/LearnBoost/socket.io/tree/master/examples/chat, y el icono de descarga ZIP resaltado](./media/web-sites-nodejs-chat-app-socketio/socketio-2.png)

2.  Diríjase a la estructura de directorios del repositorio local hasta que llegue al directorio **examples\\chat**. Copie el contenido de este directorio en un directorio aparte, como por ejemplo **\\node\\chat**.

Modificación de App.js e instalación de módulos
-----------------------------------------------

Antes de implementar la aplicación en Azure, debemos realizar algunas modificaciones menores. Realice los siguientes pasos en el archivo app.js:

1.  Abra el archivo app.js en el Bloc de notas u otro editor de texto.

2.  Busque la sección **Module dependencies** al comienzo del archivo app.js y cambie la línea que contiene **sio = require('..//..//lib//socket.io')** por **sio = require('socket.io')** como se muestra a continuación:

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Para estar seguro de que la aplicación escucha el puerto correcto, abra el archivo app.js en el Bloc de notas o su editor favorito y luego cambie la siguiente línea reemplazando **3000** por **process.env.PORT**, como se muestra a continuación:

        //app.listen(3000, function () {            //Original
		app.listen(process.env.PORT, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

Después de guardar los cambios en app.js, use los siguientes pasos para instalar los módulos necesarios:

1.  Desde la línea de comandos, cambie los directorios al directorio **\\node\\chat** y use el siguiente comando para instalar los módulos requeridos por esta aplicación:

        npm install

    Esta acción instalará los módulos que se mencionan en el archivo package.json. Después de que el comando se completa, debería ver un resultado similar al siguiente:

    ![Salida del comando npm install](./media/web-sites-nodejs-chat-app-socketio/socketio-7.png)

2.  Debido a que este ejemplo era originalmente parte del repositorio de Socket.IO GitHub y hacía referencia directa a la biblioteca de Socket.IO mediante la ruta relativa, no se hacía referencia al Socket.IO en el archivo package.json, por lo que debemos instalarlo ejecutando el siguiente comando:

        npm install socket.io -save

Creación de un sitio web de Azure y activación de la publicación Git
--------------------------------------------------------------------

Siga estos pasos para crear un sitio web de Azure y, a continuación, activar la publicación Git para el sitio web.

**Nota:**

Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A7171371E).

1.  Desde la línea de comandos, cambie los directorios al directorio **\\node\\chat** y use el siguiente comando para crear un nuevo sitio web de Azure y habilitar un repositorio de Git para el sitio web y el directorio local. De esta manera se creará también un Git remoto llamado "azure".

         azure site create mysitename --git

    Debe reemplazar 'mysitename' por un nombre único para su sitio web.

2.  Confirme los archivos existentes en el repositorio local usando los siguientes comandos:

         git add .
         git commit -m "Initial commit"

3.  Inserte los archivos en el repositorio del sitio web de Azure con el siguiente comando:

         git push azure master

    Recibirá mensajes de estado a medida que los módulos se importen en el servidor. Después de que se haya completado este proceso, la aplicación se hospedará en su sitio web de Azure.



    <b>Note</b>
    <p>Durante la instalación de módulos, es posible que aparezcan errores del tipo "The imported project ... was not found". Se pueden ignorar con seguridad.</p>
    </div>

1.  Socket.IO usa WebSockets, que no están habilitados de manera predeterminada en Azure. Para habilitar los sockets web, use el siguiente comando:

         azure site set -w

    Si se le solicita, escriba el nombre del sitio web.

    > [WACOM.NOTE] El comando "azure site set -w" solo funcionará con la versión 0.7.4 o posterior de la Interfaz de línea de comandos entre plataformas de Azure. También puede habilitar el soporte para WebSocket usando el Portal de administración de Azure.
    >
    > Para habilitar WebSockets con el [Portal de administración de Azure](https://manage.windowsazure.com), seleccione la página Configure de su sitio web, seleccione ON para la entrada de Web Sockets y, a continuación, haga clic en Save.
    >
    > ![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)

2.  Para ver el sitio web en Azure, use el siguiente comando para iniciar su explorador web y dirigirse hasta el sitio web hospedado:

         azure site browse

Su aplicación se está ejecutando ahora en Azure y puede retransmitir los mensajes de chat entre los diferentes clientes que usan Socket.IO.

**Nota:**

Para fines de simplicidad, este ejemplo se limita a un chat entre los usuarios que están conectados en la misma instancia. Esto significa que si el servicio en la nube crea dos instancias de rol de trabajo, los usuarios solo podrán conversar con otros que estén conectados en la misma instancia de rol de trabajo. Para escalar la aplicación a fin de que funcione con varias instancias de rol, puede usar una tecnología como el Bus de servicio para compartir el estado de almacén de Socket.IO en todas las instancias. Para ver ejemplos, consulte los ejemplos de uso de Colas y Temas del Bus de servicio en el [repositorio de GitHub de SDK de Azure para Node.js](https://github.com/WindowsAzure/azure-sdk-for-node) (en inglés).

Pasos siguientes
----------------

En este tutorial aprendió a crear una aplicación de chat hospedada en un sitio web de Azure. También puede hospedar esta aplicación como un servicio en la nube de Azure. Para conocer los pasos y así llevarlo a cabo, consulte [Creación de una aplicación de chat Node.js con Socket.IO en un servicio en la nube de Azure](/es-es/develop/nodejs/tutorials/app-using-socketio/).

