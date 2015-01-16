<properties urlDisplayName="Add an application to your Java website" pageTitle="Incorporación de una aplicación al sitio web de Java" metaKeywords="" description="En este tutorial se muestra cómo agregar una página o aplicación a su sitio web de Java en Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java website" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Incorporación de una aplicación al sitio web de Java en Azure

Cuando haya inicializado el sitio web de Java tal y como se documenta en [Introducción a los Sitios web Microsoft Azure y Java](../web-sites-java-get-started), podrá cargar su aplicación colocando el WAR en la carpeta **webapps**.

La ruta de acceso de navegación a la carpeta **webapps** varía en función de cómo haya configurado el sitio web.

- Si configura su sitio web con la galería de aplicaciones de Azure, la ruta a la carpeta **webapps** tiene el formato **d:\home\site\wwwroot\bin\application\_server\webapps**, donde **application\_server** es el nombre del servidor de la aplicación activa en el sitio web. 
- Si configura su sitio web con la IU de configuración de Azure, la ruta a la carpeta **webapps** tiene el formato **d:\home\site\wwwroot\webapps**. 

Tenga en cuenta que puede utilizar el control de código fuente para cargar la aplicación o páginas web, incluso en escenarios de integración continua. Las instrucciones para utilizar el control de código fuente con el sitio web están disponibles en [Publicación en Sitios web Azure desde el control de código fuente](../web-sites-publish-source-control). FTP también es una opción para cargar la aplicación o las páginas web.

Nota para sitios web de Tomcat: Después de cargar el archivo WAR a la carpeta **webapps**, el servidor de la aplicación Tomcat detectará que lo ha agregado y lo cargará automáticamente. Tenga en cuenta que si copia los archivos (excepto los archivos WAR) al directorio raíz, será necesario reiniciar el servidor de aplicaciones antes de utilizar esos archivos. La funcionalidad de carga automática para los sitios web Tomcat Java que se ejecutan en Azure se basa en un nuevo archivo WAR que se agrega, o en archivos o directorios nuevos que se agregan a la carpeta **webapps**. 
