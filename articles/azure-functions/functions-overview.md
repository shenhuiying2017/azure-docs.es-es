<properties
   pageTitle="Información general sobre Funciones de Azure | Microsoft Azure"
   description="Entienda cómo Funciones de Azure puede optimizar cargas de trabajo asincrónicas mediante la creación de funciones simples que pueden escribirse en minutos."
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
   ms.date="03/09/2016"
   ms.author="cfowler;mahender"/>
   
   
# Información general sobre Funciones de Azure

## Un acceso más rápido a las funciones

Escriba cualquier función en un momento, cuando necesite un trabajo sencillo para limpiar una base de datos o para crear la funcionalidad que procesa millones de mensajes desde los dispositivos conectados. Use el lenguaje que prefiera (C#, Node.JS, Python, etc.). Pague solo el tiempo durante el que se ejecuta el código y confíe en Azure para escalar según sea necesario.

Funciones de Azure es una solución para ejecutar fácilmente pequeños fragmentos de código, o "funciones", en la nube. Simplemente, puede escribir el código que necesita para el problema en cuestión, sin preocuparse de toda la aplicación o la infraestructura para ejecutarlo. Esto puede hacer que el desarrollo sea aún más productivo y puede [empezar con su primera función](functions-create-first-azure-function.md) en cuestión de minutos.

## ¿Qué puedo hacer con las funciones?

Funciones de Azure es una excelente solución para procesar datos e integrar sistemas, trabajar con Internet de las cosas (IoT) y generar API simples y microservicios.

Las funciones incluyen una galería de plantillas para escenarios comunes, incluidos:

* Responder a una solicitud de Webhook de GitHub
* Cambiar el tamaño de una imagen que se ha cargado en Almacenamiento de Azure
* Trabajar con colas de procesamiento de pedidos
* ¡Y mucho más! 

Para una visión más profunda de cómo trabajan las funciones y algunas configuraciones de ejemplo, vea la [Referencia para desarrolladores](functions-reference.md).

## Características

Funciones de Azure es una plataforma completa lista para empresas que incluye características que facilitan las tareas de integración y conectividad complicadas. Con este importante conjunto de funcionalidades, los desarrolladores que usan Funciones de Azure pueden sera aún más productivos al centrarse únicamente en sus objetivos, en lugar de reunir las piezas de infraestructura.

Las siguientes características se incluyen con Funciones de Azure:
    
* **Opción de lenguaje**: funciones de escritura mediante C#, Node.js, Python, F #, PHP, lote, bash, Java o cualquier archivo ejecutable.  
* **Modelo de precios de pago por uso**: pague solo el tiempo durante el que se ejecuta el código. Vea la opción de Plan del servicio de aplicaciones dinámico en la [sección sobre precios](#pricing), a continuación.  
* **Traer sus propias dependencias**:Funciones admite NuGet y NPM, para que pueda usar sus bibliotecas favoritas.  
* **Seguridad integrada**: proteja las funciones desencadenadas por HTTP con los proveedores de OAuth como Azure Active Directory, Facebook, Google, Twitter y Cuenta Microsoft.  
* **Integración sin código**: fácil aprovechamiento de los servicios de Azure y ofertas de software como servicio (SaaS). Consulte la [sección integraciones](#integrations) a continuación para ver algunos ejemplos.  
* **Desarrollo flexible**: modifique las funciones con un editor en el portal o configure la integración continua e implemente el código mediante GitHub, Visual Studio Team Services, etc.  
* **Código abierto**: Funciones es de código abierto y está [disponible en GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

### <a name="integrations"></a>Integraciones

Funciones de Azure admite diversas integraciones con Azure y servicios de terceros. Puede usarlas para desencadenar su función e iniciar la ejecución o servir de entrada y salida para el código. La siguiente tabla muestra algunos integraciones de ejemplo compatibles con Funciones de Azure.

[AZURE.INCLUDE [proceso dinámico](../../includes/functions-bindings.md)]

## <a name="pricing"></a>¿Cuánto cuesta Funciones?

Hay dos maneras de ejecutar Funciones de Azure: mediante un Plan del Servicio de aplicaciones dinámico y el uso de un mediante un Plan del Servicio de aplicaciones clásico.

En un **Plan del servicio de aplicaciones dinámico** no tiene que preocuparse acerca de la administración de recursos. Cada vez que se ejecuta la función, Azure proporcionará todos los recursos informáticos necesarios. Pague solo el tiempo durante el que se ejecuta el código. Hay completos detalles de precios en la [página Precios de Funciones](/pricing/details/functions).

Un **Plan del servicio de aplicaciones clásico** permite ejecutar funciones igual que aplicaciones web, móviles y de API. Es una gran solución si ya está utilizando el Servicio de aplicaciones para otras aplicaciones: sus funciones pueden ejecutarse en el mismo plan sin ningún coste adicional. Puede encontrar detalles completos en la [página Precios de Servicio de aplicaciones](/pricing/details/app-service/).

## Problemas de informes

[AZURE.INCLUDE [Problemas de informes](../../includes/functions-reporting-issues.md)]

<!---HONumber=AcomDC_0413_2016-->