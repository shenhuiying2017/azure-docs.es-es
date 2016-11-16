Para crear una red virtual con el modelo de implementación de Resource Manager mediante Azure Portal, siga los pasos que se indican a continuación. Las capturas de pantalla se proporcionan a modo de ejemplos. Asegúrese de reemplazar los valores por los suyos. Para más información sobre redes virtuales, consulte [Información general sobre redes virtuales](../articles/virtual-network/virtual-networks-overview.md).

1. Desde un explorador, vaya al [Portal de Azure](http://portal.azure.com) y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Nuevo**. En el campo **Buscar en el Marketplace**, escriba "Red virtual". En la lista de resultados, busque **Red virtual** y haga clic en él para abrir la hoja **Red virtual**.
   
    ![Hoja de recursos Buscar red virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Locate virtual network resource blade")
3. En la parte inferior de la hoja Red virtual, en la lista **Seleccionar un modelo de implementación**, seleccione **Resource Manager** y, finalmente, haga clic en **Crear**.

    ![Selección del Administrador de recursos](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Select Resource Manager")

1. En la hoja **Crear red virtual** , configure los valores de la red virtual. Al rellenar los campos, el signo de exclamación rojo se convertirá en una marca de verificación verde cuando los caracteres escritos en el campo sean válidos.
   
    ![Validación de campos](./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Field validation")
2. La hoja **Crear red virtual** es similar a la del ejemplo siguiente. Es posible que algunos valores se rellenen automáticamente. En tal caso, reemplace esos valores por los que desee.
   
    ![Hoja Crear red virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Create virtual network blade")
3. **Nombre**: escriba el nombre de la red virtual.
4. **Espacio de direcciones**: escriba el espacio de direcciones. Si tiene varios espacios de direcciones que agregar, agregue su primer espacio de direcciones. Podrá agregar más espacios de direcciones posteriormente, tras crear la red virtual.
5. **Nombre de subred**: agregue el nombre y el intervalo de direcciones de la subred. Podrá agregar más subredes posteriormente, tras crear la red virtual.
6. **Suscripción**: compruebe que la suscripción que aparece en la lista es la correcta. Puede cambiar las suscripciones mediante la lista desplegable.
7. **Grupo de recursos**: seleccione un grupo de recursos existente, o bien cree uno nuevo y escriba su nombre. Si va a crear un nuevo grupo, dé un nombre al grupo de recursos según los valores de configuración planeados. Para obtener más información sobre los grupos de recursos, visite [Información general del Administrador de recursos de Azure](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
8. **Ubicación**: seleccione la ubicación de la red virtual. La ubicación determina dónde van a residir los recursos que se implementen en esta red virtual.
9. Seleccione **Anclar al panel** si desea encontrar la red virtual fácilmente en el panel y, luego, haga clic en **Crear**.
   
   ![Anclar al panel](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "pin to dashboard")
10. Después de hacer clic en **Crear**, verá un icono en el panel que reflejará el progreso de la red virtual. El icono cambiará a medida que se vaya creando la red virtual.
    
    ![Icono de Crear red virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Creating virtual network tile")



<!--HONumber=Nov16_HO2-->


