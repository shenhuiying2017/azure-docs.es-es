
El proyecto de servicio móvil descargado le permite ejecutar el nuevo servicio móvil en el equipo o máquina virtual local. De este modo, resulta sencillo depurar el código del servicio antes de publicarlo en Azure.

En esta sección, probará la nueva aplicación en el servicio móvil que se ejecuta de manera local.

1. Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de solución en Visual Studio.

2. Presione la tecla **F5** para recompilar el proyecto e iniciar el servicio móvil de manera local.

	![](./media/mobile-services-dotnet-backend-test-local-service-dotnet/mobile-service-startup.png)

	Una vez que se inicia el servicio móvil correctamente, aparece una página web.

3. En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de aplicación cliente, luego en **Establecer como proyecto de inicio** y, a continuación, presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

	De este modo, se inicia la aplicación, que se conecta a la instancia local de servicio móvil.	

4. En la aplicación, escriba un texto significativo, como _Realice el tutorial_, en **Insertar una tarea pendiente** y, a continuación, haga clic en **Guardar**.

	De este modo, se envía una solicitud POST al servicio móvil local. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y se muestran los datos en la segunda columna de la aplicación.


<!--HONumber=42-->
