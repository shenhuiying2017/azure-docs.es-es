Siga estos pasos para instalar y ejecutar MongoDB en una máquina virtual con Windows Server.

> [!IMPORTANT]
> Las características de seguridad de MongoDB, como la vinculación de direcciones IP y la autenticación, no se encuentran habilitadas de forma predeterminada. Las características de seguridad deben habilitarse antes de implementar MongoDB en un entorno de producción.  Para más información, consulte [Seguridad y autenticación](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
>
>

1. Después de conectarse a la máquina virtual con Escritorio remoto, abra Internet Explorer en el menú **Inicio** de la máquina virtual.
2. Seleccione el botón **Herramientas** de la esquina superior derecha.  En **Opciones de Internet**, seleccione la pestaña **Seguridad**, luego el icono **Sitios de confianza** y, por último, haga clic en el botón **Sitios**. Agregue *https://\*.mongodb.org* a la lista de sitios de confianza.
3. Vaya a [MongoDB Download Center](https://www.mongodb.com/download-center#community) (Centro de descarga de MongoDB).
4. Busque la **versión estable actual** de **Community Server** seleccione la versión más reciente de **64 bits** en la columna Windows. Descargue y ejecute el instalador de MSI.
5. MongoDB se suele instalar en C:\Archivos de programa\MongoDB. Busque variables de entorno en el escritorio y agregue la ruta de acceso de los archivos binarios de MongoDB a la variable PATH. Por ejemplo, podría encontrar los archivos binarios en C:\Program Files\MongoDB\Server\3.4\bin en la máquina.
6. Cree directorios de datos y de registro de MongoDB en el disco de datos (por ejemplo, la unidad **F:**) que creó en los pasos anteriores. En **Inicio**, seleccione **Símbolo del sistema** para abrir una ventana del símbolo del sistema.  Escriba:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Para ejecutar la base de datos, ejecute:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Todos los mensajes de registro se dirigen al archivo *F:\MongoLogs\mongolog.log* mientras el servidor mongod.exe se inicia y preasigna los archivos de diario. MongoDB puede tardar unos minutos en preasignar los archivos de diario e iniciar la escucha de las conexiones. El símbolo del sistema permanece centrado en esta tarea mientras se ejecuta la instancia de MongoDB.
8. Para iniciar el shell administrativo de MongoDB, abra otra ventana de comandos desde **Inicio** y escriba los siguientes comandos:

        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  

    La base de datos se crea mediante la inserción.
9. Como alternativa, puede instalar mongod.exe como servicio:

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    Se instala un servicio denominado MongoDB con la descripción "Mongo DB". La opción `--logpath` debe usarse para especificar un archivo de registro, ya que el servicio en ejecución no dispone de una ventana de comandos que muestre la salida.  La opción `--logappend` especifica que un reinicio del servicio provoca que la salida se anexe al archivo de registro existente.  La opción `--dbpath` especifica la ubicación del directorio de datos. Para ver más opciones de línea de comandos relacionadas con los servicios, consulte [Opciones de línea de comandos relacionadas con los servicios][MongoWindowsSvcOptions].

    Para iniciar el servicio, ejecute este comando:

        C:\> net start MongoDB
10. Ahora que MongoDB está instalado y ejecutándose, es preciso abrir un puerto en el Firewall de Windows para que sea posible conectarse de forma remota a MongoDB.  En el menú **Inicio**, seleccione **Herramientas administrativas** y luego **Firewall de Windows con seguridad avanzada**.
11. a) En el panel izquierdo, seleccione **Reglas de entrada**.  En el panel **Acciones** de la derecha, seleccione **Nueva regla...**.

    ![Firewall de Windows][Image1]

    b) En el **Asistente para nueva regla de entrada**, seleccione **Puerto** y haga clic en **Siguiente**.

    ![Firewall de Windows][Image2]

    c) Seleccione **TCP** y luego **Puertos locales específicos**.  Especifique el puerto "27017" (el puerto predeterminado en el que escucha MongoDB) y haga clic en **Siguiente**.

    ![Firewall de Windows][Image3]

    Seleccione **Permitir la conexión** y haga clic en **Siguiente**.

    ![Firewall de Windows][Image4]

    e) Haga clic en **Siguiente** de nuevo.

    ![Firewall de Windows][Image5]

    f) Especifique un nombre para la regla, como "MongoPort", y haga clic en **Finalizar**.

    ![Firewall de Windows][Image6]

12. Si no configuró un extremo para MongoDB cuando creó la máquina virtual, puede hacerlo ahora. Necesita tanto la regla de firewall como el extremo para poder conectarse a MongoDB de manera remota.

  En Azure Portal, haga clic en **Virtual Machines (clásico)**, haga clic en el nombre de la nueva máquina virtual y luego haga clic en **Puntos de conexión**.

    ![Puntos de conexión][Image7]

13. Haga clic en **Agregar**.

14. Agregue un punto de conexión con el nombre "Mongo", el protocolo **TCP** y los puertos **Público** y **Privado** establecidos en "27017". Al abrir este puerto se permite el acceso remoto a MongoDB.

    ![Puntos de conexión][Image9]

> [!NOTE]
> El puerto 27017 es el puerto predeterminado usado por MongoDB. Para cambiar este puerto predeterminado, especifique el parámetro `--port` al iniciar el servidor mongod.exe. Asegúrese de usar el mismo número de puerto en el firewall y en el punto de conexión de "Mongo" en las instrucciones anteriores.
>
>

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/menusendpointadd.png
<!-- Removed 03/08/2017. Not in new portal. -->
<!-- [Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
-->
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/newendpointdetails.png
