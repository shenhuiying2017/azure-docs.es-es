<properties 
   pageTitle="Conector de SQL" 
   description="Uso del conector de SQL" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Conector de Microsoft SQL #

Los conectores pueden utilizarse en aplicaciones lógicas para capturar, procesar o insertar datos como parte de un flujo. Hay escenarios donde puede ser necesario trabajar con la base de datos SQL en Azure SQL o SQL Server, que se instala localmente y detrás del firewall). Al utilizar el conector de SQL en su flujo de trabajo, puede conseguir distintos escenarios. Ejemplos:  

1.	Exponer una parte de los datos que residen en la base de datos SQL a través de un front-end de usuario móvil o web.
2.	Inserte los datos en la tabla de base de datos de SQL para su almacenamiento (ejemplo: registros de empleados, pedidos de ventas, etc.).
3.	Extraer datos de SQL para su uso en un proceso empresarial.

Para estos escenarios, deben cumplirse las siguientes condiciones: 

1. Creación de una instancia de la aplicación de API del conector de SQL
2. Establecimiento de una conectividad híbrida para que la aplicación de API se comunique con el SQL local. Este paso es opcional y solo es necesario para el SQL Server local; no para SQL Azure.
3. Utilización de la aplicación de API creada en una aplicación lógica para lograr el proceso empresarial deseado

	###Acciones y desencadenadores básicos
		
    - Datos de sondeo (desencadenador) 
    - Insertar en tabla
    - Actualizar tabla
    - Seleccionar en tabla
    - Eliminar de tabla
    - Llamar a procedimiento almacenado

## Creación de una instancia de la aplicación de API del conector de SQL ##

Para usar el conector de SQL, deberá crear primero una instancia de la aplicación de API del conector de SQL. Se puede realizar del modo siguiente:

1. Abra Azure Marketplace mediante la opción "+ Nuevo", situada en la parte inferior izquierda del Portal de Azure.
2. Vaya a "Web y móvil > Aplicaciones de API" y busque "Conector de SQL".
3. Proporcione detalles genéricos, por ejemplo, el nombre, el plan de servicio de aplicación, etc., en la primera hoja.
4. Proporcione la configuración del paquete mencionada en la tabla siguiente.	

<style type="text/css">
	table.tableizer-table {
	border: 1px solid #CCC; font-family: Arial, Helvetica, sans-serif;
	font-size: 12px;
} 
.tableizer-table td {
	padding: 4px;
	margin: 3px;
	border: 1px solid #ccc;
}
.tableizer-table th {
	background-color: #525B64; 
	color: #FFF;
	font-weight: bold;
}
</style><table class="tableizer-table">
<tr class="tableizer-firstrow"><th>Nombre</th><th>Obligatorio</th><th>Valor predeterminado</th><th>Descripción</th></tr>
 <tr><td>Nombre del servidor</td><td>Sí</td><td>&nbsp;</td><td>Especifique el nombre del SQL Server. Ejemplo: "SQLserver", "SQLserver/sqlexpress" o "SQLserver.mydomain.com".</td></tr>
 <tr><td>Port</td><td>No</td><td> 1433</td><td>Opcional. El número de puerto donde se establece la conexión. Si no especifica ningún valor, el conector se conecta a través del puerto predeterminado.</td></tr>
 <tr><td>User Name</td><td>Sí</td><td>&nbsp;</td><td>Especifique un nombre de usuario válido para conectarse al SQL Server.</td></tr>
 <tr><td>Password</td><td>Sí</td><td>&nbsp;</td><td>Especifique una contraseña válida para conectarse al SQL Server.</td></tr>
 <tr><td>Nombre de la base de datos</td><td>Sí</td><td>&nbsp;</td><td>Especifique un nombre de la base de datos válido en el SQL Server. Ejemplo: "orders" o "dbo/orders" o "myaccount/employees".</td></tr>
 <tr><td>Local</td><td>Sí</td><td>FALSE</td><td>Especifique si el SQL Server es local y se encuentra detrás de un firewall o no. Si se establece en TRUE, deberá instalar un agente de escucha en un servidor que puede tener acceso al SQL Server. Puede ir a la página de resumen de la aplicación de API y hacer clic en 'Conexión híbrida' para instalar el agente.</td></tr>
 <tr><td>Cadena de conexión del bus de servicio</td><td>No</td><td>&nbsp;</td><td>Opcional. Especifique este parámetro si el SQL Server es local. Debe ser una cadena de conexión del espacio de nombres del bus de servicio que sea válida. Asegúrese de usar la edición 'Estándar' del Bus de servicio de Azure, no  'Básico'.</td></tr>
 <tr><td>Nombre del servidor asociado</td><td>No</td><td>&nbsp;</td><td>Opcional. Especifique el servidor asociado al que conectarse cuando el servidor principal está inactivo.</td></tr>
 <tr><td>Tablas</td><td>No</td><td>&nbsp;</td><td>Opcional. Especifique las tablas de la base de datos que pueden modificarse mediante el conector. P. ej.: OrdersTable,EmployeeTable</td></tr>
 <tr><td>Procedimientos almacenados</td><td>No</td><td>&nbsp;</td><td>Opcional. Especifique los procedimientos almacenados en la base de datos que se pueden llamar mediante el conector. Ejemplo: IsEmployeeEligible,CalculateOrderDiscount</td></tr>
 <tr><td>Consulta de datos disponibles</td><td>No</td><td>&nbsp;</td><td>Opcional. Especifique la instrucción SQL para determinar si los datos están disponibles para el sondeo de una tabla de la base de datos de SQL Server. Ejemplo: SELECT COUNT(*) from table_name.</td></tr>
 <tr><td>Consulta de sondeo de datos</td><td>No</td><td>&nbsp;</td><td>Opcional. Especifique la instrucción SQL para sondear la base de datos de SQL Server. Puede especificar cualquier número de instrucciones SQL separadas por un signo de punto y coma. Ejemplo: SELECT * from table_name; DELETE from table_name. NOTA: Tendrá que proporcionar la instrucción de sondeo de forma que no termine en un bucle infinito. Por ejemplo, "select" debe ir seguida de un "delete" y "select" basado en una marca debe ir seguida de un "update" de la marca.</td></tr>
