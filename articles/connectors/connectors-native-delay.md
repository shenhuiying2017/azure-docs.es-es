<properties
	pageTitle="Adición de retraso en aplicaciones lógicas | Microsoft Azure"
	description="Información general sobre las acciones de retraso y retraso hasta y cómo usarlas con una aplicación lógica de Azure."
	services=""
	documentationCenter=""
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Introducción a las acciones de retraso y retraso hasta

Con las acciones de retraso y retraso hasta, puede completar escenarios de flujo de trabajo como los siguientes.

Por ejemplo, puede:

- Esperar hasta un día de la semana para enviar una actualización de estado por correo electrónico.
- Retrasar el flujo de trabajo hasta que una llamada HTTP tenga tiempo para completarse antes de reanudarse y recuperar el resultado.

Para empezar a usar la acción de retraso en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Uso de las acciones de retraso

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](connectors-overview.md).

Esta es una secuencia de ejemplo de cómo usar un paso de retraso en una aplicación lógica:

1. Después de agregar un desencadenador, haga clic en **Nuevo paso** para agregar una acción.
2. Busque **delay** para mostrar las acciones de retraso. En este ejemplo, se seleccionará **Delay**.

	![Acciones de retraso](./media/connectors-native-delay/using-action-1.png)

3. Complete cualquiera de las propiedades de acción para configurar el retraso.

	![Configuración de retraso](./media/connectors-native-delay/using-action-2.png)

4. Haga clic en **Guardar** para publicar y activar la aplicación lógica.


## Detalles de la acción

El desencadenador de periodicidad tiene las siguientes propiedades que se pueden configurar.

### Acción de retraso

Esta acción retrasa la ejecución durante un determinado intervalo de tiempo. Un * significa que es un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Description|
|---|---|---|
|Recuento*|count|El número de unidades de tiempo de retraso|
|Unidad*|unit|La unidad de tiempo: `Second`, `Minute`, `Hour` o `Day`.|
<br>

### Acción de retraso hasta

Esta acción retrasa la ejecución hasta una fecha u hora especificada. Un * significa que es un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Description|
|---|---|---|
|Año*|timestamp|El año hasta el que retrasar (GMT)|
|Mes*|timestamp|El mes hasta el que retrasar (GMT)|
|Día*|timestamp|El día hasta el que retrasar (GMT)|
<br>


## Pasos siguientes

Ahora, pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) ahora. Puede explorar los demás conectores disponibles en aplicaciones consultando nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->