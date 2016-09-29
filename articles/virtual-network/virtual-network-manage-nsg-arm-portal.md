<properties 
   pageTitle="Administración de los NSG mediante el Portal de vista previa en Resource Manager | Microsoft Azure"
   description="Obtenga información sobre cómo administrar los NSG existentes con el Portal de vista previa en Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# Administración de los NSG mediante el Portal de vista previa

> [AZURE.SELECTOR]
- [Portal](virtual-network-manage-nsg-arm-portal.md)
- [PowerShell](virtual-network-manage-nsg-arm-ps.md)
- [CLI de Azure](virtual-network-manage-nsg-arm-cli.md)

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implementación clásica.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## Recuperar información

Puede consultar los NSG existentes, recuperar las reglas de un NSG existente y averiguar cuáles son los recursos a los que está asociado un NSG.

### Consultar los NSG existentes
Para consultar todos los NSG existentes en una suscripción, siga estos pasos.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Examinar >** > **Grupos de seguridad de red**.

![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Compruebe la lista de NSG en la hoja **Grupos de seguridad de red**.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Para consultar la lista de NSG del grupo de recursos **RG-NSG**, siga estos pasos.

1. Haga clic en **Grupos de recursos >** > **RG-NSG** > **...**.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. En la lista de recursos, busque elementos que incluyan el icono NSG, tal como aparece en la hoja **Recursos** que se muestra a continuación.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
		 
### Mostrar todas las reglas de un NSG

Para consultar las reglas de un NSG llamado **NSG-FrontEnd**, siga estos pasos.

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que aparecen a continuación, haga clic en **NSG-FrontEnd**.
2. En la pestaña **Configuración**, haga clic en **Reglas de seguridad de entrada**.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. La hoja **Reglas de seguridad de entrada** aparece como se muestra a continuación.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. En la pestaña **Configuración**, haga clic en **Reglas de seguridad de salida** para ver las reglas de salida.

>[AZURE.NOTE] Para consultar las reglas predeterminadas, haga clic en el icono **Reglas predeterminadas** que se encuentra en la parte superior de la hoja donde aparecen las reglas.

### Consultar las asociaciones de NSG

Para consultar cuáles son los recursos con que está asociado el NSG **NSG-FrontEnd**, siga estos pasos.

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que aparecen a continuación, haga clic en **NSG-FrontEnd**.
2. En la pestaña **Configuración**, haga clic en **Subredes** para consultar cuáles son las subredes que están asociadas con el NSG.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. En la pestaña **Configuración**, haga clic en **Interfaces de red** para consultar cuáles son las NIC que están asociadas con el NSG.

## Administrar las reglas

Puede agregar reglas a un NSG existente, editar reglas existentes y quitar reglas.

### Agregar una regla

Para agregar una regla que permite el tráfico **de entrada** al puerto **443** desde cualquier máquina al NSG **NSG-FrontEnd**, siga estos pasos.

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que aparecen a continuación, haga clic en **NSG-FrontEnd**.
2. En la pestaña **Configuración**, haga clic en **Reglas de seguridad de entrada**.
3. En la hoja **Reglas de seguridad de entrada**, haga clic en **Agregar**. Luego, en la hoja **Agregar regla de seguridad de entrada**, complete los valores como se muestra a continuación y, luego, haga clic en **Aceptar**.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. Después de unos segundos, observe la regla nueva en la hoja **Reglas de seguridad de entrada**.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### Cambiar una regla

Para cambiar la regla que se creó anteriormente y permitir el tráfico de entrada solo desde **Internet**, siga estos pasos.

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que aparecen a continuación, haga clic en **NSG-FrontEnd**.
2. En la pestaña **Configuración**, haga clic en la regla que se creó anteriormente.
3. En la hoja **allow-https**, cambie la propiedad **Origen** tal como se muestra a continuación y, luego, haga clic en **Guardar**.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### Eliminar una regla

Para eliminar la regla que se creó anteriormente, siga estos pasos.

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que aparecen a continuación, haga clic en **NSG-FrontEnd**.
2. En la pestaña **Configuración**, haga clic en la regla que se creó anteriormente.
3. En la hoja **allow-https**, haga clic en **Eliminar** y, luego, haga clic en **í**.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## Administrar las asociaciones

Puede asociar un NSG a subredes y NIC. También puede desasociar un NSG de cualquier recurso al que está asociado.

### Asociar un NSG a una NIC

Para asociar el NSG **NSG-FrontEnd** a la NIC **TestNICWeb1**, siga estos pasos.

1. En la hoja **Grupos de seguridad de red** o la hoja **Recursos** que aparecen a continuación, haga clic en **NSG-FrontEnd**.
2. En la pestaña **Configuración**, haga clic en **Interfaces de red** > **Asociar** > **TestNICWeb1**.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### Desasociar un NSG de una NIC

Para desasociar el NSG **NSG-FrontEnd** de la NIC **TestNICWeb1**, siga estos pasos.

1. En el Portal de Azure, haga clic en **Grupos de recursos >** > **RG-NSG** > **...** > **TestNICWeb1**.
2. En la hoja **TestNICWeb1**, haga clic en **Cambiar seguridad...** > **Ninguna**.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] También puede usar esta hoja para asociar la NIC a cualquier NSG existente.

### Desasociar un NSG de una subred

Para desasociar el NSG **NSG-FrontEnd** de la subred **FrontEnd**, siga estos pasos.

1. En el Portal de Azure, haga clic en **Grupos de recursos >** > **RG-NSG** > **...** > **TestVNet**.
2. En la hoja **Configuración**, haga clic en **Subredes** > **FrontEnd** > **Grupo de seguridad de red** > **Ninguno**.

![Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. En la hoja **FrontEnd**, haga clic en **Guardar**.

![Portal de Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### Asociación de un grupo de seguridad de red a una máquina virtual

Para asociar nuevamente el NSG **NSG-FrontEnd** a la subred **FrontEnd**, siga estos pasos.

1. En el Portal de Azure, haga clic en **Grupos de recursos >** > **RG-NSG** > **...** > **TestVNet**.
2. En la hoja **Configuración**, haga clic en **Subredes** > **FrontEnd** > **Grupo de seguridad de red** > **NSG-FrontEnd**.
3. En la hoja **FrontEnd**, haga clic en **Guardar**.

>[AZURE.NOTE] También puede asociar un NSG a una subred desde la hoja **Configuración** del NSG.

## Eliminación de un grupo de seguridad de red

Solo se puede eliminar un NSG que no está asociado a ningún recurso. Para eliminar un NSG, siga estos pasos.

1. En el Portal de Azure, haga clic en **Grupos de recursos >** > **RG-NSG** > **...** > **NSG-FrontEnd**.
2. En la hoja **Configuración**, haga clic en **Interfaces de red**.
3. Si aparece alguna NIC, haga clic en ella y siga el paso 2 de [Desasociar un NSG de una NIC](#Dissociate-an-NSG-from-a-NIC).
4. Repita el paso 3 para cada NIC.
5. En la hoja **Configuración**, haga clic en **Subredes**.
6. Si aparece alguna subred, haga clic en ella y siga los pasos 2 y 3 de [Desasociar un NSG de una subred](#Dissociate-an-NSG-from-a-subnet).
7. Desplácese a la izquierda hasta la hoja **NSG-FrontEnd** y, luego, haga clic en **Eliminar** > **í**.

[Portal de Azure: NSG](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## Pasos siguientes

- [Habilite el registro](virtual-network-nsg-manage-log.md) para los NSG.

<!---HONumber=AcomDC_0914_2016-->