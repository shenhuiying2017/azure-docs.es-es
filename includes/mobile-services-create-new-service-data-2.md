Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla en la instancia de Base de datos SQL asociada.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

    ![mobile-data-tab-empty][]

    Esto muestra el cuadro de diálogo **Create new table**.

3.  En **Table name**, escriba *TodoItem* y, a continuación, haga clic en el botón de comprobación.

    ![mobile-create-todoitem-table][]

Se crea la nueva tabla de almacenamiento **TodoItem** con los permisos predeterminados definidos. Esto significa que cualquiera que tenga la clave de aplicación, que se distribuye con la aplicación, puede tener acceso a los datos de la tabla y modificarlos.

> [WACOM.NOTE]
> El mismo nombre de tabla se utiliza en la guía de inicio rápido de Servicios móviles. Sin embargo, cada tabla se crea en un esquema que es específico de un servicio móvil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios móviles utilizan la misma base de datos.

1.  Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

2.  Haga clic en la pestaña **Columns**. Verifique que las siguientes columnas predeterminadas se hayan creado automáticamente:

    | Nombre de columna | Tipo                    | Índice                             |
    |-------------------|-------------------------|------------------------------------|
    | id                | cadena                  | Indizada                           |
    | \_\_createdAt     | fecha                   | Indizada                           |
    | \_\_updatedAt     | fecha                   | <font color="transparent">-</font> |
    | \_\_version       | marca de tiempo (MSSQL) | <font color="transparent">-</font> |

    Este es el requisito mínimo para una tabla en Servicios móviles.

    <div class="dev-callout"><b>Nota:</b>
<p>Cuando est&aacute; habilitado el esquema din&aacute;mico en su servicio m&oacute;vil, se crean columnas autom&aacute;ticamente cuando se env&iacute;an objetos JSON al servicio m&oacute;vil mediante una operaci&oacute;n de inserci&oacute;n o de actualizaci&oacute;n.</p>
</div>

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

  [mobile-data-tab-empty]: ./media/mobile-services-create-new-service-data-2/mobile-data-tab-empty.png
  [mobile-create-todoitem-table]: ./media/mobile-services-create-new-service-data-2/mobile-create-todoitem-table.png
