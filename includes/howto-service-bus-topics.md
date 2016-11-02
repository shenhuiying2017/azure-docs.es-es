## Qué son los temas y las suscripciones del Bus de servicio

Las suscripciones y los temas del Bus de servicio son compatibles con el modelo de comunicación de mensajería de *publicación/suscripción*. Cuando se usan temas y suscripciones, los componentes de una aplicación distribuida no se comunican directamente entre sí, sino que intercambian mensajes a través de un tema, que actúa como un intermediario.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

A diferencia de las colas del Bus de servicio, en las que un solo destinatario procesa cada mensaje, los temas y las suscripciones proporcionan una forma de comunicación "uno a varios" mediante un patrón de publicación/suscripción. Es posible registrar varias suscripciones en un tema. Cuando un mensaje se envía a un tema, pasa a estar disponible para cada suscripción de modo que se administra o procesa de manera independiente.

Una suscripción a un tema se asemeja a una cola virtual que recibe copias de los mensajes que se enviaron al tema. Opcionalmente, puede registrar reglas de filtros para un tema por suscripción, lo que le permite filtrar o restringir qué mensajes para un tema reciben las suscripciones a un tema.

Las suscripciones y los temas del Bus de servicio le permiten escalar y procesar un número muy elevado de mensajes entre muchos usuarios y aplicaciones.

## Creación de un espacio de nombres

Para comenzar a usar suscripciones y temas del Bus de servicio en Azure, primero debe crear un *espacio de nombres de servicio*. Un espacio de nombres proporciona un contenedor con un ámbito para el desvío de recursos del bus de servicio en la aplicación.

Para crear un espacio de nombres:

1. Inicie sesión en el [Portal de Azure][].

2. En el panel de navegación izquierdo del portal, haga clic en **Nuevo**, a continuación, haga clic en **Enterprise integration** y, a continuación, haga clic en **Bus de servicio**.

4. En el cuadro de diálogo **Crear un espacio de nombres**, especifique un nombre para el espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.

5. Después de asegurarse de que el espacio de nombres está disponible, elija el plan de tarifas (Básico, Estándar o Premium).

7. En el campo **Suscripción** elija la suscripción de Azure en la que se va a crear el espacio de nombres.

9. En el campo **Grupo de recursos**, elija un grupo de recursos existente en el que residirá el espacio de nombres o cree uno.

8. En **Ubicación**, elija el país o región donde se debe hospedar el espacio de nombres.

	![Crear espacio de nombres][create-namespace]

6. Haga clic en el botón **Crear**. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.
 
### Obtención de las credenciales

1. En la lista de espacios de nombres, haga clic en el nombre del espacio de nombres recién creado.
 
3. En la hoja **Espacio de nombres del Bus de servicio**, haga clic en **Directivas de acceso compartido**.

4. En la hoja **Directivas de acceso compartido**, haga clic en **RootManageSharedAccessKey**.

	![información de conexión][connection-info]

5. En la hoja **Directiva: RootManageSharedAccessKey**, haga clic en el botón Copiar junto a **Cadena de conexión: clave principal**, para copiar la cadena de conexión en el portapapeles para su uso posterior.

	![connection-string][connection-string]

[Portal de Azure]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png

<!---HONumber=AcomDC_0824_2016-->