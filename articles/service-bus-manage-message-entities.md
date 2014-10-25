<properties linkid="service-bus-manage-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Manage Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to create and manage your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Manage Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Administración de entidades de mensajería del bus de servicio

En este tema se describe cómo crear y administrar las entidades del bus de servicio mediante el [Portal de administración de Azure][]. Puede utilizar el portal para crear nuevos espacios de nombres de servicios o entidades de mensajería (colas, temas o suscripciones). También puede eliminar entidades o cambiar el estado de estas.

## Tabla de contenido

-   [Eliminación de Entidad de bus de servicio][]
-   [Eliminación de Entidad de bus de servicio][1]
-   [Eliminación de De una entidad de bus de servicio][]
-   [Recursos adicionales][]

## <span id="create"></span></a>Eliminación de entidad de bus de servicio

El Portal de administración de Azure admite dos formas de crear una entidad de bus de servicio: *Creación rápida* o *Creación personalizada*.

### Creación rápida

Creación rápida le permite crear una cola, un tema o un espacio de nombres de servicio de relé del bus de servicio en un paso sencillo. Siga estos pasos para crear una entidad de bus de servicio.

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  Haga clic en el icono **New** situado en la parte inferior izquierda del portal de administración.
3.  Haga clic en el icono **App Services** y, a continuación, haga clic en **Service Bus Queue** (tema o relé). Haga clic en **Creación rápida** y escriba el nombre de la cola, la región y el identificador de la suscripción a Azure.

    a. Si este es su primer espacio de nombres en la región seleccionada, el portal sugiere una cola de espacio de nombres; [nombre de la entidad]-ns. Puede cambiar este valor.

    b. Si ya tiene al menos un espacio de nombres de servicio en esta región, se selecciona un espacio de nombres automáticamente. Puede cambiar el espacio de nombres que seleccionó.

4.  Haga clic en la marca de verificación junto a **Crear una nueva cola** (o tema).
5.  Cuando se ha creado la cola o tema, verá el mensaje **Creation of Queue '[Nombre de la cola]' Completed**.

    a. Si no tiene ningún espacio de nombres en esta región o en esta suscripción a Azure, se crea automáticamente un nuevo espacio de nombres para usted. En tal caso, recibirá dos mensajes de operación correcta: uno por la creación del espacio de nombres y el otro por la creación de la entidad.

    ![][]

Haga clic en el icono **Bus de servicio** en la barra de navegación de la izquierda para obtener una lista de los espacios de nombres. Encontrará el nuevo espacio de nombres que acaba de crear. Haga clic en el espacio de nombres en la lista. Verá la entidad que acaba de crear en ese espacio de nombres.

**Nota:** es posible que no pueda ver el espacio de nombres inmediatamente. Lleva algunos segundos crear el espacio de nombres de servicio y actualizar la interfaz del portal.

**Nota:** el uso de **Creación rápida** para un relé no crea un extremo de relé nuevo. Solo crea un espacio de nombres en le que puede crear de manera programática un extremo de relé. Para obtener más detalles, consulte la [documentación sobre el bus de servicio][].

### Creación personalizada

**La creación personalizada** es la versión más detallada que le ofrece botones para cambiar los valores predeterminados de las propiedades de la entidad (cola o tema) que se está creando. Para crear un tema o entidad que use la opción **Creación personalizada**, siga estos pasos:

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  Haga clic en **New** en la parte inferior izquierda del portal de administración.
3.  Haga clic en el icono **App Services** y, a continuación, haga clic en **Service Bus Queue** (tema o relé). A continuación, haga clic en Creación personalizada.
4.  En la primera pantalla de diálogo, escriba el nombre de la cola, la región y el identificador de la suscripción a Azure.

    a. Si este es su primer espacio de nombres de servicio en la región seleccionada, el portal sugiere una cola de espacio de nombres; [nombre de la entidad]-ns. Puede cambiar este valor.

    b. Si ya tiene al menos un espacio de nombres en esta región, se selecciona un espacio de nombres automáticamente. Puede cambiar el espacio de nombres que seleccionó.

5.  Haga clic en **Next** para insertar las demás propiedades.

    ![][2]

6.  Haga clic en la marca de verificación para crear la cola.

    ![][3]

Haga clic en el icono **Bus de servicio** en la barra de navegación de la izquierda para obtener la lista de los espacios de nombres del servicio. Encontrará el nuevo espacio de nombres que acaba de crear. Haga clic en el espacio de nombres en la lista. Verá la entidad que acaba de crear en ese espacio de nombres.

## <span id="delete"></span></a>Eliminación de entidad de bus de servicio

Con la ayuda del portal puede eliminar una entidad de mensajería del bus de servicio. Esto es aplicable a las colas, los temas y las suscripciones de temas. Para eliminar una cola o un tema, realice lo siguiente:

1.  Vaya a la vista de lista del espacio de nombres de servicio y haga clic en el espacio de nombres en que se creó la entidad (cola o tema).
2.  Haga clic en el icono **Delete** situado en la parte inferior de la página y confirme la operación de eliminación.

    ![][4]

**Nota:** la eliminación de la entidad no se puede revertir. Una vez eliminada, no podrá recuperarla. Sin embargo, puede crear otra entidad con el mismo nombre.

Para eliminar una suscripción de tema, haga lo siguiente:

1.  Vaya a la vista de lista de espacios de nombres y haga clic en el espacio de nombres en que creó el tema.
2.  Haga clic en el tema en el que creó la suscripción.
3.  Haga clic en la pestaña **Subscriptions** y seleccione la suscripción que desea eliminar.
4.  Haga clic en el icono **Delete** situado en la parte inferior de la página y confirme la operación de eliminación.

## <span id="disableenable"></span></a>Eliminación de de una entidad de bus de servicio

Puede utilizar el portal para cambiar el estado de una entidad de bus de servicio. Esto es aplicable a las colas y los temas. Para habilitar o deshabilitar una cola o un tema, realice lo siguiente:

1.  Vaya a la vista de lista del espacio de nombres de servicio y haga clic en el espacio de nombres en que se creó la entidad (cola o tema).
2.  Haga clic en **Disable** (o **Enable**) en la parte inferior de la página.

    ![][5]

## <span id="seealso"></span></a>Recursos adicionales

[Service Bus][]

[Centro de desarrolladores .NET][] en el sitio web de Azure

[Creación de aplicaciones que usan temas y suscripciones del bus de servicio][]

[Colas, temas y suscripciones del Service Bus][]

  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Eliminación de Entidad de bus de servicio]: #create
  [1]: #delete
  [Eliminación de De una entidad de bus de servicio]: #disableenable
  [Recursos adicionales]: #seealso
  []: ./media/service-bus-manage-message-entities/QueueQuickCreate.png
  [documentación sobre el bus de servicio]: http://www.windowsazure.com/es-es/develop/net/how-to-guides/service-bus-relay/
  [2]: ./media/service-bus-manage-message-entities/AddQueue1.png
  [3]: ./media/service-bus-manage-message-entities/ConfigureQueue.png
  [4]: ./media/service-bus-manage-message-entities/DeleteEntity.png
  [5]: ./media/service-bus-manage-message-entities/DisableEnable.png
  [Service Bus]: http://go.microsoft.com/fwlink/?LinkId=266834
  [Centro de desarrolladores .NET]: http://go.microsoft.com/fwlink/?LinkID=262187
  [Creación de aplicaciones que usan temas y suscripciones del bus de servicio]: http://go.microsoft.com/fwlink/?LinkId=264293
  [Colas, temas y suscripciones del Service Bus]: http://go.microsoft.com/fwlink/?LinkId=264291
