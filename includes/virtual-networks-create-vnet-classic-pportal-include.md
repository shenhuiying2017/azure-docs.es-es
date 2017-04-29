## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Cómo crear una red virtual clásica en el Portal de Azure
Para crear una red virtual clásica basada en el escenario anterior, siga estos pasos.

1. Desde un explorador, vaya a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **NUEVO** > **Redes** > **Red virtual**, observe que en lista **Seleccionar un modelo de implementación** ya muestra **Clásica** y, a continuación, haga clic en **Crear**, como se muestra en la ilustración siguiente.
   
    ![Creación de una red virtual en el Portal de Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
3. En la hoja **Red virtual**, escriba el **nombre** de la red virtual y haga clic en **Espacio de direcciones**. Configure los valores del espacio de direcciones de la red virtual y su primera subred, a continuación, haga clic en **Aceptar**. En la siguiente figura se muestra la configuración del bloque CIDR de este escenario.
   
    ![Hoja de espacio de direcciones](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
4. Haga clic en **Grupo de recursos** y seleccione el grupo al que va a agregar la red virtual, o bien haga clic en **Crear nuevo grupo de recursos** para agregar la red virtual a un nuevo grupo de recursos. En la siguiente figura se muestra la configuración de grupo de recursos de un nuevo grupo de recursos denominado **TestRG**. Para más información acerca de los grupos de recursos, visite [Información general de Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Crear hoja de grupo de recursos](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
5. Si es necesario, cambie la configuración de la **Suscripción** y la **Ubicación** de la red virtual. 
6. Si no desea ver la red virtual como un icono en el **Panel de inicio**, deshabilite **Anclar a panel de inicio**. 
7. Haga clic en **Crear** y observe el icono denominado **Creación de red virtual** tal como se muestra en la ilustración siguiente.
   
    ![Crear red virtual en el portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
8. Espere a que se cree la red virtual y cuando vea el icono que aparece a continuación, haga clic en él para agregar más subredes.
   
    ![Crear red virtual en el portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
9. Debería ver la **Configuración** de la red virtual tal como se muestra a continuación. 
   
    ![Crear red virtual en el portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
10. Haga clic en **Subredes** > **Agregar**, escriba un **nombre**, especifique un **intervalo de direcciones (bloque CIDR)** para la subred y haga clic en **Aceptar**. En la siguiente figura se muestra la configuración del escenario actual.
    
    ![Creación de una red virtual en el Portal de Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

