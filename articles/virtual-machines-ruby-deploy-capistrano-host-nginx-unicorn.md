<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby on Rails Azure VM Capistrano" pageTitle="Deploying a Ruby on Rails Web application to an Azure Virtual Machine using Capistrano - tutorial" metaKeywords="ruby on rails, ruby on rails azure, rails azure, rails vm, capistrano azure vm, capistrano azure rails, unicorn azure vm, unicorn azure rails, unicorn nginx capistrano, unicorn nginx capistrano azure, nginx azure" description="Learn how to deploy a Ruby on Rails application to an Azure Virtual Machine using Capistrano, Unicorn and Nginx." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Deploy a Ruby on Rails Web application to an Azure VM using Capistrano" authors="" />

Implementación de una aplicación web de Ruby on Rails en una máquina virtual de Azure con Capistrano
====================================================================================================

En este tutorial se describe cómo implementar un sitio web basado en Ruby on Rails en una máquina virtual de Azure con [Capistrano](https://github.com/capistrano/capistrano/). También se describe cómo configurar [Nginx](http://nginx.org/) y [Unicorn](https://github.com/blog/517-unicorn) para hospedar la aplicación en la máquina virtual.

En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Al término de este tutorial, tendrá una aplicación basada en Ruby on Rails funcionando en la nube.

Aprenderá a:

-   Configurar su entorno de desarrollo

-   Instalar Ruby y Ruby on Rails

-   Instalar y configurar Nginx y Unicorn

-   Crear una aplicación nueva de Rails

-   Implementar una aplicación de Rails en una máquina virtual de Azure con Capistrano

La siguiente es una captura de pantalla de la aplicación completa:

![Explorador que muestra publicaciones de listas](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png)

**Nota:**

La aplicación utilizada en este tutorial incluye componentes binarios nativos. Por este motivo, puede que encuentre problemas si su entorno de desarrollo no se basa en Linux, ya que es posible que el Gemfile.lock que se produce en la máquina de desarrollo no incluya entradas para la versión de gemas requeridas compatible con Linux.

Se invocan pasos específicos para usar un entorno de desarrollo Windows, ya que este representa la diferencia más importante con el entorno de implementación de destino. Sin embargo, si encuentra errores durante la implementación o después de la misma que no se tratan en este artículo, puede probar de nuevo los pasos de este artículo en un entorno de desarrollo basado en Linux.

En este artículo
----------------

-   [Configuración de su entorno de desarrollo](#setup)

-   [Creación de una aplicación de Rails](#create)

-   [Prueba de la aplicación](#test)

-   [Creación de un repositorio de origen](#repository)

-   [Creación de una máquina virtual de Azure](#createvm)

-   [Prueba de Nginx](#nginx)

-   [Preparación de la implementación](#capify)

-   [Implementación](#deploy)

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

1.  Cambie los directorios por el directorio **blog\_app** si no se encuentra ya en él, e inicie el servidor de Rails con el siguiente comando:

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

    ![Página predeterminada de Rails](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png)

    Esta página es una página de bienvenida estática. Para ver los formularios que generó el comando de scaffolding, diríjase a http://localhost:3000/posts. Debería ver una página similar a la siguiente:

    ![Página con lista de publicaciones](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png)

    Para detener el proceso del servidor, escriba CTRL+C en la línea de comandos.

Creación de un repositorio de origen
------------------------------------

En este tutorial, usaremos [Git](http://git-scm.com/) y [GitHub](https://github.com/) para el control de versión y como ubicación central para nuestro código.

1.  Creación de un repositorio en [GitHub](https://github.com/). Si actualmente no tiene una cuenta GitHub, puede registrarse y obtener una cuenta gratuita. Para los pasos indicados en este tutorial se presupone que el nombre del repositorio es **blog\_app**.

    **Nota:**

    Los scripts creados en secciones posteriores de este documento contendrán la dirección de su máquina virtual y el nombre de usuario utilizado para implementar la aplicación en SSH. Por este motivo, recomendamos que use un repositorio de GitHub privado si es posible.

2.  Desde el símbolo del sistema, cambie los directorios por el directorio **blog\_app** y ejecute los siguientes comandos para cargar la versión inicial de la aplicación en el repositorio de GitHub:

        git init
        git add .
        git commit -m "initial commit on azure"
        git remote add origin https://github.com/YOUR_GITHUB_ACCOUNT/blog-azure.git
        git push -u origin master

Creación de una máquina virtual de Azure
----------------------------------------

Siga las instrucciones que se proporcionan [aquí](/es-es/manage/linux/tutorials/virtual-machine-from-gallery/) para crear una máquina virtual de Azure que hospeda Linux.

**Nota:**

Los pasos de este tutorial se realizaron en una máquina virtual de Azure con Ubuntu 12.10. Si va a usar una distribución de Linux diferente, es posible que se requieran diferentes pasos para llevar a cabo las mismas tareas.

**Nota:**

**Solo** necesita crear la máquina virtual. Deténgase después de saber cómo conectarse a la máquina virtual con SSH.

Después de crear la máquina virtual de Azure con SSH y realizar los siguientes pasos para instalar Ruby and Rails en la máquina virtual:

1.  Conéctese a la máquina virtual con SSH y utilice los siguientes comandos para actualizar los paquetes existentes e instalar su entorno de Ruby:

         sudo apt-get -y update
         sudo apt-get -y agrade
         sudo apt-get -y install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs curl git-core nginx

    Una vez finalizada la instalación, use el siguiente comando para verificar que Ruby se instaló correctamente:

         ruby -v

    De esta manera se debe obtener la versión de Ruby que se instaló en la máquina virtual, que puede ser diferente de la 1.9.1. Por ejemplo, **ruby 1.9.3p194 (2012-04-20 revisión 35410) [x86\_64-linux]**.

2.  Use el siguiente comando para instalar Bundler:

         sudo gem install bundler

    Bundler se usará para instalar las gemas que necesita su aplicación de Rails después de que se haya copiado en el servidor.

Apertura del puerto 80 y prueba de Nginx
----------------------------------------

Nginx proporciona un sitio web predeterminado que podemos usar para asegurarnos de que nuestra máquina virtual acepta el tráfico web. Realice los siguientes pasos para habilitar el tráfico en el puerto 80 y pruebe el sitio web predeterminado de Nginx.

1.  Desde la sesión de SSH con la máquina virtual, inicie el servicio de Nginx:

        sudo service nginx start

    Se iniciará el servicio de Nginx, que escuchará el tráfico entrante en el puerto 80.

2.  En su explorador, diríjase hasta el [Portal de administración de Azure](https://manage.windowsazure.com/) y seleccione su máquina virtual.

    ![Lista de máquinas virtuales](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png)

3.  Seleccione **ENDPOINTS** en la parte superior de la página y, a continuación, haga clic en **+ ADD ENDPOINT** en la parte inferior de la página.

    ![Página de extremos](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png)

4.  En el cuadro de diálogo **ADD ENDPOINT**, haga clic en la fecha de la parte inferior izquierda para continuar a la segunda página y escriba la siguiente información en el formulario:

    -   **NAME**: HTTP

    -   **PROTOCOL**: TCP

    -   **PUBLIC PORT**: 80

    -   **PRIVATE PORT**: 80

    De esta manera se crea un puerto público 80 que dirigirá el tráfico hasta el puerto privado 80, donde Nginx está escuchando.

    ![Cuadro de diálogo de extremo nuevo](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png)

5.  Haga clic en la marca de verificación para guardar el extremo.

6.  Debe aparecer un mensaje que muestre **UPDATE IN PROGRESS**. Después de que el mensaje desaparece, el extremo está activo. Ahora puede probar su aplicación dirigiéndose al nombre de DNS de la máquina virtual. El sitio web debe ser similar al siguiente:

    ![Página de bienvenida de Nginx](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png)

7.  Detenga y quite el sitio web predeterminado de Nginx con los siguientes comandos:

        sudo service nginx stop
        sudo rm /etc/nginx/sites-enabled/default

    Los scripts de implementación que se ejecutan más adelante en este tutorial convertirán blog\_app en el sitio web predeterminado servido por Nginx.

Preparación de la implementación
--------------------------------

En esta sección, modificará la aplicación para usar el servidor web Unicorn, habilitar la implementación de Capistrano, habilitar el acceso de GitHub en la máquina virtual y crear los scripts que se usarán para implementar e iniciar la aplicación.

1.  Autorice la máquina virtual para autenticarse en su cuenta GitHub con un certificado realizando los pasos descritos en la página [Generating SSH Keys](https://help.github.com/articles/generating-ssh-keys#platform-all). Esto se utilizará para obtener acceso a su repositorio de GitHub desde los scripts de implementación.

    **Nota:**

    Aunque la clave SSH se debe generar en la máquina virtual, puede agregar la clave a su cuenta GitHub con el explorador en el entorno de desarrollo.

    Para ver el contenido del certificado SSH y poder copiarlo y pegarlo en GitHub, use el siguiente comando:

    ``` {}
    cat ~/.ssh/id_rsa.pub
    ```

2.  En su máquina de desarrollo, modifique **Gemfile** y quite la marca de comentario de las líneas de **Capistrano** y **Unicorn** quitando el carácter '\#' del principio de las siguientes líneas:

         # gem 'unicorn'
         # gem 'capistrano'

    **Nota:**

    Unicorn no está disponible en Windows. Si utiliza Windows como entorno de desarrollo, modifique **Gemfile** para asegurarse de que solo intentará instalar Unicorn cuando se implemente en la máquina virtual:

    ``` {.prettyprint}
    platforms :ruby do  gem 'unicorn'end
    ```

3.  Ejecute los siguientes comandos para instalar las nuevas gemas y configurar Capistrano para su proyecto:

        bundle
        capify .

4.  Agregue los siguientes archivos al directorio **blog\_app/config** y rellene cada archivo con el contenido que se encuentra en los vínculos siguientes:

    -   [nginx.conf](https://gist.github.com/cff582f4f970a95991e9): Configura Nginx para trabajar con Unicorn y servir los archivos estáticos incluidos con la aplicación de Rails
    -   [unicorn\_init.sh](https://gist.github.com/3272994): Script de shell que se usa para iniciar el proceso del servidor Unicorn
    -   [unicorn.rb](https://gist.github.com/3273014): Archivo de configuración de Unicorn

    En cada archivo, reemplace la palabra **blogger** por el nombre de usuario que se usa para iniciar sesión en la máquina virtual. Se trata del usuario que se utilizará para implementar la aplicación.

5.  En el directorio **blog\_app/config**, edite el archivo **deploy.rb** y reemplace el contenido existente por lo siguiente:

        require "bundler/capistrano"

        set :application, "blog_app"
        set :user, "blogger"

        set :scm, :git
        set :repository, "git@github.com:YourGitHubAccount/blog_app.git"
        set :branch, "master"
        set :use_sudo, true

        server "VMDNSName", :web, :app, :db, primary: true

        set :deploy_to, "/home/#{user}/apps/#{application}"
        default_run_options[:pty] = true
        ssh_options[:forward_agent] = true
        ssh_options[:port] = SSHPort

        namespace :deploy do
          desc "Fix permissions"
          task :fix_permissions, :roles => [ :app, :db, :web ] do
            run "chmod +x #{release_path}/config/unicorn_init.sh"
          end

          %w[start stop restart].each do |command|
            desc "#{command} unicorn server"
            task command, roles: :app, except: {no_release: true} do
              run "service unicorn_#{application} #{command}"
            end
          end

          task :setup_config, roles: :app do
            sudo "ln -nfs #{current_path}/config/nginx.conf /etc/nginx/sites-enabled/#{application}"
            sudo "ln -nfs #{current_path}/config/unicorn_init.sh /etc/init.d/unicorn_#{application}"
            sudo "mkdir -p #{shared_path}/config"
          end
          after "deploy:setup", "deploy:setup_config"

          task :symlink_config, roles: :app do
            # Agregar aquí configuración de la base de datos
          end
          after "deploy:finalize_update", "deploy:fix_permissions"
          after "deploy:finalize_update", "deploy:symlink_config"
        end

    En el texto anterior, reemplace lo siguiente:

    -   **YourGitHubAccount** por el nombre de su cuenta GitHub
    -   **VMDNSName** por el DNS de la máquina virtual de Azure
    -   **blogger** por el nombre de usuario que se usa para iniciar sesión en la máquina virtual
    -   **SSHPort** por el puerto externo de SSH para la máquina virtual

    **Nota:**

    Si el entorno de desarrollo es un sistema de Windows, debe agregar la siguiente línea al archivo **deploy.rb**. Debe colocarse junto con las demás instrucciones **set** al comienzo del archivo:

    ``` {}
    set :bundle_flags, "--no-deployment --quiet"
    ```

    No es un procedimiento recomendado, ya que provoca que durante la implementación las gemas se carguen de Gemfile y no de Gemfile.lock, pero es necesario porque el sistema de destino (Linux) es distinto del sistema de desarrollo (Windows).

6.  Quite la marca de comentario de la línea de recursos de **Capfile** que se encuentra en el directorio **blog\_app**.

        load 'deploy/assets'

7.  Ejecute los siguientes comandos para confirmar los cambios en el proyecto y cargarlos en GitHub.

        git add .
        git commit -m "adding config files"
        git push

Implementación
--------------

1.  En la máquina virtual local, use el siguiente comando para configurar la máquina virtual remota de Azure para implementación.

        cap deploy:setup

    Cuando se le solicite, escriba la contraseña de la cuenta de usuario de la máquina virtual. Capistrano se conectará a la máquina virtual y creará un directorio **apps** en el directorio de inicio de la cuenta de usuario.

2.  Desde una conexión SSH con la máquina virtual de Azure, modifique los permisos del directorio **app** mediante el siguiente comando:

        sudo chmod -R 777 apps

    **Nota:**

    No es recomendable aplicarlo en entornos de producción, y ahora solo se usa con fines demostrativos.

3.  Realice una implementación en frío con el siguiente comando en el entorno de desarrollo. Con ello, se implementará la aplicación en la máquina virtual y se iniciará el servicio de Unicorn.

        cap deploy:cold

4.  Inicie el servicio de Nginx, que debe comenzar a dirigir el tráfico a Unicorn y proporcionar servicio de contenido estático:

        sudo service nginx start

En este punto, la aplicación de Ruby on Rails se debe estar ejecutando en la máquina virtual de Azure. Para comprobarlo, escriba el nombre DNS de su máquina virtual en el explorador web. Por ejemplo, http://railsvm.cloudapp.net. Debería aparecer la pantalla "Welcome aboard":

![Página de bienvenida a bordo](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png)

Si anexa "/posts" a la URL, debe aparecer el índice de publicaciones y debería poder crear, editar y eliminar publicaciones.

Pasos siguientes
----------------

En este artículo aprendió a crear y publicar una aplicación básica de Rails basada en formularios en una máquina virtual de Azure con Capistrano. La máquina virtual utilizó Unicorn y Nginx para controlar solicitudes web para la aplicación.

Para ver un ejemplo más básico de creación e implementación de una aplicación de Rails en una máquina virtual de Azure con solo SSH, consulte [Hospedaje de una aplicación web de Ruby o Rails mediante una máquina virtual Linux](/es-es/develop/ruby/tutorials/web-app-with-linux-vm/).

Si desea obtener más información sobre Ruby on Rails, visite las [Guías de Ruby on Rails](http://guides.rubyonrails.org/) (en inglés).

Para obtener información sobre cómo usar el SDK de Azure para Ruby y así tener acceso a los servicios de Azure desde su aplicación de Ruby, consulte:

-   [Almacenamiento de datos no estructurados con blobs](/es-es/develop/ruby/how-to-guides/blob-storage/)

-   [Almacenamiento de pares clave/valor con tablas](/es-es/develop/ruby/how-to-guides/table-service/)

-   [Servicio de contenido con alto ancho de banda con la Red de entrega de contenido](/es-es/develop/ruby/app-services/)


