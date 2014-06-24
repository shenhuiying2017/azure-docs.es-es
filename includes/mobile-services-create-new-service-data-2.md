Para poder almacenar datos de aplicaciones en el nuevo servicio móvil,
primero debe crear una tabla en la instancia de Base de datos SQL
asociada.

1.  En el Portal de administración, haga clic en **Servicios móviles**
    y, a continuación, en el servicio móvil que acaba de crear.

2.  Haga clic en la pestaña **Data** y, a continuación, en **+Create**.
    
        ![mobile-data-tab-empty](./media/mobile-services-create-new-service-data-2/mobile-data-tab-empty.png)
        
        Se muestra el cuadro de diálogo **Crear nueva tabla**.

3.  En **Table name**, escriba *TodoItem* y, a continuación, haga clic
    en el botón de comprobación.

	 ![mobile-create-todoitem-table](./media/mobile-services-create-new-service-data-2/mobile-create-todoitem-table.png) 

Se crea la nueva tabla de almacenamiento **TodoItem** con los permisos
predeterminados definidos. Esto significa que cualquiera que tenga la
clave de aplicación, que se distribuye con la aplicación, puede tener
acceso a los datos de la tabla y modificarlos.

> [WACOM.NOTE] El mismo nombre de tabla se utiliza en la guía de
> inicio rápido de Servicios móviles. Sin embargo, cada tabla se crea en
> un esquema que es específico de un servicio móvil determinado. De esta
> manera, se evita que colisionen los datos cuando varios servicios
> móviles utilizan la misma base de datos.

1.  Haga clic en la nueva tabla **TodoItem** y verifique que no haya
    filas de datos.

2.  Haga clic en la pestaña **Columns**. Verifique que las siguientes
    columnas predeterminadas se hayan creado automáticamente:
    
    <table  border="1" cellpadding="10">
     	<tr>
     	<th>Nombre de columna</th>
    
     	<th>Tipo</th>
    
     	<th>Índice</th>
    
     	</tr>
    
     	<tr>
     	<td>id.</td>
    
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
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
    
     	<tr>
     	<td>__version</td>
    
     	<td>marca de tiempo (MSSQL)</td>
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
     	
     	</table>

Este es el requisito mínimo para una tabla en Servicios móviles.     


<div  class="dev-callout">

<b>Nota:</b>

<p>Cuando está habilitado el esquema dinámico en su servicio móvil, se crean columnas automáticamente cuando se envían objetos JSON al servicio móvil mediante una operación de inserción o de actualización.</p>
</div>


Ahora ya está listo para utilizar el nuevo servicio móvil como
almacenamiento de datos para la aplicación.

