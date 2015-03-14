<h2><a name="what-are-service-bus-topics"></a>Qué son las suscripciones y los temas del Bus de servicio</h2>

Las suscripciones y los temas del Bus de servicio son compatibles con el modelo de **comunicación de mensajería de publicación/suscripción**. Cuando se usan temas y suscripciones, los componentes de una aplicación distribuida no se comunican directamente entre ellos, sino que intercambian mensajes a través de un tema, que actúa como un intermediario.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

A diferencia de las colas del Bus de servicio, en las que un solo destinatario procesa cada mensaje, los temas y las suscripciones proporcionan una forma de comunicación **uno a varios** mediante un patrón de publicación/suscripción. Es posible registrar varias suscripciones en un tema. Cuando un mensaje se envía a un tema, pasa a estar disponible para cada suscripción para la administración o el procesamiento de manera independiente.

Una suscripción al tema funciona de forma similar a una cola virtual que recibe copias de los mensajes que se enviaron al tema. Opcionalmente, puede registrar reglas de filtros para un tema por suscripción, lo que le permite filtrar/restringir qué mensajes para un tema reciben las suscripciones a un tema.

Las suscripciones y temas del bus de servicio le permiten escalar para realizar el procesamiento de un número bastante elevado de mensajes en una serie muy amplia de usuarios y aplicaciones.

<h2><a name="create-a-service-namespace"></a>Creación de un espacio de nombres de servicio</h2>

Para comenzar a usar suscripciones y temas del bus de servicio en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres de servicio proporciona un contenedor con un ámbito para el desvío de recursos del bus de servicio en la aplicación.

Para crear un nombre de espacio de servicio:

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En el panel de navegación izquierdo del Portal de administración, haga clic en
    **Bus de servicio**.

3.  En el panel inferior del Portal de administración, haga clic en **Create**.   
    ![][0]

4.  En el cuadro de diálogo **Add a new namespace**, especifique un nombre de espacio de nombres.
    El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.   
    ![][2]

5.  Después de asegurarse de que el nombre de espacio de nombres esté disponible, seleccione el país o región en el que debe hospedarse el espacio de nombres (asegúrese de que usa el mismo país o la misma región en los que está realizando la implementación de los recursos de proceso).

	IMPORTANTE: seleccione la **misma región** que vaya a seleccionar para la implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6. 	Deje los demás campos en el cuadro de diálogo con los valores predeterminados (**Mensajería** y **Nivel estándar**) y, a continuación, haga clic en la marca de verificación. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

	![][6]


<h2><a name="obtain-default-credentials"></a>Obtención de credenciales de administración predeterminadas para el espacio de nombres</h2>

Para realizar operaciones de administración (como la creación de un tema o una suscripción) en el nuevo espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres. Puede obtener estas credenciales en el Portal de administración o en el Explorador de servidores de Visual Studio.

###Para obtener las credenciales de administración desde el portal

1.  En el panel de navegación izquierdo, haga clic en el nodo **Bus de servicio** para ver la lista de espacios de nombres disponibles:    ![][0]

2.  Seleccione el espacio de nombres que acaba de crear en la lista desplegable:    ![][3]

3.  Haga clic en **Información de conexión**.    ![][4]

4.  En el cuadro de diálogo **Acceso a la información de conexión**,  busque las entradas **Emisor predeterminado** y **Clave predeterminada**. Anote estos valores, ya que usará la información que aparece a continuación para realizar operaciones con el espacio de nombres. 

###Para obtener las credenciales de administración desde el Explorador de servidores

Para obtener la información de conexión utilizando Visual Studio en vez del Portal de administración, siga el procedimiento descrito [aquí](http://http://msdn.microsoft.com/library/windowsazure/ff687127.aspx), en la sección titulada **Conexión a Azure desde Visual Studio**. Al iniciar sesión en Azure, el nodo **Bus de servicio** bajo el árbol **Microsoft Azure** del Explorador de servidores se rellena automáticamente con los espacios de nombres que ya ha creado. Haga clic con el botón secundario en cualquier espacio de nombre y, a continuación, haga clic en **Propiedades** para ver la cadena de conexión y otros metadatos asociados a este nombre de espacio en el panel **Propiedades** de Visual Studio. 

Anote el valor de **SharedAccessKey**  o cópielo en el Portapapeles:

![][34]

 
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-topics/sb-queues-13.png
  [2]: ./media/howto-service-bus-topics/sb-queues-04.png
  [3]: ./media/howto-service-bus-topics/sb-queues-09.png
  [4]: ./media/howto-service-bus-topics/sb-queues-06.png
  
  [6]: ./media/howto-service-bus-topics/getting-started-multi-tier-27.png
  [34]: ./media/howto-service-bus-topics/VSProperties.png
<!--HONumber=42-->
