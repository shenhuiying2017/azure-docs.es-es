<properties linkid="dev-ruby-web-app-with-linux-vm" urlDisplayName="Ruby on Rails Web App on Azure using Linux VM" pageTitle="Ruby on Rails Web App on Azure using Linux VM" metaKeywords="Azure Ruby web application, Azure Ruby application, Ruby app Azure, Ruby azure vm, ruby virthal machine, ruby linux vm" description="Host a Ruby on Rails-based web site on Azure using a Linux virtual machine. " metaCanonical="" services="virtual-machines" documentationCenter="Ruby" title="Ruby on Rails Web application on an Azure VM" authors="larryfr" solutions="" manager="" editor="" />

Aplicación web de Ruby on Rails en una máquina virtual de Azure
===============================================================

En este tutorial se describe cómo hospedar un sitio web basado en Ruby on Rails en Azure usando una máquina virtual del Linux. En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Al término de este tutorial, tendrá una aplicación basada en Ruby on Rails funcionando en la nube.

Aprenderá a:

-   Configurar su entorno de desarrollo.

-   Configurar una máquina virtual de Azure para hospedar Ruby on Rails.

-   Crear una aplicación nueva de Rails.

-   Copiar archivos en la máquina virtual usando scp.

La siguiente es una captura de pantalla de la aplicación completa:

![Explorador que muestra publicaciones de listas](./media/virtual-machines-ruby-rails-web-app-linux/blograilscloud.png)

En este artículo
----------------

