<properties
   pageTitle="Diagnóstico de errores de aplicaciones lógicas | Microsoft Azure"
   description="Métodos comunes para detectar dónde se producen los errores de las aplicaciones lógicas"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/18/2016"
   ms.author="jehollan"/>

# Diagnóstico de errores de aplicaciones lógicas

Si surgen problemas o errores con la característica Aplicaciones lógicas del Servicio de aplicaciones de Azure, puede adoptar varios enfoques para conocer mejor de dónde proceden dichos errores.

## Herramientas del Portal de Azure

El Portal de Azure proporciona muchas herramientas para diagnosticar todas las aplicaciones lógicas en cada paso.

### Historial de desencadenamiento

Cada aplicación lógica tiene al menos un desencadenador. Si observa que las aplicaciones no se activan, debe empezar por mirar el historial de desencadenamiento. A dicho historial se puede acceder desde la hoja principal de la aplicación lógica.

![Ubicación del historial de desencadenamiento][1]

Aquí se enumeran todos los intentos de desencadenamiento que ha realizado la aplicación lógica. Puede hacer clic en cada uno de ellos para obtener el siguiente nivel de detalle (en concreto, las entradas o salidas generadas por el intento de desencadenamiento). Si ve desencadenadores con error, haga clic en el intento de desencadenamiento y aumente el detalle del vínculo **Salidas** para conocer los mensajes de error que pueden haberse generado (por ejemplo: credenciales de FTP no válidas).

Estos son los diferentes estados que puede ver:

* **Omitido**. El desencadenador sondeó el punto de conexión para buscar datos y recibió como respuesta que los datos no estaban disponibles.
* **Correcto**. El desencadenador recibió como respuesta que los datos estaban disponibles. Esta podría proceder de un desencadenador manual, un desencadenador de periodicidad o un desencadenador de sondeo. Es probable que vaya acompañado de un estado **Fired**, pero puede que no sea así si tiene una condición o un comando SplitOn en la vista de código que no se haya satisfecho.
* **Error**. Se generó un error.

#### Inicio manual de un desencadenador

Si desea que la aplicación lógica compruebe si hay algún desencadenador disponible de inmediato (sin que haya que esperar a la siguiente repetición), puede hacer clic el botón **Select Trigger** (Seleccionar desencadenador) en la hoja principal para forzar una comprobación. Por ejemplo, si hace clic en este vínculo con un desencadenador de Dropbox, provocará que el flujo de trabajo sondee Dropbox inmediatamente en busca de archivos nuevos.

### Historial de ejecuciones

Cada desencadenador que se activa da como resultado una ejecución. Puede tener acceso a información de la ejecución desde la hoja principal, que contiene una gran cantidad de datos que pueden ser útiles para comprender qué ha sucedido durante el flujo de trabajo.

![Ubicación del historial de ejecuciones][2]

Una ejecución muestra uno de los siguientes estados:

* **Correcto**. Todas las acciones se realizaron correctamente o, si se produjo un error, este se controló mediante una acción que se produjo más adelante en el flujo de trabajo. Es decir, se controló con una acción que se configuró para ejecutarse después de una acción con error.
* **Error**. Se produjo un error en al menos una acción que no se controló mediante una acción posteriormente en el flujo de trabajo.
* **Cancelado**. El flujo de trabajo se estaba ejecutando pero recibió una solicitud de cancelación.
* **En ejecución**. El flujo de trabajo se ejecuta actualmente. Esto puede darse en los flujos que se limitan o debido al plan del Servicio de aplicaciones actual. Consulte los límites de las acciones acción en la [página de precios](https://azure.microsoft.com/pricing/details/app-service/plans/). La configuración de diagnósticos (los gráficos que figuran a continuación el historial de ejecución) también pueden proporcionar información sobre los eventos de limitación que se están produciendo.

Cuando se encuentre ante un historial de ejecución, puede profundizar en él para obtener más detalles.

#### Salidas del desencadenador

Las salidas del desencadenador mostrarán los datos que se han recibido del desencadenador. Esto puede ayudarle a determinar si se devolvieron todas las propiedades del modo esperado.

>[AZURE.NOTE] Puede resultar de gran ayuda saber la forma en que la característica Aplicaciones lógicas [controla los diferentes tipos de contenido](app-service-logic-content-type.md) si se ve cualquier contenido que no conoce.

![Ejemplos de salidas del desencadenador][3]

#### Entradas y salidas de acciones

Puede profundizar en las entradas y salidas que recibió una acción. Esto resulta útil para conocer el tamaño y la forma de las salidas, así como para ver los mensajes de error que se han generado.

![Entradas y salidas de acciones][4]

## Depuración de tiempo de ejecución de flujo de trabajo

Además de supervisar las entradas, las salidas y los desencadenadores de una ejecución, puede resultar útil agregar algunos pasos a un flujo de trabajo para facilitar su depuración. Una herramienta eficaz que se puede agregar en forma de paso de un flujo de trabajo es [RequestBin](http://requestb.in). Mediante el uso de RequestBin, puede configurar un inspector de la solicitud HTTP para determinar el tamaño exacto, la forma y el formato de una solicitud HTTP. Puede crear un nuevo RequestBin y pegar la dirección URL de una acción HTTP POST de la aplicación lógica junto con el contenido del cuerpo que se desee probar (una expresión, la salida de otro paso, etc.). Después de ejecutar la aplicación lógica puede actualizar RequestBin para ver cómo se formó la solicitud cuando se generó desde el motor de Aplicaciones lógicas.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0727_2016-->