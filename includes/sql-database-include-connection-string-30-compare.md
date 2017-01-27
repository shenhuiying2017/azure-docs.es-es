
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Comparación de la cadena de conexión
La siguiente tabla compara las cadenas de conexión que necesita el programa de C# para conectarse al  SQL Server local y la de la base de datos SQL de Azure en la nube. Las diferencias están resaltadas en negrita.

| Cadena de conexión para <br/>Base de datos SQL de Azure | Cadena de conexión para <br/>Microsoft SQL Server |
|:--- |:--- |
| Server=**tcp:**{el_NombreDeServidor_aquí}**.database.windows.net,1433**;<br/>Id. de usuario={el_NombreDeIniciodeSesión_aquí}**@{your_serverName_here}**;<br/>Contraseña={su_contraseña_aquí};<br/>**Base de datos = {el_NombreDeBasedeDatos_aquí};**<br/>**Tiempo de espera de conexión=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; |Server={el_NombredeServidor_aquí};<br/>Id. de usuario={el_NombreDeIniciodeSesión_aquí};<br/>Contraseña={su_contraseña_aquí}; |

El parámetro **Database=** es opcional para SQL Server, pero es obligatorio para la Base de datos SQL.

[Propiedades de SqlConnectionStringBuilder de .NET ADO](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) se explican con detalle todos los parámetros.

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Jan17_HO3-->


