<properties
    pageTitle="Incorporación del conector de DB2 a Logic Apps | Microsoft Azure"
    description="Información general del conector de DB2 con parámetros de la API de REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="09/19/2016"
   ms.author="plarsen"/>


# Introducción al conector de DB2
Microsoft Connector para DB2 permite conectar Logic Apps a los recursos almacenados en una base de datos de IBM DB2. Este conector incluye un cliente de Microsoft para comunicarse con equipos de servidor DB2 remotos en una red TCP/IP, incluidas las bases de datos en la nube (por ejemplo, IBM Bluemix dashDB o IBM DB2 para Windows que se ejecuta en virtualización de Azure) y las bases de datos locales mediante la puerta de enlace de datos local. Consulte la lista de plataformas y versiones admitidas de IBM DB2 al final de este tema.

El conector de DB2 admite las siguientes operaciones de base de datos:

- Mostrar tablas de base de datos
- Leer una fila mediante SELECT
- Leer todas las filas mediante SELECT
- Agregar una fila mediante INSERT
- Modificar una fila mediante UPDATE
- Quitar una fila mediante DELETE

Este tema le muestra cómo usar el conector en una aplicación lógica para procesar operaciones de base de datos.

>[AZURE.NOTE] Esta versión del artículo se aplica a la disponibilidad general de las aplicaciones lógicas.

