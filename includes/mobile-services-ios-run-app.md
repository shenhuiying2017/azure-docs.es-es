


La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de proyecto utilizando Xcode.

   	![](./media/mobile-services-ios-run-app/mobile-xcode-project.png)

2. Presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en el emulador de iPhone, que es la solución predeterminada para este proyecto.

3. En la aplicación, escriba un texto significativo, como _Realice el tutorial_ y, a continuación, haga clic en el icono (**+**).

   	![](./media/mobile-services-ios-run-app/mobile-quickstart-startup-ios.png)

   	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	>[WACOM.NOTE]Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo TodoService.m.</p> 
 	</div>
