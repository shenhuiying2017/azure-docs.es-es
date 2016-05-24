<properties
   pageTitle="Información general sobre Funciones de Azure | Microsoft Azure"
   description="Entender cómo utilizar las funciones de Azure para optimizar las cargas de trabajo asincrónicas en minutos."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/08/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# Información general sobre Funciones de Azure

Funciones de Azure es una solución para ejecutar fácilmente pequeños fragmentos de código, o "funciones", en la nube. Simplemente, puede escribir el código que necesita para el problema en cuestión, sin preocuparse de toda la aplicación o la infraestructura para ejecutarlo. Gracias a ello las tareas de desarrollo pueden resultar aún más productivas, y además le permite utilizar el lenguaje de desarrollo que elija, como C#, Node.js, Python o PHP. Pague solo el tiempo durante el que se ejecuta el código y confíe en Azure para escalar según sea necesario.

Este tema proporciona información general de alto nivel de Funciones de Azure. Si desea comenzar de inmediato y empezar a trabajar con Funciones de Azure, comience con el artículo [Creación de su primera función de Azure](functions-create-first-azure-function.md). Si busca información más técnica acerca de las Funciones, consulte [Referencia para desarrolladores de Funciones de Azure](functions-reference.md).

## Características

Estas son algunas características clave de Funciones de Azure:
    
* **Opción de lenguaje**: funciones de escritura utilizando C#, Node.js, Python, F#, PHP, lote, bash, Java o cualquier archivo ejecutable.  
* **Modelo de precios de pago por uso**: pague solo el tiempo que haya empleado ejecutando el código. Consulte la opción de Plan del Servicio de aplicaciones dinámico en la [sección sobre precios](#pricing), que encontrará más adelante.  
* **Traiga sus propias dependencias**: Funciones de Azure admite NuGet y NPM, para que pueda usar sus bibliotecas favoritas.  
* **Seguridad integrada**: proteja las funciones desencadenadas por HTTP con los proveedores de OAuth como Azure Active Directory, Facebook, Google, Twitter y cuenta Microsoft.  
* **Integración simplificada**: fácil aprovechamiento de los servicios de Azure y ofertas de software como servicio (SaaS). Consulte la [sección Integraciones](#integrations) más adelante, para ver algunos ejemplos.  
* **Desarrollo flexible**: codifique las funciones directamente en el portal o configure la integración continua e implemente el código mediante GitHub, Visual Studio Team Services y otras [herramientas de desarrollo compatibles](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Código abierto**: el tiempo de ejecución de Funciones de Azure es de código abierto y está [disponible en GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## ¿Qué puedo hacer con las funciones?

Funciones de Azure es una excelente solución para procesar datos e integrar sistemas, trabajar con Internet de las cosas (IoT) y generar API simples y microservicios. Considere utilizar Funciones de Azure para tareas como el procesamiento de imágenes o pedidos, el mantenimiento de archivos, las tareas de larga duración que quiera ejecutar en un subproceso en segundo plano, o para cualquier tarea que desee ejecutar de acuerdo a una programación.

Funciones de Azure ofrece plantillas para comenzar con situaciones clave, incluidas las siguientes:

* **BlobTrigger**: procesar blobs de Almacenamiento de Azure cuando se agregan a los contenedores. Se puede usar para cambiar el tamaño de las imágenes.
* **EventHubTrigger**: responder a eventos que se producen en los dispositivos de IoT.
* **Webhook genérico**: procesar las solicitudes HTTP de webhook de cualquier servicio que admita webhooks.
* **GitHub webhook**: responder a los eventos que se producen en los repositorios de GitHub. Para ver un ejemplo, consulte [Creación de un webhook o una función de API de Azure](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger**: desencadenar la ejecución del código mediante una solicitud HTTP.
* **QueueTrigger**: responder a mensajes conforme llegan a una cola de Almacenamiento de Azure. Para ver un ejemplo, consulte [Creación de una función de Azure que se enlaza a un servicio de Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger**: conectar el código a otros servicios de Azure o servicios locales, escuchando las colas de mensajes. 
* **ServiceBusQueueTrigger**: conectar el código a otros servicios de Azure o servicios locales, con la suscripción a temas. 
* **TimerTrigger**: ejecutar limpieza u otras tareas de lote dentro de una programación predefinida. Para ver un ejemplo, consulte [Creación de una función de Azure de procesamiento de eventos](functions-create-an-event-processing-function.md).

Funciones de Azure admite *desencadenadores* que son formas de iniciar la ejecución del código, y *enlaces* que son formas de simplificar la codificación para los datos de entrada y salida. Para una descripción detallada de los desencadenadores y los enlaces que proporciona Funciones de Azure, consulte [Referencias para desarrolladores de desencadenadores y enlaces de Funciones de Azure](functions-triggers-bindings.md).


## <a name="integrations"></a>Integraciones

Funciones de Azure se integra con diversos servicios de Azure y de terceros. Puede usarlas para desencadenar su función e iniciar la ejecución o servir de entrada y salida para el código. Las siguientes integraciones de servicio son compatibles con Funciones de Azure.

* DocumentDB de Azure
* Centros de eventos de Azure 
* Aplicaciones móviles de Azure (tablas)
* Centros de notificaciones de Azure
* Bus de servicio de Azure (colas y temas)
* Almacenamiento de Azure (blob, colas y tablas) 
* GitHub (webhooks)
* Local (mediante el Bus de servicio)

## <a name="pricing"></a>¿Cuánto cuesta Funciones de Azure?

Funciones de Azure tiene dos tipos de planes de precios, elija el que mejor se adapte a sus necesidades:

* **Plan de hospedaje dinámico**: cuando se ejecuta la función, Azure proporciona todos los recursos informáticos necesarios. No tiene que preocuparse de la administración de recursos y solo paga por el tiempo que haya empleado ejecutando el código. Puede encontrar todos los detalles de precios en la [página de Precios de Funciones](/pricing/details/functions). 

* **Plan del Servicio de aplicaciones**: se ejecutan las funciones igual que aplicaciones web, móviles y de API. Cuando ya se usa el Servicio de aplicaciones para las otras aplicaciones, las funciones pueden ejecutarse en el mismo plan sin costo adicional. Puede encontrar todos los detalles en la [página Precios de Servicio de aplicaciones](/pricing/details/app-service/).

Para más información acerca de cómo escalar sus funciones, consulte [Escalado de Funciones de Azure](functions-scale.md).

##Pasos siguientes

+ [Creación de su primera función de Azure](functions-create-first-azure-function.md) Comience de inmediato y cree su primera función mediante el inicio rápido de Funciones de Azure. 
+ [Referencia para desarrolladores de Funciones de Azure](functions-reference.md) Proporciona información técnica sobre el tiempo de ejecución de Funciones de Azure y una referencia para las funciones de codificación y la definición de enlaces y desencadenadores.
+ [Prueba de Funciones de Azure](functions-test-a-function.md) Este artículo describe las diversas herramientas y técnicas para probar las funciones.
+ [Escalado de Funciones de Azure](functions-scale.md) Aborda los planes de servicio disponibles con Funciones de Azure, incluido el plan de servicio dinámico, y cómo elegir el plan adecuado. 
+ [¿Qué es Servicios de aplicaciones de Azure?](../app-service/app-service-value-prop-what-is.md) Funciones de Azure aprovecha la plataforma de Servicio de aplicaciones de Azure para obtener funcionalidad básica como implementaciones, variables de entorno y diagnósticos. 

<!---HONumber=AcomDC_0518_2016-->