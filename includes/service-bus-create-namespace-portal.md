1. Inicie sesión en el [Portal de Azure][].

2. En el panel de navegación izquierdo del portal, haga clic en **Nuevo**, a continuación, haga clic en **Enterprise integration** y, a continuación, haga clic en **Bus de servicio**.

4. En el cuadro de diálogo **Crear un espacio de nombres**, especifique un nombre para el espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.

5. Después de asegurarse de que el espacio de nombres está disponible, elija el plan de tarifas (Básico, Estándar o Premium).

7. En el campo **Suscripción** elija la suscripción de Azure en la que se va a crear el espacio de nombres.

9. En el campo **Grupo de recursos**, elija un grupo de recursos existente en el que residirá el espacio de nombres o cree uno.

8. En **Ubicación**, elija el país o región donde se debe hospedar el espacio de nombres.

	![Crear espacio de nombres][create-namespace]

6. Haga clic en el botón **Crear**. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.
 
### Obtención de las credenciales de administración

1. En la lista de espacios de nombres, haga clic en el nombre del espacio de nombres recién creado.
 
3. En la hoja **Espacio de nombres del Bus de servicio**, haga clic en **Directivas de acceso compartido**.

4. En la hoja **Directivas de acceso compartido**, haga clic en **RootManageSharedAccessKey**.

	![información de conexión][connection-info]

5. En la hoja **Directiva: RootManageSharedAccessKey**, haga clic en el botón Copiar junto a **Cadena de conexión: clave principal**, para copiar la cadena de conexión en el portapapeles para su uso posterior.

	![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Portal de Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0824_2016-->