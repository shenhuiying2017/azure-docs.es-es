<properties
   pageTitle="Aplicaciones que diagnostican Aplicaciones lógicas | Microsoft Azure"
   description="Enfoques comunes para saber dónde se producen los errores de las aplicaciones"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/18/2016"
   ms.author="jehollan"/>
   
# Diagnóstico de aplicaciones lógicas

Si surgen problemas o errores en alguna aplicación lógica, puede adoptar varios enfoques para conocer mejor de dónde proceden dichos errores.

## Herramientas del Portal de administración

El Portal de Azure proporciona muchas herramientas para diagnosticar todas las aplicaciones lógicas en cada paso.

### Historial de desencadenamiento

Cada aplicación lógica tiene al menos un desencadenador. Si observa que hay aplicaciones que se activan, debe empezar por mirar el historial de desencadenamiento. A dicho historial se puede acceder desde la hoja principal de la aplicación lógica, que aparece a continuación:

![][1]

Aquí se enumeran todos los intentos de desencadenamiento que ha realizado la aplicación lógica. Se puede hacer clic en todas y cada una de estas entradas para acceder al siguiente nivel de detalle (en concreto en aquellas entradas o salidas que generó el intento de desencadenamiento). Si observa los desencadenadores con 'error', debe hacer clic en el intento de desencadenamiento y aumentar el detalle del vínculo de salidas para conocer los mensajes de error que se puedan generar (por ejemplo: credenciales de FTP no válidas).

Estos son los diferentes estados que puede ver:

* Omitido: sondeó el punto de conexión para comprobar que si había datos disponibles y obtuvo la respuesta de que no había datos disponibles.
* Correcto: el desencadenador recibió una respuesta de que los datos estaban disponibles. Puede proceder de un desencadenador manual, un desencadenador de periodicidad o el desencadenador de sondeo. Es probable que vaya acompañado de 'Activado', pero puede que no sea así si tiene una condición o splitOn en la vista de código que no se hayan satisfecho.
* Error: se generó un error.

#### Inicio manual de un desencadenador

Si desea que la aplicación lógica para comprobar si hay algún desencadenador disponible inmediatamente (sin que haya que esperar a la siguiente repetición), puede hacer clic el botón **Select Trigger** (Seleccionar desencadenador) en la hoja principal para forzar una comprobación. Por ejemplo, si hace clic en él con un desencadenador de Dropbox, provocará que el flujo de trabajo sondee Dropbox inmediatamente en busca de archivos nuevos.

### Historial de ejecuciones

Cada vez que se activa un desencadenador, da como resultado una ejecución. A dichas ejecuciones se puede acceder desde la hoja principal y contienen mucha información útil para saber lo que ha sucedido durante el flujo.

![][2]

Una ejecución puede tener cualquiera de los siguientes estados:

* Correcto: todas las acciones se realizaron correctamente, o si se produjo un error, lo controló una acción posteriormente en el flujo de trabajo (es decir, se estableció que se ejecutara una acción después de una acción de 'Error').
* Error: al menos una acción tenía un error que no se ha controlado mediante una acción posteriormente en el flujo de trabajo.
* Cancelado: el flujo de trabajo se estaba ejecutando, pero recibió una solicitud de cancelación.
* En ejecución: si un flujo de trabajo se ejecuta actualmente. Esto puede suceder en los flujos que se limitan, así como con el plan del Servicio de aplicaciones actual. Para más información, consulte los límites de las acciones acción en el [página de precios](https://azure.microsoft.com/pricing/details/app-service/plans/). La configuración de Diagnósticos (los gráficos que están debajo del historial de ejecuciones) también le permitirá conocer los eventos de limitación que se producen.

Cuando se encuentre dentro de una ejecución, puede profundizar para conocer más detalles.

#### Salidas del desencadenador

Salidas del desencadenador mostrará los datos que se ha recibido del desencadenador. Esto puede resultar útil para comprender si todas las propiedades se devuelven como se esperaba.

>[AZURE.NOTE] Puede resultar de gran ayuda saber la forma en que Aplicaciones lógicas [controla los diferentes tipos de contenido](app-service-logic-content-type.md) si se ve cualquier contenido que no conoce.

![][3]

#### Entradas y salidas de acciones

Puede profundizar en las entradas y salidas que recibió una acción. Esto resulta útil para conocer el tamaño y la forma de las salidas, así como para ver los mensajes de error que se han generado.

![][4]

## Depuración de tiempo de ejecución de flujo de trabajo

Además de supervisar las entradas, las salidas y los desencadenadores de una ejecución, puede resultar útil agregar algunos pasos a un flujo de trabajo para facilitar su depuración. Una herramienta eficaz que se puede agregar en forma de paso de un flujo de trabajo es [RequestBin](http://requestb.in). RequestBin permite configurar un inspector de solicitudes HTTP para conocer exactamente el tamaño, la forma y el formato de una solicitud HTTP. Puede crear RequestBin y pegar la dirección URL de una acción HTTP POST de la aplicación lógica, con cualquier cuerpo de contenido que se desee probar (una expresión, la salida de otro paso, etc.). Después de ejecutar la aplicación lógica puede actualizar RequestBin para ver cómo se formó la solicitud cuando se generó desde el motor de aplicaciones lógicas.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0601_2016-->