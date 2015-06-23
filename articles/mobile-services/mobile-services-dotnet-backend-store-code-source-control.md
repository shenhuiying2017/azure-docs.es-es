<properties 
	pageTitle="Almacenamiento del código del proyecto en control de código fuente - Servicios móviles de Azure" 
	description="Aprenda a almacenar el proyecto de back-end de .NET en el equipo y a publicar desde un repositorio Git local en el equipo." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

<div class="dev-center-tutorial-subselector">
	<a href="/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/" title="Back-end de .NET" class="current">Back-end de .NET</a> | <a href="/documentation/articles/mobile-services-store-scripts-source-control/"  title="Back-end de JavaScript">Back-end de JavaScript</a>
</div>

# Almacenamiento del código del proyecto en control de código fuente

En este tema se muestra cómo utilizar el control de código fuente que proporcionan los servicios móviles de Azure para almacenar el proyecto de servicio de back-end. NET. Para publicar el proyecto, basta con cargarlo desde el repositorio Git local al servicio móvil de producción.

El tutorial le guiará a través de los siguientes pasos:

1. [Habilitación del control de código fuente en el servicio móvil].
2. [Instalación de Git y creación del repositorio local].
3. [Publicación del proyecto mediante Git].

Para completar este tutorial, debe haber creado un servicio móvil tras completar los tutoriales [Introducción a los servicios móviles] o [Incorporación de servicios móviles a una aplicación existente].

##<a name="enable-source-control"></a>Habilitación del control de código fuente en el servicio móvil.

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Instalación de Git y creación del repositorio local

1. Instale Git en su equipo local. 

	Los pasos requeridos para instalar Git varían según los sistemas operativos. Consulte [Installing Git] para obtener una guía sobre la instalación y las distribuciones específicas del sistema operativo.

	> [AZURE.NOTE]Algunos sistemas operativos disponen de versiones de Git en línea de comandos y de GUI. Las instrucciones proporcionadas en este artículo utilizan la versión en línea de comandos.

2. Abra una línea de comandos, como **GitBash** (Windows) o **Bash** (shell de Unix). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación **Terminal**.

3. Desde la línea de comandos, cambie al directorio donde almacenará los scripts. Por ejemplo: `cd SourceControl`.

4. Use el comando siguiente para crear una copia local del nuevo repositorio Git. Deberá reemplazar `<your_git_URL>` por la dirección URL del repositorio Git del servicio móvil:

		git clone <your_git_URL>

5. Cuando se le solicite, escriba el nombre de usuario y la contraseña que estableció al habilitar el control de código fuente en el servicio móvil. Después de realizar la autenticación correctamente, aparecerá una serie de respuestas, como la siguiente:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Vaya al directorio desde el que ejecutó el comando `git clone` y observe que se creó un nuevo directorio con el nombre del servicio móvil. Para un servicio móvil de back-end. NET, el repositorio está vacío al inicio.

Ahora que ha creado el repositorio local, puede publicar el proyecto de servicio de back-end de .NET desde este repositorio.

##<a name="deploy-scripts"></a>Publicación del proyecto mediante Git

1. Cree un nuevo proyecto de servicio móvil de back-end de .NET en Visual Studio 2013 o mueva un proyecto existente al nuevo repositorio local.  

	Para realizar una prueba rápida, descargue y guarde el proyecto de inicio rápido de Servicios móviles en esta carpeta.

2. Quite las carpetas de paquetes de NuGet y deje el archivo packages.config.

	Los Servicios móviles restaurarán automáticamente los paquetes de NuGet según el archivo packages.confign. También puede definir un archivo .gitignore para evitar que se agreguen los directorios de paquete.
 
3. En el símbolo del sistema de Git, escriba el comando siguiente para empezar a realizar un seguimiento del nuevo archivo de scripts:

		$ git add .
	
4. Escriba el comando siguiente para confirmar los cambios:

		$ git commit -m "adding the .NET backend service project"

5. Escriba el comando siguiente para cargar los cambios en el repositorio remoto y proporcione sus credenciales:

		$ git push origin master
	
	Debería ver una serie de comandos que indican que el proyecto está implementado en los servicios móviles, que se han agregado los paquetes y que se reinició el servicio.

6. Vaya a la dirección URL de su servicio móvil de back-end de .NET y verá lo siguiente:

	![Página de inicio de servicios móviles](./media/mobile-services-dotnet-backend-store-code-source-control/mobile-service-startup.png)

Ahora, el proyecto de servicio móvil se mantiene en el control de código fuente y puede publicar actualizaciones del servicio mediante la simple inserción de las actualizaciones desde el repositorio local. Para obtener información sobre cómo realizar cambios en el modelo de datos en un servicio móvil de back-end de .NET que usa una base de datos de SQL, consulte [Modificación del modelo de datos de un servicio móvil back-end de .NET].

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha completado este tutorial, ya sabe cómo almacenar sus scripts en el control de código fuente. Considere la posibilidad de obtener más información acerca de los servicios móviles:

+ [Cómo efectuar cambios en el modelo de datos a un servicio móvil back-end de .NET] <br/> En este tema se muestra cómo utilizar Migraciones de Entity Framework Code First para realizar cambios en el modelo de datos de una base de datos SQL de Azure existente para evitar perder datos existentes. 	

+ [Llamar a una API personalizada desde el cliente] <br/> Muestra cómo crear API personalizadas que se pueden llamar desde el cliente.

<!-- Anchors. -->
[Habilitación del control de código fuente en el servicio móvil]: #enable-source-control
[Instalación de Git y creación del repositorio local]: #clone-repo
[Publicación del proyecto mediante Git]: #deploy-scripts

<!-- Images. -->

<!-- URLs. -->
[Git website]: http://git-scm.com
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Introducción a los servicios móviles]: mobile-services-dotnet-backend-ios-get-started.md
[Incorporación de servicios móviles a una aplicación existente]: mobile-services-dotnet-backend-ios-get-started-data.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Llamar a una API personalizada desde el cliente]: mobile-services-dotnet-backend-ios-call-custom-api.md
[Cómo efectuar cambios en el modelo de datos a un servicio móvil back-end de .NET]: mobile-services-dotnet-backend-how-to-use-code-first-migrations.md
[Modificación del modelo de datos de un servicio móvil back-end de .NET]: mobile-services-dotnet-backend-how-to-use-code-first-migrations.md

<!--HONumber=54--> 