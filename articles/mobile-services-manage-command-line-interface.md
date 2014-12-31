<properties urlDisplayName="Command Line Administration" pageTitle="Administración de un Servicio móvil en la línea de comandos: tutorial de Azure" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Automatización de servicios móviles con herramientas de línea de comandos 

En este tema se muestra cómo usar las herramientas de línea de comandos de Azure para automatizar la creación y administración de servicios móviles de Azure. Este tema le muestra cómo instalar y comenzar a usar herramientas de línea de comandos y utilizarlas para realizar las siguientes tareas de servicios móviles:

-	[Creación de un servicio móvil] 
-	[Creación de una tabla]
-   [Registro de un script en una operación de tabla][Register a new table script]
-   [Enumeración de tablas]
- 	[Eliminación de una tabla existente]
-	[Enumeración de servicios móviles]
-   [Eliminación de un servicio móvil existente]
 
Cuando se combinan en un archivo por lotes o un único script, estos comandos individuales automatizan los procesos de creación, comprobación y eliminación de un servicio móvil. 

Para usar las herramientas de línea de comandos de Azure para administrar servicios móviles, necesita una cuenta de Azure que disponga de la característica Servicios móviles de Azure habilitada.

+ En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/en-us/pricing/free-trial/" target="_blank">Evaluación gratuita de Azure</a>.

+ Si tiene una cuenta existente pero necesita habilitar la vista previa de Servicios móviles de Azure, consulte <a href="http://azure.microsoft.com/en-us/documentation/articles/php-create-account/#enable" target="_blank">Habilitación de las características de vista previa de Azure</a>.

Este tema incluye una selección de tareas de administración comunes compatibles con las herramientas de línea de comandos de Azure. Para obtener más información, consulte [Documentación de herramientas de línea de comandos de Azure][reference-docs].

<!--+  You must download and install the Azure command-line tools to your local machine. To do this, follow the instructions in the first section of this topic. 

+ (Optional) To be able to execute HTTP requests directly from the command-line, you must use cURL or an equivalent tool. cURL runs on a variety of platforms. Locate and install cURL for your specific platform from the <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">cURL download  page</a>.-->

<h2><a name="install"></a>Instalación de las herramientas de línea de comandos de Azure</h2>

La siguiente lista contiene información para instalar las herramientas de línea de comandos, según su sistema operativo:

* **Windows**: descargue el [instalador de las herramientas de línea de comandos de Azure][windows-installer]. Abra el archivo .msi descargado y complete los pasos de instalación a medida que se le solicita.

* **Mac**: descargue el [instalador de SDK de Azure][mac-installer]. Abra el archivo .pkg descargado y complete los pasos de instalación a medida que se le solicita.

* **Linux**: instale la última versión de [Node.js][nodejs-org] (consulte [Instalación de Node.js a través del Administrador de paquetes][install-node-linux]) y, a continuación, ejecute el siguiente comando:

		npm install azure-cli -g

Para probar la instalación, escriba "azure" en el símbolo del sistema. Si la instalación es correcta, verá una lista de los comandos "azure" disponibles.
<h2><a name="import-account"></a>Descarga e importación de la configuración de publicación</h2>

Para comenzar, primero necesita descargar e importar su configuración de publicación. A continuación, puede usar las herramientas para crear y administrar servicios de Azure. Para descargar su configuración de publicación, use el comando "account download":

		azure account download

Esto abrirá su explorador predeterminado y le solicitará iniciar sesión en el Portal de administración. Después de iniciar sesión, se descargará su archivo ".publishsettings". Anote la ubicación del archivo guardado.

A continuación, importe el archivo ".publishsettings" ejecutando el siguiente comando y reemplace <path to .publishsettings file> por la ruta al archivo ".publishsettings":

		azure account import <path-to-settings-file>

Puede quitar toda la información almacenada por el comando <code>Importación</code> utilizando el comando <code>account clear</code> :

		azure account clear

Para ver una lista de opciones para los comandos "account", use la opción "-help":

		azure account -help

Después de importar su configuración de publicación, debe eliminar el archivo ".publishsettings" por motivos de seguridad. Para obtener más información, consulte [Instalación de las herramientas de línea de comandos de Azure para Mac y Linux]. Ahora está preparado para comenzar a crear y administrar servicios móviles de Azure desde la línea de comandos o en los archivos por lotes.  

<h2><a name="create-service"></a>Creación de un servicio móvil</h2>

Puede usar las herramientas de línea de comandos para crear una nueva instancia de servicio móvil. Cuando se crea un servicio móvil, también se crea una instancia de Base de datos SQL en un nuevo servidor. 

El siguiente comando crea una nueva instancia de servicios móviles en la suscripción, donde `<service-name>` es el nombre del nuevo servicio móvil, `<server-admin>` es el nombre de inicio de sesión del nuevo servidor y `<server-password>` es la contraseña para el nuevo inicio de sesión:

		azure mobile create <service-name> <server-admin> <server-password>

Se produce un error en el comando `mobile create` cuando existe el servicio móvil especificado. En los scripts de automatización, debe intentar eliminar un servicio móvil antes de intentar volver a crearlo.

<h2><a name="list-services"></a>Enumeración de servicios móviles existentes en una suscripción</h2>

El siguiente comando devuelve una lista de todos los servicios móviles en una suscripción a Azure:

		azure mobile list

Este comando también muestra el estado actual y la dirección URL de cada servicio móvil.

<h2><a name="delete-service"></a>Eliminación de un servicio móvil existente</h2>

