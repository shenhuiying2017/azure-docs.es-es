<properties
	pageTitle="Adición de la acción de consulta a las aplicaciones lógicas | Microsoft Azure"
	description="En este artículo se muestra información general sobre las acciones de consulta para realizar acciones como Filtrar matriz."
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
   ms.date="07/20/2016"
   ms.author="jehollan"/>

# Introducción a la acción de consulta

Con la acción de consulta puede utilizar lotes y matrices para realizar flujos de trabajo como los siguientes:

- Crear una tarea para todos los registros de alta prioridad desde una base de datos
- Guardar todos los datos adjuntos en PDF de correos electrónicos en el Blob de Azure

Para empezar a usar la acción de consulta en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Uso de la acción de consulta
	
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones.](connectors-overview.md) En estos momentos, la acción de consulta tiene una operación expuesta en el diseñador: Filtrar matriz. Gracias a ella, podrá consultar una matriz y devolver un conjunto de resultados filtrados. Este es el procedimiento para agregarlo en una aplicación lógica:

1. Haga clic en el botón **Nuevo paso**.
1. Elija **Agregar una acción**.
1. En el cuadro de búsqueda de la acción, escriba "Filtrar" para mostrar la acción **Filtrar matriz**.

	![Acción de consulta de selección](./media/connectors-native-query/using-action-1.png)

1. Seleccione una matriz que quiera filtrar (en la captura de pantalla siguiente se muestra la matriz de resultados de búsqueda de Twitter).
1. Cree una condición para evalúan cada elemento (en la captura de pantalla siguiente, se filtran los tweets de los usuarios que tienen más de 100 seguidores).

	![Acción de consulta de finalización](./media/connectors-native-query/using-action-2.png)

1. La acción generará una nueva matriz que contiene solo los resultados que cumplen los requisitos de filtro.
1. Haga clic en Guardar en la esquina superior izquierda de la barra de herramientas; la aplicación lógica se guardará y se publicará (activará).

---

## Detalles técnicos

A continuación, se muestran los detalles de las acciones que admite este conector.

## Acciones de consulta

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones.](connectors-overview.md) El conector tiene 1 acción posible.

|Acción|Descripción|
|---|---|
|Filtar matriz|Evalúa una condición de cada elemento de una matriz y devuelve los resultados|

### Detalles de la acción

La acción de consulta incluye 1 acción posible. A continuación, se incluye información acerca de cada una de las acciones, los campos de entrada obligatorios y opcionales, y los detalles de salida correspondientes asociados a su uso.

#### Filtar matriz
Realice una solicitud de salida HTTP. Un asterisco (*) indica un campo obligatorio.

|Display Name (Nombre para mostrar)|Nombre de propiedad|Descripción|
|---|---|---|
|De*|from|La matriz que se va a filtrar.|
|Condición*|donde|La condición que se va a evaluar en cada elemento.|
<br>

**Detalles de salida**

Respuesta HTTP

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Matriz filtrada|array|Matriz que contiene un objeto de cada resultado filtrado.|

---

## Pasos siguientes

A continuación se muestran detalles sobre cómo avanzar con aplicaciones lógicas y nuestra comunidad.

## Creación de una aplicación lógica

Pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) ahora. Puede explorar los demás conectores disponibles en aplicaciones consultando nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->