---
title: "Informática de base de datos sin servidor: Azure Functions y Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo Azure Cosmos DB y Azure Functions se pueden usar en conjunto para crear aplicaciones informáticas sin servidor basadas en eventos."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mimig
ms.openlocfilehash: aeef39294bbf3ad4192fe116c6972e52bfa1c816
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="azure-cosmos-db-serverless-database-computing-using-azure-functions"></a>Azure Cosmos DB: informática de base de datos sin servidor con Azure Functions

La informática sin servidor se trata de la capacidad de centrarse en partes de lógica individuales repetibles y sin estado. Estas partes no requieren administración de la infraestructura y solo usan recursos durante los segundos o milisegundos durante los cuales se ejecutan. La base del movimiento de la informática sin servidor son las funciones, las que están disponibles en el ecosistema de Azure mediante [Azure Functions](https://azure.microsoft.com/services/functions).

Con la integración nativa entre [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) y Azure Functions, puede crear desencadenadores de base de datos, enlaces de entrada y enlaces de salida directamente desde la cuenta de Azure Cosmos DB. Con Azure Functions y Azure Cosmos DB, puede crear e implementar aplicaciones sin servidor basadas en eventos con acceso de baja latencia a los datos enriquecidos para una base de usuarios global.

## <a name="overview"></a>Información general

Azure Cosmos DB y Azure Functions permite integrar las aplicaciones sin servidor y bases de datos de las maneras siguientes:

* Cree un **desencadenador de Azure Cosmos DB** basado en eventos en una instancia de Azure Function. Este desencadenador se basa en secuencias de [fuente de cambios](change-feed.md) para supervisar los cambios en el contenedor de Azure Cosmos DB. Cuando se hace algún cambio en un contenedor, la secuencia de fuente de cambios se envía al desencadenador, que invoca la instancia de Azure Function.
* También puede enlazar una instancia de Azure Function a una colección de Azure Cosmos DB mediante un **enlace de entrada**. Los enlaces de entrada leen los datos de un contenedor cuando se ejecuta una función.
* Enlace una función a una colección de Azure Cosmos DB mediante un **enlace de salida**. Los enlaces de salida escriben datos en un contenedor cuando se completa una función.

> [!NOTE]
> En este momento, el desencadenador de Azure Cosmos DB y los enlaces de entrada y de salida solo funcionan con cuentas de SQL API, y API Graph.

En el diagrama siguiente se muestran cada una de estas tres integraciones: 

![Integración de Azure Cosmos DB y Azure Functions](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

El desencadenador de Azure Cosmos DB, el enlace de entrada y el enlace de salida se pueden usar en las combinaciones siguientes:
* Un desencadenador de Azure Cosmos DB se puede usar con un enlace de salida a otro contenedor de Azure Cosmos DB. Una vez que una función realiza una acción en un elemento de la fuente de cambios, puede escribirlo en otro contenedor (si lo escribe en el mismo contenedor de origen, se crearía efectivamente un bucle recursivo). O bien puede usar un desencadenador de Azure Cosmos DB para migrar de manera eficaz todos los elementos modificados de un contenedor a otro, mediante un enlace de salida.
* Los enlaces de entrada y los enlaces de salida de Azure Cosmos DB se pueden usar en la misma instancia de Azure Function. Esto funciona bien cuando desea encontrar ciertos datos con el enlace de entrada, modificarlos en la instancia de Azure Function y, luego, guardarlos en el mismo contenedor o en uno distinto, después de la modificación.
* Un enlace de entrada a un contenedor de Azure Cosmos DB se puede usar en la misma función que un desencadenador de Azure Cosmos DB y también se puede usar con un enlace de salida o sin él. Podría usar esta combinación para aplicar información actualizada sobre el cambio de moneda (extraído con un enlace de entrada a un contenedor de intercambio) a la fuente de cambios de los pedidos nuevos en el servicio del carro de la compra. El total actualizado del carro de la compra, con la conversión de moneda actual aplicada, se puede escribir en un tercer contenedor con un enlace de salida.

## <a name="use-cases"></a>Casos de uso

Los casos de uso siguientes muestran algunas formas en las que puede aprovechar al máximo los datos de Azure Cosmos DB, mediante la conexión los datos a instancias de Azure Functions basadas en eventos.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Caso de uso de IoT: desencadenador de Azure Cosmos DB y enlace de salida

En las implementaciones de IoT, puede invocar una función cuando la luz de comprobación del motor se muestra en un automóvil conectado.

**Implementación:** use un desencadenador de Azure Cosmos DB y un enlace de salida

1. Un **desencadenador de Azure Cosmos DB** se usa para desencadenar eventos relacionados con alertas de automóviles, como la luz de comprobación del motor que aparece en un automóvil conectado.
2. Cuando aparece la luz de comprobación del motor, los datos del sensor se envían a Azure Cosmos DB.
3. Azure Cosmos DB crea o actualiza los documentos de datos de sensor nuevos y, luego, esos cambios se transmiten al desencadenador de Azure Cosmos DB.
4. El desencadenador se invoca en cada cambio de datos de la recopilación de datos del sensor, del mismo modo que todos los datos se transmiten a través de la fuente de cambios.
5. Una condición de umbral se usa en la función para enviar los datos del sensor al departamento de garantías.
6. Si la temperatura también supera cierto valor, también se envía una alerta al propietario.
7. El **enlace de salida** en la función actualiza el registro del automóvil en otra colección de Azure Cosmos DB para almacenar información sobre el evento de comprobación del motor.

La imagen siguiente muestra el código escrito en Azure Portal para este desencadenador.

![Creación de un desencadenador de Azure Cosmos DB en Azure Portal](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Caso de uso financiero: desencadenador de temporizador y enlace de entrada

En implementaciones financieras, puede invocar una función cuando el saldo de una cuenta bancaria cae por debajo de cierto importe.

**Implementación:** desencadenador de temporizador con un enlace de entrada de Azure Cosmos DB

1. Con un [desencadenador de temporizador](../azure-functions/functions-bindings-timer.md), puede recuperar la información del saldo de la cuenta bancaria almacenada en un contenedor de Azure Cosmos DB a intervalos de tiempo con un **enlace de entrada**.
2. Si el saldo está por debajo del umbral de saldo mínimo que establece el usuario, debe hacer seguimiento con una acción desde Azure Function.
3. El enlace de salida puede ser una [integración de SendGrid](../azure-functions/functions-bindings-sendgrid.md) que envía un correo electrónico desde una cuenta de servicio a las direcciones de correo electrónico identificadas para cada una de las cuentas con saldo bajo.

En las imágenes siguientes se muestra el código de Azure Portal para este escenario.

![Archivo Index.js para un desencadenador de temporizador en un escenario financiero](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Archivo Run.csx para un desencadenador de temporizador en un escenario financiero](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Caso de uso de juegos: desencadenador de Azure Cosmos DB y enlace de salida

En el ámbito de los juegos, cuando se crea un usuario nuevo, puede buscar otros usuarios que tal vez lo conozcan con [API Graph de Azure Cosmos DB](graph-introduction.md). A continuación, puede escribir los resultados en una [base de datos SQL de Azure Cosmos DB] para poder recuperarlos fácilmente.

**Implementación:** use un desencadenador de Azure Cosmos DB y un enlace de salida

1. Con una [base de datos de gráficos](graph-introduction.md) de Azure para almacenar a todos los usuarios, puede crear una función nueva con un desencadenador de Azure Cosmos DB. 
2. Cada vez que se inserta un usuario nuevo, se invoca la función y, luego, el resultado se almacena con un **enlace de salida**.
3. La función consulta la base de datos de gráficos para buscar todos los usuarios que están relacionados directamente con el usuario nuevo y devuelve ese conjunto de datos a la función.
4. Estos datos se almacenan en una base de datos de Azure Cosmos DB que cualquier aplicación de front-en puede recuperar fácilmente y que muestra al usuario nuevo sus amigos conectados.

### <a name="retail-use-case---multiple-functions"></a>Caso de uso de venta minorista: varias funciones

En implementaciones de venta minorista, cuando un usuario agrega un elemento a su cesta, ahora se tiene la flexibilidad de crear e invocar funciones para componentes opcionales de la canalización comercial.

**Implementación:** varios desencadenadores de Azure Cosmos DB que escuchan una colección

1. Puede crear varias instancias de Azure Functions si agrega desencadenadores de Azure Cosmos DB a cada una de ellas, todas las cuales escuchan la misma fuente de cambios de los datos del carro de la compra. Tenga en cuenta que cuando varias funciones escuchan en la misma fuente de cambios, es necesario una colección de concesiones para cada función.
2. Cada vez que un elemento nuevo se agrega al carro de la compra de los usuarios, la fuente de cambios invoca cada función de manera independiente desde el contenedor de carros de la compra.
    * Una función puede usar el contenido de la cesta actual para cambiar la visualización de otros artículos que puedan interesarle al usuario.
    * Otra función podría actualizar los totales de inventario.
    * Otra función podría enviar la información del cliente para determinados productos al departamento de marketing, el que envía a los clientes un correo electrónico masivo promocional. 

    Todos los departamentos pueden crear un desencadenador de Azure Cosmos DB si escuchan la fuente de cambios, con la seguridad de no retrasar los eventos de procesamiento de pedidos críticos en el proceso.

En todos estos casos de uso, como la función desacopló la aplicación misma, no es necesario iniciar instancias de aplicación nuevas todo el tiempo. En lugar de eso, Azure Functions inicia funciones individuales para completar los procesos discretos según sea necesario.

## <a name="tooling"></a>Herramientas

La integración nativa entre Azure Cosmos DB y Azure Functions está disponible en Azure Portal.
* En el portal de Azure Functions, puede crear un desencadenador de Azure Cosmos DB. Para instrucciones de inicio rápido, consulte [Creación de un desencadenador de Azure Cosmos DB en Azure Portal](https://aka.ms/cosmosdbtriggerportalfunc) ![Creación de un desencadenador de Azure Cosmos DB en el portal de Azure Functions](./media/serverless-computing-database/azure-function-cosmos-db-trigger.png) 
* En el portal de Azure Functions, también puede agregar enlaces de entrada y enlaces de salida de Azure Cosmos DB a otros tipos de desencadenadores. Para instrucciones de inicio rápido, consulte [Almacenamiento de datos no estructurados mediante Azure Functions y Cosmos DB](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md).
    ![Creación de un desencadenador de Azure Cosmos DB en el portal de Azure Functions](./media/serverless-computing-database/function-portal-input-binding.png)
*   En el portal de Azure Cosmos DB, puede agregar un desencadenador de Azure Cosmos DB a una aplicación de Azure Function existente en el mismo grupo de recursos.
    ![Creación de un desencadenador de Azure Cosmos DB en el portal de Azure Functions](./media/serverless-computing-database/cosmos-db-portal.png)

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>¿Por qué elegir la integración de Azure Functions para la informática sin servidor?

Azure Functions ofrece la capacidad de crear unidades de trabajo escalables, o partes concisas de lógica que se pueden ejecutar a petición, sin tener que aprovisionar o administrar infraestructura. Con Azure Functions, no es necesario crear una aplicación completa para responder a los cambios en la base de datos de Azure Cosmos DB, porque puede crear funciones pequeñas reutilizables para tareas específicas. Además, también puede usar los datos de Azure Cosmos DB como entrada o salida a una instancia de Azure Functions en respuesta a eventos tales como solicitudes HTTP o un desencadenador de temporizador.

Azure Cosmos DB es la base de datos recomendada para la arquitectura de informática sin servidor por los motivos siguientes:

* **Acceso instantáneo a todos los datos**: tiene acceso pormenorizado a cada valor almacenado porque Azure Cosmos DB [indexa automáticamente](indexing-policies.md) todos los datos de manera predeterminada y permite que esos índices estén disponibles de inmediato. Esto significa que puede consultar, actualizar y agregar elementos nuevos constantemente a la base de datos y tiene acceso instantánea vía Azure Functions.

* **Sin esquema**. Azure Cosmos DB no tiene esquemas, por lo que es capaz de forma exclusiva de controlar cualquier salida de datos de una instancia de Azure Function. Este enfoque de "controlar todo" permite que el proceso de crear una variedad de instancias de Functions que tengan salida a Azure Cosmos DB sea sencillo.

* **Rendimiento escalable**. En Azure Cosmos DB, el rendimiento se puede escalar y reducir verticalmente de manera instantánea. Si tiene cientos o miles de instancias de Functions consultando y escribiendo en la misma colección, puede escalar verticalmente las [RU/s](request-units.md) para controlar la carga. Todas las funciones pueden trabajar en paralelo con las RU/s asignadas y se garantiza que los datos serán [coherentes](consistency-levels.md).

* **Replicación global**. Puede replicar los datos de Azure Cosmos DB [en todo el mundo](distribute-data-globally.md) para reducir la latencia y localizar geográficamente los datos más cercanos al lugar en que se encuentran los usuarios. Del mismo modo que ocurre con todas las consultas de Azure Cosmos DB, los datos de los desencadenadores basados en eventos son datos de lectura de la instancia de Azure Cosmos DB más cercana al usuario.

Si busca la integración con Azure Functions para almacenar datos y no necesita una indexación profunda o si necesita almacenar los archivos adjuntos y los archivos multimedia, una mejor opción podría ser el [desencadenador de Azure Blob Storage](../azure-functions/functions-bindings-storage-blob.md).

Ventajas de Azure Functions: 

* **Basado en eventos**. Azure Functions está basado en eventos y puede escuchar una fuente de cambios de Azure Cosmos DB. Esto significa que no es necesario crear una lógica de escucha, sino que solo debe estar atento a los cambios que escucha. 

* **Sin límites**. Las instancias de Functions se ejecutan en paralelo y el servicio se inicia las veces que sea necesario. Los parámetros los establece usted.

* **Ideal para las tareas rápidas**. El servicio inicia instancias nuevas de funciones cada vez que se activa un evento y los cierra tan pronto se completa la función. Solo se paga por el tiempo durante el cual se ejecutan las funciones.

Si no está seguro si Flow, Logic Apps, Azure Functions o WebJobs es la mejor opción para su implementación, consulte [Elección entre Flow, Logic Apps, Functions y WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>pasos siguientes

Ahora conectaremos realmente Azure Cosmos DB y Azure Functions: 

* [Creación de un desencadenador de Azure Cosmos DB en Azure Portal](https://aka.ms/cosmosdbtriggerportalfunc)
* [Creación de un desencadenador HTTP de Azure Functions con un enlace de entrada de Azure Cosmos DB](https://aka.ms/cosmosdbinputbind)
* [Almacenamiento de datos no estructurados mediante Azure Functions y Cosmos DB](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)
* [Enlaces y desencadenadores de Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb.md)


 



