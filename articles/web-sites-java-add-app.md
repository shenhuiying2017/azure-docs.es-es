<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java web site" pageTitle="Add an application to your Java web site" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java web site on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java web site" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

Incorporación de una aplicación al sitio web de Java en Azure
=============================================================

En este tutorial se muestra cómo agregar una página web o una aplicación a un sitio web de Java que se ha creado previamente utilizando la galería de Azure o la interfaz de usuario de configuración de Azure.

Tenga en cuenta que puede utilizar el control de código fuente para cargar la aplicación o páginas web, incluso en escenarios de integración continua. Las instrucciones para utilizar el control de código fuente con el sitio web están disponibles en [Publicación en Sitios web Azure desde el control de código fuente](../web-sites-publish-source-control). Como alternativa al control de código fuente, en este artículo se mostrará cómo utilizar FTP para cargar la aplicación.

Este tutorial asume que ya ha completado los pasos en [Introducción a los Sitios web Microsoft Azure y Java](../web-sites-java-get-started).

Personalización de un sitio web con FTP
=======================================

Tendrá que determinar las credenciales FTP y utilizarlas para tener acceso a los contenidos del sitio web. A continuación, puede modificar el contenido para ejecutar la aplicación. El ejemplo que se muestra aquí utiliza el Explorador de archivos para facilitar el FTP, pero también puede utilizar otras técnicas de FTP.

Uso de credenciales FTP para tener acceso a los contenidos del sitio
--------------------------------------------------------------------

1.  En el Portal de Administración de Microsoft Azure, vaya a la vista **Sitios web**.
2.  En la vista **Sitios web**, haga clic en el nombre del sitio web.
3.  Haga clic en **Panel**.
4.  En la vista **Panel**, bajo **Vista rápida**, haga clic en **Descargar el perfil de publicación**. Guarde este archivo localmente. Asegúrese de mantener este archivo seguro, ya que contiene el nombre de usuario y la contraseña que permite la publicación en su sitio (así como la copia del contenido del sitio).
5.  Abra el archivo de configuración de publicación descargado con un editor de texto. Dentro de ese archivo, ponga atención a los valores de **userName** y **userPwd**. Ellos representan el nombre de usuario y la contraseña, respectivamente, que va a utilizar para tener acceso a los archivos del sitio.
6.  Obtenga acceso a los archivos del sitio web y proporcione el nombre de usuario y la contraseña cuando se le solicite. En este ejemplo se utilizará FTP en Internet Explorer, pero también se pueden usar otras técnicas. Para continuar con el uso de FTP, en la vista de **Panel**, haga clic en la URL que aparece bajo **Nombre del host FTP**. (También se puede determinar el nombre de host de FTP desde el archivo de configuración de publicación, que es el valor asignado a **publishUrl**).
7.  Cuando se le pida el nombre de usuario y la contraseña, utilice los valores especificados en el archivo de configuración de publicación para **userName** y **userPwd**.
8.  Aún dentro de Internet Explorer, para cambiar a la vista de Explorador de archivos, haga clic en **Ver** y, a continuación, haga clic en **Abrir el sitio FTP en el Explorador de archivos**.

Acceso a la carpeta webapps para el sitio web
---------------------------------------------

Ahora puede personalizar el sitio web en la vista del Explorador de archivos del sitio web en Azure. Tendrá que copiar la aplicación a la carpeta **webapps** del sitio web. La ruta de acceso de navegación a esa carpeta varía en función de cómo haya configurado el sitio web.

-   Si configura el sitio web mediante el uso de la galería de aplicaciones de Azure, en el Explorador de archivos, haga doble clic en **site**, haga doble clic en **wwwroot**, haga doble clic en **bin**, haga doble clic en la versión del servidor de aplicaciones que el sitio web está utilizando y, a continuación, haga doble clic en **webapps**.
-   Si configura el sitio web mediante la interfaz de usuario de configuración de Azure, en el Explorador de archivos, haga doble clic en **site**, haga doble clic en **wwwroot** y haga doble clic en **webapps**.
-   Si configura el sitio web mediante una carga personalizada, navegue siguiendo los pasos necesarios para llegar a la carpeta **webapps**.

Para agregar un archivo WAR al sitio web a través de FTP
--------------------------------------------------------

1.  Vaya a la carpeta **webapps** utilizando la técnica adecuada para el sitio web, como se describió anteriormente.
2.  Copie el archivo WAR a la carpeta **webapps**.

El servidor de aplicaciones detectará que ha agregado el archivo WAR y lo cargará automáticamente. A continuación, puede ejecutar la aplicación en el navegador, a través de la URL del sitio web con el nombre del archivo WAR adjunta a la misma.

Por ejemplo, vaya a http://*mysitename*.azurewebsites.net/*mywar*, donde *mysitename* es el nombre que ha especificado para la URL y *mywar* es el nombre que distingue mayúsculas de minúsculas de WAR que copió (sin el final **.war**).

Para agregar una página web al sitio web a través de FTP
--------------------------------------------------------

1.  Vaya a la carpeta **webapps**.
2.  Cree una carpeta nueva dentro de la carpeta **webapps**.
3.  Abra la carpeta nueva.
4.  Agregue la página web a la carpeta nueva.

El servidor de aplicaciones detectará que ha agregado la carpeta nueva y el archivo web y lo cargará automáticamente. A continuación, ejecute el archivo JSP utilizando la dirección URL en la forma de http://*mysitename*.azurewebsites.net/*myfolder*/*myfile.jsp*, donde *mysitename* es el nombre que ha especificado para la URL, *myfolder* es la carpeta que creó en **webapps** y *myfile.jsp* es el nombre del archivo JSP que ha creado.

Tenga en cuenta que si copia los archivos (excepto los archivos WAR) al directorio raíz, será necesario reiniciar el servidor de aplicaciones antes de utilizar esos archivos. La funcionalidad de carga automática para los sitios web Java que se ejecutan en Azure se basa en un nuevo archivo WAR que se agrega, o en archivos o directorios nuevos que se agregan a la carpeta **webapps**.

