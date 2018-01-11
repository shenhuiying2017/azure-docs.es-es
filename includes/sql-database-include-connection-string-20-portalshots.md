
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Obtenga la cadena de conexión del portal de Azure
Utilice [Azure Portal](https://portal.azure.com/) para obtener la cadena de conexión necesaria para que su programa cliente interactúe con Azure SQL Database. 

1. Seleccione **Examinar todo** > **Bases de datos SQL**.

2. Escriba el nombre de la base de datos en el cuadro de texto de filtro situado en la esquina superior izquierda de la hoja **Bases de datos SQL**.

3. Seleccione la fila correspondiente a la base de datos.

4. Cuando aparezca la hoja de su base de datos, para una mayor comodidad visual puede seleccionar los botones **Minimizar** para contraer las hojas que utilizó para examinar y filtrar la base de datos. 
   
    ![Aplique un filtro para aislar la base de datos][10-FilterDatabase]
5. En la hoja de la base de datos, seleccione **Mostrar cadenas de conexión de la base de datos**.

6. Si piensa utilizar la biblioteca de conexiones de ADO.NET, copie la cadena etiquetada con **ADO**. 
   
    ![Copie la cadena de conexión ADO correspondiente a la base de datos][20-CopyAdoConnectionString]
7. En un formato u otro, pegue la información de la cadena de conexión en el código del programa cliente.

Para obtener más información, consulte [Cadenas de conexión y archivos de configuración](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
