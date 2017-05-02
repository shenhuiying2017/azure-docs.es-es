
1. Visite [Azure Portal].
2. Haga clic en **Examinar todo** > **Mobile Apps** > el back-end que acaba de crear.
3. En la configuración de la aplicación de móvil, haga clic en **Inicio rápido** > **Cordova**.
4. En **Configurar la aplicación cliente**, seleccione **Crear aplicación nueva** y, a continuación, haga clic en **Descargar**.
2. Desempaquete el archivo ZIP descargado en un directorio del disco duro, vaya al archivo de la solución (.sln) y ábralo con Visual Studio.
3. En Visual Studio, elija la plataforma de la solución (Android, iOS o Windows) en la lista desplegable situada junto a la flecha de inicio. Para seleccionar un dispositivo de implementación o un emulador específicos, haga clic en la lista desplegable de la flecha verde. Puede usar la plataforma Android y el emulador Rippler predeterminados. Para tutoriales más avanzados (por ejemplo, las notificaciones de inserción), deberá seleccionar un dispositivo o emulador compatibles.
4. Presione F5 o haga clic en la flecha verde para compilar y ejecutar su aplicación Cordova. Si ve en el emulador un cuadro de diálogo de seguridad que solicita el acceso a la red, acéptelo.
5. Una vez que la aplicación se inicia en el dispositivo o emulador, escriba texto significativo en **Enter new text** (Escriba el nuevo texto), como *Completar el tutorial* y haga clic en el botón **Add** (Agregar).

El back-end inserta datos de la solicitud en la tabla TodoItem de SQL Database y devuelve información acerca de los elementos recién almacenados a la aplicación móvil. La aplicación móvil muestra estos datos en la lista.

![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

Puede repetir los pasos del 3 al 5 para otras plataformas.

[Azure Portal]: https://portal.azure.com/
