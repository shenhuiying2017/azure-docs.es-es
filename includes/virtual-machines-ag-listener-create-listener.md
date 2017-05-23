En este paso, se crea manualmente el agente de escucha del grupo de disponibilidad en el Administrador de clústeres de conmutación por error y SQL Server Management Studio.

1. Abra el Administrador de clústeres de conmutación por error desde el nodo que hospeda la réplica principal.

2. Seleccione el nodo **Redes** y anote el nombre de red del clúster. Este nombre se utilizará en la variable $ClusterNetworkName en el script de PowerShell.

3. Amplía el nombre del clúster y haz clic en **Funciones**.

4. En el panel **Roles**, haga clic con el botón derecho en el nombre del grupo de disponibilidad y después seleccione **Agregar recurso** > **Punto de acceso cliente**.
   
    ![Agregar un punto de acceso cliente para el grupo de disponibilidad](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. En la casilla **Nombre**, cree un nombre para este nuevo agente de escucha, después haga clic en **Siguiente** dos veces y haga clic en **Finalizar**.  
    No pongas el agente de escucha o el recurso en línea en este momento.

6. Haga clic en la pestaña **Recursos** y después expanda el punto de acceso cliente que acaba de crear. 
    Se muestra el recurso de dirección IP de cada red del clúster existente. Si se trata de una solución solo de Azure, se muestra un único recurso de dirección IP.

7. Realice cualquiera de las siguientes acciones:
   
   * Para configurar una solución híbrida:
     
        a. Haga clic con el botón derecho en el recurso de dirección IP que corresponda a la subred local y, a continuación, seleccione **Propiedades**. Anote el nombre de dirección IP y el nombre de red.
   
        b. Seleccione **Dirección IP estática**, asigne una dirección IP no utilizada y, a continuación, haga clic en **Aceptar**.
 
   * Para configurar una solución solo de Azure:

        a. Haga clic en el recurso de dirección IP que corresponde a la subred de Azure y, a continuación, seleccione **Propiedades**.
       
       > [!NOTE]
       > Si el agente de escucha no se puede poner en línea debido a una dirección IP en conflicto seleccionada por DHCP, puede configurar una dirección IP estática válida en esta ventana de propiedades.
       > 
       > 

       b. En la misma ventana de propiedades de la **Dirección IP**, cambia el valor de **Nombre de dirección IP**.  
        Este nombre se utilizará en la variable $IPResourceName del script de PowerShell. Si la solución abarca varias redes virtuales de Azure, repita este paso para cada recurso de dirección IP.

