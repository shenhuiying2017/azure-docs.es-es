<a id="what-are-service-bus-queues"></a>
##¿Qué son las colas del Bus de servicio?

Las colas del Bus de servicio son compatibles con el modelo de **comunicación de mensajería asíncrona** m. Cuando se usan colas, los componentes de una aplicación distribuida no cse comunican directamente entre sí, en su lugar intercambian mensajes a través de la a cola, que actúa como intermediario (agente). El productor del mensaje (remitente) hmanda un mensaje a la cola y, a continuación, sigue con su procesamiento.
De forma asíncrona, el destinatario del mensaje (receptor) extrae el mensaje de la qcola y lo procesa. El productor no tiene que esperar una respuesta fdel destinatario para continuar el proceso y el envío de más mmensajes. Las colas ofrecen una entrega de mensajes **FIFO (PEPS, primero en entrar, primero en salir)** ta uno o más destinatarios en competencia. Es decir, normalmente los receptores rreciben y procesan los mensajes en el orden en el que se aagregaron a la cola y solo un destinatario del mensaje recibe y procesa ocada uno de los mensajes.
 
![QueueConcepts](./media/howto-service-bus-queues/sb-queues-08.png)

Las colas del Bus de servicio son una tecnología de uso general que puede utilizarse a en una variedad de escenarios:

-   Comunicación entre los roles de trabajo y web en una aplicación de Azure de niveles múltiples -   Comunicación entre aplicaciones locales y aplicaciones hospedadas de Azure en una solución híbrida
-   Comunicación entre componentes de una aplicación distribuida que se ejecuta en local en distintas organizaciones o departamentos de una organización

El uso de las colas puede permitirle escalar mejor sus aplicaciones horizontalmente y edotar de más resistencia a su arquitectura.

## Creación de un espacio de nombres de servicio

Para empezar a usar las colas del Bus de servicio en Azure, primero debe ccrear un espacio de nombres de servicio. Un espacio de nombres de servicio proporciona un contenedor ccon un ámbito para el desvío de recursos del bus de servicio en la aplicación.

Para crear un nombre de espacio de servicio:
 1.  Inicie sesión en el [Portal de administración de Azure][].
 2.  En el panel de navegación izquierdo del Portal de administración, haga clic en **Bus de servicio**.
 3.  En el panel inferior del Portal de administración, haga clic en **Create**.   
	![](./media/howto-service-bus-queues/sb-queues-03.png)
 4.  En el cuadro de diálogo **Add a new namespace**, especifique un nombre de espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.   
	![](./media/howto-service-bus-queues/sb-queues-04.png)
 5.  Después de asegurarse de que el nombre de espacio de nombres esté disponible, elija el país o región donde debe hospedarse el espacio de nombres (asegúrese de que se use el mismo país o la misma región donde se está realizando la implementación de los recursos del equipo).

	IMPORTANTE: seleccione la **misma región** que vaya a seleccionar para la implementación de la aplicación. Con esto conseguirá el máximo rendimiento.
 6. Deje los demás campos en el cuadro de diálogo con los valores predeterminados (**Mensajería** y **Nivel estándar**) y, a continuación, haga clic en la marca de verificación. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

	![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

El espacio de nombres que creó aparecerá a continuación en el Portal de administración y ttardará un poco en activarse. Espere hasta que el estado sea **Activo** antes de ccontinuar.

## Obtención de credenciales de administración predeterminadas para el espacio de nombres

Para realizar operaciones de administración (como la creación de una cola) en el tnuevo espacio de nombres, debe obtener las credenciales de administración para el nespacio de nombres. Puede obtener estas credenciales en el Portal de administración o en el Explorador de servidores de Visual Studio.

###Para obtener las credenciales de administración desde el portal
 1.  En el panel de navegación izquierdo, haga clic en el nodo **Bus de servicio** para ver la lista de espacios de nombres disponibles:   
	![](./media/howto-service-bus-queues/sb-queues-13.png)
 2.  Seleccione el espacio de nombres que acaba de crear en la lista desplegable:   
	![](./media/howto-service-bus-queues/sb-queues-09.png)
 3.  Haga clic en **Información de conexión**.   
	![](./media/howto-service-bus-queues/sb-queues-06.png)
 4.  En el panel **Acceso a la información de conexión**, busque la cadena de conexión que contiene la clave SAS y el nombre de la clave.   

	![](./media/howto-service-bus-queues/multi-web-45.png)
    
 4.  Anote la clave o cópiela en el Portapapeles.

###Para obtener las credenciales de administración desde el Explorador de servidores

Para obtener la información de conexión utilizando Visual Studio en vez del Portal de administración, siga el procedimiento descrito [aquí](http://msdn.microsoft.com/library/windowsazure/ff687127.aspx), en la sección titulada **Conexión a Azure desde Visual Studio**. Al iniciar sesión en Azure, el nodo **Bus de servicio** bajo el árbol **Microsoft Azure** del Explorador de servidores se rellena automáticamente con los espacios de nombres que ya ha creado. Haga clic con el botón derecho en cualquier espacio de nombre y, a continuación, haga clic en **Propiedades** para ver la cadena de conexión y otros metadatos asociados a este nombre de espacio en el panel **Propiedades** de Visual Studio. 

Anote el valor de **SharedAccessKey** o cópielo en el Portapapeles:

![][34]

  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Portal de administración de Azure]: http://manage.windowsazure.com

  [34]: ./media/howto-service-bus-queues/VSProperties.png

<!--HONumber=47-->
