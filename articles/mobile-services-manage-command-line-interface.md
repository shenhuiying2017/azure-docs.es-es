<properties linkid="develop-mobile-tutorials-command-line-administration" urlDisplayName="Command Line Administration" pageTitle="Administering a Mobile Service at the command line - Azure tutorial" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="" editor="" />

Automatización de servicios móviles con herramientas de línea de comandos
=========================================================================

En este tema se muestra cómo usar las herramientas de línea de comandos de Azure para automatizar la creación y administración de servicios móviles de Azure. Este tema le muestra cómo instalar y comenzar a usar herramientas de línea de comandos y utilizarlas para realizar las siguientes tareas de servicios móviles:

-   [Creación de un servicio móvil](#create-service)
-   [Creación de una tabla nueva](#create-table)
-   [Registro de un script en una operación de tabla](#register-script)
-   [Enumeración de tablas](#list-tables)
-   [Eliminación de una tabla existente](#delete-table)
-   [Enumeración de servicios móviles](#list-services)
-   [Eliminación de un servicio móvil existente](#delete-service)

Cuando se combinan en un archivo por lotes o un único script, estos comandos individuales automatizan los procesos de creación, comprobación y eliminación de un servicio móvil.

Para usar las herramientas de línea de comandos de Azure para administrar servicios móviles, necesita una cuenta de Azure que disponga de la característica Servicios móviles de Azure habilitada.

-   En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/).

-   Si tiene una cuenta existente pero necesita habilitar la vista previa de Servicios móviles de Azure, consulte [Habilitación de las características de vista previa de Azure](../create-a-windows-azure-account/#enable).

Este tema incluye una selección de tareas de administración comunes compatibles con las herramientas de línea de comandos de Azure. Para obtener más información, consulte [Documentación de herramientas de línea de comandos de Azure](/es-es/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services).

Instalación de herramientasInstalación de las herramientas de línea de comandos de Azure
----------------------------------------------------------------------------------------

La siguiente lista contiene información para instalar las herramientas de línea de comandos, según su sistema operativo:

-   **Windows**: Descargue el [instalador de las herramientas de línea de comandos de Azure](http://go.microsoft.com/fwlink/p?LinkID=275464). Abra el archivo .msi descargado y complete los pasos de instalación a medida que se le solicita.

-   **Mac**: Descargue el [Instalador de SDK de Azure](http://go.microsoft.com/fwlink/p?LinkId=252249). Abra el archivo .pkg descargado y complete los pasos de instalación a medida que se le solicita.

-   **Linux**: Instale la última versión de [Node.js](http://nodejs.org/) (consulte [Install Node.js via Package Manager](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)) y, a continuación, ejecute el siguiente comando:

        npm install azure-cli -g

Para probar la instalación, escriba `azure` en el símbolo del sistema. Si la instalación es correcta, verá una lista de los comandos de `azure` disponibles.

Importación de configuraciónDescarga e importación de la configuración de publicación
-------------------------------------------------------------------------------------

Para comenzar, primero necesita descargar e importar su configuración de publicación. A continuación, puede usar las herramientas para crear y administrar servicios de Azure. Para descargar su configuración de publicación, use el comando `account download`:

     azure account download

Esto abrirá su explorador predeterminado y le solicitará iniciar sesión en el Portal de administración. Después de iniciar sesión, se descargará su archivo `.publishsettings`. Anote la ubicación del archivo guardado.

A continuación, importe el archivo `.publishsettings` mediante la ejecución del siguiente comando y reemplace `<path-to-settings-file>` por la ruta al archivo `.publishsettings`:

     azure account import <path-to-settings-file>

Puede eliminar toda la información almacenada con el comando `import` usando el comando `account clear`:

     azure account clear

Para ver una lista de opciones para los comandos `account`, use la opción `-help`:

    azure account -help

Después de importar su configuración de publicación, debe eliminar el archivo `.publishsettings` por motivos de seguridad. Para obtener más información, consulte [Instalación de las herramientas de línea de comandos de Azure para Mac y Linux](http://go.microsoft.com/fwlink/p/?LinkId=275795). Ahora está preparado para comenzar a crear y administrar servicios móviles de Azure desde la línea de comandos o en los archivos por lotes.

Creación de un servicioCreación de un servicio móvil
----------------------------------------------------

Puede usar las herramientas de línea de comandos para crear una nueva instancia de servicio móvil. Cuando se crea un servicio móvil, también se crea una instancia de Base de datos SQL en un nuevo servidor.

El siguiente comando crea una nueva instancia de servicios móviles en la suscripción, donde `<service-name>` es el nombre del nuevo servicio móvil, `<server-admin>` es el nombre de inicio de sesión del nuevo servidor y `<server-password>` es la contraseña para el nuevo inicio de sesión:

     azure mobile create <service-name> <server-admin> <server-password>

Se produce un error en el comando `mobile create` cuando existe el servicio móvil especificado. En los scripts de automatización, debe intentar eliminar un servicio móvil antes de intentar volver a crearlo.

Enumeración de serviciosEnumeración de servicios móviles existentes en una suscripción
--------------------------------------------------------------------------------------

El siguiente comando devuelve una lista de todos los servicios móviles en una suscripción a Azure:

     azure mobile list

Este comando también muestra el estado actual y la dirección URL de cada servicio móvil.

Eliminación del servicioEliminación de un servicio móvil existente
------------------------------------------------------------------

Puede usar las herramientas de línea de comandos para eliminar un servicio móvil existente, junto con el servidor y la base de datos SQL relacionados. El siguiente comando elimina el servicio móvil, donde `<service-name>` es el nombre del servicio móvil para eliminar:

     azure mobile delete <service-name> -a -q

Si incluye los parámetros `-a` y `-q`, este comando también elimina el servidor y la base de datos SQL usados por el servicio móvil sin mostrar una solicitud.

**Nota:**

Si no especifica el parámetro `-q` junto con `-a` o `-d`, la ejecución se pausa y se le solicita que seleccione las opciones de eliminación para su base de datos SQL. Utilice únicamente el parámetro `-a` cuando ningún otro servicio use la base de datos o el servidor; de lo contrario, use el parámetro `-d` para eliminar solamente los datos que pertenezcan al servicio móvil que se vaya a eliminar.

Creación de una tablaCreación de una tabla en el servicio móvil
---------------------------------------------------------------

El siguiente comando crea una tabla en el servicio móvil especificado, donde `<service-name>` es el nombre del servicio móvil y `<table-name>` el nombre de la tabla que desea crear:

     azure mobile table create <service-name> <table-name>

De esta forma, se crea una nueva tabla con permisos predeterminados, `application`, para las operaciones de tabla: `insert`, `read`, `update` y `delete`.

El siguiente comando crea una nueva tabla con un permiso `read` público pero con el permiso `delete` concedido solo a administradores:

     azure mobile table create <service-name> <table-name> -p read=public,delete=admin

La siguiente tabla muestra el valor de permiso de script en comparación con el valor de permiso en el [Portal de administración de Azure](https://manage.windowsazure.com/).

<table border="1" width="100%"><tr><th>Valor de script</th><th>Valor del Portal de administración</th></tr>
<tr><td><code>public</code></td><td>Todo el mundo</td></tr>
<tr><td><code>application (predeterminado)</code> (default)</td><td>Cualquier persona con la clave de aplicación</td></tr>
<tr><td><code>user</code></td><td>Solo usuarios autenticados</td></tr>
<tr><td><code>admin	</code></td><td>Solo scripts y administradores</td></tr></table>

Se produce un error en el comando `mobile table create` cuando existe ya la tabla especificada. En los scripts de automatización, debe intentar eliminar una tabla antes de intentar volver a crearlo.

Enumeración de tablasEnumeración de tablas existentes en un servicio móvil
--------------------------------------------------------------------------

El siguiente comando devuelve una lista de todas las tablas de un servicio móvil, donde `<service-name>` es el nombre del servicio móvil:

     azure mobile table list <service-name>

Este comando también muestra el número de índices en cada tabla y el número de filas de datos actualmente en la tabla.

Eliminación de la tablaEliminación de una tabla existente del servicio móvil
----------------------------------------------------------------------------

El siguiente comando elimina una tabla del servicio móvil, donde `<service-name>` es el nombre del servicio móvil y `<table-name>` es el nombre de la tabla para eliminar:

     azure mobile table delete <service-name> <table-name> -q

En los scripts de automatización, use el parámetro `-q` para eliminar la tabla sin mostrar un mensaje de confirmación que bloquee la ejecución.

Registro de un scriptRegistro de un script en una operación de tabla
--------------------------------------------------------------------

El siguiente comando carga y registra una función en una operación en una tabla, donde `<service-name>` es el nombre del servicio móvil, `<table-name>` es el nombre de la tabla y `<operation>` es la operación de tabla, que puede ser `read`, `insert`, `update` o `delete`:

     azure mobile script upload <service-name> table/<table-name>.<operation>.js

Tenga en cuenta que esta operación carga un archivo JavaScript (.js) desde el equipo local. El nombre del archivo debe estar compuesto por los nombres de operación y de tabla, y debe encontrarse en la subcarpeta `table` relativa a la ubicación en la que se ejecuta el comando. Por ejemplo, la siguiente operación carga o registra un nuevo script `insert` que pertenece a la tabla `TodoItems`:

     azure mobile script upload todolist table/todoitems.insert.js

La declaración de función en el archivo de script también debe coincidir con la operación de tabla registrada. Esto significa que para un script `insert`, el script cargado contiene una función con la siguiente firma:

     function insert(item, user, request) {
            ...
        } 

Para obtener más información sobre el registro de scripts, consulte [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/p?LinkId=262293).

