## Emparejamiento entre suscripciones

En este escenario creará un emparejamiento entre dos redes virtuales que pertenecen a distintas suscripciones.

![escenario entre suscripciones](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

En el emparejamiento de VNET, la autorización se basa en el control de acceso basado en roles (RBAC). En escenarios entre suscripciones, primero necesita conceder permisos suficientes a los usuarios que crearán el vínculo de emparejamiento. NOTA: Si el mismo usuario tiene el privilegio sobre ambas suscripciones, puede omitir los pasos del 1 al 4 que se indican a continuación.

<!---HONumber=AcomDC_0803_2016-->