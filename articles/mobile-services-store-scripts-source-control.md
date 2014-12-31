<properties urlDisplayName="Store JavaScript project code in source control" pageTitle="Almacenamiento del código del proyecto en el control del código fuente: Servicios móviles de Azure" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Store project code in source control" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

<div class="dev-center-tutorial-subselector">
	<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/" title=".NET backend">Back-end de .NET</a> | <a href="/en-us/documentation/articles/mobile-services-store-scripts-source-control/"  title="JavaScript backend" class="current">Back-end de JavaScript</a>
</div>

# Almacenamiento del código del proyecto en el control del código fuente

Este tema le muestra cómo usar el control del código fuente proporcionado por Servicios móviles de Azure para almacenar los scripts del servidor. Los scripts y otros archivos de código de back-end de JavaScript pueden promoverse desde el repositorio local al servicio móvil de producción. También se muestra cómo definir código compartido que pueden requerir varios scripts y cómo usar el archivo package.json para agregar módulos Node.js al servicio móvil. 

El tutorial le guiará a través de los siguientes pasos:

1. [Habilitación del control de código fuente en el servicio móvil].
2. [Instalación de Git y creación del repositorio local].
3. [Implementación de archivos de script actualizados en el servicio móvil].
4. [Aprovechamiento del código compartido y de módulos Node.js en los scripts del servidor].

Para completar este tutorial, ya debe haber creado un servicio móvil tras completar el tutorial [Introducción a los Servicios móviles] o [Incorporación de Servicios móviles a una aplicación existente].

##<a name="enable-source-control"></a>Habilitación del control de código fuente en el servicio móvil

[WACOM.INCLUDE [mobile-services-enable-source-control](../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Instalación de Git y creación del repositorio local

1. Instale Git en su equipo local. 

	Los pasos requeridos para instalar Git varían según los sistemas operativos. Consulte [Instalación de Git] para obtener una guía sobre la instalación y las distribuciones específicas del sistema operativo.

	> [WACOM.NOTE]
	Algunos sistemas operativos disponen de versiones de Git en línea de comandos y de GUI. Las instrucciones proporcionadas en este artículo utilizan la versión en línea de comandos.

2. Abra una línea de comandos, como **GitBash** (Windows) o **Bash** (shell de Unix). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación **Terminal**.

3. Desde la línea de comandos, cambie al directorio donde almacenará los scripts. Por ejemplo, `cd SourceControl`.

4. Use el comando siguiente para crear una copia local del nuevo repositorio Git. Deberá reemplazar `<your_git_URL>` por la dirección URL del repositorio Git del servicio móvil:

		git clone <your_git_URL>

5. Cuando se le solicite, escriba el nombre de usuario y la contraseña que estableció al habilitar el control de código fuente en el servicio móvil. Después de realizar la autenticación correctamente, aparecerá una serie de respuestas, como la siguiente:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Desplácese al directorio desde el que ejecutó el comando `git clone` y observe la estructura correspondiente a continuación:

	![4][4]

	En este caso, se crea un nuevo directorio con el nombre del servicio móvil, que es el repositorio local para el servicio de datos. 

7. Abra la subcarpeta .\service\table y observe que contiene un archivo TodoItem.json, que es una representación JSON de los permisos de funcionamiento de la tabla TodoItem. 

	Cuando se hayan definido scripts de servidor en esta tabla, también aparecerán uno o varios archivos denominados <code>TodoItem._&lt;operation&gt;_.js</code> que contendrán los scripts para la operación de tabla determinada. Los scripts del programador y de la API personalizada se mantienen en carpetas independientes con esos nombres respectivos. Para obtener más información, consulte [Control de código fuente].

Ahora que ha creado su repositorio local, puede realizar cambios en los scripts del servidor e insertarlos en el servicio móvil.

##<a name="deploy-scripts"></a>Implementación de archivos de script actualizados en el servicio móvil

1. Desplácese a la subcarpeta .\service\table y, si aún no existe un archivo todoitem.insert.js, créelo en este momento.

2. Abra el nuevo archivo todoitem.insert.js en un editor de texto, pegue en él el código siguiente y guarde los cambios:

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}
	
	Este código se limita a escribir el elemento insertado en el registro. Si el archivo ya contiene código, solo tiene que agregarle código válido de JavaScript, como una llamada a `console.log()` y, a continuación, guardar los cambios. 

3. En el símbolo del sistema de Git, escriba el comando siguiente para empezar a realizar un seguimiento del nuevo archivo de scripts:

		$ git add .
	

4. Escriba el comando siguiente para confirmar los cambios:

		$ git commit -m "updated the insert script"

5. Escriba el comando siguiente para cargar los cambios en el repositorio remoto:

		$ git push origin master
	
	Aparecerá una serie de comandos que indica que la confirmación se ha implementado en el servicio móvil.

6. Nuevamente en el Portal de administración, haga clic en la pestaña **Datos** y, a continuación, en la tabla **TodoItem**.

	![][5]

3. Haga clic en **Script** y, a continuación, seleccione la operación **Insert**.

	![][6]

	Observe que el script de operación de inserción que se muestra es el mismo que el del código de JavaScript que acaba de cargar en el repositorio.

##<a name="use-npm"></a>Aprovechamiento del código compartido y de módulos Node.js en los scripts del servidor

Servicios móviles proporciona acceso a todo el conjunto de módulos Node.js básicos, que puede usar en su código mediante la función **require**. Asimismo, el servicio móvil puede usar módulos Node.js que no formen parte del paquete Node.js básico, y el usuario puede incluso definir su propio código compartido como módulos Node.js. Para obtener más información acerca de la creación de módulos, consulte la sección [Módulos][Node.js API Documentation: Modules] en la documentación de referencia de API Node.js.

La manera recomendada de agregar módulos Node.js al servicio móvil es agregando referencias al archivo package.json del servicio. A continuación, agregará el módulo Node.js [node-uuid] al servicio móvil a través de la actualización del archivo package.json. Cuando la actualización se inserte en Azure, el servicio móvil se reiniciará y se instalará el módulo. Este módulo se usará después para generar un nuevo valor de GUID para la propiedad **uuid** en los elementos insertados. 

2. Desplácese a la carpeta `.\service` del repositorio Git local y abra el archivo package.json en un editor de texto.

3. Ubique  

		npm install node-uuid

	NPM crea el directorio `node_modules` en la ubicación actual e instala el módulo [node-uuid] en el subdirectorio `\node-uuid`. 

	<div class="dev-callout">
	<strong>Nota:</strong>
	<p>Si <code>node_modules</code> ya existe en la jerarquía de directorios, NPM creará el subdirectorio <code>\node-uuid</code> ahí en lugar de crear un nuevo <code>node_modules</code> en el repositorio. En este caso, elimine el directorio <code>node_modules</code> existente.</p>
	</div>

4. Ahora, desplácese a la subcarpeta .\service\table, abra el archivo todoitem.insert.js y modifíquelo de la siguiente forma:

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}

	Este código agrega una columna uuid a la tabla y la rellena con identificadores GUID únicos.

