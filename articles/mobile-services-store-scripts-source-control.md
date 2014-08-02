<properties linkid="develop-mobile-tutorials-store-scripts-in-source-control" urlDisplayName="Store server scripts in source control" pageTitle="Store server scripts in source control - Azure Mobile Services" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="" documentationCenter="Mobile" title="Store server scripts in source control" authors="glenga" solutions="" manager="" editor="" />

Almacenamiento de scripts de servidor en control de código fuente
=================================================================

En este tema se muestra cómo configurar el control de código fuente por primera vez en Servicios móviles de Azure para almacenar los scripts de servidor en un repositorio Git. Los scripts y otros archivos de código de JavaScript pueden promoverse desde el repositorio local al servicio móvil de producción. También se muestra cómo definir código compartido que pueden requerir varios scripts y cómo cargar módulos Node.js.

El tutorial le guiará a través de los siguientes pasos:

1.  [Habilitación del control de código fuente en el servicio móvil](#enable-source-control).
2.  [Instalación de Git y creación del repositorio local](#clone-repo).
3.  [Implementación de archivos de script actualizados en el servicio móvil](#deploy-scripts).
4.  [Aprovechamiento del código compartido y de módulos Node.js en los scripts del servidor](#use-npm).

Para completar este tutorial, ya debe haber creado un servicio móvil tras completar el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started) o [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet).

Habilitación del control de código fuenteHabilitación del control de código fuente en el servicio móvil
-------------------------------------------------------------------------------------------------------

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

    ![](./media/mobile-services-store-scripts-source-control/mobile-services-selection.png)

2.  Haga clic en la pestaña **Panel** y, bajo **Quick glance**, haga clic en **Set up source control** y después en **Yes** para confirmar.

    ![](./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png)

    > [WACOM.NOTE] El control de código fuente es una característica de vista previa. Se recomienda hacer copias de seguridad de los archivos de script con regularidad, aunque estén almacenados en Servicios móviles.

3.  Proporcione un valor para **User name** y **New password**, confirme la contraseña y haga clic en el botón de comprobación.

    ![](./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png)

    Se crea el repositorio Git en el servicio móvil. Tome nota de las credenciales que acaba de proporcionar, ya que se usarán para obtener acceso a este repositorio.

4.  Haga clic en la pestaña Configure y observe los nuevos campos que aparecen bajo **Source control**.

    ![](./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png)

    Se muestra la dirección URL del repositorio Git, que se usará para clonar dicho repositorio en el equipo local.

Ahora que ha habilitado el control de código fuente en el servicio móvil, es el momento de usar Git para clonar el repositorio en el equipo local.

Clonación del repositorioInstalación de Git y creación del repositorio local
----------------------------------------------------------------------------

1.  Instale Git en su equipo local.

    Los pasos requeridos para instalar Git varían según los sistemas operativos. Consulte [Installing Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) para obtener una guía sobre la instalación y las distribuciones específicas del sistema operativo.

    > [WACOM.NOTE] Algunos sistemas operativos disponen de versiones de Git en línea de comandos y de GUI. Las instrucciones proporcionadas en este artículo utilizan la versión en línea de comandos.

2.  Abra una línea de comandos, como **GitBash** (Windows) o **Bash** (shell de Unix). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación **Terminal**.

3.  Desde la línea de comandos, cambie al directorio donde almacenará los scripts. Por ejemplo, `cd SourceControl`.

4.  Use el comando siguiente para crear una copia local del nuevo repositorio Git. Deberá reemplazar `<your_git_URL>` por la dirección URL del repositorio Git del servicio móvil:

         git clone <your_git_URL>

5.  Cuando se le solicite, escriba el nombre de usuario y la contraseña que estableció al habilitar el control de código fuente en el servicio móvil. Después de realizar la autenticación correctamente, aparecerá una serie de respuestas, como la siguiente:

         remote: Counting objects: 8, done.
         remote: Compressing objects: 100% (4/4), done.
         remote: Total 8 (delta 1), reused 0 (delta 0)
         Unpacking objects: 100% (8/8), done.

6.  Desplácese al directorio desde el que ejecutó el comando `git clone` y observe la estructura correspondiente a continuación:

    ![4](./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png)

    En este caso, se crea un nuevo directorio con el nombre del servicio móvil, que es el repositorio local para el servicio de datos.

7.  Abra la subcarpeta .\\service\\table y observe que contiene un archivo TodoItem.json, que es una representación JSON de los permisos de funcionamiento de la tabla TodoItem.

    Cuando se hayan definido scripts de servidor en esta tabla, también aparecerán uno o varios archivos denominados `TodoItem._<operation>_.js` que contendrán los scripts para la operación de tabla determinada. Los scripts del programador y de la API personalizada se mantienen en carpetas independientes con esos nombres respectivos. Para obtener más información, consulte [Control de código fuente](http://msdn.microsoft.com/en-us/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643).

Ahora que ha creado su repositorio local, puede realizar cambios en los scripts del servidor e insertarlos en el servicio móvil.

Implementación de scriptsImplementación de archivos de script actualizados en el servicio móvil
-----------------------------------------------------------------------------------------------

1.  Desplácese a la subcarpeta .\\service\\table y, si aún no existe un archivo todoitem.insert.js, créelo en este momento.

2.  Abra el nuevo archivo todoitem.insert.js en un editor de texto, pegue en él el código siguiente y guarde los cambios:

         function insert(item, user, request) {
             request.execute();
             console.log(item);
         }

    Este código se limita a escribir el elemento insertado en el registro. Si el archivo ya contiene código, solo tiene agregarle código válido de JavaScript, como una llamada a `console.log()`, y, a continuación, guardar los cambios.

3.  En el símbolo del sistema de Git, escriba el comando siguiente para empezar a realizar un seguimiento del nuevo archivo de scripts:

         $ git add .

4.  Escriba el comando siguiente para confirmar los cambios:

         $ git commit -m "updated the insert script"

5.  Escriba el comando siguiente para cargar los cambios en el repositorio remoto:

         $ git push origin master

    Aparecerá una serie de comandos que indica que la confirmación se ha implementado en el servicio móvil.

6.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

    ![](./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png)

7.  Haga clic en **Script** y, a continuación, seleccione la operación **Insert**.

    ![](./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png)

    Observe que el script de operación de inserción que se muestra es el mismo que el del código de JavaScript que acaba de cargar en el repositorio.

Código compartido y módulosAprovechamiento del código compartido y de módulos Node.js en los scripts del servidor
-----------------------------------------------------------------------------------------------------------------

Servicios móviles proporciona acceso a todo el conjunto de módulos Node.js básicos, que puede usar en su código mediante la función **require**. Asimismo, el servicio móvil puede usar módulos Node.js que no formen parte del paquete Node.js básico, y el usuario puede incluso definir su propio código compartido como módulos Node.js. Para obtener más información acerca de la creación de módulos, consulte la sección [Modules](http://nodejs.org/api/modules.html) (en inglés) correspondiente en la documentación de referencia de API Node.js.

A continuación, se agregará el módulo Node.js [node-uuid](https://npmjs.org/package/node-uuid) al servicio móvil mediante el control de código fuente y el administrador de paquetes Node.js (NPM). Este módulo se usará después para generar un nuevo valor de GUID para la propiedad **uuid** en los elementos insertados.

1.  Si aún no lo ha hecho, instale Node.js en el equipo local; para ello, siga los pasos que se indican en el [sitio web de Node.js](http://nodejs.org/) (en inglés).

2.  Desplácese a la carpeta `.\service` del repositorio Git local y, a continuación, ejecute el siguiente comando desde el símbolo del sistema:

         npm install node-uuid

    NPM crea el directorio `node_modules` en la ubicación actual e instala el módulo [node-uuid](https://npmjs.org/package/node-uuid) en el subdirectorio `\node-uuid`.

    **Nota:**

    Si `node_modules` ya existe en la jerarquía de directorios, NPM creará el subdirectorio `\node-uuid` ahí en lugar de crear un nuevo `node_modules` en el repositorio. En este caso, elimine el directorio `node_modules` existente.

3.  Ahora, desplácese a la subcarpeta .\\service\\table, abra el archivo todoitem.insert.js y modifíquelo de la siguiente forma:

         function insert(item, user, request) {
             var uuid = require('node-uuid');
             item.uuid = uuid.v1();
             request.execute();
             console.log(item);
         }

    Este código agrega una columna uuid a la tabla y la rellena con identificadores GUID únicos.

4.  Al igual que en la sección anterior, escriba el siguiente comando en el símbolo del sistema de Git:

         $ git add .
         $ git commit -m "added node-uuid module"
         $ git push origin master

    Al hacerlo, se agrega el nuevo archivo, se confirman los cambios y se insertan el nuevo módulo node-uuid y los cambios del script todoitem.insert.js en el servicio móvil.

Pasos siguientes
----------------

Ahora que ha completado este tutorial, ya sabe cómo almacenar sus scripts en el control de código fuente. Considere la posibilidad de obtener más información sobre cómo trabajar con scripts de servidor y con las API personalizadas:

-   [Uso de scripts del servidor en Servicios móviles](/en-us/develop/mobile/how-to-guides/work-with-server-scripts)
    Muestra cómo trabajar con scripts del servidor, el programador de trabajos y las API personalizadas.

-   [Definición de una API personalizada que admita notificaciones periódicas](/en-us/develop/mobile/tutorials/create-pull-notifications-dotnet)
     Muestra cómo usar las API personalizadas para admitir notificaciones periódicas que actualicen los iconos dinámicos en una aplicación de la Tienda Windows.


