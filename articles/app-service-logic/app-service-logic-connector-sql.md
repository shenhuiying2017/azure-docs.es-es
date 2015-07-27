<properties
   pageTitle="Uso del conector de SQL en el Servicio de aplicaciones de Microsoft Azure"
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
   ms.date="06/30/2015"
   ms.author="sameerch"/>


# Conector de Microsoft SQL

Conéctese a un servidor SQL Server o a una Base de datos SQL de Azure local para crear y cambiar información o datos. Los conectores pueden utilizarse en aplicaciones lógicas para recuperar, procesar o insertar datos como parte de un “flujo de trabajo”. Al utilizar el conector de SQL en su flujo de trabajo, puede conseguir distintos escenarios. Por ejemplo, puede:

- Exponer una parte de los datos que residen en la base de datos SQL a través de una aplicación móvil o web.
- Insertar los datos en la tabla de base de datos de SQL para almacenarlos. Por ejemplo, puede especificar los registros de empleados, actualizar los pedidos de ventas y así sucesivamente.
- Obtener datos de SQL para usarlos en un proceso empresarial. Por ejemplo, puede obtener los registros de clientes y colocarlos en SalesForce.

## Acciones y desencadenadores
Los *desencadenadores* son eventos que se producen. Por ejemplo, cuando se actualiza un pedido o cuando se agrega un cliente nuevo. Un *acción* es el resultado del desencadenador. Por ejemplo, cuando se actualiza un pedido,se envía una alerta al vendedor. O bien, cuando se agrega un nuevo cliente, a este se le envía un correo electrónico de bienvenida.

El conector de SQL puede usarse como un desencadenador o una acción en una aplicación lógica y es compatible con los datos en formato JSON y XML. Para cada tabla incluida en la configuración del paquete (puede encontrar información adicional al respecto más adelante en este tema), hay un conjunto de acciones JSON y un conjunto de acciones XML.

El conector de SQL tiene los siguientes desencadenadores y acciones disponibles:

Desencadenadores | Acciones
--- | ---
Datos de sondeo | <ul><li>Insertar en tabla</li><li>Actualizar tabla</li><li>Seleccionar en tabla</li><li>Eliminar de tabla</li><li>Llamar a procedimiento almacenado</li></ul>

## Creación del conector de SQL

Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Seleccione **Aplicaciones de API** y busque "Conector de SQL".
3. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades.
4. Escriba la siguiente configuración del paquete:

	Nombre | Obligatorio | Descripción
