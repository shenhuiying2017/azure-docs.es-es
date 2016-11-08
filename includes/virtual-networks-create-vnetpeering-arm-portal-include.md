## Creación de emparejamiento de VNet en el Portal de Azure
Para emparejar una red virtual según el escenario anterior por medio del Portal de Azure, siga los pasos a continuación.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Para establecer el emparejamiento de VNET, debe crear dos vínculos, una para cada dirección entre dos redes virtuales. Primero puede crear un vínculo de emparejamiento de red virtual de VNET1 a VNET2. En el Portal, haga clic en **Examinar** y elija **Redes virtuales**.
   
    ![Emparejamiento de VNet en el Portal de Azure](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure01.png)
3. En la hoja Redes virtuales, elija VNET1 y haga clic en Emparejamientos y, después, en Agregar
   
    ![Elija el emparejamiento](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure02.png)
4. En la hoja Agregar emparejamiento, asigne un nombre de vínculo de emparejamiento LinkToVnet2, elija la suscripción y la red virtual VNET2 emparejada, y haga clic en Aceptar.
   
    ![Vínculo a la red virtual](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure03.png)
5. Una vez creado este vínculo de emparejamiento de red virtual, verá el estado del vínculo siguiente:
   
    ![Estado del vínculo](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure04.png)
6. Después, cree el vínculo de emparejamiento de VNET de VNET2 a VNET1. En la hoja Redes virtuales, elija VNET2 y haga clic en Emparejamientos y, después, en Agregar
   
    ![Emparejar con otra red virtual](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure05.png)
7. En la hoja Agregar emparejamiento, asigne un nombre de vínculo de emparejamiento LinkToVnet1, elija la suscripción y la red virtual emparejada, y haga clic en Aceptar.
   
    ![Icono de Crear red virtual](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure06.png)
8. Una vez creado este vínculo de emparejamiento de red virtual, verá el estado del vínculo siguiente:
   
    ![Estado final del vínculo](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure07.png)
9. Compruebe el estado de LinkToVnet2, que también cambiará a Conectado.
   
    ![Estado final del vínculo 2](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure08.png)
10. NOTA: El emparejamiento de VNET se establece solo si ambos vínculos están conectados.

<!---HONumber=AcomDC_0803_2016-->