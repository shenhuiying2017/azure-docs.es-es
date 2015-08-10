Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla en la instancia de Base de datos SQL asociada.

1. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

   	Esto muestra el cuadro de diálogo **Create new table**.

3. En **Nombre de tabla** escriba _TodoItem_ y, a continuación, haga clic en el botón de comprobación.

  Se crea la nueva tabla de almacenamiento **TodoItem** con los permisos predeterminados definidos. Esto significa que cualquiera que tenga la clave de aplicación, que se distribuye con la aplicación, puede tener acceso a los datos de la tabla y modificarlos.

	>[AZURE.NOTE] The same table name is used in Mobile Services quickstart. However, each table is created in a schema that is specific to a given mobile service. This is to prevent data collisions when multiple mobile services use the same database.

4. Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

5. Haga clic en la pestaña **Columnas**. Compruebe que las siguientes columnas predeterminadas se hayan creado automáticamente:
	
	<table border="1" cellpadding="10">
<tr>
<th>Nombre de columna</th>
<th>Escriba</th>
<th>Índice</th>
</tr>
<tr>
<td>id</td>
<td>cadena</td>
<td>Indizada</td>
</tr>
<tr>
<td>__createdAt</td>
<td>fecha</td>
<td>Indizada</td>
</tr>
<tr>
<td>__updatedAt</td>
<td>fecha</td>
<td><font color="transparent">-</font></td>
</tr>
<tr>
<td>__version</td>
<td>marca de tiempo (MSSQL)</td>
<td><font color="transparent">-</font></td>
</tr> 	
</table>Este es el requisito mínimo para una tabla en Servicios móviles.

    > [AZURE.NOTE]Cuando está habilitado el esquema dinámico en su servicio móvil, se crean columnas automáticamente cuando se envían objetos JSON al servicio móvil mediante una operación de inserción o de actualización.

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

<!---HONumber=July15_HO5-->