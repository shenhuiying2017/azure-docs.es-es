
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. En la lista de la izquierda, seleccione **Examinar**. 

3. Desplácese y seleccione **Servidores SQL Server**. 
   
    ![Encontrar el servidor de Azure SQL Database en el portal][b21-FindServerInPortal]
4. Para mayor comodidad, minimice la hoja **Examinar**.

5. En el cuadro de texto de filtro, empiece a escribir el nombre del servidor. Aparecerá su fila.

6. Seleccione la fila para el servidor. Aparecerá una hoja para el servidor.

7. En la hoja del servidor, seleccione **Configuración**. 

8. Seleccione **Firewall**. 
   
    ![Selección de Configuración > Firewall][b31-SettingsFirewallNavig]
9. Seleccione **Agregar IP de cliente**. Escriba un nombre para la regla nueva en el primer cuadro de texto.

10. Escriba los valores de dirección IP inferior y superior para el intervalo que quiere habilitar.
    
    * Puede resultar útil que el valor inferior termine en **.0** y el valor alto en **.255**.
    
    ![Agregar un intervalo de direcciones IP para permitir][b41-AddRange]
11. Seleccione **Guardar**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