-   [Configuración de su entorno de desarrollo](#setup)

-   [Creación de una aplicación de Rails](#create)

-   [Prueba de la aplicación](#test)

-   [Creación de una máquina virtual de Azure](#createvm)

-   [Copia de la aplicación en la máquina virtual](#copy)

-   [Instalación de gemas e inicio de la aplicación](#start)

-   [Pasos siguientes](#next)

Configuración de su entorno de desarrollo
-----------------------------------------

1.  Instale Ruby en su entorno de desarrollo. Los pasos pueden variar según el sistema operativo.

    -   **Apple OS X**: Puede haber varias distribuciones de Ruby para OS X. Este tutorial se validó en OS X mediante [Homebrew](http://brew.sh/) para instalar **rbenv** y **ruby-build**. Puede encontrar información sobre la instalación en <https://github.com/sstephenson/rbenv/>.

    -   **Linux**: Use su sistema de administración de paquetes de distribución. Este tutorial se validó en Ubuntu 12.10 usando los paquetes de desarrollo de ruby1.9.1 y ruby1.9.1-dev.

    -   **Windows**: Existen varias distribuciones de Ruby para Windows. Este tutorial se validó usando [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392.

2.  Abra una nueva línea de comandos o sesión de terminal y escriba el siguiente comando para instalar Ruby on Rails:

         gem install rails --no-rdoc --no-ri

    **Nota:**

    Es posible que este comando requiera privilegios de administrador o raíz en algunos sistemas operativos. Si recibe un error al ejecutar este comando, intente usar 'sudo' como se muestra:

    ``` {.prettyprint}
    sudo gem install rails
    ```

    **Nota:**

    Se usó la versión 3.2.12 de la gema Rails para este tutorial.

3.  Debe instalar también un intérprete de JavaScript, que Rails usará para compilar los recursos de CoffeeScript que usó su aplicación de Rails. Se puede encontrar una lista de intérpretes compatibles en <https://github.com/sstephenson/execjs#readme>.

    Se usó [Node.js](http://nodejs.org/) durante la validación de este tutorial, debido a que estaba disponible para los sistemas operativos OS X, Linux y Windows.

Creación de una aplicación de Rails
-----------------------------------

1.  Desde la línea de comandos o la sesión de terminal, cree una aplicación nueva de Rails llamada "blog\_app" con la ayuda del siguiente comando:

         rails new blog_app

    Este comando crea un directorio nuevo llamado **blog\_app** y lo rellena con los archivos y subdirectorios que necesita una aplicación de Rails.

    **Nota:**

    Este comando puede tardar un minuto o más en completarse. Realiza una instalación silenciosa de las gemas necesarias para una aplicación predeterminada y, durante este tiempo, puede parece que se ha colgado.

2.  Cambie los directorios por el directorio **blog\_app** y, a continuación, use el siguiente comando para crear un scaffolding de blog básico:

         rails generate scaffold Post name:string title:string content:text

    De esta manera se crea el controlador, la vista, el modelo y las migraciones de la base de datos que se usaron para mantener las publicaciones en el blog. Cada publicación tendrá el nombre de un autor, un título para la publicación y el contenido del texto.

3.  Para crear la base de datos que va a almacenar las publicaciones del blog, use el siguiente comando:

         rake db:migrate

    De esta manera se usará el proveedor de la base de datos predeterminada para Rails, que es la [Base de datos de SQLite3](http://www.sqlite.org/) (en inglés). Si bien es posible que desee usar una base de datos diferente para una aplicación de producción, SQLite es suficiente para los propósitos de este tutorial.

Prueba de la aplicación
-----------------------

Realice los siguientes pasos para iniciar el servidor de Rails en su entorno de desarrollo.

1.  Desde la línea de comandos o la sesión de terminal, inicie el servidor del Rails usando el siguiente comando:

         rails s

    Debería ver una salida similar a la siguiente. Tenga en cuenta el puerto que escucha el servidor web. En el ejemplo a continuación, está escuchando el puerto 3000.

         => Booting WEBrick
         => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
         => Call with -d to detach
         => Ctrl-C to shutdown server
         [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
         [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
         [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2.  Abra el explorador y diríjase a http://localhost:3000/. Debería ver una página similar a la siguiente:

    ![Página predeterminada de Rails](./media/virtual-machines-ruby-rails-web-app-linux/basicrailslocal.png)

    Esta página es una página de bienvenida estática. Para ver los formularios que generó el comando de scaffolding, diríjase a http://localhost:3000/posts. Debería ver una página similar a la siguiente:

    ![Página con lista de publicaciones](./media/virtual-machines-ruby-rails-web-app-linux/blograilslocal.png)

    Para detener el proceso del servidor, escriba CTRL+C en la línea de comandos.

Creación de una máquina virtual de Azure
----------------------------------------

Siga las instrucciones que se proporcionan [aquí](http://windowsazure.com/en-us/documentation/articles/virtual-machines-linux-tutorial) para crear una máquina virtual de Azure que hospeda Linux.

**Nota:**

Los pasos de este tutorial se realizaron en una máquina virtual de Azure con Ubuntu 12.10. Si va a usar una distribución de Linux diferente, es posible que se requieran diferentes pasos para llevar a cabo las mismas tareas.

**Nota:**

**Solo** necesita crear la máquina virtual. Deténgase después de saber cómo conectarse a la máquina virtual con SSH.

Después de crear la máquina virtual de Azure, realice los siguientes pasos para instalar Ruby and Rails en la máquina virtual:

1.  En la línea de comandos o la sesión de terminal, use el siguiente comando para conectarse a la máquina virtual con SSH:

         ssh username@vmdns -p port

    Reemplace el nombre usuario que se específica durante la creación de una máquina virtual, la dirección DNS de la máquina virtual y el puerto del extremo de SSH. Por ejemplo:

         ssh railsdev@railsvm.cloudapp.net -p 61830

    **Nota:**

    Si va a usar Windows como su entorno de desarrollo, puede usar una utilidad como **PuTTY** para la funcionalidad de SSH. PuTTY puede obtenerse desde la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (en inglés).

2.  En la sesión de SSH, use los siguientes comandos para instalar Ruby en la máquina virtual:

         sudo apt-get update -y
         sudo apt-get upgrade -y
         sudo apt-get install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs -y

    Una vez finalizada la instalación, use el siguiente comando para verificar que Ruby se instaló correctamente:

         ruby -v

    De esta manera se debe obtener la versión de Ruby que se instaló en la máquina virtual, que puede ser diferente de la 1.9.1. Por ejemplo, **ruby 1.9.3p194 (2012-04-20 revisión 35410) [x86\_64-linux]**.

3.  Use el siguiente comando para instalar Bundler:

         sudo gem install bundler --no-rdoc --no-ri

    Bundler se usará para instalar las gemas requeridas por sus aplicaciones de Rails después de que se haya copiado en el servidor.

Copia de la aplicación en la máquina virtual
--------------------------------------------

Desde el entorno de desarrollo, abra una nueva línea de comandos o sesión de terminal y use el comando **scp** para copiar el directorio **blog\_app** en la máquina virtual. El formato de este comando es el siguiente:

    scp -r -P 54822 -C directory-to-copy user@vmdns:

Por ejemplo:

    scp -r -P 54822 -C ~/blog_app railsdev@railsvm.cloudapp.net:

**Nota:**

Si va a usar Windows como entorno de desarrollo, puede usar una utilidad como **pscp** para la funcionalidad de scp. Pscp puede obtenerse desde la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (en inglés).

Los parámetros que se usan para este comando tienen el siguiente efecto:

-   **-r**: Copia de manera recursiva el contenido del directorio especificado y todos los subdirectorios.

-   **-P**: Usa el puerto especificado para la comunicación de SSH.

-   **-C**: Habilita la compresión.

-   **directory-to-copy**: El directorio local que se va a copiar.

-   **user@vmdns**: La dirección de la máquina en la cual se van a copiar los archivos, además de la cuenta de usuario con la cual se inicia sesión.

Después de la operación de copia, el directorio **blog\_app** se ubicará en el directorio principal de los usuarios. Use los siguientes comandos en la sesión de SSH con la máquina virtual para ver los archivos que se copiaron:

    cd ~/blog_app
    ls

La lista de archivos devuelta debe coincidir con las archivos contenidos en el directorio **blog\_app** en su entorno de desarrollo.

Instalación de gemas e inicio de Rails
--------------------------------------

1.  En la máquina virtual, cambie los directorios por el directorio **blog\_app** y use el siguiente comando para instalar las gemas especificadas en **Gemfile**:

         sudo bundle install

2.  Para crear la base de datos, use el siguiente comando:

         rake db:migrate

3.  Utilice el comando siguiente para iniciar el servidor:

         rails s

    Debería ver una salida similar a la siguiente. Tenga en cuenta el puerto que escucha el servidor web. En el ejemplo a continuación, está escuchando el puerto 3000.

         => Booting WEBrick
         => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
         => Call with -d to detach
         => Ctrl-C to shutdown server
         [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
         [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
         [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

4.  En su explorador, diríjase hasta el [Portal de administración de Azure](https://manage.windowsazure.com/) y seleccione su máquina virtual.

    ![Lista de máquinas virtuales](./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png)

5.  Seleccione **ENDPOINTS** en la parte superior de la página y, a continuación, haga clic en **+ ADD ENDPOINT** en la parte inferior de la página.

    ![Página de extremos](./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png)

6.  En el cuadro de diálogo **ADD ENDPOINT**, haga clic en la fecha de la parte inferior izquierda para continuar a la segunda página y escriba la siguiente información en el formulario:

    -   **NAME**: HTTP

    -   **PROTOCOL**: TCP

    -   **PUBLIC PORT**: 80

    -   **PRIVATE PORT**:

    De esta manera se crea un puerto público 80 que dirigirá el tráfico hasta el puerto privado 300, donde el servidor de Rails está escuchando.

    ![Cuadro de diálogo de extremo nuevo](./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png)

7.  Haga clic en la marca de verificación para guardar el extremo.

8.  Debe aparecer un mensaje que muestre **UPDATE IN PROGRESS**. Después de que el mensaje desaparece, el extremo está activo. Ahora puede probar su aplicación dirigiéndose al nombre de DNS de la máquina virtual. El sitio web debe ser similar al siguiente:

    ![Página predeterminada de Rails](./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png)

    El anexo de **/posts** a la dirección URL debe mostrar las páginas que se generaron mediante el comando de scaffolding.

    ![Página de publicaciones](./media/virtual-machines-ruby-rails-web-app-linux/blograilscloud.png)

Pasos siguientes
----------------

En este artículo aprendió a crear y publicar una aplicación básica de Rails basada en formularios en una máquina virtual de Azure. La mayoría de las acciones que realizamos fueron manuales y, en un entorno de producción, sería deseable automatizar. Además, la mayoría de los entornos de producción hospedan la aplicación de Rails conjuntamente con otro proceso de servidor, como Apache o NginX, que controla el enrutamiento de solicitudes a varias instancias de la aplicación de Rails y el servicio a recursos estáticos.

Para obtener información sobre la implementación de automatización de su aplicación de Rails, y sobre el uso del servidor web Unicorn y NginX, consulte [Unicorn+NginX+Capistrano con una Máquina virtual de Azure](http://windowsazure.com/en-us/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/).

Si desea obtener más información sobre Ruby on Rails, visite las [Guías de Ruby on Rails](http://guides.rubyonrails.org/) (en inglés).

Para obtener información sobre cómo usar el SDK de Azure para Ruby y así tener acceso a los servicios de Azure desde su aplicación de Ruby, consulte:

-   [Almacenamiento de datos no estructurados con blobs](http://windowsazure.com/en-us/documentation/articles/storage-ruby-how-to-use-blob-storage)

-   [Almacenamiento de pares clave/valor con tablas](http://www.windowsazure.com/en-us/develop/ruby/how-to-guides/table-service/)

-   [Servicio de contenido con alto ancho de banda con la Red de entrega de contenido](http://www.windowsazure.com/en-us/develop/ruby/app-services/)