--- | --- | ---
Nombre del servidor | Sí | Especifique el nombre del servidor SQL Server. Por ejemplo, escriba *SQLserver/sqlexpress* o *SQLserver.mydomain.com*.
Port | No | El valor predeterminado es 1433.
User Name | Sí | Escriba un nombre de usuario con el que puede iniciar sesión en el servidor SQL Server. Si se va a conectar a un servidor local SQL Server, escriba las credenciales de autenticación de SQL.
Password | Sí | Escriba la contraseña del nombre de usuario.
Nombre de la base de datos | Sí | Especifique la base de datos a la que se está conectando. Por ejemplo, puede escribir *Clientes* o *dbo/pedidos*.
Local | Sí | El valor predeterminado es False. Escriba False si se conecta a una base de datos SQL de Azure. Escriba True si se conecta a un servidor SQL Server local.
Cadena de conexión del bus de servicio | No | Si se conecta en un entorno local, escriba la cadena de conexión de Retransmisión de bus de servicio.<br/><br/>[Uso del Administrador de conexiones híbridas](app-service-logic-hybrid-connection-manager.md)<br/>[Precios de Bus de servicio](http://azure.microsoft.com/pricing/details/service-bus/)
Nombre del servidor asociado | No | Si el servidor principal no está disponible, puede especificar un servidor asociado como un servidor de copia de seguridad o alternativo.
Tablas | No | Enumere las tablas de base de datos que se pueden actualizar mediante el conector. Por ejemplo, escriba *OrdersTable* o *EmployeeTable*. Si no se especifica ninguna tabla, todas las tablas pueden usarse. Las tablas válidas o los procedimientos almacenados son necesarios para usar este conector como una acción.
Procedimientos almacenados | No | Escriba un procedimiento almacenado que se pueda llamar mediante el conector. Por ejemplo, escriba *sp_IsEmployeeEligible* o *sp_CalculateOrderDiscount*. Las tablas válidas o los procedimientos almacenados son necesarios para usar este conector como una acción.
Consulta de datos disponibles | Para la compatibilidad del desencadenador | Instrucción SQL para determinar si los datos están disponibles para el sondeo de una tabla de la base de datos de SQL Server. Esto debería devolver un valor numérico que representa el número de filas de datos disponibles. Ejemplo: SELECT COUNT(*) from table_name.
Consulta de sondeo de datos | Para la compatibilidad del desencadenador | La instrucción SQL para sondear la tabla de base de datos de SQL Server. Puede especificar cualquier número de instrucciones SQL separadas por un signo de punto y coma. Esta instrucción se ejecuta transaccionalmente y se confirma solo cuando los datos estén almacenados de forma segura en la aplicación lógica. Ejemplo: SELECT * FROM table_name; DELETE FROM table_name. <br/><br/>**Nota**<br/>Debe proporcionar una instrucción de sondeo que evite un bucle infinito mediante la eliminación, la transferencia o la actualización de los datos seleccionados para garantizar que no se vuelven a sondear los mismos datos.

5. Cuando haya terminado, la configuración del paquete tiene un aspecto similar al siguiente: <br/> ![][1]

## Uso del conector como desencadenador
Veamos una aplicación lógica sencilla que sondea datos en una tabla de SQL, agrega los datos en otra tabla y actualiza los datos.

Para usar el conector de SQL como un desencadenador, especifique los valores **Consulta de datos disponibles** y **Sondear consulta de datos**. **Consulta de datos disponibles** se ejecuta en la programación que especifique y determina si hay algún dato disponible. Dado que esta consulta solo devuelve un número escalar, puede adaptarse y optimizarse para una ejecución frecuente.

**Sondear consulta de datos** solo se ejecuta cuando la consulta de datos disponibles indica que hay datos disponibles. Esta instrucción se ejecuta dentro de una transacción y solo se confirma cuando los datos extraídos se almacenan de forma duradera en el flujo de trabajo. Es importante evitar volver a extraer infinitamente los mismos datos. La naturaleza transaccional de esta ejecución puede utilizarse para eliminar o actualizar los datos a fin de asegurarse de que no se recopilan la próxima vez que se consulten los datos.

> [AZURE.NOTE]El esquema devuelto por esta instrucción identifica las propiedades disponibles en el conector. Todas las columnas deben tener nombres.

#### Ejemplo de consulta de datos disponibles

	SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### Ejemplo de sondeo de consulta de datos

	SELECT *, GetData() as 'PollTime' FROM [Order]
		WHERE OrderStatus = 'ProcessedForCollection'
		ORDER BY Id DESC;
	UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
		WHERE Id =
		(SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### Incorporación del desencadenador
1. Al crear o editar una aplicación lógica, seleccione el conector de SQL que ha creado como desencadenador. Se mostrarán los desencadenadores disponibles: **Datos de sondeo (JSON)** y **Datos de sondeo (XML)**: <br/> ![][5]

2. Seleccione el desencadenador **Datos de sondeo (JSON)**, especifique la frecuencia y haga clic en el signo ✓: <br/> ![][6]

3. El desencadenador aparece ahora como configurado en la aplicación lógica. Se mostrarán las salidas del desencadenador y se podrán utilizar como entradas en un paso posterior: <br/> ![][7]

## Uso del conector como acción
Veamos el escenario de una aplicación lógica sencilla que sondea datos en una tabla de SQL, agrega los datos en otra tabla y actualiza los datos.

Para usar el conector de SQL como una acción, escriba el nombre de las tablas o procedimientos almacenados que especificó al crear el conector de SQL:

1. Después del desencadenador (o seleccione “Ejecutar esta lógica manualmente”), agregue el conector de SQL que ha creado desde la Galería. Seleccione una de las acciones de inserción, como *insertar en TempEmployeeDetails (JSON)*: <br/> ![][8]

2. Escriba los valores de entrada del registro que se va a insertar y haga clic en el signo ✓: <br/> ![][9]

3. En la Galería, seleccione el mismo conector de SQL que ha creado. Como una acción, seleccione la acción de actualización en la misma tabla, como *actualizar EmployeeDetails*: <br/> ![][11]

4. Escriba los valores de entrada para la acción de actualización y haga clic en el signo ✓: <br/> ![][12]

Puede probar la aplicación lógica mediante la adición de un nuevo registro en la tabla que se sondea.

## Lo que puede y no puede hacer

Consulta SQL | Compatible | No compatible
--- | --- | ---
Cláusula WHERE | <ul><li>Operadores: AND, OR, =, <>, <, <=, >, >= y LIKE</li><li>Se pueden combinar varias subcondiciones mediante ‘(‘ y ‘)’</li><li>Literales de cadena, Fecha/hora (entre comillas sencillas), números (solo deben contener caracteres numéricos)</li><li>Debe estar de forma estricta en un formato de expresión binaria, como ((operando operador operando) AND/OR (operando operador operando))*</li></ul> | <ul><li>Operadores: Between, IN</li><li>Todas las funciones integradas como ADD(), MAX() NOW(), POWER(), etc.</li><li>Operadores matemáticos como *, -, +, etc.</li><li>Concatenaciones de cadenas con +.</li><li>Todas las combinaciones</li><li>IS NULL y IS NOT Null</li><li>Cualquier número sin caracteres numéricos, como números hexadecimales</li></ul> Campos (en consulta Select) | <ul><li>Nombres de columna válidos separados por coma. No se permiten prefijos de nombre de tabla (el conector funciona en una tabla cada vez).</li><li>Los nombres se pueden escapar con ' [' y ']'</li></ul>|<ul><li>Palabras clave como TOP, DISTINCT, etc.</li><li>Alias, como calle + ciudad + código postal como dirección</li><li>Todas las funciones integradas, como ADD(), MAX() NOW(), POWER(), etc.</li><li>Operadores matemáticos, como *-, +, etc.</li><li>Concatenaciones de cadenas mediante +</li></ul>

#### Sugerencias

- Para consultas avanzadas, le recomendamos que cree un procedimiento almacenado y que lo ejecute con la API para ejecutar procedimientos almacenados.
- Cuando use consultas internas, úselas dentro de procedimientos almacenados.
- Para unir varias condiciones, puede usar los operadores 'AND' y 'OR'.

## Configuración híbrida (opcional)

> [AZURE.NOTE]Este paso solo es necesario si está utilizando SQL Server local detrás del firewall.

El Servicio de aplicaciones utiliza el Administrador de configuración híbrida para conectarse de forma segura al sistema local. Si el conector usa SQL Server en un entorno local, se necesita el Administrador de conexiones híbridas.

Consulte [Uso del Administrador de conexiones híbridas](app-service-logic-hybrid-connection-manager.md).


## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

Cree las aplicaciones de API mediante las API de REST. Consulte [Referencia sobre conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png

<!---HONumber=July15_HO3-->