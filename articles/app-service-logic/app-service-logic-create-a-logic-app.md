<properties 
	pageTitle="Crear una aplicación lógica" 
	description="Introducción a la creación de una aplicación lógica básica" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/20/2015"
	ms.author="stepsic"/>

#Creación de una nueva aplicación lógica
En este tema se muestra cómo, en solo unos minutos, puede comenzar a trabajar con las aplicaciones lógicas de Servicios de aplicaciones. Recorreremos un flujo de trabajo que permite entregar un conjunto de tweets en el que está interesado a una carpeta de Dropbox.

Para usar este escenario necesitará:

- Una suscripción de Azure
- Una cuenta de Twitter
- Una cuenta de Dropbox

<!--- TODO: Add try it now information here -->

##Obtención de conectores

En primer lugar, deberá crear los dos conectores que se van a usar: el **conector de Dropbox** y el **conector de Twitter**. Para crearlos:

1. Haga clic en  **Marketplace** en la pantalla principal y busque **Twitter**. 

2. Seleccione el conector Twitter y haga clic en el botón Crear. Conseguirá una hoja para todas las configuraciones. Puede dejar el nombre como **Conector Twitter**.

3. Escriba un nombre de plan en **Crear nuevo plan de servicio de aplicaciones**.
	
	>[AZURE.NOTE]En los pasos de esta sección se supone que va a crear un nuevo plan de servicio de aplicaciones. Si usa un plan de servicio de aplicaciones existente, haga clic en **Seleccionar existente**, seleccione el plan existente y, a continuación, vaya a la sección siguiente.
 
4.  Seleccione un **nivel de precios** para su nuevo plan.
 
	>[AZURE.NOTE]De forma predeterminada, se muestran únicamente los planes recomendados para aplicaciones lógicas. Haga clic en **Ver todos** para ver todos los planes disponibles. Al ejecutar una aplicación lógica en el nivel Gratis, solo puede realizar ejecuciones cada hora y usar hasta 1.000 acciones por mes.

5. Cree un **grupo de recursos** para el flujo.

	Los grupos de recursos actúan como contenedores para las aplicaciones. Todos los recursos de la aplicación residirán en el mismo grupo de recursos.

6. Si tiene más de una suscripción de Azure, elija la que va a usar.

7. Elija la **ubicación** para ejecutar la aplicación lógica.

	![Creación de hoja de aplicación de API](./media/app-service-logic-create-a-logic-app/gallery.png)

8. Haga clic en **Crear**. El paso de aprovisionamiento puede tardar un minuto o dos.

9. Ahora repita el proceso con Dropbox.

##Inicio de la aplicación lógica

Ahora debe crear una nueva aplicación lógica:

1. Haga clic en el **+ Nuevo** botón situado en la parte inferior izquierda de la pantalla, expanda **Web + Móvil** y haga clic en **Aplicación lógica**. 

 	Esto muestra la hoja Crear aplicación lógica, donde se proporciona alguna configuración básica para empezar.

	![Creación de hoja de aplicación lógica](./media/app-service-logic-create-a-logic-app/createlogicapp.png)
	
2. En **Nombre** escriba un nombre significativo para la aplicación lógica.

3. Elija el **plan de servicio de aplicaciones** que usó al crear los conectores. Esto debería elegir automáticamente la ubicación, la suscripción y el grupo de recursos.

Esto se encarga de la configuración básica, pero no haga clic en **Crear**. A continuación, agregará los desencadenadores y acciones al flujo de trabajo.

## Adición de un desencadenador

Los desencadenadores son los que permiten que la aplicación lógica se ejecute. A continuación, agregará un desencadenador de periodicidad, que inicia el flujo de trabajo en una programación predefinida.

1. Todavía en la hoja **Crear aplicación lógica**, haga clic en **Desencadenadores y acciones**. 

	Esto muestra un diseñador de pantalla completa que muestra el flujo. En el lado derecho hay una lista de todos los servicios que podrían tener desencadenadores.

2. En la sección **Integrado**, haga clic en **Periodicidad**.
	
	Esto agrega un cuadro donde puede especificar la configuración de periodicidad.

	![Periodicidad](./media/app-service-logic-create-a-logic-app/recurrence.png)


4.  Elija una opción de periodicidad en **Frecuencia** e **Intervalo** (por ejemplo, una vez cada hora) y, a continuación, haga clic en la marca de verificación verde.

