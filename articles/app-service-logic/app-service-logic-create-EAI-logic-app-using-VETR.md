<properties 
   pageTitle="Creación de una aplicación lógica de EAI con VETR" 
   description="Este tema trata las características Validar, Codificar y Transformar de los servicios XML de BizTalk." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/24/2015"
   ms.author="rajram"/>


# Creación de una aplicación lógica de EAI con VETR

La mayoría de los escenarios de Integración de aplicaciones empresariales (EAI) median datos entre un origen y un destino. Tales escenarios suelen tener un conjunto común de requisitos:

- Asegúrese de que los datos procedentes de distintos sistemas tienen el formato correcto
- Realice un “búsqueda” en los datos entrantes para tomar decisiones
- Convierta datos de un formato a otro (por ejemplo, del formato de datos de un sistema CRM al formato de datos de un sistema ERP)
- Enrute datos a la aplicación o al sistema deseado

En este artículo, nos centramos en un patrón de integración común: "mediación de mensaje unidireccional" o VETR (Validar, Enriquecer, Transformar, Enrutar). El patrón VETR media datos entre una entidad de origen y una entidad de destino. Normalmente, el origen y destino son orígenes de datos.

Imagine un sitio web que acepta pedidos. Los usuarios exponen pedidos en el sistema mediante HTTP. En segundo plano, el sistema valida los datos entrantes comprobando si son correctos, los normaliza y conserva en una cola de Bus de servicio para su posterior procesamiento. El sistema extrae pedidos de la cola, esperando que tengan un formato concreto. De este modo: el flujo de un extremo a extremo es:

HTTP -> Validar -> Transformar -> Bus de servicio

![Flujo VETR básico][1]

Las siguientes aplicaciones de API de BizTalk ayudan a crear este patrón:

*Desencadenador HTTP*: origen para desencadenar el evento de mensaje *Validar*: valida la exactitud de los datos entrantes *Transformar*: transforma los datos del formato entrante para dar el formato necesario para el sistema de nivel inferior *Conector de Bus de servicio*: entidad de destino en la que se envían los datos


## Construcción del patrón VETR básico
### Conceptos básicos

En el Portal de administración de Azure, haga clic en el botón **+Nuevo** en la parte inferior izquierda de la pantalla y, después, haga clic en Aplicación lógica. Elija un nombre, una ubicación, una suscripción, un grupo de recursos y una ubicación que funcionen. Los grupos de recursos actúan como contenedores para las aplicaciones y todos los recursos de la aplicación van al mismo grupo de recursos.

A continuación, vamos a agregar desencadenadores y acciones.


## Adición de un desencadenador HTTP

1. Seleccione **Agente de escucha HTTP** desde la galería para crear un nuevo agente de escucha. Llámelo **HTTP1**.
2. Deje la opción **¿Enviar respuesta automáticamente?** como false. Configure la acción del desencadenador estableciendo _Método HTTP_ en _EXPONER_ y _Dirección URL relativa_ en _/OneWayPipeline_.

![Desencadenador HTTP][2]


## Adición de la acción Validar

Ahora, vamos a especificar acciones que se ejecutan cada vez que el desencadenador se activa, es decir, siempre que se recibe una llamada en el extremo HTTP.

1. Agregue **Validador XML de BizTalk** desde la galería y asígnele el nombre _(Validate1)_ para crear una instancia.
2. Configure un esquema XSD para validar los mensajes XML entrantes. Seleccione la acción _Validar_ y seleccione _triggers(‘httplistener’).outputs.Content_ como el valor para el parámetro _inputXml_.

Ahora, la acción de validación es la primera acción después del agente de escucha HTTP. De forma similar, vamos a agregar el resto de las acciones.

![Validador XML de BizTalk][3]


## Adición de la acción Transformar
Vamos a configurar transformaciones para normalizar los datos de entrada.

1. Agregue **Transformar** desde la galería. 
2. Para configurar una transformación para transformar los mensajes XML de entrada, seleccione la acción **Transformar** como la acción que se ejecutará cuando esta API se llame y seleccione ```triggers(‘httplistener’).outputs.Content``` como el valor de _inputXml_. Map es un parámetro opcional, ya que los datos de entrada se hacen corresponder con todas las transformaciones configuradas y se aplican solo aquellos que coincidan con el esquema.
3. Por último, la transformación se ejecuta solamente si la validación se realiza correctamente. Para configurar esta condición, seleccione el icono de engranaje situado en la parte superior para _agregar una condición que debe cumplirse_. Establezca la condición en ```equals(actions('xmlvalidator').status,'Succeeded')```


![Transformaciones de BizTalk][4]


## Adición del conector de Bus de servicio
A continuación, vamos a agregar el destino (una cola de Bus de servicio) para escribir los datos en él.

1. Agregue un **conector de Bus de servicio** desde la galería. Establezca _Nombre_ en _Servicebus1_, _Cadena de conexión_ en la cadena de conexión para la instancia de bus de servicio, _Nombre de entidad_ en _Cola_ y omita _Nombre de suscripción_. 
2. Seleccione la acción **Enviar mensaje acción** y establezca el campo _Mensaje_ para la acción en _actions('transformservice').outputs.OutputXml_.

![Bus de servicio][5]


## Envío de respuesta HTTP
Una vez que se realiza el procesamiento de canalización, se devuelve una respuesta HTTP tanto para operación correcta como para error con los pasos siguientes:

1. Agregue un **agente de escucha HTTP** desde la galería y seleccione la acción **Enviar respuesta HTTP**.
2. Establezca _Contenido de respuesta_ en “Procesamiento de canalización completado”, _Código de estado de respuesta_ en “200” para indicar HTTP 200 correcto y la condición en la expresión de ```@equals(actions('servicebusconnector').status,'Succeeded')```.
	
Repita los pasos anteriores para enviar una respuesta HTTP en caso de error también. Modificación de la condición a ```@not(equals(actions('servicebusconnector').status,'Succeeded')).```


## Finalización
Cada vez que alguien envía un mensaje al extremo HTTP, desencadena la aplicación y ejecuta las acciones que acabamos de crear. Para administrar cualquiera de las aplicaciones lógicas que cree, haga clic en **Examinar** en el Portal de administración de Azure y haga clic en **Aplicaciones lógicas**. Haga clic en la aplicación para obtener más información.


<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

 

<!---HONumber=62-->