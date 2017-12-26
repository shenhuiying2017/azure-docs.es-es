Para empezar a usar entidades de mensajería de Service Bus en Azure, primero hay que crear un espacio de nombres con un nombre que sea único en Azure. Un espacio de nombres proporciona un contenedor con un ámbito para el desvío de recursos de Service Bus en la aplicación.

Para crear un espacio de nombres:

1. Inicie sesión en [Azure Portal][Azure portal].
2. En el panel de navegación izquierdo del portal, haga clic en **+ Crear un recurso**, luego, en **Enterprise Integration** y, finalmente, en **Service Bus**.
3. En el cuadro de diálogo **Crear un espacio de nombres** , especifique un nombre para el espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.
4. Después de asegurarse de que el espacio de nombres está disponible, elija el plan de tarifas (Básico, Estándar o Premium).
5. En el campo **Suscripción** elija la suscripción de Azure en la que se va a crear el espacio de nombres.
6. En el campo **Grupo de recursos** , elija un grupo de recursos existente en el que residirá el espacio de nombres o cree uno.      
7. En **Ubicación**, elija el país o región donde se debe hospedar el espacio de nombres.
   
    ![Crear un espacio de nombres][create-namespace]
8. Haga clic en **Crear**. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

### <a name="obtain-the-management-credentials"></a>Obtención de las credenciales de administración
La creación un nuevo espacio de nombres genera automáticamente una regla de firma de acceso compartido (SAS) inicial con un par asociado de claves principal y secundaria en el que ambas conceden control total sobre todos los aspectos del espacio de nombres. Para obtener información acerca de cómo crear reglas adicionales con derechos más restringidos para remitentes y destinatarios normales, consulte [Autenticación y autorización de Service Bus](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md). Para copiar la regla inicial, siga estos pasos: 

1.  Haga clic en **Todos los recursos** y, después, en el nombre del espacio de nombres recién creado.
2. En la ventana del espacio de nombres, haga clic en **Directivas de acceso compartido**.
3. En la pantalla **Directivas de acceso compartido**, haga clic en **RootManageSharedAccessKey**.
   
    ![información de conexión][connection-info]
4. En la ventana **Directiva: RootManageSharedAccessKey**, haga clic en el botón Copiar que hay junto a **Cadena de conexión: clave principal** para copiar la cadena de conexión en el portapapeles para su uso posterior. Pegue este valor en el Bloc de notas o cualquier otra ubicación temporal.
   
    ![connection-string][connection-string]

5. Repita el paso anterior, copie y pegue el valor de **clave principal** en una ubicación temporal para su uso posterior.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
