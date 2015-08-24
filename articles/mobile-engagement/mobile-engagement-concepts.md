<properties
	pageTitle="Conceptos de Mobile Engagement"
	description="Conceptos de Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/10/2015"
	ms.author="piyushjo" />

#Conceptos de Mobile Engagement de Azure

Mobile Engagement define algunos conceptos comunes para todas las plataformas compatibles. En esta página se describen brevemente esos conceptos.

La página es un punto de partida ideal si no está familiarizado con Mobile Engagement. No obstante, asegúrese de leer también la documentación específica de la plataforma que usa porque en esta se definirán los conceptos descritos en esta página en mayor detalle y con ejemplos, así como las posibles limitaciones.

##Dispositivos y usuarios
Mobile Engagement identifica a los usuarios mediante la generación de un identificador único para cada dispositivo, Este identificador se denomina identificador de dispositivo (o `deviceid`). Se genera de tal forma que todas las aplicaciones que se ejecutan en el mismo dispositivo comparten el mismo identificador de este tipo.

Esto significa, implícitamente, que Mobile Engagement considera que un dispositivo pertenece exactamente a un usuario y, por lo tanto, usuarios y dispositivos son conceptos equivalentes.

##Sesiones y actividades
Una sesión es un uso de la aplicación por parte de un usuario, desde el momento en que comienza a usarla hasta que se detiene.

Una actividad es un uso de una subparte determinada de la aplicación por un usuario (suele ser una pantalla, pero puede ser cualquier elemento adecuado para la aplicación).

Un usuario solo puede realizar una actividad a la vez.

Una actividad se identifica mediante un nombre (limitado a 64 caracteres) y, opcionalmente, puede insertar algunos datos adicionales (con un límite de 1024 bytes).

Las sesiones se procesan automáticamente a partir de la secuencia de actividades realizadas por los usuarios: una sesión se inicia cuando el usuario comienza su primera actividad y se detiene cuando este finaliza su última actividad. Esto significa que no es necesario iniciar o detener las sesiones explícitamente, pero las actividades sí: si no se notifica actividad alguna, tampoco lo hace ninguna sesión.

##Eventos
Los eventos se usan para notificar acciones instantáneas (como un botón que se presiona o la lectura de artículos por parte de los usuarios).

Un evento puede estar relacionado con la sesión actual, con un trabajo en ejecución o bien ser independiente.

Se identifica mediante un nombre (limitado a 64 caracteres) y, opcionalmente, puede insertar algunos datos adicionales (con un límite de 1024 bytes).

##Error
Los errores se usan para notificar problemas detectados correctamente por la aplicación (como acciones de usuario incorrectas o errores de llamada a la API).

Un error puede estar relacionado con la sesión actual, con un trabajo en ejecución o bien ser independiente.

Se identifica mediante un nombre (limitado a 64 caracteres) y, opcionalmente, puede insertar algunos datos adicionales (con un límite de 1024 bytes).

##Trabajo
Los trabajos se usan para notificar acciones que tienen una duración (como la duración de las llamadas API, el tiempo que se muestran los anuncios, la duración de las tareas en segundo plano o de las acciones del usuario).

Un trabajo no está relacionado con una sesión, porque la tarea se puede realizar en segundo plano sin interacción del usuario.

Se identifica mediante un nombre (limitado a 64 caracteres) y, opcionalmente, puede insertar algunos datos adicionales (con un límite de 1024 bytes).

##Bloqueo
El SDK de Mobile Engagement emite bloqueos automáticamente para notificar errores de la aplicación (es decir, problemas no detectados por la aplicación que hacen que esta se bloquee).

##Información de la aplicación
La información de la aplicación (o `app info`) se usa para etiquetar a los usuarios, es decir, para asociar determinados datos a los usuarios de una aplicación (de forma similar a las cookies web, con la excepción de que la información de la aplicación se almacena en el lado del servidor en la plataforma Mobile Engagement).

Para registrar información de la aplicación se puede usar la API del SDK de Mobile Engagement o la API de dispositivos de la plataforma Mobile Engagement.

Este tipo de información es un par clave-valor asociado a un dispositivo. La clave es el nombre de la información de la aplicación(limitado a 64 letras [a-zA-Z], números [0-9] y caracteres de subrayado [\_] ASCII). El valor (limitado a 1024 caracteres) puede ser cualquier cadena, entero, fecha (aaaa-MM-dd) o valor booleano (true o false).

Se puede asociar un número cualquiera de informaciones de la aplicación a un dispositivo dentro de los límites definidos por los términos de precios de Mobile Engagement. Para una clave determinada, Mobile Engagement solo realiza un seguimiento del conjunto de valores más reciente (sin historial). Al establecer o cambiar el valor de una información de aplicación, se fuerza a Mobile Engagement a volver a evaluar los criterios de audiencia establecidos en esta (si los hay), lo que significa que esa información de la aplicación puede usarse para desencadenar inserciones en tiempo real.

##Datos adicionales
Los datos adicionales (o extras) son datos arbitrarios que pueden asociarse a los eventos, errores, actividades y trabajos.

Su estructura es similar a la de los objetos JSON: se componen de un árbol de pares clave-valor. Las claves tienen un límite de 64 letras [a-zA-Z], números [0-9] y caracteres de subrayado [\_] ASCII y el tamaño total de los extras se limita a 1024 caracteres (una vez codificados en JSON por el SDK de Mobile Engagement).

El árbol completo de pares clave-valor se almacena como objeto JSON. Sin embargo, solo se descompone el primer nivel de claves-valores para que puedan tener acceso directamente determinadas funciones avanzadas, como Segmentos (por ejemplo, puede definir sin problemas un segmento “fans ciencia ficción” compuesto por todos los usuarios que hayan enviado al menos 10 veces el evento denominado “contenido\_visualizado” con la clave extra “tipo\_contenido” establecida en el valor “ciencia ficción” durante el último mes). Por tanto, se recomienda enviar solamente extras formados por listas sencillas de pares clave-valor con valores escalares (por ejemplo, cadenas, fechas, enteros o valores booleanos).
 

<!---HONumber=August15_HO7-->