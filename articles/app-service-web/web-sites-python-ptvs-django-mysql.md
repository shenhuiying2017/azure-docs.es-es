---
title: Django y MySQL en Azure con Python Tools 2.2 para Visual Studio
description: "Obtenga información acerca de cómo usar las herramientas de Python para Visual Studio para crear una aplicación web Django que almacene datos en una instancia de base de datos MySQL y se pueda implementar en Aplicaciones web del Servicio de aplicaciones de Azure."
services: app-service\web
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: c60a50b5-8b5e-4818-a442-16362273dabb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: get-started-article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 759441c5d64ee59f13d50eb415fbaa884dd4821a


---
# <a name="django-and-mysql-on-azure-with-python-tools-22-for-visual-studio"></a>Django y MySQL en Azure con Python Tools 2.2 para Visual Studio
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

En este tutorial, se usarán las [Herramientas de Python para Visual Studio](https://www.visualstudio.com/vs/python) a fin de crear una aplicación web de sondeos sencilla mediante una de las plantillas de ejemplo de PTVS. Aprenderá a usar un servicio de MySQL hospedado en Azure, a configurar la aplicación web para usar MySQL y publicar la aplicación web en [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

> [!NOTE]
> La información de este tutorial también está disponible en el siguiente vídeo:
> 
> [PTVS 2.1: Django app with MySQL (PTVS 2.1: Aplicación Django con MySQL)][vídeo]
> 
> 

Consulte el [Centro para desarrolladores de Python] para acceder a más artículos sobre el desarrollo de Aplicaciones web del Servicio de aplicaciones de Azure con PTVS mediante marcos web Bottle, Flask y Django, con Almacenamiento de tablas de Azure y los servicios de Base de datos MySQL y SQL. Si bien estos artículos se centran en el Servicio de aplicaciones, los pasos son similares a los que se aplican para desarrollar [Servicios en la nube de Azure].

## <a name="prerequisites"></a>Requisitos previos
* Visual Studio 2015
* [Python 2.7 de 32 bits] o [Python 3.4 de 32 bits]
* [Python Tools 2.2 para Visual Studio]
* [Python Tools 2.2 para archivos VSIX de ejemplo de Visual Studio]
* [Azure SDK y herramientas para VS 2015]
* Django 1.9 o posterior

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

## <a name="create-the-project"></a>Creación del proyecto
En esta sección, va a crear un proyecto de Visual Studio con la utilización de una plantilla de ejemplo. Va a crear un entorno virtual e instalar los paquetes necesarios. Va a crear una base de datos local con sqlite. Después, va a ejecutar la aplicación localmente.

1. En Visual Studio, seleccione **Archivo**, **Nuevo proyecto**.
2. Las plantillas de proyecto de los [archivos VSIX de ejemplo de Python Tools 2.2 para Visual Studio] se encuentran disponibles en **Python**, **Ejemplos**. Seleccione **Polls Django Web Project** (Proyecto web de Django para sondeos) y haga clic en OK (Aceptar) para crear el proyecto.
   
    ![Cuadro de diálogo Nuevo proyecto](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)
3. Se le pedirá que instale paquetes externos. Seleccione **Instalar en un entorno virtual**.
   
    ![Cuadro de diálogo Paquetes externos](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)
4. Seleccione **Python 2.7** o **Python 3.4** como intérprete de base.
   
    ![Cuadro de diálogo Agregar entorno virtual](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)
5. En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo de proyecto, seleccione **Python** y **Django Migrate** (Migración de Django).  Después, seleccione **Django Create Superuser (Crear superusuario de Django)**.
6. Se abre la consola de administración de Django donde se va a crear una base de datos de sqlite en la carpeta del proyecto. Siga las indicaciones para crear un usuario.
7. Presione `F5`para confirmar que la aplicación funciona.
8. Haga clic en **Iniciar sesión** en la barra de navegación de la parte superior.
   
    ![Barra de navegación de Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)
9. Escriba las credenciales del usuario que ha creado al sincronizar la base de datos.
   
    ![Formulario de inicio de sesión](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)
10. Haga clic en **Create Sample Polls**(Crear sondeos de ejemplo).
    
     ![Create Sample Polls](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)
11. Haga clic en un sondeo y vote.
    
     ![Votación en sondeos de ejemplo](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-mysql-database"></a>Creación de una base de datos MySQL
Para la base de datos, va a crear una base de datos MySQL de ClearDB hospedada en Azure.

Como alternativa, puede crear su propia máquina virtual para que se ejecute en Azure y, a continuación, instalar y administrar MySQL por su cuenta.

Siga estos pasos para crear una base de datos con un plan gratuito.

1. Inicie sesión en el [Portal de Azure].
2. En la parte superior del panel de navegación, haga clic en **NUEVO**, **Datos y almacenamiento** y, finalmente, en **Base de datos MySQL**.
3. Configure la nueva base de datos MySQL mediante la creación de un nuevo grupo de recursos y seleccione la ubicación adecuada para él.
4. Una vez creada la base de datos MySQL, haga clic en **Propiedades** en la hoja de la base de datos.
5. Use el botón Copiar para colocar el valor de **CONNECTIONSTRING** en el Portapapeles.

## <a name="configure-the-project"></a>Configuración del proyecto
En esta sección, va a configurar nuestra aplicación web para usar la base de datos MySQL que acaba de crear. También va a instalar paquetes adicionales de Python necesarios para utilizar bases de datos MySQL con Django. Después, ejecutará la aplicación web localmente.

1. En Visual Studio, abra **settings.py**desde la carpeta *ProjectName* . Pegue temporalmente la cadena de conexión en el editor. La cadena de conexión tiene el siguiente formato:
   
        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>
   
    Cambie el **MOTOR** predeterminado de la base de datos para que use MySQL y establezca los valores de **NOMBRE**, **USUARIO**, **CONTRASEÑA** y **HOST** desde **CONNECTIONSTRING**.
   
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }
2. En el Explorador de soluciones, en **Entornos de Python**, haga clic con el botón derecho en el entorno virtual y seleccione **Instalar paquete de Python**.
3. Instale el paquete `mysqlclient` con **pip**.
   
    ![Cuadro de diálogo Instalar paquete](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)
4. En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo de proyecto, seleccione **Python** y **Django Migrate** (Migración de Django).  Después, seleccione **Django Create Superuser (Crear superusuario de Django)**.
   
    De esta forma, se crearán las tablas para la base de datos MySQL que ha creado en la sección anterior. Siga las indicaciones para crear un usuario, que no tiene que coincidir con el usuario de la base de datos sqlite creada en la primera sección de este artículo.
5. Presione `F5`para ejecutar la aplicación. Los sondeos creados con **Create Sample Polls** (Crear sondeos de ejemplo) y los datos enviados al votar se serializarán en la base de datos MySQL.

## <a name="publish-the-web-app-to-azure-app-service"></a>Publicación de aplicación web del Servicio de aplicaciones de Azure
El SDK de Azure .NET ofrece una forma fácil de implementar la aplicación web en el Servicio de aplicaciones de Azure.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo del proyecto y seleccione **Publicar**.
   
    ![Cuadro de diálogo Publicación web](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)
2. Haga clic en **Servicio de aplicaciones de Microsoft Azure**.
3. Haga clic en **Nuevo** para crear una aplicación web nueva.
4. Rellene los campos siguientes y haga clic en **Crear**:
   
   * **Nombre de aplicación web**
   * **Plan del Servicio de aplicaciones**
   * **Grupos de recursos**
   * **Región**
   * Deje **Servidor de base de datos** establecido en **No hay base de datos**
5. Acepte todos los valores predeterminados y haga clic en **Publicar**.
6. El explorador web se abrirá automáticamente en la aplicación web publicada. La aplicación web ahora debe funcionar según lo previsto, con el uso de la base de datos **MySQL** hospedada en Azure.
   
    ![Explorador web](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)
   
    ¡Enhorabuena! Ha publicado correctamente la aplicación web basada en MySQL en Azure.

## <a name="next-steps"></a>Pasos siguientes
Siga estos vínculos para obtener más información sobre las herramientas de Python para Visual Studio, Django y MySQL.

* [Documentación sobre Python Tools para Visual Studio]
  * [Proyectos web]
  * [Proyectos de servicio en la nube]
  * [Depuración remota en Microsoft Azure]
* [Documentación de Django]
* [MySQL]

Para más información, vea el [Centro para desarrolladores de Python](/develop/python/).

<!--Link references-->

[Centro para desarrolladores de Python]: /develop/python/
[Azure Cloud Services]: ../cloud-services/cloud-services-python-ptvs.md

<!--External Link references-->

[Portal de Azure]: https://portal.azure.com
[Herramientas de Python para Visual Studio]: https://www.visualstudio.com/vs/python/
[Python Tools 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 para archivos VSIX de ejemplo de Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK y herramientas para VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentación sobre Python Tools para Visual Studio]: http://aka.ms/ptvsdocs
[Depuración remota en Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Proyectos web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Proyectos de servicio en la nube]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentación de Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[vídeo]: http://youtu.be/oKCApIrS0Lo



<!--HONumber=Nov16_HO2-->


