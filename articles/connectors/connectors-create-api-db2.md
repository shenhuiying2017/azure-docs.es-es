---
title: "Incorporación del conector de DB2 a Logic Apps | Microsoft Docs"
description: "Información general del conector de DB2 con parámetros de la API de REST"
services: 
documentationcenter: 
author: gplarsen
manager: erikre
editor: 
tags: connectors
ms.assetid: 1c6b010c-beee-496d-943a-a99e168c99aa
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/26/2016
ms.author: plarsen; ladocs
ms.openlocfilehash: 4501b3d9a2fdc00582596cb907f7130591e4782e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-db2-connector"></a>Introducción al conector de DB2
Microsoft Connector para DB2 permite conectar Logic Apps a los recursos almacenados en una base de datos de IBM DB2. Este conector incluye un cliente de Microsoft para comunicarse con equipos de servidor de DB2 remotos a través de una red TCP/IP. Este incluye bases de datos en la nube, como IBM Bluemix dashDB o IBM DB2 para Windows que se ejecuta en virtualización en Azure y bases de datos locales con la puerta de enlace de datos locales. Consulte la [lista compatible](connectors-create-api-db2.md#supported-db2-platforms-and-versions) de versiones y plataformas de IBM DB2 (en este tema).

El conector de DB2 admite las siguientes operaciones de base de datos:

* Mostrar tablas de base de datos
* Leer una fila mediante SELECT
* Leer todas las filas mediante SELECT
* Agregar una fila mediante INSERT
* Modificar una fila mediante UPDATE
* Quitar una fila mediante DELETE

Este tema le muestra cómo usar el conector en una aplicación lógica para procesar operaciones de base de datos.

Para más información acerca de Logic Apps, consulte el artículo sobre [creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="available-actions"></a>Acciones disponibles
El conector de DB2 admite las siguientes acciones de Logic Apps:

* GetTables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Enumeración de tablas
La creación de una aplicación lógica para cualquier operación consta de varios pasos que se realizan mediante Microsoft Azure Portal.

Dentro de la aplicación lógica, puede agregar una acción para mostrar las tablas en una base de datos DB2. La acción indica al conector que procese una instrucción de esquema de DB2, como `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Creación de una aplicación lógica
1. En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Especifique el **nombre**, como la `Db2getTables`, **suscripción**, **grupo de recursos**, **ubicación** y **plan de App Service**. Seleccione **Anclar al panel** y, después, seleccione **Crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y acción
1. En el **Diseñador de Logic Apps**, seleccione **Blank LogicApp** en la lista **Plantillas**.
2. En la lista de **desencadenadores**, seleccione **Periodicidad**. 
3. En el desencadenador **Periodicidad**, seleccione **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, establezca el **Intervalo** y escriba **7**.  
4. Seleccione el cuadro **+ Nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5. En la lista de **acciones**, escriba `db2` en el cuadro de edición **Buscar más acciones** y, a continuación, seleccione **DB2 - Get tables (Preview)** (DB2 - Obtener tablas [Vista previa]).
   
   ![](./media/connectors-create-api-db2/Db2connectorActions.png)  
6. En el panel de configuración **DB2 - Get tables** (DB2 - Obtener tablas), active la **casilla** para habilitar la opción **Connect via on-premises data gateway** (Conectarse a través de la puerta de enlace de datos local). Tenga en cuenta que la configuración cambia de configuración de nube a local.
   
   * Escriba un valor para **Servidor**, en forma de número de puerto con dirección o alias delimitado por dos puntos. Por ejemplo, escriba `ibmserver01:50000`.
   * Escriba un valor para **Base de datos**. Por ejemplo, escriba `nwind`.
   * Seleccione un valor para **Autenticación**. Por ejemplo, seleccione **Básico**.
   * Escriba un valor para **Nombre de usuario**. Por ejemplo, escriba `db2admin`.
   * Escriba un valor para **Contraseña**. Por ejemplo, escriba `Password1`.
   * Seleccione el valor para **Puerta de enlace**. Por ejemplo, seleccione **datagateway01**.
7. Seleccione **Crear** y, a continuación, seleccione **Guardar**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)
8. En la hoja **Db2getTables**, en la lista **Todas las ejecuciones** en **Resumen**, seleccione el primer elemento de la lista (la ejecución más reciente).
9. En la hoja **Ejecución de la aplicación lógica**, seleccione **Detalles de ejecución**. En la lista de **acciones**, seleccione **Get_tables**. Observe el valor de **Estado**, que debería ser **Correcto**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de salidas**para ver las salidas; que deben incluir una lista de tablas.
   
   ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Crear las conexiones
El conector admite conexiones a bases de datos hospedadas localmente y en la nube mediante las siguientes propiedades de conexión. 

| Propiedad | Description |
| --- | --- |
| Servidor |Obligatorio. Acepta un valor de cadena que representa una dirección TCP/IP o alias en formato IPv4 o IPv6 seguido de un número de puerto TCP/IP (delimitado por dos puntos). |
| Base de datos |Obligatorio. Acepta un valor de cadena que representa un nombre de base de datos relacional de DRDA (RDBNAM). DB2 para z/OS acepta una cadena de 16 bytes (la base de datos se conoce como una base de datos IBM DB2 para la ubicación z/OS). DB2 para i5/OS acepta una cadena de 18 bytes (la base de datos se conoce como una base de datos IBM DB2 para la base de datos relacional i). DB2 para LUW acepta una cadena de 8 bytes. |
| Autenticación |Opcional. Acepta un valor de elemento de lista que puede ser Basic o Windows (kerberos). |
| Nombre de usuario |Obligatorio. Acepta un valor de cadena. DB2 para z/OS acepta una cadena de 8 bytes. DB2 para i acepta una cadena de 10 bytes. DB2 para Linux o UNIX acepta una cadena de 8 bytes. DB2 para Windows acepta una cadena de 30 bytes. |
| Contraseña |Obligatorio. Acepta un valor de cadena. |
| Puerta de enlace |Obligatorio. Acepta un valor de elemento de lista que representa la puerta de enlace de datos local definida para Logic Apps en el grupo de almacenamiento. |

## <a name="create-the-on-premises-gateway-connection"></a>Crear una conexión a una puerta de enlace local
Este conector puede tener acceso a una base de datos DB2 local a través de la puerta de enlace local. Consulte los temas sobre puertas de enlace para más información. 

1. En el panel de configuración **Puertas de enlace**, active la **casilla** para habilitar la opción **Connect via gateway** (Conectar a través de puerta de enlace). Tenga en cuenta que la configuración cambia de configuración de nube a local.
2. Escriba un valor para **Servidor**, en forma de número de puerto con dirección o alias delimitado por dos puntos. Por ejemplo, escriba `ibmserver01:50000`.
3. Escriba un valor para **Base de datos**. Por ejemplo, escriba `nwind`.
4. Seleccione un valor para **Autenticación**. Por ejemplo, seleccione **Básico**.
5. Escriba un valor para **Nombre de usuario**. Por ejemplo, escriba `db2admin`.
6. Escriba un valor para **Contraseña**. Por ejemplo, escriba `Password1`.
7. Seleccione el valor para **Puerta de enlace**. Por ejemplo, seleccione **datagateway01**.
8. Seleccione **Crear** para continuar. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Crear la conexión en la nube
Este conector puede tener acceso a una base de datos DB2 en la nube. 

1. En el panel de configuración **Puertas de enlace**, deje la **casilla** **Connect via gateway** (Conectar a través de puerta de enlace) deshabilitada (no haga clic en ella). 
2. Escriba un valor para **Nombre de conexión**. Por ejemplo, escriba `hisdemo2`.
3. Escriba un valor para **Nombre del servidor DB2**, en forma de número de puerto con dirección o alias delimitado por dos puntos. Por ejemplo, escriba `hisdemo2.cloudapp.net:50000`.
4. Escriba un valor para **Nombre de la base de datos DB2**. Por ejemplo, escriba `nwind`.
5. Escriba un valor para **Nombre de usuario**. Por ejemplo, escriba `db2admin`.
6. Escriba un valor para **Contraseña**. Por ejemplo, escriba `Password1`.
7. Seleccione **Crear** para continuar. 
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Recuperar todas las filas mediante SELECT
Puede definir una acción de aplicación lógica para recuperar todas las filas de una tabla de DB2. Esto indica al conector que procese una instrucción SELECT de DB2, como `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Creación de una aplicación lógica
1. En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Especifique el **nombre**, como la `Db2getRows`, **suscripción**, **grupo de recursos**, **ubicación** y **plan de App Service**. Seleccione **Anclar al panel** y, después, seleccione **Crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y acción
1. En el **Diseñador de Logic Apps**, seleccione **Blank LogicApp** en la lista **Plantillas**.
2. En la lista de **desencadenadores**, seleccione **Periodicidad**. 
3. En el desencadenador **Periodicidad**, seleccione **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, seleccione **Intervalo** y escriba **7**. 
4. Seleccione el cuadro **+ Nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5. En la lista de **acciones**, escriba `db2` en el cuadro de edición **Buscar más acciones** y, a continuación, seleccione **DB2 - Get rows (Preview)** (DB2 - Obtener filas [Vista previa]).
6. En la acción **Get rows (Preview)** (Obtener filas [Vista previa]), seleccione **Cambiar conexión**.
7. En el panel de configuración **Conexiones**, seleccione **Crear nuevo**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
8. En el panel de configuración **Puertas de enlace**, deje la **casilla** **Connect via gateway** (Conectar a través de puerta de enlace) deshabilitada (no haga clic en ella).
   
   * Escriba un valor para **Nombre de conexión**. Por ejemplo, escriba `HISDEMO2`.
   * Escriba un valor para **Nombre del servidor DB2**, en forma de número de puerto con dirección o alias delimitado por dos puntos. Por ejemplo, escriba `HISDEMO2.cloudapp.net:50000`.
   * Escriba un valor para **Nombre de la base de datos DB2**. Por ejemplo, escriba `NWIND`.
   * Escriba un valor para **Nombre de usuario**. Por ejemplo, escriba `db2admin`.
   * Escriba un valor para **Contraseña**. Por ejemplo, escriba `Password1`.
9. Seleccione **Crear** para continuar.
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)
10. En la lista **Nombre de tabla**, seleccione **flecha abajo** y, a continuación, seleccione **ÁREA**.
11. Opcionalmente, seleccione **Mostrar opciones avanzadas** para especificar las opciones de consulta.
12. Seleccione **Guardar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)
13. En la hoja **Db2getRows**, en la lista **Todas las ejecuciones** en **Resumen**, seleccione el primer elemento de la lista (la ejecución más reciente).
14. En la hoja **Ejecución de la aplicación lógica**, seleccione **Detalles de ejecución**. En la lista de **acciones**, seleccione **Get_rows**. Observe el valor de **Estado**, que debería ser **Correcto**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de salidas**para ver las salidas; que deben incluir una lista de filas.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Agregar una fila mediante INSERT
Puede definir una acción de aplicación lógica para agregar una fila en una tabla de DB2. La acción indica al conector que procese una instrucción INSERT de DB2, como `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Creación de una aplicación lógica
1. En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Especifique el **nombre**, como la `Db2insertRow`, **suscripción**, **grupo de recursos**, **ubicación** y **plan de App Service**. Seleccione **Anclar al panel** y, después, seleccione **Crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y acción
1. En el **Diseñador de Logic Apps**, seleccione **Blank LogicApp** en la lista **Plantillas**.
2. En la lista de **desencadenadores**, seleccione **Periodicidad**. 
3. En el desencadenador **Periodicidad**, seleccione **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, seleccione **Intervalo** y escriba **7**. 
4. Seleccione el cuadro **+ Nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5. En la lista de **acciones**, escriba **db2** en el cuadro de edición **Buscar más acciones** y, a continuación, seleccione **DB2 - Insert row (Preview)** (DB2 - Insertar fila [Vista previa]).
6. En la acción **DB2 - Insert row (Preview)** (DB2: Insertar fila [Vista previa]), seleccione **Cambiar conexión**. 
7. En el panel de configuración **Conexiones** , seleccione una conexión. Por ejemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. En la lista **Nombre de tabla**, seleccione **flecha abajo** y, a continuación, seleccione **ÁREA**.
9. Escriba valores para todas las columnas obligatorias (busque un asterisco rojo). Por ejemplo, escriba `99999` para **AREAID**, escriba `Area 99999` y escriba `102` para **REGIONID**. 
10. Seleccione **Guardar**.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
11. En la hoja **Db2insertRow**, en la lista **Todas las ejecuciones** en **Resumen**, seleccione el primer elemento de la lista (la ejecución más reciente).
12. En la hoja **Ejecución de la aplicación lógica**, seleccione **Detalles de ejecución**. En la lista de **acciones**, seleccione **Get_rows**. Observe el valor de **Estado**, que debería ser **Correcto**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de salidas**para ver las salidas; que debe incluir la nueva fila.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Recuperar una fila mediante SELECT
Puede definir una acción de aplicación lógica para recuperar una fila en una tabla de DB2. La acción indica al conector que procese una instrucción SELECT WHERE de DB2, como `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Creación de una aplicación lógica
1. En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Escriba el **nombre** (por ejemplo, "**Db2getRow**"), la **suscripción**, el **grupo de recursos**, la **ubicación** y el **plan de App Service**. Seleccione **Anclar al panel** y, después, seleccione **Crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y acción
1. En el **Diseñador de Logic Apps**, seleccione **Blank LogicApp** en la lista **Plantillas**. 
2. En la lista de **desencadenadores**, seleccione **Periodicidad**. 
3. En el desencadenador **Periodicidad**, seleccione **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, seleccione **Intervalo** y escriba **7**. 
4. Seleccione el cuadro **+ Nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5. En la lista de **acciones**, escriba **db2** en el cuadro de edición **Buscar más acciones** y, a continuación, seleccione **db2 - Get rows (Preview)** (DB2 - Obtener filas [Vista previa]).
6. En la acción **Get rows (Preview)** (Obtener filas [Vista previa]), seleccione **Cambiar conexión**. 
7. En el panel de configuración **Conexiones** , seleccione una conexión existente. Por ejemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. En la lista **Nombre de tabla**, seleccione **flecha abajo** y, a continuación, seleccione **ÁREA**.
9. Escriba valores para todas las columnas obligatorias (busque un asterisco rojo). Por ejemplo, escriba `99999` para **AREAID**. 
10. Opcionalmente, seleccione **Mostrar opciones avanzadas** para especificar las opciones de consulta.
11. Seleccione **Guardar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)
12. En la hoja **Db2getRow**, en la lista **Todas las ejecuciones** en **Resumen**, seleccione el primer elemento de la lista (la ejecución más reciente).
13. En la hoja **Ejecución de la aplicación lógica**, seleccione **Detalles de ejecución**. En la lista de **acciones**, seleccione **Get_rows**. Observe el valor de **Estado**, que debería ser **Correcto**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de salidas**para ver las salidas; que debe incluir la fila.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Cambiar una fila mediante UPDATE
Puede definir una acción de aplicación lógica para cambiar una fila en una tabla de DB2. La acción indica al conector que procese una instrucción UPDATE de DB2, como `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Creación de una aplicación lógica
1. En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Especifique el **nombre**, como la `Db2updateRow`, **suscripción**, **grupo de recursos**, **ubicación** y **plan de App Service**. Seleccione **Anclar al panel** y, después, seleccione **Crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y acción
1. En el **Diseñador de Logic Apps**, seleccione **Blank LogicApp** en la lista **Plantillas**.
2. En la lista de **desencadenadores**, seleccione **Periodicidad**. 
3. En el desencadenador **Periodicidad**, seleccione **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, seleccione **Intervalo** y escriba **7**. 
4. Seleccione el cuadro **+ Nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5. En la lista de **acciones**, escriba **db2** en el cuadro de edición **Buscar más acciones** y, a continuación, seleccione **DB2 - Update row (Preview)** (DB2 - Actualizar fila [Vista previa]).
6. En la acción **DB2 - Update row (Preview)** (DB2: Actualizar fila [Vista previa]), seleccione **Cambiar conexión**. 
7. En el panel de configuración **Conexiones** , seleccione una conexión existente. Por ejemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. En la lista **Nombre de tabla**, seleccione **flecha abajo** y, a continuación, seleccione **ÁREA**.
9. Escriba valores para todas las columnas obligatorias (busque un asterisco rojo). Por ejemplo, escriba `99999` para **AREAID**, escriba `Updated 99999` y escriba `102` para **REGIONID**. 
10. Seleccione **Guardar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)
11. En la hoja **Db2updateRow**, en la lista **Todas las ejecuciones** en **Resumen**, seleccione el primer elemento de la lista (la ejecución más reciente).
12. En la hoja **Ejecución de la aplicación lógica**, seleccione **Detalles de ejecución**. En la lista de **acciones**, seleccione **Get_rows**. Observe el valor de **Estado**, que debería ser **Correcto**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de salidas**para ver las salidas; que debe incluir la nueva fila.
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Quitar una fila mediante DELETE
Puede definir una acción de aplicación lógica para quitar una fila en una tabla de DB2. La acción indica al conector que procese una instrucción DELETE de DB2, como `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Creación de una aplicación lógica
1. En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Especifique el **nombre**, como la `Db2deleteRow`, **suscripción**, **grupo de recursos**, **ubicación** y **plan de App Service**. Seleccione **Anclar al panel** y, después, seleccione **Crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y acción
1. En el **Diseñador de Logic Apps**, seleccione **Blank LogicApp** en la lista **Plantillas**. 
2. En la lista de **desencadenadores**, seleccione **Periodicidad**. 
3. En el desencadenador **Periodicidad**, seleccione **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, seleccione **Intervalo** y escriba **7**. 
4. Seleccione el cuadro **+ Nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5. En la lista de **acciones**, seleccione **db2** en el cuadro de edición **Buscar más acciones** y, a continuación, seleccione **DB2 - Delete row (Preview)** (DB2 - Eliminar fila [Vista previa]).
6. En la acción **DB2 - Delete row (Preview)** (DB2: Eliminar fila [Vista previa]), seleccione **Cambiar conexión**. 
7. En el panel de configuración **Conexiones** , seleccione una conexión existente. Por ejemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. En la lista **Nombre de tabla**, seleccione **flecha abajo** y, a continuación, seleccione **ÁREA**.
9. Escriba valores para todas las columnas obligatorias (busque un asterisco rojo). Por ejemplo, escriba `99999` para **AREAID**. 
10. Seleccione **Guardar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)
11. En la hoja **Db2deleteRow**, en la lista **Todas las ejecuciones** en **Resumen**, seleccione el primer elemento de la lista (la ejecución más reciente).
12. En la hoja **Ejecución de la aplicación lógica**, seleccione **Detalles de ejecución**. En la lista de **acciones**, seleccione **Get_rows**. Observe el valor de **Estado**, que debería ser **Correcto**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de salidas**para ver las salidas; que debe incluir la fila eliminada.
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="supported-db2-platforms-and-versions"></a>Versiones y plataformas DB2 compatibles
Este conector admite las siguientes plataformas y versiones de IBM DB2, así como los productos compatibles de este (por ejemplo, IBM Bluemix dashDB) que admiten las versiones 10 y 11 de SQL Access Manager (SQLAM) de la arquitectura distribuida de bases de datos relacionales (DRDA):

* IBM DB2 para z/OS 11.1
* IBM DB2 para z/OS 10.1
* IBM DB2 para i 7.3
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/db2/). 

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).

