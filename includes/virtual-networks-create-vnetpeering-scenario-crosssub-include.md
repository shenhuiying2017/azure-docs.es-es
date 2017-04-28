## <a name="x-sub"></a>Emparejamiento entre suscripciones
En este escenario creará un emparejamiento entre dos redes virtuales que pertenecen a distintas suscripciones.

![escenario entre suscripciones](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

En el emparejamiento de VNET, la autorización se basa en el control de acceso basado en roles (RBAC). En el escenario entre suscripciones, debe conceder primero los permisos suficientes para los usuarios que crearán el vínculo de emparejamiento.

> [!NOTE]
> Si el mismo usuario tiene el privilegio sobre ambas suscripciones, puede ignorar los pasos 1 a 1-4 siguientes.
> 
> 

