Siga estos pasos para instalar y ejecutar MongoDB en una máquina virtual con Windows Server.

> [AZURE.IMPORTANT] Las características de seguridad de MongoDB, como la vinculación de direcciones IP y la autenticación, no se encuentran habilitadas de forma predeterminada. Las características de seguridad deben habilitarse antes de implementar MongoDB en un entorno de producción.  Consulte [Seguridad y autenticación](http://www.mongodb.org/display/DOCS/Security+and+Authentication) para obtener más información.

1. Una vez que se haya conectado a la máquina virtual mediante el Escritorio remoto, abra Internet Explorer desde el menú **Inicio**.
2. Seleccione el botón **Herramientas** de la esquina superior derecha.  En **Opciones de Internet**, seleccione la pestaña **Seguridad** y, a continuación, seleccione el icono **Sitios de confianza**. Finalmente, haga clic en el botón **Sitios**. Agregue   *http://\*.mongodb.org*  a la lista de sitios de confianza.
3. Vaya a [Descargas - MongoDB] [MongoDownloads].
4. Busque la versión más reciente en la sección **Production Versión de producción (recomendado)** y haga clic en el vínculo ***2008+** en la columna de Windows de 64 bits.  Haga clic en **Guardar como** y guarde el archivo zip en el escritorio.
5. Haga clic con el botón derecho en el archivo zip y seleccione **Extraer todo...**  Especifique "C:\" y haga clic en **Extraer**.  Una vez que los archivos se hayan extraído, es posible que desee cambiar el nombre de la carpeta de instalación para que sea más sencillo.  "MongoDB", por ejemplo.
6. Cree datos de MongoDB y directorios de registro en el disco de datos (unidad **F:**, por ejemplo) que creó en los pasos anteriores. En **Inicio**, , seleccione **Command Símbolo del sistema** para abrir una ventana de símbolo del sistema.  Tipo:

		C:\> F:
		F:\> mkdir \MongoData
		F:\> mkdir \MongoLogs

7. Para ejecutar la base de datos, ejecute: 

		F:\> C:
		C:\> cd \MongoDB\bin
		C:\my_mongo_dir\bin> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

	Todos los mensajes de registro se dirigirán al archivo  *F:\MongoLogs\mongolog.log* como mongod.exe mientras el servidor mongod.exe se inicia y preasigna los archivos de diario. MongoDB puede tardar unos minutos en preasignar los archivos de diario e iniciar la escucha de las conexiones.

8. Para iniciar el shell administrativo de MongoDB, abra otra ventana de comandos desde **Inicio** y escriba lo siguiente:

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
9. (Opcional) mongod.exe permite la instalación y ejecución como servicio de Windows. Para instalar mongod.exe como servicio, ejecute lo siguiente en el símbolo del sistema:

		C:\mongodb\bin>mongod --logpath "c:\mongodb\logs\logfile.log" --logappend --dbpath "c:\data" --install 

	De esta forma, se crea un servicio llamado "Mongo DB" con una descripción de "Mongo DB". La opción **--logpath** debe usarse para especificar un archivo de registro, puesto que el servicio en ejecución no dispondrá de una ventana de comandos para mostrar el resultado.  La opción **--logpath** especifica que el reinicio del servicio provocará que el resultado se anexe al archivo de registro existente.  La opción **--dbpath** especifica la ubicación del directorio de datos. Para obtener información sobre más opciones de la línea de comandos relacionada con los servicios, consulte [Opciones de línea de comandos relacionada con el servicio] [MongoWindowsSvcOptions].
10. Ahora que MongoDB está instalado y ejecutándose, debe abrir un puerto en el Firewall de Windows para conectarse de forma remota a MongoDB.  En el menú **Inicio**, seleccione **Herramientas del administrador** y, a continuación, **Firewall de Windows con seguridad avanzada**. 

11. En el panel izquierdo, seleccione **Reglas de entrada**.  En el panel **Acciones** de la derecha, seleccione **Nueva regla...**.
	
	![Windows Firewall][Image1]

	En el **Asistente para nueva regla de entrada**, seleccione **Puerto** y, a continuación, haga clic en **Siguiente**.
	
	![Windows Firewall][Image2]

	Seleccione **TCP** y **Puertos locales específicos**.  Especifique un puerto de "27017" (con el puerto de MongoDB con la escucha activada) y haga clic en **Siguiente**.

	![Windows Firewall][Image3]

	Seleccione **Permitir la conexión** y haga clic en **Siguiente**.

	![Windows Firewall][Image4]

	Vuelva a hacer clic en **Siguiente**.
	
	![Windows Firewall][Image5]

	Especifique un nombre para la regla, como "MongoPort", y haga clic en **Finalizar**.

	![Windows Firewall][Image6]
	
12. Si no configuró un extremo para MongoDB cuando creó la máquina virtual, puede hacerlo ahora. Necesita tanto la regla de firewall como el extremo para poder conectarse a MongoDB de manera remota. En el Portal de administración, haga clic en **Máquinas virtuales**, en el nombre de la nueva máquina virtual y, a continuación, en **Extremos**.

	![Endpoints][Image7]
13. Haga clic en **Agregar extremo** en la parte inferior de la página. Seleccione **Agregar extremo**  y haga clic en **Siguiente**.
	
	![Endpoints][Image8]

14. Agregue un extremo con el nombre "Mongo", el protocolo **TCP** y los puertos **Público** y **Privado** establecidos en "27017". Esto permitirá que se obtenga acceso remoto a MongoDB.

	![Endpoints][Image9]

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png

<!--HONumber=42-->
