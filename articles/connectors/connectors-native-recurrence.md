<properties
	pageTitle="Adición del desencadenador de periodicidad en aplicaciones lógicas | Microsoft Azure"
	description="Información general sobre el desencadenador de periodicidad y cómo utilizarlo con una aplicación lógica de Azure."
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="app-service-logic"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Introducción al desencadenador de periodicidad

Con el desencadenador de periodicidad, puede crear flujos de trabajo eficaces en la nube, como:

- Programar un flujo de trabajo para ejecutar un procedimiento almacenado de SQL cada día.
- Enviar por correo electrónico un resumen de todos los tweets de la última semana sobre un determinado hashtag.

Para empezar a usar la acción de periodicidad en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Uso de un desencadenador de periodicidad

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](connectors-overview.md).

Esta es una secuencia de ejemplo de cómo configurar un desencadenador de periodicidad en una aplicación lógica.

1. Agregue el desencadenador de **periodicidad** como primer paso en una aplicación lógica.
1. Rellene los parámetros para el intervalo de periodicidad.
1. La aplicación lógica iniciará una ejecución después de cada intervalo de tiempo.

![Desencadenador HTTP](./media/connectors-native-recurrence/using-trigger.png)

---

## Detalles técnicos

A continuación se muestran los detalles del desencadenador de periodicidad y los parámetros que puede configurar.

### Detalles del desencadenador

El desencadenador de periodicidad tiene las siguientes propiedades que se pueden configurar.

#### Periodicidad
Active una aplicación lógica después de un intervalo de tiempo especificado. Un asterisco (*) indica un campo obligatorio.

|Display Name (Nombre para mostrar)|Nombre de propiedad|Descripción|
|---|---|---|
|Frecuencia*|frequency|La unidad de tiempo: `Second`, `Minute`, `Hour`, `Day` o `Year`.|
|Intervalo*|interval|Intervalo de frecuencia definido para la periodicidad.|
|Zona horaria|timeZone|Si se especifica una hora de inicio sin una diferencia horaria con UTC, se usará esta zona horaria.|
|Hora de inicio|startTime|Hora de inicio en [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Si se especifica una hora de inicio sin una diferencia horaria con UTC, se usará esta zona horaria.|
<br>


## Pasos siguientes

A continuación se muestran detalles sobre cómo avanzar con aplicaciones lógicas y nuestra comunidad.

## Creación de una aplicación lógica

Pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) ahora. Explore los demás conectores disponibles en aplicaciones lógicas en nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0720_2016-->