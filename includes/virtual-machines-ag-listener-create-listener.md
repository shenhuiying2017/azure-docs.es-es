En este paso, creas manualmente el agente de escucha del grupo de disponibilidad en el Administrador de clústeres de conmutación por error y en SQL Server Management Studio (SSMS).

1. Abre el Administrador de clústeres de conmutación por error desde el nodo que aloja la réplica principal.
2. Seleccione el nodo **Redes** y anota el nombre de red del clúster. Este nombre se utilizará en la variable $ClusterNetworkName en el script de PowerShell.
3. Amplía el nombre del clúster y haz clic en **Funciones**.
4. En el panel **Roles**, haga clic en el nombre del grupo de disponibilidad y después seleccione **Agregar recurso** > **Punto de acceso cliente**.
   
    ![Agrega un Punto de acceso cliente para el Grupo de disponibilidad](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)
5. En la casilla **Nombre**, cree un nombre para este nuevo agente de escucha, después haga clic en **Siguiente** dos veces y haga clic en **Finalizar**. No pongas el agente de escucha o el recurso en línea en este momento.
6. Haz clic en la pestaña **Recursos** y después amplía el punto de acceso de cliente que acabas de crear. Verás el recurso **Dirección IP** para todas las redes del clúster en tu clúster. Si esta es una solución solo de Azure, solo verás un recurso de dirección IP.
7. Si estás configurando una solución híbrida, sigue con este paso. Si estás configurando una solución solo de Azure, salta al paso siguiente. 
   
   * Haz clic con el botón derecho en el recurso de dirección IP que corresponde a tu subred local y después selecciona **Propiedades**. Anota el nombre de la dirección IP y el nombre de red.
   * Selecciona **Dirección IP estática**, asigna una dirección IP sin usar y haz clic en **Aceptar**.
8. Haz clic con el botón derecho en el recurso de dirección IP que corresponde a tu subred de Azure y después selecciona Propiedades.
   
   > [!NOTE]
   > Si el agente de escucha no se puede poner en conexión más adelante debido a una dirección IP en conflicto seleccionada por DHCP, puedes configurar una dirección IP estática válida en esta ventana de propiedades.
   > 
   > 
9. En la misma ventana de propiedades de la **Dirección IP**, cambia el valor de **Nombre de dirección IP**. Este nombre de dirección IP se utilizará en la variable **$IPResourceName** del script de PowerShell. Repite este paso en cada recurso de IP si tu solución abarca varias redes virtuales de Azure.



<!--HONumber=Nov16_HO3-->