Ahora, agregará una acción al flujo.

## Adición de una acción de Twitter

Las acciones son lo que hace el flujo de trabajo. Puede tener cualquier número de acciones y organizarlas de forma que la información de una acción se pase a la siguiente.

1. En el panel de la derecha, busque **Conector Twitter** y haga clic en él. 


2. Una vez cargado, haga clic en el botón **Autorizar**, inicie sesión en su cuenta de Twitter y haga clic en **Autorizar aplicación**.

	Esto concede acceso al conector a su cuenta de Twitter. Se muestra una lista de las posibles operaciones proporcionadas por el conector Twitter.

	![Acciones](./media/app-service-logic-create-a-logic-app/actions.png)

3. Haga clic en **Buscar tweets**, a continuación en **Especificar una consulta**, escriba algo como `#MicrosoftAzure` y haga clic en la marca de verificación verde.

	![Búsqueda de Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

El conector Twitter ahora forma parte del flujo de trabajo.

## Adición de una acción de Dropbox y creación de la aplicación

El último paso es agregar una acción que cargue unos tweets a un archivo de Dropbox.

1. En el panel de la derecha, haga clic en **Conector Dropbox**. 
  
2. Tras completar el aprovisionamiento, haga clic en el botón **Autorizar**, inicie sesión en su cuenta de Dropbox y haga clic en **Permitir**.

	![Autorización del conector de Dropbox.](./media/app-service-logic-create-a-logic-app/authorize.png)
	
	Esto concede acceso al conector a su cuenta de Dropbox. Se muestra una lista de las posibles operaciones proporcionadas por el conector Dropbox.
 
4. Haga clic en **Cargar archivo**.

	Esto muestra la configuración del conector Dropbox, que se debe establecer para pasar los datos de la búsqueda de Twitter a Dropbox.

	![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. En el campo **Ruta de acceso al archivo**, escriba `/tweet.txt`.
  
4. En el campo **Contenido**, haga clic en el botón `...` y haga clic en la opción **Texto del tweet**.
 
	Esto introduce el valor `@first(body('twitterconnector')).TweetText` en el cuadro de texto. Este valor generado contiene las siguientes partes:

	Parte de contenido | Descripción
	------------------------------------------ | ------------
	 `@` | Indica que ha introducido una función, en lugar de un valor real.
	`actions('twitterconnector').outputs.body` | Obtiene los tweets devueltos por la consulta del conector Twitter.
	`first()` | La acción de búsqueda de tweets devuelve una lista, pero el usuario solo desea cargar un archivo
	`.TweetText` | Selecciona la propiedad de mensaje del tweet.
	
5. Haga clic en la marca de verificación verde para guardar la configuración del conector.

5. Ahora que el diseño se ha completado, haga clic en **Vista Código** en la parte superior izquierda del diseñador y tenga en cuenta que este es el código JSON que define el flujo de trabajo que acaba de crear en el diseñador. Trataremos este código más en el [siguiente tema][Use logic app features].

6. Haga clic en el botón **Aceptar** en la parte inferior de la pantalla y, a continuación, haga clic en el botón **Crear**.

	Esto crea la nueva aplicación lógica.

## Administración de la aplicación lógica tras la creación

Ahora la aplicación lógica está en funcionamiento. Cada vez que se ejecuta el flujo de trabajo programado, dicho flujo busca tweets con el hashtag específico. Cuando encuentra un tweet coincidente, lo coloca en Dropbox. Por último, verá cómo deshabilitar la aplicación o ver cómo marcha.

1. Haga clic en **Examinar** en el lado izquierdo de la pantalla y seleccione **Aplicaciones lógicas**. 
 
2. Haga clic en la nueva aplicación lógica que acaba de crear para ver información general y el estado actual.

3. Para editar la nueva aplicación lógica, haga clic en **Desencadenadores y acciones**.
 
5. Para desactivar la aplicación, haga clic en **Deshabilitar** en la barra de comandos.

En menos de 5 minutos ha sido capaz de configurar una aplicación lógica sencilla que se ejecuta en la nube. Para obtener más información acerca del uso de las características de las aplicaciones lógicas, consulte [Uso de las características de aplicaciones lógicas].

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features]: app-service-logic-use-logic-app-features.md
[Uso de las características de aplicaciones lógicas]: app-service-logic-use-logic-app-features.md

<!--HONumber=54--> 