Puede usar las herramientas de línea de comandos para eliminar un servicio móvil existente, junto con el servidor y la base de datos SQL relacionados. El siguiente comando elimina el servicio móvil, donde `<service-name>` es el nombre del servicio móvil para eliminar:

		azure mobile delete <service-name> -a -q

Si incluye los parámetros `-a` y `-q` , este comando también elimina el servidor y la base de datos SQL usados por el servicio móvil sin mostrar una solicitud.

<div class="dev-callout"><strong>Nota:</strong> 
   <p>si no especifica el parámetro <code>-q</code> junto con <code>-a</code> o <code>-d</code>, la ejecución se pausa y se le solicita que seleccione las opciones de eliminación para su Base de datos SQL. Use solo el parámetro <code>-a</code> cuando ningún otro servicio usa la base de datos o el servidor; de lo contrario, use el parámetro <code>-d</code> para eliminar solamente los datos que pertenezcan al servicio móvil que se vaya a eliminar.</p>
</div>

<h2><a name="create-table"></a>Creación de una tabla en el servicio móvil</h2>

El siguiente comando elimina el servicio móvil, donde `<service-name>` es el nombre del servicio móvil para eliminar y `<table-name>` es el nombre de la tabla que se va a crear:

		azure mobile table create <service-name> <table-name>

De esta forma, se crea una nueva tabla con permisos predeterminados, `application`, para las operaciones de tabla: `insert`, `read`, `update` y `delete`. 

El siguiente comando crea una nueva tabla con un permiso "read" público pero con el permiso "delete" concedido solo a administradores:

		azure mobile table create <service-name> <table-name> -p read=public,delete=admin

La siguiente tabla muestra el valor de permiso de script en comparación con el valor de permiso en el [Portal de administración de Azure].

<table border="1" width="100%"><tr><th>Valor de script</th><th>Valor del Portal de administración</th></tr>
<tr><td><code>public</code></td><td>Todo el mundo</td></tr>
<tr><td><code>aplicación</code> (predeterminado)</td><td>Cualquier persona con la clave de aplicación</td></tr>
<tr><td><code>user</code></td><td>Solo usuarios autenticados</td></tr>
<tr><td><code>admin	</code></td><td>Solo scripts y administradores</td></tr></table>

Se produce un error en el comando "mobile table create" cuando existe ya la tabla especificada. En los scripts de automatización, debe intentar eliminar una tabla antes de intentar volver a crearlo.

<h2><a name="list-tables"></a>Enumeración de tablas existentes en un servicio móvil</h2>

El siguiente comando devuelve una lista de todas las tablas de un servicio móvil, donde `<service-name>` es el nombre del servicio móvil:

		azure mobile table list <service-name>

Este comando también muestra el número de índices en cada tabla y el número de filas de datos actualmente en la tabla.

<h2><a name="delete-table"></a>Eliminación de una tabla existente del servicio móvil</h2>

El siguiente comando elimina el servicio móvil, donde `<service-name>` es el nombre del servicio móvil para eliminar y `<table-name>` es el nombre de la tabla que se va a crear:

		azure mobile table delete <service-name> <table-name> -q

En los scripts de automatización, use el parámetro `-q` para eliminar la tabla sin mostrar un mensaje de confirmación que bloquee la ejecución.

<h2><a name="register-script"></a>Registro de un script en una operación de tabla</h2>

El siguiente comando elimina el servicio móvil, donde `<service-name>` es el nombre del servicio móvil, `<table-name>` es el nombre de la tabla y `<operation>` es la operación de tabla, que puede ser `read`, `insert`, `update` o `delete`:

		azure mobile script upload <service-name> table/<table-name>.<operation>.js

Tenga en cuenta que esta operación carga un archivo JavaScript (.js) desde el equipo local. El nombre del archivo debe estar compuesto por los nombres de operación y de tabla, y debe encontrarse en la subcarpeta `table` relativa a la ubicación en la que se ejecuta el comando. Por ejemplo, la siguiente operación carga o registra un nuevo script `insert` que pertenece a la tabla `TodoItems`:

		azure mobile script upload todolist table/todoitems.insert.js

La declaración de función en el archivo de script también debe coincidir con la operación de tabla registrada. Esto significa que para un script `insert`, el script cargado contiene una función con la siguiente firma:

		function insert(item, user, request) {
		    ...
		} 

Para obtener más información sobre el registro de scripts, consulte [Referencia del script del servidor de Servicios móviles].

<!--<h2><a name="test-service"></a>Test the new mobile service</h2>

When you are automating the creation of your mobile service, you can optionally use cURL or another command-line request generator to 

## <a name="nextsteps"> </a>Next Steps
Next steps here....
-->
<!-- Anchors. -->
[Descarga e instalación de las herramientas de la línea de comandos]: #install
[Descarga e importación de la configuración de publicación]: #import
[Creación de un servicio móvil]: #create-service
[Obtención de la clave maestra]: #get-master-key
[Creación de una tabla]: #create-table
[Registro de un script de tabla nuevo]: #register-script
[Eliminación de una tabla existente]: #delete-table
[Eliminación de un servicio móvil existente]: #delete-service
[Prueba del servicio móvil]: #test-service
[Enumeración de servicios móviles]: #list-services
[Enumeración de tablas]: #list-tables
[Pasos siguientes]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Portal de administración de Azure]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: /en-us/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
[Instalación de las herramientas de línea de comandos de Azure para Mac y Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795


<!--HONumber=35_1-->
