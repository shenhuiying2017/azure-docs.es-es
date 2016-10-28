<properties
	pageTitle="Información general de API de exportación de Mobile Engagement"
	description="Aprender los conceptos básicos sobre la exportación de datos sin procesar generados por los dispositivos del usuario para aprovecharlos en sus propias herramientas."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="kpiteira"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile"
	ms.date="04/26/2016"
	ms.author="kpiteira"/>

# Información general de API de exportación de Mobile Engagement

## Introducción

Con en este documento aprenderá los conceptos básicos sobre la exportación de datos sin procesar generados por los dispositivos del usuario para aprovecharlos en sus propias herramientas.

## Requisitos previos

La exportación de datos sin procesar desde Mobile Engagement requiere:

- Configuración de autenticación de API para poder usar las API (consulte la [configuración manual de la autenticación](mobile-engagement-api-authentication-manual.md)),
- Usar las API de REST o el [SDK de .net](mobile-engagement-dotnet-sdk-service-api.md),
- Una cuenta de Almacenamiento de Azure.

>[AZURE.NOTE] También recomendamos el excelente [Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com/), al menos durante la fase de desarrollo, ya que proporciona una IU fácil de usar para interactuar con Almacenamiento de Azure.

## ¿Qué se puede exportar?

Mobile Engagement permite a sus usuarios recopilar muchos tipos de datos y, por tanto, tiene varios tipos de exportación adecuados para estos tipos de datos diferentes. Hay 2 tipos esenciales de exportación:

- Instantánea: se usa normalmente para exportar datos que representan un estado y para los que Mobile Engagement no tiene un historial. Esto incluye, por ejemplo, etiquetas (información de la aplicación), tokens o comentarios de campañas de inserción. En consecuencia, estos tipos de exportación no están relacionados con una fecha.
- Históricos: este tipo de exportación se usa para datos que se acumulan a lo largo del tiempo, como eventos o actividades, por ejemplo.

La tabla siguiente describe exhaustivamente todas las exportaciones posibles:

| Tipo de exportación | Tipo de datos | Descripción |
|-------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Instantánea | Insertar | Genera una exportación de comentarios de campañas de inserción por identificador de dispositivo o identificador de usuario |
| Instantánea | Etiqueta | Genera una exportación de las etiquetas (información de la aplicación) asociadas a cada dispositivo |
| Instantánea | Dispositivo | Genera una exportación de la mayoría de los datos sobre dispositivos, como los aspectos técnicos (modelo, configuración regional, zona horaria, etc..), las etiquetas, la primera que se ve, etc. |
| Instantánea | Se necesita el cifrado de tokens | Genera una exportación de todos los tokens válidos |
| Históricos | Actividad | Genera una exportación de todas las actividades para cada dispositivo en un período de tiempo determinado |
| Históricos | Evento | Genera una exportación de todas las actividades para cada dispositivo en un período de tiempo determinado |
| Históricos | Trabajo | Genera una exportación de todos los trabajos para cada dispositivo en un período de tiempo determinado |
| Históricos | Error | Genera una exportación de todos los errores para cada dispositivo en un período de tiempo determinado |

## ¿Cómo funciona?

Las exportaciones son tareas de larga ejecución que puede producir archivos de datos grandes. Por ese motivo, no se pueden invocar para devolver inmediatamente un archivo para descargar. Para exportar datos desde Mobile Engagement, tendrá que crear un **trabajo de exportación** a través de la API donde, por lo general, debe especificar:

- El tipo de exportación (instantánea o histórico)
- El tipo de datos,
- El **contenedor de Almacenamiento de Azure** (incluida una SAS válida con acceso de escritura) donde se escribirá el resultado de la exportación.

Tenga en cuenta que el trabajo puede tardar varios minutos en iniciarse y después, su ejecución, puede durar desde unos pocos segundos para las aplicaciones pequeñas hasta varias horas para aplicaciones con una gran cantidad de usuarios o actividades.

Una vez que se crea el trabajo, es posible comprobar su estado para ver si todavía se está ejecutando o si se ha completado.

Cuando el trabajo se complete CORRECTAMENTE, el archivo de datos resultante estará disponible en el contenedor de almacenamiento proporcionado.

<!---HONumber=AcomDC_0504_2016-->