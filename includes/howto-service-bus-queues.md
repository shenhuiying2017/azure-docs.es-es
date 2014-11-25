<span id="what-are-service-bus-queues"></span></a>

## ¿Qué son las colas del bus de servicio?

Las colas del bus de servicio son compatibles con el modelo de **comunicación de mensajería asíncrona**
. Cuando se usan colas, los componentes de una aplicación distribuida no
se comunican directamente entre ellos, sino que intercambian mensajes a través de
una cola, que actúa como un intermediario. El productor del mensaje (remitente)
manda un mensaje a la cola y, a continuación sigue con su procesamiento.
De forma asíncrona, el destinatario del mensaje (receptor) extrae el mensaje de
la cola y lo procesa. El productor no tiene que esperar una respuesta
del destinatario para continuar el proceso y el envío de más
mensajes. Las colas ofrecen una entrega de mensajes **FIFO (PEPS, primero en entrar, primero en salir)**
 a uno o más destinatarios en competencia. Es decir, normalmente los receptores
reciben y procesan los mensajes en el orden en el que se
agregaron a la cola y solo un destinatario del mensaje recibe y procesa
cada uno de los mensajes.

![QueueConcepts][QueueConcepts]

Las colas del Bus de servicio son una tecnología de uso general que puede utilizarse
en una variedad de escenarios:

-   Comunicación entre los roles de trabajo y web en una aplicación de
    Azure de niveles múltiples
-   Comunicación entre aplicaciones locales y aplicaciones hospedadas de Azure
    en una solución híbrida
-   Comunicación entre componentes de una aplicación distribuida
    que se ejecuta en local en distintas organizaciones o departamentos de una
    organización

El uso de las colas puede permitirle escalar mejor sus aplicaciones horizontalmente y
dotar de más resiliencia a su arquitectura.

<span id="create-a-service-namespace"></span></a>

## Creación de un espacio de nombres de servicio

</p>
Para comenzar a usar colas del Bus de servicio en Azure, primero debe
crear un espacio de nombres de servicio. Un espacio de nombres de servicio proporciona un contenedor
con un ámbito para el desvío de recursos del Bus de servicio en la aplicación.

Para crear un nombre de espacio de servicio:

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].

2.  En el panel de navegación izquierdo del Portal de administración, haga clic en
    **Bus de servicio**.

3.  En el panel inferior del Portal de administración, haga clic en **Crear**.
    ![][0]

4.  En el cuadro de diálogo **Agregar un nuevo espacio de nombres**, especifique un nombre de espacio de nombres.
    El sistema realiza la comprobación de inmediato para ver si el nombre está disponible.
    ![][1]

5.  Después de asegurarse de que el nombre de espacio de nombres esté disponible, seleccione el
    país o región en el que debe hospedarse el espacio de nombres (asegúrese
    de que usa el mismo país o la misma región en los que está realizando la implementación de los
    recursos de proceso).

    IMPORTANTE: Seleccione la **misma región** que vaya a seleccionar para la
    implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6.  Haga clic en la marca de verificación. El sistema crea ahora el espacio de nombres del
    servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras
    el sistema realiza el aprovisionamiento de los recursos para la cuenta.

    ![][2]

El espacio de nombres que creó aparecerá a continuación en el Portal de administración y
tardará un poco en activarse. Espere hasta que el estado sea **Activo** antes
 de continuar.

<span id="obtain-default-credentials"></span></a>

## Obtención de credenciales de administración predeterminadas para el espacio de nombres

</p>
Para realizar operaciones de administración (como la creación de una cola) en el
nuevo espacio de nombres, debe obtener las credenciales de administración para
el espacio de nombres. Puede obtener estas credenciales en el Portal de administración o en el Explorador de servidores de Visual Studio.

### Para obtener las credenciales de administración desde el portal

1.  En el panel de navegación izquierdo, haga clic en el nodo **Bus de servicio** para
    ver la lista de espacios de nombres disponibles:
    ![][3]

2.  Seleccione el espacio de nombres que acaba de crear en la lista que se muestra:
    ![][4]

3.  Haga clic en **Información de conexión**.
    ![][5]

4.  En el cuadro de diálogo **Access connection information**, busque las entradas **Default Issuer** y **Default Key**. Anote estos valores, ya que usará la información que aparece a continuación para realizar operaciones con el espacio de nombres.

### Para obtener las credenciales de administración desde el Explorador de servidores

Para obtener la información de conexión utilizando Visual Studio en vez del Portal de administración, siga el procedimiento descrito [aquí][aquí], en la sección titulada **Para conectar a Azure desde Visual Studio**. Al iniciar sesión en Azure, el nodo **Bus de servicio** bajo el árbol **Microsoft Azure** del Explorador de servidores se rellena automáticamente con los espacios de nombre que ya haya creado. Haga clic con el botón secundario en cualquier espacio de nombre, a continuación haga clic en **Propiedades** para ver la cadena de conexión y otros metadatos asociados a este nombre de espacio en el panel **Propiedades** de Visual Studio.

Anote el valor de **SharedAccessKey**, o cópielo en el Portapapeles:

![][6]

  [QueueConcepts]: ./media/howto-service-bus-queues/sb-queues-08.png
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-queues/sb-queues-03.png
  [1]: ./media/howto-service-bus-queues/sb-queues-04.png
  [2]: ./media/howto-service-bus-queues/getting-started-multi-tier-27.png
  [3]: ./media/howto-service-bus-queues/sb-queues-13.png
  [4]: ./media/howto-service-bus-queues/sb-queues-09.png
  [5]: ./media/howto-service-bus-queues/sb-queues-06.png
  [aquí]: http://http://msdn.microsoft.com/es-es/library/windowsazure/ff687127.aspx
  [6]: ./media/howto-service-bus-queues/VSProperties.png
