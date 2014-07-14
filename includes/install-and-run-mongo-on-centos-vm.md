Siga estos pasos para instalar y ejecutar MongoDB en una máquina virtual con CentOS Linux.
<div class="dev-callout">
<b>Advertencia</b>
<p>Las características de seguridad de MongoDB, como la vinculación de direcciones IP y la autenticación, no se encuentran habilitadas de forma predeterminada. Las características de seguridad deben habilitarse antes de implementar MongoDB en un entorno de producción.  Consulte <a href="http://www.mongodb.org/display/DOCS/Security+and+Authentication">Security and Authentication</a> para obtener más información.</p></div>

1.  Configure el sistema de administración de paquetes (YUM) para poder instalar MongoDB. Cree un archivo */etc/yum.repos.d/10gen.repo* para almacenar información sobre el repositorio y agregue lo siguiente:
    	 
		 [10gen]
         name=10gen Repository
         baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
         gpgcheck=0
         enabled=1

2. Guarde el archivo de repositorio y, a continuación, ejecute el siguiente comando para actualizar la base de datos del paquete local:
    
         $ sudo yum update
3.  Para instalar el paquete, ejecute el siguiente comando para instalar la última versión estable de MongoDB y las herramientas asociadas:
    
         $ sudo yum install mongo-10gen mongo-10gen-server
    
    Espere hasta que MongoDB se descargue y se instale.

4.  Cree un directorio de datos. De forma predeterminada, MongoDB almacena datos en el directorio */data/db*, pero debe crear ese directorio. Para crearlo, ejecute:
    
         $ sudo mkdir -p /mnt/datadrive/data
         $ sudo chown `id -u` /mnt/datadrive/data
    
    Para obtener más información sobre la instalación de MongoDB en Linux, consulte [Quickstart Unix][1].

5.  Para iniciar la base de datos, ejecute:
    
         $ mongod --dbpath /mnt/datadrive/data --logpath /mnt/datadrive/data/mongod.log
    
    Todos los mensajes de registro se dirigirán al archivo *mnt/datadrive/data/mongod.log* mientras el servidor MongoDB se inicia y preasigna los archivos de diario. MongoDB puede tardar unos minutos en preasignar los archivos de diario e iniciar la escucha de las conexiones.

6.  Para iniciar el shell administrativo de MongoDB, abra una ventana PuTTY o SSH independiente y ejecute:
    
         $ mongo
         > db.foo.save ( { a:1 } )
         > db.foo.find()
         { _id : ..., a : 1 }
         > show dbs  
         ...
         > show collections  
         ...  
         > help  
    
    La base de datos se crea mediante la inserción.

7.  Cuando MongoDB esté instalado, debe configurar un extremo para que pueda obtenerse acceso a MongoDB de manera remota. En el Portal de administración, haga clic en **Máquinas virtuales**, en el nombre de la nueva máquina virtual y, a continuación, en **Endpoints**.
    
    ![Extremos](./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png)

8.  Haga clic en **Add Endpoint** en la parte inferior de la página.
    
    ![Extremos](./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png)

9.  Agregue un extremo con el nombre "Mongo", el protocolo **TCP** y los puertos **Public** y **Private** establecidos en "27017". Esto permitirá que se obtenga acceso remoto a MongoDB 
    
    ![Extremos](./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint3.png)



[1]: http://www.mongodb.org/display/DOCS/Quickstart+Unix