5. Al igual que en la sección anterior, escriba el siguiente comando en el símbolo del sistema de Git: 

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master
		
	Al hacerlo, se agrega el nuevo archivo, se confirman los cambios y se insertan el nuevo módulo node-uuid y los cambios del script todoitem.insert.js en el servicio móvil.

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha completado este tutorial, ya sabe cómo almacenar sus scripts en el control de código fuente. Considere la posibilidad de obtener más información sobre cómo trabajar con scripts de servidor y con las API personalizadas: 

+ [Uso de scripts del servidor en Servicios móviles]
	<br/>Muestra cómo trabajar con scripts del servidor, el programador de trabajos y las API personalizadas.

+ [Llamada a una API personalizada desde el cliente] 
	<br/> Muestra cómo crear API personalizadas que se pueden llamar desde el cliente.

<!-- Anchors. -->
[Habilitación del control de código fuente en el servicio móvil]: #enable-source-control
[Instalación de Git y creación del repositorio local]: #clone-repo
[Implementación de archivos de script actualizados en el servicio móvil]: #deploy-scripts
[Aprovechamiento del código compartido y de módulos Node.js en los scripts del servidor]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Sitio web de Git]: http://git-scm.com
[Control de código fuente]: http://msdn.microsoft.com/en-us/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Instalación de Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Introducción a los Servicios móviles]: /en-us/documentation/articles/mobile-services-ios-get-started/
[Incorporación de Servicios móviles a una aplicación existente]: /en-us/documentation/articles/mobile-services-ios-get-started-data/
[Uso de scripts del servidor en Servicios móviles]: /en-us/documentation/articles/mobile-services-how-to-use-server-scripts/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Llamada a una API personalizada desde el cliente]: /en-us/documentation/articles/mobile-services-ios-call-custom-api/
[Documentación sobre la API de Node.js: Módulos]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid

<!--HONumber=35_1-->
