
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2015-09-04 , GeneMi.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) en http://portal.azure.com/.

2. En el banner de la izquierda, haga clic en **Examinar**. Se mostrará la hoja **Examinar**.

3. Desplácese y haga clic en **Servidores SQL Server**. Aparecerá la hoja **Servidores SQL Server**.

	![Encontrar el servidor de base de datos SQL de Azure en el portal][b21-FindServerInPortal]

4. Haga clic en la fila para el servidor. Aparecerá una hoja para el servidor.

5. En la hoja del servidor, haga clic en **Configuración**. Se mostrará la hoja **Configuración**.

6. Haga clic en **Firewall**. Se mostrará la hoja **Configuración de firewall**.

	![Haga clic en > Firewall.][b31-SettingsFirewallNavig]

7. Haga clic en **Agregar IP de cliente** para agregar la dirección IP de su equipo cliente. El portal generará un nombre para la regla. O bien puede escribir un nombre y especificar los valores de dirección IP inferior y superior para el intervalo que quiere habilitar. Puede resultar útil que el valor inferior termine por **,0** y el valor alto por **,255**.

	![Agregar un intervalo de direcciones IP para permitir][b41-AddRange]

8. Haga clic en **Guardar**.



<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->

<!---HONumber=AcomDC_0316_2016-->