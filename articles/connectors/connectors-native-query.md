<properties
	pageTitle="Adición de la acción de consulta a las aplicaciones lógicas | Microsoft Azure"
	description="En este artículo se muestra información general sobre las acciones de consulta para realizar acciones como Filter array (Filtrar matriz)."
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

Mediante la acción de consulta puede utilizar lotes y matrices para realizar flujos de trabajo como los siguientes:

- Crear una tarea para todos los registros de alta prioridad desde una base de datos.
- Guardar todos los datos adjuntos en PDF de correos electrónicos en un blob de Azure.

Para empezar a usar la acción de consulta en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Uso de la acción de consulta

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](connectors-overview.md).

En estos momentos, la acción de consulta tiene una operación expuesta en el diseñador que se denomina Filter array (Filtrar matriz). Gracias a ella, podrá consultar una matriz y devolver un conjunto de resultados filtrados.

Este es el procedimiento para agregarlo en una aplicación lógica:

1. Seleccione el botón **Nuevo paso**.
2. Elija **Agregar una acción**.
3. En el cuadro de búsqueda de acciones, escriba **Filter** para mostrar la acción **Filter array**.

	![Seleccione la acción de consulta](./media/connectors-native-query/using-action-1.png)

4. Seleccione la matriz que se va a filtrar. (La captura de pantalla siguiente muestra la matriz de resultados de búsqueda de Twitter).
5. Cree una condición para evaluar en cada elemento. (La captura de pantalla siguiente filtra tweets de usuarios que tienen más de 100 seguidores).

	![Complete la acción de consulta](./media/connectors-native-query/using-action-2.png)

	La acción generará una nueva matriz que contiene solo los resultados que cumplen los requisitos de filtro.
6. Haga clic en la esquina superior izquierda de la barra de herramientas para guardarla; la aplicación lógica se guardará y se publicará (activará).

## Acción de consulta

Aquí se muestran los detalles de la acción que admite este conector. El conector tiene una acción posible.

|Acción|Description|
|---|---|
|Filter array|Evalúa una condición de cada elemento de una matriz y devuelve los resultados|

## Detalles de la acción

La acción de consulta incluye una acción posible. Las tablas siguientes describen los campos de entrada obligatorios y opcionales para la acción y los detalles de salida correspondientes asociados a su uso.

### Filter array
Los siguientes son los campos de entrada para la acción que realiza una solicitud de salida HTTP. Un * significa que es un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Description|
|---|---|---|
|De*|from|La matriz que se va a filtrar.|
|Condición*|donde|La condición que se va a evaluar en cada elemento.|
<br>

### Detalles de salida

Los detalles de la salida de la respuesta HTTP son los siguientes.

|Nombre de propiedad|Tipo de datos|Description|
|---|---|---|
|Matriz filtrada|array|Una matriz que contiene un objeto de cada resultado filtrado|

## Pasos siguientes

Ahora, pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Puede explorar los demás conectores disponibles en aplicaciones lógicas consultando nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0817_2016-->