Para más información acerca de Logic Apps, consulte el artículo sobre [creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Acciones de Logic Apps
El conector admite las siguientes acciones de Logic Apps:

- Getables
- GetRow
- GetRows
- InsertRow
- UpdateRow
- DeleteRow


## Definición de aplicación lógica para mostrar las tablas
La definición de una aplicación lógica para cualquier operación consta de varios pasos que se realizan mediante Microsoft Azure Portal. Puede definir una acción de aplicación lógica para que se muestren las tablas de una base de datos DB2, que solicite al conector que procese una instrucción de esquema de DB2 (CALL SYSIBM.SQLTABLES).

### Definición de instancia de aplicación lógica
1.	En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2.	Escriba el **nombre** (por ejemplo, "**Db2getTables**"), la **suscripción**, el **grupo de recursos**, la **ubicación** y el **plan de App Service**. Haga clic en **Anclar al panel** y, después, seleccione **Crear**.

### Definición del desencadenador y la acción de la aplicación lógica
1.	En el **Diseñador de Logic Apps**, en la lista **Plantillas**, haga clic en **Blank LogicApp**.
2.	En la lista de **desencadenadores** haga clic en **Periodicidad**.
3.	En el desencadenador **Periodicidad**, haga clic en **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, haga clic en **Intervalo** y escriba **7**.
4.	Haga clic en el cuadro **+ Nuevo paso** y, a continuación, haga clic en **Agregar una acción**.
5.	En la lista de **acciones**, escriba **db2** en el cuadro de edición **Search for more actions** (Buscar más acciones) y, a continuación, haga clic en **DB2 - Get tables (Preview)** (DB2 - Obtener tablas [Vista previa]).

	![](./media/connectors-create-api-db2/Db2connectorActions.png)

6.	En el panel de configuración **DB2 - Get tables** (DB2 - Obtener tablas), haga clic en la **casilla** para habilitar la opción **Connect via on-premises data gateway** (Conectarse a través de la puerta de enlace de datos local). Vea el cambio de configuración de nube a local.
	- Escriba un valor para **Servidor**, en forma de número de puerto con dirección o alias delimitado por dos puntos. Por ejemplo, escriba **ibmserver01:50000**.
	- Escriba un valor para **Base de datos**. Por ejemplo, escriba **nwind**.
	- Seleccione un valor para **Autenticación**. Por ejemplo, seleccione **Básico**.
	- Escriba un valor para **Nombre de usuario**. Por ejemplo, escriba **db2admin**.
	- Escriba un valor para **Contraseña**. Por ejemplo, escriba **Password1**.
	- Seleccione el valor para **Puerta de enlace**. Por ejemplo, haga clic en **datagateway01**.
7. Haga clic en **Crear** y, a continuación, haga clic en **Guardar**.

	![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

8.	En la hoja **Db2getTables**, en la lista **Todas las ejecuciones** en **Resumen**, haga clic en el primer elemento de la lista (la ejecución más reciente).
9.	En la hoja **Ejecución de la aplicación lógica**, haga clic en **Detalles de ejecución**. En la lista de **acciones** haga clic en **Get\_tables**. Observe el valor de **Estado**, que debería ser **Correcto**. Haga clic en el **vínculo de entradas**. Vea las entradas. Haga clic en el **vínculo de salidas**. Vea las salidas, que deben incluir una lista de tablas.

	![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## Definición de las conexiones de DB2
El conector admite las conexiones a bases de datos locales y en la nube mediante las siguientes propiedades de conexión.

Propiedad | Description
--- | ---
server | La propiedad de servidor requerida acepta un valor de cadena que representa una dirección TCP/IP o alias en formato IPv4 o IPv6 seguido de un número de puerto TCP/IP (delimitado por dos puntos). 
database | La propiedad de base de datos requerida acepta un valor de cadena que representa un nombre de la base de datos relacional (RDBNAM) de DRDA. DB2 para z/OS acepta una cadena de 16 bytes (la base de datos se conoce como una base de datos IBM DB2 para la ubicación z/OS). DB2 para i5/OS acepta una cadena de 18 bytes (la base de datos se conoce como una base de datos IBM DB2 para la base de datos relacional i). DB2 para LUW acepta una cadena de 8 bytes.
authentication | La propiedad de autenticación opcional acepta un valor de elemento de lista que puede ser Basic o Windows (kerberos). 
nombre de usuario | La propiedad username requerida acepta un valor de cadena. DB2 para z/OS acepta una cadena de 8 bytes. DB2 para i acepta una cadena de 10 bytes. DB2 para Linux o UNIX acepta una cadena de 8 bytes. DB2 para Windows acepta una cadena de 30 bytes.
contraseña | La propiedad password requerida acepta un valor de cadena.
puerta de enlace | La propiedad gateway requerida acepta un valor de elemento de lista que representa la puerta de enlace de datos local definida para Logic Apps en el grupo de almacenamiento.  

## Definición de una conexión a una puerta de enlace de datos local
El conector puede tener acceso a una base de datos DB2 local a través de la puerta de enlace local. Consulte los temas sobre puertas de enlace para más información.

1. En el panel de configuración **Puertas de enlace**, haga clic en la **casilla** para habilitar la opción **Connect via gateway** (Conectar a través de puerta de enlace). Vea el cambio de configuración de nube a local.
2. Escriba un valor para **Servidor**, en forma de número de puerto con dirección o alias delimitado por dos puntos. Por ejemplo, escriba **ibmserver01:50000**.
3. Escriba un valor para **Base de datos**. Por ejemplo, escriba **nwind**.
4. Seleccione un valor para **Autenticación**. Por ejemplo, seleccione **Básico**.
5. Escriba un valor para **Nombre de usuario**. Por ejemplo, escriba **db2admin**.
6. Escriba un valor para **Contraseña**. Por ejemplo, escriba **Password1**.
7. Seleccione el valor para **Puerta de enlace**. Por ejemplo, haga clic en **datagateway01**.
8. Haga clic en **Crear** para continuar.

	![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## Definición de una conexión en la nube
El conector puede tener acceso a una base de datos DB2 en la nube.

1. En el panel de configuración **Puertas de enlace**, deje la **casilla** **Connect via gateway** (Conectar a través de puerta de enlace) deshabilitada (no haga clic en ella).
2. Escriba un valor para **Nombre de conexión**. Por ejemplo, escriba **hisdemo2**.
3. Escriba un valor para **Nombre del servidor DB2**, en forma de número de puerto con dirección o alias delimitado por dos puntos. Por ejemplo, escriba **hisdemo2.cloudapp.net:50000**.
3. Escriba un valor para **Nombre de la base de datos DB2**. Por ejemplo, escriba **nwind**.
4. Escriba un valor para **Nombre de usuario**. Por ejemplo, escriba **db2admin**.
5. Escriba un valor para **Contraseña**. Por ejemplo, escriba **Password1**.
6. Haga clic en **Crear** para continuar.

	![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## Definición de la aplicación lógica para recuperar todas las filas con SELECT
Puede definir una acción de aplicación lógica para que se muestren todas las filas de una tabla DB2, que solicite al conector que procese una instrucción SELECT para DB2 (por ejemplo, **SELECT * FROM AREA**).

### Definición de instancia de aplicación lógica
1.	En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2.	Escriba el **nombre** (por ejemplo, "**Db2getRows**"), la **suscripción**, el **grupo de recursos**, la **ubicación** y el **plan de App Service**. Haga clic en **Anclar al panel** y, después, seleccione **Crear**.

### Definición del desencadenador y la acción de la aplicación lógica
1.	En el **Diseñador de Logic Apps**, en la lista **Plantillas**, haga clic en **Blank LogicApp**.
2.	En la lista de **desencadenadores** haga clic en **Periodicidad**.
3.	En el desencadenador **Periodicidad**, haga clic en **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, haga clic en **Intervalo** y escriba **7**.
4.	Haga clic en el cuadro **+ Nuevo paso** y, a continuación, haga clic en **Agregar una acción**.
5.	En la lista de **acciones**, escriba **db2** en el cuadro de edición **Search for more actions** (Buscar más acciones) y, a continuación, haga clic en **DB2 - Get rows (Preview)** (DB2 - Obtener filas [Vista previa]).
6. En la acción **Get rows (Preview)**, haga clic en **Cambiar conexión**.
7. En el panel de configuración **Conexiones**, haga clic en **Crear nuevo**.

	![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
  
8. En el panel de configuración **Puertas de enlace**, deje la **casilla** **Connect via gateway** (Conectar a través de puerta de enlace) deshabilitada (no haga clic en ella).
	- Escriba un valor para **Nombre de conexión**. Por ejemplo, escriba **HISDEMO2**.
	- Escriba un valor para **Nombre del servidor DB2**, en forma de número de puerto con dirección o alias delimitado por dos puntos. Por ejemplo, escriba **HISDEMO2.cloudapp.net:50000**.
	- Escriba un valor para **Nombre de la base de datos DB2**. Por ejemplo, escriba **NWIND**.
	- Escriba un valor para **Nombre de usuario**. Por ejemplo, escriba **db2admin**.
	- Escriba un valor para **Contraseña**. Por ejemplo, escriba **Password1**.
9. Haga clic en **Crear** para continuar.

	![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

10. En la lista **Nombre de tabla**, haga clic en la **flecha abajo** y, a continuación, haga clic en **ÁREA**.
11. Opcionalmente, haga clic en **Mostrar opciones avanzadas** para especificar las opciones de consulta.
12. Haga clic en **Save**.

	![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)

13.	En la hoja **Db2getRows**, en la lista **Todas las ejecuciones** en **Resumen**, haga clic en el primer elemento de la lista (la ejecución más reciente).
14.	En la hoja **Ejecución de la aplicación lógica**, haga clic en **Detalles de ejecución**. En la lista de **acciones** haga clic en **Get\_rows**. Observe el valor de **Estado**, que debería ser **Correcto**. Haga clic en el **vínculo de entradas**. Vea las entradas. Haga clic en el **vínculo de salidas**. Vea las salidas, que deben incluir una lista de filas.

	![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## Definición de la aplicación lógica para agregar una fila mediante INSERT
Puede definir una acción de aplicación lógica para agregar una fila en una tabla DB2, que solicite al conector que procese una instrucción INSERT para DB2 (por ejemplo, **INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)**).

### Definición de instancia de aplicación lógica
1.	En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2.	Escriba el **nombre** (por ejemplo, "**Db2insertRow**"), la **suscripción**, el **grupo de recursos**, la **ubicación** y el **plan de App Service**. Haga clic en **Anclar al panel** y, después, seleccione **Crear**.

### Definición del desencadenador y la acción de la aplicación lógica
1.	En el **Diseñador de Logic Apps**, en la lista **Plantillas**, haga clic en **Blank LogicApp**.
2.	En la lista de **desencadenadores** haga clic en **Periodicidad**.
3.	En el desencadenador **Periodicidad**, haga clic en **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, haga clic en **Intervalo** y escriba **7**.
4.	Haga clic en el cuadro **+ Nuevo paso** y, a continuación, haga clic en **Agregar una acción**.
5.	En la lista de **acciones**, escriba **db2** en el cuadro de edición **Search for more actions** (Buscar más acciones) y, a continuación, haga clic en **DB2 - Insert row (Preview)** (DB2 - Insertar fila [Vista previa]).
6. En la acción **Get rows (Preview)**, haga clic en **Cambiar conexión**.
7. En el panel de configuración **Conexiones**, haga clic para seleccionar una conexión. Por ejemplo, haga clic en **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. En la lista **Nombre de tabla**, haga clic en la **flecha abajo** y, a continuación, haga clic en **ÁREA**.
9. Escriba valores para todas las columnas obligatorias (busque un asterisco rojo). Por ejemplo, escriba "**99999**" para **AREAID**, escriba "**Área 99999**" y escriba "**102**" para **REGIONID**.
10. Haga clic en **Save**.

	![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
 
11.	En la hoja **Db2insertRow**, en la lista **Todas las ejecuciones** en **Resumen**, haga clic en el primer elemento de la lista (la ejecución más reciente).
12.	En la hoja **Ejecución de la aplicación lógica**, haga clic en **Detalles de ejecución**. En la lista de **acciones** haga clic en **Get\_rows**. Observe el valor de **Estado**, que debería ser **Correcto**. Haga clic en el **vínculo de entradas**. Vea las entradas. Haga clic en el **vínculo de salidas**. Vea las salidas, que deben incluir la nueva fila.

	![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## Definición de la aplicación lógica para recuperar una fila con SELECT
Puede definir una acción de aplicación lógica para recuperar una fila de una tabla DB2, que solicite al conector que procese una instrucción SELECT WHERE para DB2 (por ejemplo, **SELECT FROM AREA WHERE AREAID = '99999'**).

### Definición de instancia de aplicación lógica
1.	En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2.	Escriba el **nombre** (por ejemplo, "**Db2getRow**"), la **suscripción**, el **grupo de recursos**, la **ubicación** y el **plan de App Service**. Haga clic en **Anclar al panel** y, después, seleccione **Crear**.

### Definición del desencadenador y la acción de la aplicación lógica
1.	En el **Diseñador de Logic Apps**, en la lista **Plantillas**, haga clic en **Blank LogicApp**.
2.	En la lista de **desencadenadores** haga clic en **Periodicidad**.
3.	En el desencadenador **Periodicidad**, haga clic en **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, haga clic en **Intervalo** y escriba **7**.
4.	Haga clic en el cuadro **+ Nuevo paso** y, a continuación, haga clic en **Agregar una acción**.
5.	En la lista de **acciones**, escriba **db2** en el cuadro de edición **Search for more actions** (Buscar más acciones) y, a continuación, haga clic en **DB2 - Get rows (Preview)** (DB2 - Obtener filas [Vista previa]).
6. En la acción **Get rows (Preview)**, haga clic en **Cambiar conexión**.
7. En el panel de configuración **Conexiones**, haga clic para seleccionar una conexión existente. Por ejemplo, haga clic en **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. En la lista **Nombre de tabla**, haga clic en la **flecha abajo** y, a continuación, haga clic en **ÁREA**.
9. Escriba valores para todas las columnas obligatorias (busque un asterisco rojo). Por ejemplo, escriba "**99999**" para **AREAID**.
10. Opcionalmente, haga clic en **Mostrar opciones avanzadas** para especificar las opciones de consulta.
11. Haga clic en **Save**.

	![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)

12.	En la hoja **Db2getRow**, en la lista **Todas las ejecuciones** en **Resumen**, haga clic en el primer elemento de la lista (la ejecución más reciente).
13.	En la hoja **Ejecución de la aplicación lógica**, haga clic en **Detalles de ejecución**. En la lista de **acciones** haga clic en **Get\_rows**. Observe el valor de **Estado**, que debería ser **Correcto**. Haga clic en el **vínculo de entradas**. Vea las entradas. Haga clic en el **vínculo de salidas**. Vea las salidas, que deben incluir la nueva fila.

	![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## Definición de la aplicación lógica para cambiar una fila con UPDATE
Puede definir una acción de aplicación lógica para cambiar una fila en una tabla DB2, que solicite al conector que procese una instrucción UPDATE para DB2 (por ejemplo, **UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)**).

### Definición de instancia de aplicación lógica
1.	En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2.	Escriba el **nombre** (por ejemplo, "**Db2updateRow**"), la **suscripción**, el **grupo de recursos**, la **ubicación** y el **plan de App Service**. Haga clic en **Anclar al panel** y, después, seleccione **Crear**.

### Definición del desencadenador y la acción de la aplicación lógica
1.	En el **Diseñador de Logic Apps**, en la lista **Plantillas**, haga clic en **Blank LogicApp**.
2.	En la lista de **desencadenadores** haga clic en **Periodicidad**.
3.	En el desencadenador **Periodicidad**, haga clic en **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, haga clic en **Intervalo** y escriba **7**.
4.	Haga clic en el cuadro **+ Nuevo paso** y, a continuación, haga clic en **Agregar una acción**.
5.	En la lista de **acciones**, escriba **db2** en el cuadro de edición **Search for more actions** (Buscar más acciones) y, a continuación, haga clic en **DB2 - Update row (Preview)** (DB2 - Actualizar fila [Vista previa]).
6. En la acción **Get rows (Preview)**, haga clic en **Cambiar conexión**.
7. En el panel de configuración **Conexiones**, haga clic para seleccionar una conexión existente. Por ejemplo, haga clic en **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. En la lista **Nombre de tabla**, haga clic en la **flecha abajo** y, a continuación, haga clic en **ÁREA**.
9. Escriba valores para todas las columnas obligatorias (busque un asterisco rojo). Por ejemplo, escriba "**99999**" para **AREAID**, escriba "**Actualizada 99999**" y escriba "**102**" para **REGIONID**.
10. Haga clic en **Save**.

	![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)

11.	En la hoja **Db2updateRow**, en la lista **Todas las ejecuciones** en **Resumen**, haga clic en el primer elemento de la lista (la ejecución más reciente).
12.	En la hoja **Ejecución de la aplicación lógica**, haga clic en **Detalles de ejecución**. En la lista de **acciones** haga clic en **Get\_rows**. Observe el valor de **Estado**, que debería ser **Correcto**. Haga clic en el **vínculo de entradas**. Vea las entradas. Haga clic en el **vínculo de salidas**. Vea las salidas, que deben incluir la nueva fila.

	![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## Definición de la aplicación lógica para quitar una fila con DELETE
Puede definir una acción de aplicación lógica para quitar una fila de una tabla DB2, que solicite al conector que procese una instrucción DELETE para DB2 (por ejemplo, **DELETE FROM AREA WHERE AREAID = '99999'**).

### Definición de instancia de aplicación lógica
1.	En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2.	Escriba el **nombre** (por ejemplo, "**Db2deleteRow**"), la **suscripción**, el **grupo de recursos**, la **ubicación** y el **plan de App Service**. Haga clic en **Anclar al panel** y, después, seleccione **Crear**.

### Definición del desencadenador y la acción de la aplicación lógica
1.	En el **Diseñador de Logic Apps**, en la lista **Plantillas**, haga clic en **Blank LogicApp**.
2.	En la lista de **desencadenadores** haga clic en **Periodicidad**.
3.	En el desencadenador **Periodicidad**, haga clic en **Editar**, después, en la lista desplegable **Frecuencia** para seleccionar **Día** y, a continuación, haga clic en **Intervalo** y escriba **7**.
4.	Haga clic en el cuadro **+ Nuevo paso** y, a continuación, haga clic en **Agregar una acción**.
5.	En la lista de **acciones**, escriba **db2** en el cuadro de edición **Search for more actions** (Buscar más acciones) y, a continuación, haga clic en **DB2 - Delete row (Preview)** (DB2 - Eliminar fila [Vista previa]).
6. En la acción **Get rows (Preview)**, haga clic en **Cambiar conexión**.
7. En el panel de configuración **Conexiones**, haga clic para seleccionar una conexión existente. Por ejemplo, haga clic en **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. En la lista **Nombre de tabla**, haga clic en la **flecha abajo** y, a continuación, haga clic en **ÁREA**.
9. Escriba valores para todas las columnas obligatorias (busque un asterisco rojo). Por ejemplo, escriba "**99999**" para **AREAID**.
10. Haga clic en **Save**.

	![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)

11.	En la hoja **Db2deleteRow**, en la lista **Todas las ejecuciones** en **Resumen**, haga clic en el primer elemento de la lista (la ejecución más reciente).
12.	En la hoja **Ejecución de la aplicación lógica**, haga clic en **Detalles de ejecución**. En la lista de **acciones** haga clic en **Get\_rows**. Observe el valor de **Estado**, que debería ser **Correcto**. Haga clic en el **vínculo de entradas**. Vea las entradas. Haga clic en el **vínculo de salidas**. Vea las salidas, que deben incluir la fila eliminada.

	![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## Detalles técnicos

## Acciones
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. El conector de la base de datos DB2 incluye las siguientes acciones.

|Acción|Description|
|--- | ---|
|[GetRow](connectors-create-api-db2.md#get-row)|Recupera una sola fila de una tabla DB2.|
|[GetRows](connectors-create-api-db2.md#get-rows)|Recupera filas de una tabla DB2.|
|[InsertRow](connectors-create-api-db2.md#insert-row)|Inserta una nueva fila en una tabla DB2.|
|[DeleteRow](connectors-create-api-db2.md#delete-row)|Elimina una fila de una tabla DB2.|
|[GetTables](connectors-create-api-db2.md#get-tables)|Recupera tablas de una base de datos DB2.|
|[UpdateRow](connectors-create-api-db2.md#update-row)|Actualiza una fila existente en una tabla DB2.|

### Detalles de la acción

En esta sección, consulte los detalles específicos acerca de cada acción, incluidas las propiedades de entrada obligatorias u opcionales y cualquier salida correspondiente asociada con el conector.

#### Obtener fila 
Recupera una sola fila de una tabla DB2.

| Nombre de propiedad| Display Name (Nombre para mostrar) |Description|
| ---|---|---|
|table * | Nombre de tabla |Nombre de la tabla DB2|
|id * | Identificador de fila |Identificador único de la fila que se va a recuperar|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|string|


#### Obtener filas 
Recupera filas de una tabla DB2.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Nombre de tabla|Nombre de la tabla DB2|
|$skip|Omitir conteo|Número de entradas para omitir (valor predeterminado = 0)|
|$top|Número máximo de entradas|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|Consulta de filtro|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|Ordenar por|Consulta orderBy de ODATA para especificar el orden de las entradas|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|


#### Insertar fila 
Inserta una nueva fila en una tabla DB2.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Nombre de tabla|Nombre de la tabla DB2|
|item*|Fila|Fila que se va a insertar en la tabla especificada en DB2|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|string|


#### Eliminar fila 
Elimina una fila de una tabla DB2.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Nombre de tabla|Nombre de la tabla DB2|
|id*|Identificador de fila|Identificador único de la fila que se va a eliminar|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.

#### Obtener tablas 
Recupera tablas de una base de datos DB2.

No hay parámetros para esta llamada.

##### Detalles de salida 
TablesList

| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|

#### Actualizar fila 
Actualiza una fila existente en una tabla DB2.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Nombre de tabla|Nombre de la tabla DB2|
|id*|Identificador de fila|Identificador único de la fila que se va a actualizar|
|item*|Fila|Fila con valores actualizados|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida  
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|string|


### Respuestas HTTP

Al realizar llamadas a las distintas acciones, es posible que obtenga determinadas respuestas. En la tabla siguiente se describen las respuestas y sus descripciones:

|Nombre|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## Versiones y plataformas DB2 compatibles
El conector admite estas plataformas y versiones de IBM DB2, así como los productos compatibles de este (por ejemplo, IBM Bluemix dashDB) que admiten las versiones 10 y 11 de SQL Access Manager (SQLAM) de la arquitectura distribuida de bases de datos relacionales (DRDA).

- IBM DB2 para z/OS 11.1
- IBM DB2 para z/OS 10.1
- IBM DB2 para i 7.3
- IBM DB2 para i 7.2
- IBM DB2 para i 7.1
- IBM DB2 para LUW 11
- IBM DB2 para LUW 10.5


## Pasos siguientes

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0921_2016-->