<properties 
	pageTitle="Implementación de una aplicación web de Ruby on Rails en una máquina virtual de Azure con Capistrano - Tutorial" 
	description="Aprenda a implementar una aplicación de Ruby on Rails en una máquina virtual de Azure con Capistrano, Unicorn y Nginx." 
	authors="wpickett" 
	manager="wpickett" 
	editor="" 
	services="virtual-machines" 
	documentationCenter=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="wpickett"/>


#Implementación de una aplicación web de Ruby on Rails en una máquina virtual de Azure con Capistrano

En este tutorial se describe cómo implementar un sitio web basado en Ruby on Rails en una máquina virtual de Azure con [Capistrano 3](https://github.com/capistrano/capistrano/). Una vez implementado, usará [Nginx](http://nginx.org/) y [Unicorn](https://github.com/blog/517-unicorn) para hospedar el sitio web. [PostgreSQL](https://www.postgresql.org) almacenará los datos de la aplicación implementada.

En este tutorial se supone que no tiene experiencia previa con Azure, pero que está familiarizado con Ruby, Rails, Git y Linux. Al término de este tutorial, tendrá una aplicación basada en Ruby on Rails funcionando en la nube.

Aprenderá a:

* Configurar entornos de desarrollo y producción

* Instalar Ruby y Ruby on Rails

* Instalar Unicorn, Nginx y PostgreSQL

* Crear una aplicación nueva de Rails

* Crear una implementación de Capistrano y usarla para implementar la aplicación

La siguiente es una captura de pantalla de la aplicación completa:

![a browser displaying Listing Posts][blog-rails-cloud]

> [AZURE.NOTE] La aplicación utilizada en este tutorial incluye componentes binarios nativos. Es posible que encuentre errores cuando la implemente en la máquina virtual si su entorno de desarrollo no está basado en Linux. El archivo Gemfile.lock utilizado durante la implementación contiene gemas específicas de la plataforma, que pueden no incluir entradas para las versiones nativas de Linux de las gemas que requiere la máquina virtual.
> 
> Algunos pasos deben realizarse en un entorno de desarrollo de Windows. No obstante, si encuentra errores durante la implementación o después de la misma que no se tratan en este artículo, puede probar de nuevo los pasos de este artículo en un entorno de desarrollo basado en Linux.

##En este artículo

* [Configuración de su entorno de desarrollo](#setup)

* [Creación de una aplicación de Rails](#create)

* [Prueba de la aplicación](#test)

* [Creación de un repositorio de origen](#repository)

* [Creación de una máquina virtual de Azure](#createvm)

* [Prueba de Nginx](#nginx)

* [Preparación de la implementación](#capify)

* [Implementación](#deploy)

* [Pasos siguientes](#next)

##<a id="setup"></a>Configuración de su entorno de desarrollo

1. Instale Ruby en su entorno de desarrollo. Los pasos pueden variar según el sistema operativo.

	* **Apple OS X**: Puede haber varias distribuciones de Ruby para OS X. Este tutorial se validó en OS X mediante [Homebrew](http://brew.sh/) para instalar **rbenv**, **ruby-build** y **Ruby 2.0.0-p451**. Puede encontrar información sobre la instalación en [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/).

	* **Linux**: Use su sistema de administración de paquetes de distribución. Este tutorial se validó en Ubuntu 12.10 usando **rbenv**, **ruby-build** y **Ruby 2.0.0-p451**.

	* **Windows**: Existen varias distribuciones de Ruby para Windows. Este tutorial se validó usando [RubyInstaller](http://RubyInstaller.org/) para instalar **Ruby 2.0.0-p451**. Los comandos se emitieron con la línea de comandos de **GitBash** disponible con [Git para Windows](http://git-scm.com/download/win).

2. Abra una nueva línea de comandos o sesión de terminal y escriba el siguiente comando para instalar Ruby on Rails:

		gem install rails --no-rdoc --no-ri

	> [AZURE.NOTE] Es posible que este comando requiera privilegios de administrador o raíz en algunos sistemas operativos. Si recibe un error mientras se ejecuta este comando, pruebe a usar 'sudo' como sigue.
	> 
	> `sudo gem install rails`

	> [AZURE.NOTE] Se usó la versión 4.0.4 de la gema Rails para este tutorial.

3. Debe instalar también un intérprete de JavaScript, que Rails usará para compilar los recursos de CoffeeScript que usó su aplicación de Rails. Se puede encontrar una lista de intérpretes compatibles en [https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme).
	
	> [AZURE.NOTE] Se usó [Node.js](http://nodejs.org/) para este tutorial porque estaba disponible para los sistemas operativos OS X, Linux y Windows.

##<a id="create"></a>Creación de una aplicación de Rails

1. Desde la línea de comandos o la sesión de terminal, cree una aplicación nueva de Rails llamada "blog_app" con la ayuda del siguiente comando:

		rails new blog_app

	Este comando crea un directorio nuevo llamado **blog_app** y lo rellena con los archivos y subdirectorios que necesita una aplicación de Rails.

	> [AZURE.NOTE] Este comando puede tardar un minuto o más en completarse. Realiza una instalación silenciosa de las gemas necesarias para una aplicación predeterminada y no responderá durante este tiempo.

2. Cambie los directorios por el directorio **blog_app** y, a continuación, use el siguiente comando para crear un scaffolding de blog básico:

		rails generate scaffold Post name:string title:string content:text

	De esta manera se crea el controlador, la vista, el modelo y las migraciones de la base de datos que se usaron para mantener las publicaciones en el blog. Cada publicación tendrá el nombre de un autor, un título para la publicación y el contenido del texto.

3. Para crear la base de datos que va a almacenar las publicaciones del blog, use el siguiente comando:

		rake db:migrate

	Este comando creará el esquema de la base de datos para almacenar publicaciones usando el proveedor de base de datos predeterminado para Rails, que es [SQLite3 Database][sqlite3].

4. Para mostrar un índice de publicaciones como página principal, modifique el archivo **config/routes.rb** y agregue lo siguiente después de la línea `resources :posts`.

		root 'posts#index'

	Este código mostrará una lista de publicaciones cuando los usuarios visiten el sitio web.

##<a id="test"></a>Prueba de la aplicación

1. Cambie los directorios al directorio **blog_app**, si no se encuentra ya en él, e inicie el servidor de Rails con el siguiente comando.

		rails s

	Debería ver una salida similar a la siguiente. Tenga en cuenta el puerto que escucha el servidor web. En el ejemplo a continuación, está escuchando el puerto 3000.

		=> Booting WEBrick
		=> Rails 4.0.4 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 2.0.0 (2014-02-24) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Abra el explorador y diríjase a http://localhost:3000/. Debe ver una página similar a la siguiente.

	![a page listing posts][blog-rails]

	Para detener el proceso del servidor, escriba CTRL+C en la línea de comandos.

##<a id="repository"></a>Creación de un repositorio de origen

Cuando implemente una aplicación usando Capistrano, los archivos se extraen de un repositorio. En este tutorial, usaremos [Git](http://git-scm.com/) para el control de versiones y [GitHub](https://github.com/) para el repositorio.

1.	Creación de un repositorio en [GitHub](https://github.com/). Si no tiene cuenta de GitHub, puede suscribirse para obtener una gratis. En los pasos siguientes se supone que el nombre del repositorio es **blog_app**.

	> [AZURE.NOTE] Para permitir implementaciones automatizadas de la aplicación, debe usar claves SSH para autenticarse en GitHub. Para obtener más información, consulte la documentación de GitHub en [Generación de claves SSH](https://help.github.com/articles/generating-ssh-keys).

2.	Desde el símbolo del sistema, cambie los directorios al directorio **blog_app** y ejecute los siguientes comandos para cargar la aplicación en el repositorio de GitHub. Reemplace **YourGitHubName** por el nombre de su cuenta de GitHub.

		git init
		git add .
		git commit -m "initial commit on azure"
		git remote add origin git@github.com:YourGitHubName/blog-azure.git
		git push -u origin master

En la siguiente sección, creará la máquina virtual en la que se implementará esta aplicación.

##<a id="createvm"></a>Creación de una máquina virtual de Azure

Siga las instrucciones que se proporcionan [aquí][vm-instructions] para crear una máquina virtual de Azure que hospeda Linux.

1. Inicie sesión en el [Portal de administración de Azure][management-portal]. En la barra de comandos, haga clic en **Nuevo**.

2. Seleccione **Máquina virtual** y elija **De la galería**.

3. En **Elija una imagen**, seleccione **Ubuntu** y, a continuación, seleccione la versión **12.04 LTS**. Seleccione la flecha para continuar.

	> [AZURE.NOTE] Los pasos de este tutorial se realizaron en una máquina virtual de Azure con Ubuntu 12.04 LTS. Si va a usar una distribución de Linux diferente, es posible que se requieran diferentes pasos para llevar a cabo las mismas tareas.

4. En **Nombre de la máquina virtual**, escriba el nombre que desea usar. Este nombre se usará para crear el nombre de dominio de esta máquina virtual.

5. En **Nuevo nombre de usuario**, escriba el nombre de la cuenta de administrador para esta máquina.

	> [AZURE.NOTE] Para este tutorial, se usará también la cuenta de administrador para implementar la aplicación. Para obtener información acerca de la creación de una cuenta diferente para la implementación, consulte la documentación de [Capistrano][capistrano].

6. En **Autenticación**, seleccione **Cargar clave de SSH compatible para autenticación**, después busque y seleccione el archivo **.pem** que contiene el certificado. Finalmente, seleccione la flecha para continuar.

	> [AZURE.NOTE] Si no está familiarizado con la generación o el uso de claves SSH, consulte [Uso de SSH con Linux en Azure][ssh-on-azure] para obtener instrucciones para la creación de claves SSH.
	> 
	> También puede habilitar la autenticación con contraseña. Sin embargo, debe proporcionarse también la clave SSH porque se usa para automatizar la implementación.

7. En **Extremos**, utilice la lista desplegable **Escriba/seleccione un valor** para seleccionar **HTTP**. En los demás campos de esta página se pueden dejar los valores predeterminados. Tome nota del **Nombre DNS de servicio en la nube**, porque usará este valor más adelante. Finalmente, seleccione la flecha para continuar.

8. En la página final, seleccione la marca de verificación para crear la máquina virtual.

###Instalación de Git, Ruby y Nginx

Una vez creada la máquina virtual, conéctese a ella usando SSH y utilice los comandos siguientes para preparar el entorno de hospedaje para la aplicación Ruby.

	sudo apt-get update
	sudo apt-get -y upgrade
	sudo apt-get -y install git build-essential libssl-dev curl nodejs nginx
	git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
	echo 'export RBENV_ROOT=~/.rbenv' >> ~/.bash_profile
	echo 'export PATH="$RBENV_ROOT/bin:$PATH"' >> ~/.bash_profile
	echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
	source ~/.bash_profile
	eval "$(rbenv init -)"
	git clone git://github.com/sstephenson/ruby-build.git
	cd ruby-build
	sudo ./install.sh
	~/.rbenv/bin/rbenv install 2.0.0-p451
	~/.rbenv/bin/rbenv global 2.0.0-p451
	gem install bundler
	~/.rbenv/bin/rbenv rehash

> [AZURE.NOTE] Si lo desea, puede guardar el código anterior en un script (archivo .sh) para evitar errores de escritura cuando ejecute los comandos.
> 
> El comando **~/.rbenv/bin/rbenv install 2.0.0-p451** puede tardar varios minutos en completarse.

El script **rbenv-install.sh** realiza las acciones siguientes:
	
* Actualiza los paquetes instalados
* Instala herramientas de compilación
* Instala Git
* Instala Node.js
* Instala Nginx
* Instala herramientas de compilación y otras utilidades necesarias para Ruby and Rails
* Configura una implementación (de usuario) local de [rbenv]
* Instala Ruby 2.0.0-p451
* Instala la gema [Bundler]

Una vez finalizada la instalación, use el siguiente comando para verificar que Ruby se instaló correctamente:

	ruby -v

Esto debería devolver `ruby 2.0.0p451` como la versión.

###Instalación de PostgreSQL

La base de datos predeterminada que usa Rails en la fase de desarrollo es SQLite. Normalmente, se utiliza otra en producción. Con los pasos siguientes se instala PostgreSQL en la máquina virtual y se crea un usuario y una base de datos. En pasos posteriores, configurará la aplicación de Rails para que use PostgreSQL durante la implementación.

1. Instale PostgreSQL y los elementos de desarrollo con el siguiente comando.

		sudo apt-get -y install postgresql postgresql-contrib libpq-dev

6. Utilice los siguientes comandos para crear un usuario y una base de datos para el inicio de sesión. Reemplace **my_username** y **my_database** por su nombre de inicio de sesión. Por ejemplo, si el inicio de sesión para su máquina virtual es **deploy**, reemplace **my_username** y **my_database** por **deploy**.

		sudo -u postgres createuser -D -A -P my_username
		sudo -u postgres createdb -O my_username my_database

	> [AZURE.NOTE] Use el nombre de usuario también para el nombre de la base de datos. Esto es necesario para la gema capistrano-postgresql que utiliza esta aplicación.

	Cuando el sistema lo pida, escriba una contraseña para el usuario. Cuando le pregunte si desea permitir que el usuario cree roles nuevos, seleccione **y**, ya que se utilizará este usuario durante la implementación para crear la base de datos y el inicio de sesión que se usará la aplicación de Rails.

7. Para comprobar que se conecta a la base de datos usando la cuenta de usuario, utilice el comando siguiente. Reemplace **my_username** y **my_database** por los valores que ha usado antes.

		psql -U my_username -W my_database

	Debe llegar a una petición  `database=>`. Para salir de la utilidad psql, escriba  `\q` en el símbolo del sistema.

###<a id="nginx"></a>Prueba de Nginx

El extremo HTTP agregado durante la creación de la máquina virtual le permitirá Aceptar solicitudes HTTP a través del puerto 80. Para comprobarlo, use los pasos siguientes para comprobar que puede tener acceso al sitio predeterminado creado por Nginx.

2.	Desde la sesión de SSH con la máquina virtual, inicie el servicio de Nginx:

		sudo service nginx start

	Se iniciará el servicio de Nginx, que escuchará el tráfico entrante en el puerto 80.

6. Navegue al nombre DNS de la máquina virtual para probar la aplicación. El sitio web que aparece debe ser similar al siguiente:

	![nginx welcome page][nginx-welcome]

	> [AZURE.NOTE] Los scripts de implementación que se usan más adelante en este tutorial convertirán a blog_app en el sitio web predeterminado que ofrece Nginx.

Llegado este punto, dispone de una máquina virtual de Azure con Ruby, Nginx y PostgreSQL que está lista para la implementación. En la siguiente sección, modificará la aplicación de Rails para agregar los scripts y la información que llevan a cabo la implementación.

##<a id="capify"></a>Preparación de la implementación

En su entorno de desarrollo, modifique la aplicación para que use el servidor web Unicorn, PostgreSQL y habilite Capistrano para la implementación, y cree los scripts usados para implementar e iniciar la aplicación.

1. En la máquina de desarrollo, modifique el archivo **Gemfile** y agregue las siguientes líneas para incorporar gemas para Unicorn, PostgreSQL, Capistrano y gemas relacionadas a la aplicación de Rails.

		# Use Unicorn
		gem 'unicorn'
		# Use PostgreSQL
		gem 'pg', group: :production

		group :development do
		  # Use Capistrano for deployment
		  gem 'capistrano', '~> 3.1'
		  gem 'capistrano-rails', '~> 1.1.1'
		  gem 'capistrano-bundler'
		  gem 'capistrano-rbenv', '~> 2.0'
		  gem 'capistrano-unicorn-nginx', '~> 2.0'
		  gem 'capistrano-postgresql', '~> 3.0'
		end

	> [AZURE.NOTE] Unicorn no está disponible en Windows. Si utiliza Windows como entorno de desarrollo, modifique el archivo __Gemfile__ para asegurarse de que solo intentará instalar Unicorn cuando se implemente en la máquina virtual. Para ello, use el código siguiente cuando especifique la gema para Unicorn.
	> 
	> `platforms :ruby do`
	> `  gem 'unicorn'`
	> `end`

	La mayoría de las gemas capistrano-* son programas auxiliares que funcionan con cosas específicas que usamos en el servidor de producción (rbenv,) o el marco (rails).

	La gema capistrano-unicorn-nginx crea automáticamente los scripts que se usan con Unicorn y Nginx durante la implementación, de forma que no tenemos que crearlos manualmente. La gema capistrano-postgresql genera automáticamente una base de datos, un usuario y una contraseña en PostgreSQL para la aplicación. Si bien no tiene que usarlas, ambas simplifican notablemente el proceso de implementación.
 
5.	Utilice el comando siguiente para instalar las gemas nuevas.
		
		bundle

6. Utilice el comando siguiente para crear archivos de implementación predeterminados que usa Capistrano.

		cap install

	Cuando el comando  `cap install` se complete, se habrán agregado los siguientes archivos y directorios a la aplicación.

		├── Capfile
		├── config
		│   ├── deploy
		│   │   ├── production.rb
		│   │   └── staging.rb
		│   └── deploy.rb
		└── lib
		    └── capistrano
		            └── tasks

	El archivo **deploy.rb** proporciona configuración y acciones genéricas para todos los tipos de implementación, mientras que **production.rb** y **staging.rb** contienen configuración adicional para implementaciones de producción y de ensayo.

	La carpeta **capistrano** contiene tareas y otros archivos que se usan como parte del proceso de implementación.

5. Edite el archivo **Capfile** en la raíz de la aplicación y quite las marcas de comentario (__#__) de las líneas siguientes:

		require 'capistrano/rbenv'
		require 'capistrano/bundler'
		require 'capistrano/rails/assets'
		require 'capistrano/rails/migrations'

	Cuando haya quitado las marcas de comentario de las líneas anteriores, agregue las líneas siguientes:

		require 'capistrano/unicorn_nginx'
		require 'capistrano/postgresql'

	Estas líneas indican a Capistrano que utilice las gemas que se habían agregado al archivo Gemfile.

	Cuando haya realizado estos cambios, guarde el archivo.

6.  Edite el archivo **config/deploy.rb** y reemplace su contenido por el siguiente. Cambie **YourApplicationName** por el nombre de su aplicación y reemplace **https://github.com/YourGitHubName/YourRepoName.git** por la dirección URL del repositorio de GitHub para este proyecto.

		lock '3.1.0'
		# application name and the github repository
		set :application, 'YourApplicationName'
		set :repo_url, 'git@github.com:YourGitHubName/YourRepoName.git'
		
		# describe the rbenv environment we are deploying into
		set :rbenv_type, :user
		set :rbenv_ruby, '2.0.0-p451'
		set :rbenv_prefix, "RBENV_ROOT=#{fetch(:rbenv_path)} RBENV_VERSION=#{fetch(:rbenv_ruby)} #{fetch(:rbenv_path)}/bin/rbenv exec"
		set :rbenv_map_bins, %w{rake gem bundle ruby rails}
		
		# dirs we want symlinked to the shared folder
		# during deployment
		set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}
		
		namespace :deploy do

		  desc 'Restart application'
		  task :restart do
		    on roles(:app), in: :sequence, wait: 5 do
		      # Your restart mechanism here, for example:
		      # execute :touch, release_path.join('tmp/restart.txt')
              #
			  # The capistrano-unicorn-nginx gem handles all this
			  # for this example
		    end
		  end
		
		  after :publishing, :restart
		
		  after :restart, :clear_cache do
		    on roles(:web), in: :groups, limit: 3, wait: 10 do
		      # Here we can do anything such as:
		      # within release_path do
		      #   execute :rake, 'cache:clear'
		      # end
		    end
		  end
		
		end

	Este archivo proporciona información y tareas genéricas para todos los tipos de implementación.

5.  Edite el archivo **config/deploy/production.rb** y reemplace su contenido por el siguiente. Cambie **YourVm.cloudapp.net** por el nombre de dominio de su máquina virtual. Reemplace **YourAzureVMUserName** por la cuenta de inicio de sesión que creó anteriormente para la máquina virtual de Azure.

		# production deployment
		set :stage, :production
		# use the master branch of the repository
		set :branch, "master"
		# the user login on the remote server
		# used to connect and deploy
		set :deploy_user, "YourAzureVMUserName"
		# the 'full name' of the application
		set :full_app_name, "#{fetch(:application)}_#{fetch(:stage)}"
		# the server(s) to deploy to
		server 'YourVM.cloudapp.net', user: 'YourAzureVMUserName', roles: %w{web app db}, primary: true
		# the path to deploy to
		set :deploy_to, "/home/#{fetch(:deploy_user)}/apps/#{fetch(:full_app_name)}"
        # set to production for Rails
		set :rails_env, :production

	Este archivo proporciona información específica para implementaciones de producción.

8.	Ejecute los comandos siguientes para confirmar los cambios en los archivos que ha modificado en pasos anteriores y cargue los cambios en GitHub.

		git add .
		git commit -m "adding config files"
		git push

La aplicación debe estar ya lista para implementarla.

> [AZURE.NOTE] Para una aplicación más compleja o para una base de datos o un servidor de aplicaciones diferentes, pueden ser necesarios más scripts de configuración o implementación.

##<a id="deploy"></a>Implementación

2.	En su máquina de desarrollo local, use el siguiente comando para implementar los archivos de configuración que usa la aplicación en la máquina virtual.

		bundle exec cap production setup

	Capistrano se conectará a la máquina virtual usando SSH y después creará el directorio (~/apps) en el que se implementará la aplicación. Si esta es la primera implementación, la gema capistrano-postgresql creará también un rol y una base de datos en PostgreSQL en el servidor. También creará un archivo de configuración database.yml que Rails usará para conectarse a la base de datos.

	> [AZURE.NOTE] Si obtiene un error de tipo **Error al leer la longitud de respuesta del socket de autenticación** durante la implementación, puede ser necesario iniciar el agente SSH en el entorno de desarrollo usando el comando `ssh-agent`.  Por ejemplo, agregue `eval $(ssh-agent)` al archivo ~/.bash\_profile.
	> 
	> También puede ser necesario agregar la clave SSH a la memoria caché del agente usando el comando  `ssh-add`.

4.	Lleve a cabo una implementación de producción con el siguiente comando. Esto implementará la aplicación en la máquina virtual, iniciará el servicio Unicorn y configurará Nginx para enrutar el tráfico a Unicorn.

		bundle exec cap production deploy

	Este comando implementará la aplicación en la máquina virtual, instalará las gemas necesarias y después iniciará o reiniciará Unicorn y Nginx.

	> [AZURE.NOTE] El proceso puede detenerse varios minutos durante el procesamiento.

	> [AZURE.NOTE] Algunas partes de la implementación pueden devolver el error 'estado de salida 1 (error).' Normalmente, se puede pasar por alto si la implementación se completa correctamente.

	> [AZURE.NOTE] En algunos sistemas, puede verse en una situación donde el agente SSH no puede reenviar credenciales a la máquina virtual remota para la autenticación en GitHub. Si esto ocurre, puede solucionar el error modificando el archivo **config/deploy.rb** para cambiar la línea  `set :repo_url` para que use HTTPS cuando se acceda a Github. Cuando se usa HTTPS, debe especificar su nombre de usuario y contraseña (o token autenticado) para GitHub como parte de la dirección URL. Por ejemplo:
	> 
	> `set :repo_url, 'https://you:yourpassword@github.com/You/yourrepository.git'
	> 
	> Si bien esto debería permitirle evitar el error y completar este tutorial, no es una solución recomendada para una implementación de producción, ya que almacena las credenciales de autenticación como texto sin codificar y como parte de la aplicación. Consulte la documentación del sistema operativo sobre el uso de reenvíos con el agente SSH

En este punto, la aplicación de Ruby on Rails se debe estar ejecutando en la máquina virtual de Azure. Para comprobarlo, escriba el nombre DNS de su máquina virtual en el explorador web. Por ejemplo, http://railsvm.cloudapp.net. Debe aparecer el índice de publicaciones y debe poder crear, editar y eliminar publicaciones.

##<a id="next"></a>Pasos siguientes

En este artículo ha aprendido a crear y publicar una aplicación básica de Rails en una máquina virtual de Azure con Capistrano. Trabajar con una aplicación básica como la de este artículo es solo una parte mínima de lo que se puede hacer con Capistrano para la implementación. Para obtener más información sobre el uso de Capistrano, consulte:

* [Capistranorb.com](http://capistranorb.com): sitio de Capistrano.
* [Azure, Ruby on Rails, Capistrano 3 y PostgreSQL](http://wootstudio.ca/articles/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql): un método alternativo para la implementación en Azure que implica scripts de implementación personalizados.
* [Tutorial de Capistrano 3](http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/): tutorial sobre cómo trabajar con Capistrano 3.

Para ver un ejemplo más básico de creación e implementación de una aplicación de Rails en una máquina virtual de Azure con solo SSH, consulte [Hospedaje de una aplicación web de Ruby o Rails mediante una máquina virtual Linux][ruby-vm].

Si desea obtener más información sobre Ruby on Rails, visite las [Guías de Ruby on Rails][rails-guides] (en inglés).

Para obtener información sobre cómo usar el SDK de Azure para Ruby y así tener acceso a los servicios de Azure desde su aplicación de Ruby, consulte:

* [Almacenamiento de datos no estructurados con blobs][blobs]

* [Almacenamiento de parejas de clave/valor con tablas][tables]

* [Servicio de contenido de alto ancho de banda con la Red de entrega de contenido][cdn-howto]

[vm-instructions]: /manage/linux/tutorials/virtual-machine-from-gallery/


[rails-guides]: http://guides.rubyonrails.org/
[blobs]: /develop/ruby/how-to-guides/blob-storage/
[tables]: /develop/ruby/how-to-guides/table-service/
[cdn-howto]: /develop/ruby/app-services/
[ruby-vm]: /develop/ruby/tutorials/web-app-with-linux-vm/
 
[blog-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
[blog-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png 
[default-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png
[default-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png
[vmlist]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png
[endpoints]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png
[new-endpoint]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png
[nginx-welcome]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png

[management-portal]: https://manage.windowsazure.com/
[sqlite3]: http://www.sqlite.org/
[ssh-on-azure]: http://azure.microsoft.com/documentation/articles/linux-use-ssh-key/
[capistrano]: http://capistranorb.com

<!--HONumber=47-->
