<properties linkid="develop-python-web-app-with-django-and-mysql" urlDisplayName="Web with Django + MySQL" pageTitle="Python web app with Django and MySQL - Azure tutorial" metaKeywords="Azure django web app, Azure Django MySQL, Azure django Python" description="A tutorial that teaches you how to use MySQL in with Django on an Azure virtual machine. Code samples written in Python." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World - MySQL Windows Edition" authors="" solutions="" manager="" editor="" />

Django Hello World - MySQL Windows Edition
==========================================

En este tutorial se describe como usar MySQL conjuntamente con Django en una sola máquina virtual de Azure. En esta guía se asume que tiene cierta experiencia previa en el uso de Azure y Django. Para obtener una introducción a Azure y Django, consulte [Django Hello World](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server). La guía también asume que tiene cierto conocimiento de MySQL. Para obtener información general sobre MySQL, consulte el [sitio web de MySQL](http://dev.mysql.com/doc/) (en inglés).

En este tutorial, aprenderá a:

-   Configurar una máquina virtual de Azure para hospedar MySQL y Django. Aunque este tutorial explica cómo llevar a cabo esta acción en Windows Server 2008 R2, también se puede hacer lo mismo con una máquina virtual de Linux hospedada en Azure.
-   Instalar un [controlador de MySQL](http://pypi.python.org/pypi/MySQL-python/1.2.3) (en inglés) para Python.
-   Configurar una aplicación Django existente para usar una base de datos MySQL.
-   Usar MySQL directamente desde Python.
-   Hospedar y ejecutar su aplicación MySQL Django.

Podrá expandirse con la muestra de [Django Hello World](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server) al usar una base de datos MySQL, hospedada en una máquina virtual de Azure, para buscar un reemplazo interesante de *World*. El reemplazo, a su vez, se determinará mediante una aplicación de *contador* Django con el respaldo de MySQL. Como fue el caso de la muestra de Hello World, esta aplicación de Django nuevamente se hospedará en una máquina virtual de Azure.

Los archivos del proyecto para este tutorial se almacenarán en un directorio llamado **C:\\django\\helloworld** y la aplicación completada tendrá una apariencia similar a la siguiente:

![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

Configuración de una máquina virtual de Azure para hospedar MySQL y Django
--------------------------------------------------------------------------

1.  Siga las instrucciones que se proporcionan [aquí](/en-us/manage/windows/tutorials/virtual-machine-from-gallery/) para crear una máquina virtual de Azure de la distribución *Windows Server 2008 R2*.

2.  Abra un puerto TCP para las transacciones de MySQL en la máquina virtual:
    -   Diríjase hasta la máquina virtual recién creada en el Portal de Azure y haga clic en la pestańa *ENDPOINTS*.
    -   Haga clic en *ADD ENDPOINT* en la parte inferior de la pantalla.
    -   Agregue un extremo con NAME = **mysql**, PROTOCOL = **TCP**, PUBLIC PORT = **3306**, PRIVATE PORT = **3306**.

3.  Agregue otro extremo con NAME = **web**, PROTOCOL = **TCP**, PUBLIC PORT = **80**, PRIVATE PORT = **80**. Esto redirige las solicitudes externas de Internet hacia el puerto en el que se ejecuta Django, es decir *80*.

4.  Use el *Escritorio remoto* para iniciar sesión en la máquina virtual de Azure recién creada.

5.  Abra el puerto TCP **80** en la máquina virtual:
    -   En el menú **Inicio**, seleccione **Herramientas de administrador** y, a continuación, **Firewall de Windows con seguridad avanzada**.
    -   En el panel izquierdo, seleccione **Reglas de entrada**. En el panel **Acciones** de la derecha, seleccione **Nueva regla...**.
    -   En **Asistente para nueva regla de entrada**, seleccione **Puerto** y, a continuación, haga clic en **Siguiente**.
    -   Seleccione **TCP** y, a continuación, **Puertos locales específicos**. Especifique un puerto de "80" (el puerto donde Django escucha) y haga clic en **Siguiente**.
    -   Seleccione **Permitir la conexión** y haga clic en **Siguiente**.
    -   Haga clic en **Siguiente** de nuevo.
    -   Especifique un nombre para la regla, como "DjangoPort", y haga clic en Finalizar.

6.  Instale la última versión de [MySQL Community Server](http://dev.mysql.com/downloads/mysql/) para Windows en la máquina virtual:

    **Nota**: recomendamos instalar su base de datos en una partición de datos diferente a la del SO.

    -   Ejecute el vínculo de *Windows (x86, 64 bits), Instalador MSI* [aquí](http://dev.mysql.com/downloads/mysql/) y descargue el instalador MSI adecuado desde el espejo de descarga más cercano. Algunos consejos útiles son:
        -   Seleccione un tipo de instalación *Complete*.
        -   Seleccione un valor para *Detailed Configuration* dentro del Asistente de configuración.
        -   **Asegúrese de habilitar la red TCP/IP en el puerto número 3306 y agregue una excepción de firewall para el puerto.**
        -   Establezca una contraseńa raíz y habilite el acceso raíz desde las máquinas remotas.
    -   Instale una muestra de [base de datos "world"](http://dev.mysql.com/doc/index-other.html) (en inglés, versión MyISAM):
        -   Descargue [este](http://downloads.mysql.com/docs/world.sql.zip) archivo zip en la máquina virtual de Azure.
        -   **Descomprímala en *C:\\Users\\Administrator\\Desktop\\world.sql*.**

1.  Después de instalar MySQL, haga clic en el menú *Inicio* de Windows y ejecute *MySQL 5.5 Command Line Client* recién instalado. Emita los siguientes comandos:

		CREATE DATABASE world;
		USE world;
		SOURCE C:\Users\Administrator\Desktop\world.sql
		CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
		CREATE DATABASE djangoazure;
		GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
		GRANT ALL ON world.* TO 'testazureuser'@'%';
		SELECT name from country LIMIT 1;

       Ahora debería ver una respuesta similar a la siguiente:
  
       ![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png)

      1.  Antes de que pueda empezar a desarrollar su aplicación Django, por supuesto necesita instalar Python+Django en la máquina virtual. Puede hacerlo usando el [instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx). Después de instalarlo, úselo para buscar "Django" e instalar el producto Django (Python).

      **Nota:** simplemente necesita instalar el producto *Django* de la WebPI para que este tutorial funcione. **No** necesita tener *Python Tools para Visual Studio* ni el SDK de Azure Python instalado para nuestros objetivos.

      1.  Instale el paquete de cliente MySQL Python. Puede instalarlo directamente [desde este vínculo](http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=) (en inglés). Después de completado, ejecute el siguiente comando para verificar la instalación:

     ![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png)

Extensión de la aplicación Django Hello World
---------------------------------------------

1.  Siga las instrucciones que se proporcionan en el tutorial de [Django Hello World](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server) para crear una aplicación web trivial de "Hello World" en Django.

2.  Abra **C:\\django\\helloworld\\helloworld\\settings.py** en su editor de texto de su preferencia. Modifique el diccionario global de **DATABASES** para que incluya lo siguiente:

         DATABASES = {
             'default': {
                 'ENGINE': 'django.db.backends.mysql',
                 'NAME': 'djangoazure',               
                 'USER': 'testazureuser',  
                 'PASSWORD': 'testazure',
                 'HOST': '127.0.0.1', 
                 'PORT': '3306',
                 },
             'world': {
                 'ENGINE': 'django.db.backends.mysql',
                 'NAME': 'world',               
                 'USER': 'testazureuser',  
                 'PASSWORD': 'testazure',
                 'HOST': '127.0.0.1', 
                 'PORT': '3306',
                 }
             }

    Como puede observar, acabamos de proporcionar instrucciones de Django para encontrar nuestra base de datos MySQL.

    **Nota:debe** cambiar la clave *HOST* para que coincida la dirección IP **permanente** de sus máquinas virtuales de Azure. En este punto, *HOST* debe establecerse en lo que indique el comando de Windows *ipconfig*.

    Después de modificar *HOST* para que coincida con la dirección IP de las máquinas virtuales de MySQL, guarde este archivo y ciérrelo.

1.  Ahora que hemos hecho una referencia a nuestra base de datos *djangoazure*, hagamos algo útil con ella. En este punto, crearemos un modelo para una aplicación de *contador* trivial. Para indicarle a Django la creación de esto, ejecute los siguientes comandos:

         cd C:\django\helloworld
         C:\Python27\python.exe manage.py startapp counter

    Si Django no informa ningún resultado proveniente del comando final anterior, el proceso se ha completado correctamente.

1.  Anexe el siguiente texto a **C:\\django\\helloworld\\counter\\models.py**:

         class Counter(models.Model):
             count = models.IntegerField()
             def __unicode__(self):
                 return u'%s' % (self.count)

    Todo lo que hemos hecho aquí se define como una subclase de la clase de *Model* de Django llamada *Counter* con un solo campo de enteros, *count*. Este modelo de contador trivial terminará registrando la cantidad de visitas a nuestra aplicación Django.

1.  Posteriormente, dejamos que Django se entere de la existencia de *Counter*:

    -   Vuelva a editar **C:\\django\\helloworld\\helloworld\\settings.py**. Agregue *'counter'* a la tupla *INSTALLED\_APPS*.
    -   Desde un símbolo de sistema, ejecute:

              cd C:\django\helloworld
              C:\Python27\python manage.py sql counter
              C:\Python27\python manage.py syncdb

         Estos comandos almacenan el modelo *Counter* en la base de datos directa de Django y tienen como resultado una salida similar a la siguiente:

		      C:\django\helloworld> C:\Python27\python manage.py sql counter
		      BEGIN;
		      CREATE TABLE `counter_counter` (
    		  `id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY,
		      `count` integer NOT NULL
		      )
		      ;
		      COMMIT;
		
		      C:\django\helloworld> C:\Python27\python manage.py syncdb
		      Creating tables ...
		      Creating table auth_permission
		      Creating table auth_group_permissions
		      Creating table auth_group
		      Creating table auth_user_user_permissions
		      Creating table auth_user_groups
		      Creating table auth_user
		      Creating table django_content_type
		      Creating table django_session
		      Creating table django_site
		      Creating table counter_counter
		
		      You just installed Django's auth system, which means you don't have any superusers defined.
		      Would you like to create one now? (yes/no): no
		      Installing custom SQL ...
		      Installing indexes ...
		      Installed 0 object(s) from 0 fixture(s)

6 .  Reemplace el contenido de **C:\\django\\helloworld\\helloworld\\views.py**. La implementación nueva de la función *hello* a continuación usa nuestro modelo *Counter* conjuntamente con una base de datos de muestra aparte que instalamos con anterioridad, *world*, para generar un reemplazo adecuado para la cadena "*World*":

         from django.http import HttpResponse
         import django.db
         from counter.models import Counter
            
         def getCountry(intId):
             #Conectar con la base de datos de ejemplo de MySQL 'world'
             cur = django.db.connections['world'].cursor()
             #Ejecute una consulta de SQL trivial que devuelve el nombre de 
             #todos los países contenidos en 'world'
             cur.execute("SELECT name from country")
             tmp = cur.fetchall()
             #Limpiar después de terminar
             cur.close()
             if intId >= len(tmp):
                 return "countries exhausted"
             return tmp[intId][0]
            
         def hello(request):
             if len(Counter.objects.all())==0:
                 #si la base de datos correspondiente a 'helloworld.counter' está 
                 #inicialmente vacía...
                 c = Counter(count=0)
             else:
                 c = Counter.objects.all()[0]
                 c.count += 1
             c.save()      
             world = getCountry(int(c.count))
             return HttpResponse("

        Hello " + world + "")

Implementación y ejecución de su sitio web Django
-------------------------------------------------

Nota: a continuación se muestra cómo ejecutar Django en un entorno de prueba. Para ejecutarlo en producción, siga la sección "Configuración de IIS con FastCGI" del "tutorial Django Hello World". No es necesario usar el cliente de Firewall de Windows para abrir el puerto 80 al tráfico de Internet en la máquina virtual de Windows Server 2K8 R2 con FastCGI.

1.  Regrese a una ventana de Windows PowerShell y escriba los siguientes comandos para implementar de manera pública su sitio web Django:

        PS C:\django\helloworld> $ipPort = 
        PS C:\django\helloworld> $ipPort = [string]$ipPort.AddressList[1]
        PS C:\django\helloworld> $ipPort += ":80"
        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort

    El parámetro **runserver** le indica a Django que ejecute nuestro sitio web *helloworld* en el puerto TCP *80*. Los resultados de este comando deben ser similares a lo siguiente:

        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort
        Validating models...
            
        0 errors found
        Django version 1.4, using settings 'helloworld.settings'
        Development server is running at http://123.34.56.78:80
        Quit the server with CTRL-BREAK.

2.  Desde su explorador web local, abra **http://*yourVmName*.cloudapp.net** (donde *yourVmName* es el nombre que haya utilizado en el paso de creación de la máquina virtual). Debe ver "Hello ... !" como se muestra en la captura de pantalla siguiente. De este modo se indica que Django se está ejecutando en la máquina virtual y que funciona correctamente.

    ![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png)

   Actualice el explorador web unas cuantas veces para poder ver el cambio del mensaje de *"Hello ****"* a *"Hello ****"*.

1.  Para que Django deje de hospedar el sitio web, simplemente cambie a la ventana de PowerShell y presione **CTRL-C**.

Apagado de la máquina virtual de Azure
--------------------------------------

Cuando finalice este tutorial, apague o quite la máquina virtual de Azure recién creada para liberar recursos para otros tutoriales y así evitar incurrir en cargos por uso de Azure.

