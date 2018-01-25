---
title: "Adición del conector de una base de datos de Oracle en Azure Logic Apps | Microsoft Docs"
description: "Use el conector de una base de datos de Oracle en una aplicación lógica"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: mandia; ladocs
ms.openlocfilehash: 4119e72abd568c9b53245a872691f06329b040de
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-oracle-database-connector"></a>Introducción al conector de una base de datos de Oracle

Mediante el conector de una base de datos de Oracle, crea flujos de trabajo organizativos que utilizan datos de la base de datos existente. Este conector puede conectarse a una base de datos de Oracle local o a una máquina virtual de Azure con una base de datos de Oracle instalada. Con este conector, puede hacer lo siguiente:

* Agregue un nuevo cliente a una base de datos de clientes o actualice un pedido en una base de datos de pedidos para crear el flujo de trabajo.
* Use acciones para obtener una fila de datos, insertar una fila nueva e incluso eliminarla. Por ejemplo, al crear un registro en Dynamics CRM Online (un desencadenador), inserte una fila en una base de datos de Oracle (una acción). 

Este tema le muestra cómo usar el conector de una base de datos de Oracle en una aplicación lógica.

## <a name="prerequisites"></a>requisitos previos

* Versiones de Oracle compatibles: 
    * Oracle 9 y versiones posteriores
    * Software cliente de Oracle 8.1.7 y posteriores

* Instale la puerta de enlace de datos local. Puede ver los pasos en [Conexión a datos locales desde aplicaciones lógicas](../logic-apps/logic-apps-gateway-connection.md). Esta puerta de enlace puede conectarse a una base de datos de Oracle local o a una VM de Azure con una base de datos de Oracle instalada. 

    > [!NOTE]
    > La puerta de enlace de datos local actúa como un puente, proporcionando una transferencia de datos segura entre los datos locales (datos que no están en la nube) y las aplicaciones lógicas. La misma puerta de enlace se puede utilizar con varios servicios y varios orígenes de datos. Por lo tanto, solo debe instalar la puerta de enlace una vez.

* Instale el cliente Oracle en la misma máquina en la que instaló la puerta de enlace de datos local. Asegúrese de instalar el proveedor de datos de Oracle de 64-bits para .NET de Oracle:  

  [64-bits ODAC 12c versión 4 (12.1.0.2.4) para Windows x64](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Si no se instala el cliente Oracle, se producirá un error cuando intente crear o usar la conexión. Consulte los errores comunes en este tema.


## <a name="add-the-connector"></a>Incorporación del conector

> [!IMPORTANT]
> Este conector no tiene ningún desencadenador. Solo tiene acciones. Por lo tanto, cuando cree la aplicación lógica, agregue otro desencadenador para iniciar la aplicación lógica, como **Programación: periodicidad** o **Solicitud/Respuesta: respuesta**. 

1. En [Azure Portal](https://portal.azure.com), cree una aplicación lógica en blanco.

2. Al principio de la aplicación lógica, seleccione el desencadenador **Solicitud/Respuesta: respuesta**: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Seleccione **Guardar**. Cuando lo guarde, se generará automáticamente la URL de solicitud. 

4. Seleccione **Nuevo paso** y seleccione **Agregar una acción**. Escriba en `oracle` para ver las acciones disponibles: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Esta también es la forma más rápida para ver los desencadenadores y las acciones disponibles para cualquier conector. Escriba parte del nombre del conector, como `oracle`. El diseñador muestra todos los desencadenadores y las acciones. 

5. Seleccione una de las acciones, como **Base de datos de Oracle: obtener fila**. Seleccione **Connect via on-premises data gateway** (Conectarse a través de la puerta de enlace de datos local). Escriba el nombre del servidor de Oracle, el método de autenticación, el nombre de usuario, la contraseña y seleccione la puerta de enlace:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Una vez conectado, seleccione una tabla de la lista y escriba el ID de la fila en la tabla. Debe conocer el identificador de la tabla. Si no lo sabe, póngase en contacto con el administrador de la base de datos de Oracle y obtenga el resultado de `select * from yourTableName`. Esto le ofrece la información de identificación que necesita para continuar.

    En el ejemplo siguiente, se devuelven los datos de trabajo de una base de datos de recursos humanos: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. En este paso, puede usar cualquiera de los demás conectores para crear el flujo de trabajo. Si desea probar obtener los datos de Oracle, envíe a sí mismo un correo electrónico con los datos de Oracle mediante uno de los conectores de envío de correo electrónico, como Office 365 o Gmail. Use los tokens dinámicos de la tabla de Oracle para generar el `Subject` y `Body` de su correo electrónico:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Guarde** la aplicación lógica y, a continuación, seleccione **Ejecutar**. Cierre el diseñador y observe el historial de ejecuciones para el estado. Si se produce un error, seleccione la fila del mensaje con errores. El diseñador se abre y muestra en qué paso se produjo el error. También muestra la información de error. Si se realiza correctamente, debería recibir un correo electrónico con la información que agregó.


### <a name="workflow-ideas"></a>Ideas de flujo de trabajo

* Le recomendamos supervisar el hashtag #oracle y poner los tweets en una base de datos para que se pueden consultar y usar en otras aplicaciones. En una aplicación lógica, agregue el desencadenador `Twitter - When a new tweet is posted` y escriba el hashtag **#oracle**. A continuación, agregue la acción `Oracle Database - Insert row` y seleccione la tabla:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Los mensajes se envían a la cola de Service Bus. Le recomendamos recibir estos mensajes y colocarlos en una base de datos. En una aplicación lógica, agregue el desencadenador `Service Bus - when a message is received in a queue` y seleccione la cola. A continuación, agregue la acción `Oracle Database - Insert row` y seleccione la tabla:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Errores comunes

#### <a name="error-cannot-reach-the-gateway"></a>**Error**: No se puede alcanzar la puerta de enlace

**Causa**: La puerta de enlace de datos local no puede conectarse con la nube. 

**Mitigación**: Asegúrese de que la puerta de enlace se esté ejecutando en el equipo local donde la instaló y que se pueda conectar a Internet.  Se recomienda no instalar la puerta de enlace en un equipo que pueda apagarse o suspenderse. También puede reiniciar el servicio de la puerta de enlace de datos local (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-please-visit-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Error**: El proveedor que está usando está en desuso: 'System.Data.OracleClient requiere la versión 8.1.7 del software cliente de Oracle o una versión posterior'. Visite [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) para instalar el proveedor oficial.

**Causa**: El SDK del cliente Oracle no está instalado en la misma máquina en la que se ejecuta la puerta de enlace de datos local.  

**Resolución**: Descargue e instale el SDK del cliente Oracle en la misma máquina que la puerta de enlace de datos local.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Error**: La tabla '[Tablename]' no define las columnas clave.

**Causa**: La tabla no tiene ninguna clave principal.  

**Resolución**: El conector de la base de datos de Oracle requiere que se utilice una tabla con una columna de clave principal.

#### <a name="currently-not-supported"></a>Actualmente no se admite

* Vistas y procedimientos almacenados 
* Todas las tablas con claves compuestas
* Tipos de objetos anidados en tablas
 
## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/oracle/). 

## <a name="get-some-help"></a>Obtener ayuda

El [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) es un excelente lugar para formular preguntas, o responderlas, y ver lo que hacen otros usuarios de Azure Logic Apps. 

Puede ayudar a mejorar Logic Apps y conectores al votar y enviar sus ideas en [http://aka.ms/logicapps-wish](http://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>pasos siguientes
[Cree una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) y explore los conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).