</table>


 ![][1]  

## Configuración híbrida (opcional) ##

Nota: Este paso solo es necesario si está utilizando SQL Server local detrás de firewall.

Acceda a la aplicación de API que acaba de crear mediante Examinar -> Aplicaciones de API -> <Nombre de la aplicación de API que acaba de crear>. Verá el siguiente comportamiento. La instalación está incompleta, ya que todavía no se ha establecido la conexión híbrida.

![][2] 

Para establecer la conectividad híbrida, haga lo siguiente:

1. Copie la cadena de conexión principal.
2. Haga clic en el vínculo 'Descargar y configurar'
3. Siga el proceso de instalación que se inicia y especifique la cadena de conexión principal cuando se le pida.
4. Una vez que se completa el proceso de instalación, se muestra un cuadro de diálogo similar al siguiente.

![][3] 

Ahora, cuando acceda de nuevo a la aplicación de API que ha creado, verá que el estado de la conexión híbrida se muestra como "conectado". 

![][4] 

Nota: si desea cambiar a la cadena de conexión secundaria, solo tiene que volver a realizar la configuración híbrida y proporcionar la cadena de conexión secundaria en lugar de la cadena de conexión principal.  

## Uso en una aplicación lógica ##

El conector de SQL puede usarse como una acción/desencadenador en una aplicación lógica. El desencadenador y todas las acciones admiten el formato de datos JSON y XML. Para cada tabla que se proporciona como parte de la configuración del paquete, habrá un conjunto de acciones JSON y un conjunto de acciones XML. Si se utiliza la acción/desencadenador XML, puede utilizar la aplicación de API de transformación para convertir datos en otro formato de datos XML. 

Veamos una aplicación lógica sencilla que sondea datos en una tabla de SQL, agrega los datos en otra tabla y actualiza los datos.



-  Al crear o editar una aplicación lógica, elija la aplicación de API del conector de SQL creada como desencadenador. Se mostrarán los desencadenadores disponibles: datos de sondeo (JSON) y datos de sondeo (XML).

 ![][5] 


- Seleccione el desencadenador, como datos de sondeo (JSON), especifique la frecuencia y haga clic en el signo ✓.

![][6] 



- El desencadenador aparecerá ahora como configurado en la aplicación lógica. Se mostrarán las salidas del desencadenador y se podrán utilizar como entradas en un paso posterior. 

![][7] 


- Seleccione el mismo conector de SQL de la Galería como una acción. Seleccione una de las acciones de inserción, como insertar en TempEmployeeDetails (JSON).

![][8] 



- Proporcione las entradas del registro que se va a insertar y haga clic en el signo ✓. 

![][9] 



- Seleccione el mismo conector de SQL de la Galería como una acción. Seleccione la acción de actualización en la misma tabla (por ejemplo: Update EmployeeDetails)

![][11] 



- Proporcione las entradas para la acción de actualización y haga clic en el signo ✓. 

![][12] 

Puede probar la aplicación lógica mediante la adición de un nuevo registro en la tabla que se sondea.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.jpg
[2]: ./media/app-service-logic-connector-sql/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sql/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sql/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sql/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sql/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sql/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-sql/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-sql/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-sql/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-sql/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-sql/LogicApp8.jpg




<!--HONumber=52--> 