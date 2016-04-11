<properties 
   pageTitle="Creación de grupos de seguridad de red en el modo ARM mediante el Portal de Azure | Microsoft Azure"
   description="Aprenda a crear e implementar grupos de seguridad de red en ARM mediante el Portal de Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# Cómo administrar grupos de seguridad de red con el Portal de Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [crear grupos de seguridad de red en el modelo de implementación clásica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

En los siguientes comandos de PowerShell de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos tal y como aparecen en este documento, cree primero el entorno de prueba mediante la implementación de [esta plantilla](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal. En los pasos siguientes se emplea **RG-GSN** como nombre del grupo de recursos en el que se implementó la plantilla.

## Creación del grupo de seguridad de red NSG-FrontEnd

Para crear el grupo de seguridad de red **NSG-FrontEnd** según el escenario anterior, siga estos pasos.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Examinar >** > **Grupos de seguridad de red**.

    ![Portal de Azure - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. En la hoja **Grupos de seguridad de red**, haga clic en **Agregar**.
  
    ![Portal de Azure: NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. En la hoja **Crear grupo de seguridad de red**, cree un NSG llamado *NSG-FrontEnd* en el grupo de recursos *RG-NSG* y, después, haga clic en **Crear**.

	![Portal de Azure - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## Creación de reglas en un grupo de seguridad de red existente

Para crear reglas en un grupo de seguridad de red existente desde el Portal de Azure, siga estos pasos.

2. Haga clic en **Examinar >** > **Grupos de seguridad de red**.

3. En la lista de grupos de seguridad de red, haga clic en **NSG-FrontEnd** > **Reglas de seguridad de entrada**

	![Portal de Azure - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. En la lista de **Reglas de seguridad de entrada**, haga clic en **Agregar**.

	![Portal de Azure - Agregar regla](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. En la hoja **Agregar regla de seguridad de entrada**, cree una regla denominada *web-rule* con prioridad de *200* que permita el acceso a través de *TCP* al puerto *80* y a cualquier máquina virtual desde cualquier origen y luego haga clic en **Aceptar**. Observe que la mayoría de estas configuraciones son ya valores predeterminados.

	![Portal de Azure - Configuración de la regla](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Después de unos segundos, verá la nueva regla en el grupo de seguridad de red.

	![Portal de Azure - Nueva regla](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. Repita los pasos hasta el sexto para crear una regla de entrada denominada *rdp-rule* con una prioridad de *250* que permita el acceso a través de *TCP* al puerto *3389* a cualquier VM desde cualquier origen.

## Asociación del grupo de seguridad de red a la subred FrontEnd

1. Haga clic en **Examinar >** > **Grupos de recursos** > **RG-NSG**.
2. En la hoja **RG-NSG**, haga clic en **...** > **TestVNet**.

	![Portal de Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. En la hoja **Configuración**, haga clic en **Subredes** > **FrontEnd** > **Grupo de seguridad de red** > **NSG-FrontEnd**.

	![Portal de Azure - Configuración de la subred](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. En la hoja **FrontEnd**, haga clic en **Guardar**.

	![Portal de Azure - Configuración de la subred](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## Creación del grupo de seguridad de red NSG-BackEnd

Para crear el grupo de seguridad de red **NSG-BackEnd** y asociarlo a la subred **BackEnd**, siga estos pasos.

1. Repita los pasos que se describen en [Creación del grupo de seguridad de red NSG-FrontEnd](#Create-the-NSG-FrontEnd-NSG) para crear un NSG llamado *NSG-BackEnd*.
2. Repita los pasos que se describen en [Creación de reglas en un grupo de seguridad de red existente](#Create-rules-in-an-existing-NSG) para crear las reglas **de entrada** que aparecen en la tabla siguiente.

	|Regla de entrada|Regla de salida|
	|---|---|
	|![Portal de Azure - Regla de entrada](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Portal de Azure - Regla de salida](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. Repita los pasos que se describen en [Asociación del grupo de seguridad de red a la subred FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet) para asociar el grupo de seguridad de red **NSG-BackEnd** a la subred **BackEnd**.

## Pasos siguientes

- Información sobre cómo [administrar grupos de seguridad de red existentes](virtual-network-manage-nsg-arm-portal.md)
- [Habilite el registro](virtual-network-nsg-manage-log.md) para los grupos de seguridad de red.

<!---HONumber=AcomDC_0330_2016-->