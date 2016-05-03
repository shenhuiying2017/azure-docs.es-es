<properties
   pageTitle="Creación de su primera función de Azure | Microsoft Azure"
   description="Cree su primera función de Azure, una aplicación sin servidor, en menos de dos minutos."
   services="functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="hero-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="04/21/2016"
   ms.author="glenga"/>

# Creación de su primera función de Azure

##Información general
Funciones de Azure es una experiencia de proceso a petición basada en eventos que amplía la plataforma de aplicaciones existente de Azure con funcionalidades para implementar código desencadenado por eventos que ocurren en otros servicios de Azure, productos de SaaS y en sistemas locales. Con Funciones de Azure, sus aplicaciones escalan en función de la demanda y solo hay que pagar por los recursos consumidos. Funciones de Azure permite crear unidades de código programadas o desencadenadas que se implementan en varios lenguajes de programación. Para más información acerca de Funciones de Azure, consulte [Información general sobre Funciones de Azure](functions-overview.md).

En este tema se muestra cómo utilizar el inicio rápido de Funciones de Azure en el portal de Funciones de Azure para crear una sencilla función de Node.js "hello world" invocada por un desencadenador de HTTP. También puede ver un vídeo de corta duración en el que podrá seguir la realización de estos pasos en el portal.

## Visualización del vídeo

El siguiente vídeo muestra cómo realizar los pasos básicos de este tutorial.

[AZURE.VIDEO create-your-first-azure-function]

##Creación de una función desde el inicio rápido

Una aplicación de función hospeda la ejecución de sus funciones en Azure. Siga estos pasos para crear una nueva aplicación de función, así como la nueva función. Para poder crear la primera función, tiene que tener una cuenta activa de Azure. Si aún no tiene una cuenta de Azure, [tiene a su disposición la creación de una cuenta gratis](https://azure.microsoft.com/free/).

1. Vaya al [Portal de Funciones de Azure](https://functions.azure.com/signin) e inicie sesión con su cuenta de Azure.

2. Escriba un **Nombre** único para la nueva aplicación de función o acepte el que se ha generado, seleccione la **Región**, a continuación, haga clic en **Create + get started** (crear + comenzar).

3. En la pestaña **Inicio rápido**, haga clic en **WebHook + API** > **Create a function** (Crear una función). Se crea una nueva función predefinida de Node.js.

4. (Opcional) En este momento en el inicio rápido, puede realizar un recorrido rápido de las características de Funciones de Azure en el portal. Una vez que haya realizado u omitido el recorrido, puede probar la nueva función mediante el desencadenador de HTTP.

##Prueba de la función

Puesto que los inicios rápidos de Funciones de Azure contienen código funcional, puede probar la nueva función inmediatamente.

1. En la pestaña **Desarrollar**, revise la ventana **Código** y observe que este código Node.js espera una solicitud HTTP con un valor de *nombre* pasado en el cuerpo del mensaje o en una cadena de consulta. Cuando se ejecuta la función, este valor se devuelve en el mensaje de respuesta.

2. Desplácese hacia abajo hasta el cuadro de texto **Cuerpo de la solicitud**, cambie el valor de la propiedad *nombre* a su nombre y haga clic en **Ejecutar**. Verá que se desencadena la ejecución a partir de una solicitud HTTP de prueba, se escribe información en los registros de streaming y se muestra la respuesta "hello" en **Output** (salida).

3. Para desencadenar la ejecución de la misma función desde otra ventana o pestaña del explorador, copie el valor **Function URL** (dirección URL de la función) de la pestaña **Desarrollar** y péguelo en la barra de direcciones, luego agregue el valor de la cadena de consulta `&name=yourname` y presione ENTRAR. La misma información se escribe en los registros y el explorador muestra la respuesta "hello" como antes.

##Pasos siguientes

Este inicio rápido demuestra una ejecución muy sencilla de una función desencadenada de HTTP básica. Consulte estos temas para más información acerca de cómo aprovechar el potencial de Funciones de Azure en sus aplicaciones.

+ [Azure Functions developer reference](functions-reference.md) (Referencia para desarrolladores de Funciones de Azure) contiene las referencias del programador para codificar las funciones y definir los desencadenadores y los enlaces.
+ [Testing Azure Functions](functions-test-a-function.md) (Prueba de Funciones de Azure) Este artículo describe las diversas herramientas y técnicas para probar sus funciones.
+ [How to scale Azure Functions](functions-scale.md) (Cómo escalar Funciones de Azure) aborda los planes de servicio disponibles con Funciones de Azure, incluido el plan de servicio dinámico, y cómo elegir el plan adecuado. 
+ [¿Qué es Servicios de aplicaciones de Azure?](../app-service/app-service-value-prop-what-is.md) Funciones de Azure aprovecha la plataforma de Servicio de aplicaciones de Azure para obtener funcionalidad básica como implementaciones, variables de entorno y diagnósticos. 

[AZURE.INCLUDE [Nota de introducción](../../includes/functions-get-help.md)]

<!---HONumber=AcomDC_0427_2016-->