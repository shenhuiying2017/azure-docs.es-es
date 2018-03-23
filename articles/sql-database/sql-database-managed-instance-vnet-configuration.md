---
title: Configuración de red virtual con Instancia administrada de Azure SQL Database | Microsoft Docs
description: En este tema se describen las opciones de configuración para una red virtual con Instancia administrada de Azure SQL Database.
services: sql-database
author: srdjan-bozovic
manager: cguyer
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 1a839a9bb2355da9451816828f6f9f0e99f43f5e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Configuración de una red virtual para Instancia administrada de Azure SQL Database

Instancia administrada de Azure SQL Database (versión preliminar) debe implementarse dentro de una [red virtual](../virtual-network/virtual-networks-overview.md) de Azure. Esta implementación permite los siguientes escenarios: 
- Conexión a Instancia administrada directamente desde una red local 
- Conexión de Instancia administrada con un servidor vinculado o con otro almacén de datos local 
- Conexión de Instancia administrada a los recursos de Azure  

## <a name="plan"></a>Plan

Planee cómo va a implementar Instancia administrada en una red virtual utilizando las respuestas a las siguientes preguntas: 
- ¿Tiene pensado implementar una o varias instancias administradas? 

  El número de instancias administradas determina el tamaño mínimo de la subred a asignar para las mismas. Para más información, consulte la sección [Determinación del tamaño de subred para Instancia administrada](#create-a-new-virtual-network-for-managed-instances). 
- ¿Tiene que implementar Instancia administrada en una red virtual existente o va a crear una nueva red? 

   Si piensa usar una red virtual existente, tiene que modificar la configuración de red para dar cabida a Instancia administrada. Para más información, consulte [Modificación de una red virtual existente para Instancia administrada](#modify-an-existing-virtual-network-for-managed-instances). 

   Si piensa crear una nueva red virtual, consulte la sección [Creación de una nueva red virtual para Instancia administrada](#create-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Requisitos

Para crear una instancia administrada tiene que dedicar una subred dentro de la red virtual que se ajuste a los requisitos siguientes:
- **Estar vacía**: la subred no puede contener ningún otro servicio de nube asociado a ella, y no puede ser la subred de puerta de enlace. No podrá crear una instancia administrada en una subred que contenga otros recursos que no sean los de la instancia administrada, ni podrá agregar otros recursos más adelante dentro de la subred.
- **No ser NSG**: la subred no puede tener un grupo de seguridad de red asociado a ella.
- **Tener una tabla de rutas específica**: la subred tiene que tener una tabla de rutas de usuario (UDR) con Internet de próximo salto 0.0.0.0/0 como única ruta asignada. Para más información, consulte la sección [Creación de la tabla de rutas necesaria y su asociación](#create-the-required-route-table-and-associate-it)
3. **DNS personalizado opcional**: si se especifica un DNS personalizado en la red virtual, es necesario agregar la dirección IP de las resoluciones recursivas de Azure (por ejemplo, 168.63.129.16) a la lista. Para más información, consulte [Configuración de DNS personalizado](sql-database-managed-instance-custom-dns.md).
4. **No tener ningún punto de conexión de servicio**: la subred no puede tener un punto de conexión de servicio (Storage o SQL) asociado a ella. Asegúrese de que la opción Puntos de conexión de servicio está deshabilitada cuando cree la red virtual.
5. **Tener suficientes direcciones IP**: la subred tiene que tener como mínimo 16 direcciones IP. Para más información, consulte la sección [Determinación del tamaño de subred para instancias administradas](#determine-the-size-of-subnet-for-managed-instances).

> [!IMPORTANT]
> No podrá implementar una nueva instancia administrada si la subred de destino no es compatible con todos los requisitos anteriores. La red virtual de destino y la subred tienen que mantenerse de acuerdo con estos requisitos de Instancia administrada (antes y después de la implementación), ya que cualquier infracción puede provocar en la instancia un estado defectuoso y hacer que deje de estar disponible. La recuperación a partir de un estado así, requiere la creación de una nueva instancia en una red virtual con las directivas de red compatibles, la recreación de los datos a nivel de instancia y la restauración de las bases de datos. Esto supone un tiempo de inactividad considerable para las aplicaciones.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Determinación del tamaño de subred para instancias administradas

Cuando se crea una instancia administrada, Azure asigna un número de máquinas virtuales según el tamaño del nivel que seleccione durante el aprovisionamiento. Debido a que estas máquinas virtuales están asociadas a la subred, requieren direcciones IP. Para garantizar la alta disponibilidad durante las operaciones normales y el mantenimiento del servicio, Azure puede asignar máquinas virtuales adicionales. Como resultado, el número de direcciones IP necesarias en una subred es mayor que el número de instancias administradas en esa subred. 

Por definición, una instancia administrada necesita un mínimo de 16 direcciones IP en una subred y puede utilizar hasta 256 direcciones IP. En consecuencia, puede utilizar máscaras de subred /28 a /24 al definir los intervalos IP de la subred. 

Si tiene previsto implementar varias instancias administradas dentro de la subred y tiene que optimizar el tamaño de la subred, utilice estos parámetros para hacer un cálculo: 

- Azure utiliza cinco direcciones IP de la subred para sus propias necesidades 
- Cada instancia de uso general necesita dos direcciones 

**Ejemplo**: tiene pensado tener ocho instancias administradas. Esto significa que necesita 5 + 8 * 2 = 21 direcciones IP. Como los intervalos de IP se definen en potencias de 2, necesita el intervalo de IP de 32 (2 ^ 5) direcciones IP. Por lo tanto, tiene que reservar la subred con la máscara de subred de /27. 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>Creación de una nueva red virtual para instancias administradas 

La creación de una red virtual de Azure es un requisito previo para crear una instancia administrada. Puede usar Azure Portal, [PowerShell](../virtual-network/quick-create-powershell.md), o [CLI de Azure](../virtual-network/quick-create-cli.md). En la siguiente sección se muestran los pasos a seguir cuando se usa Azure Portal. Los detalles que se tratan aquí se aplican a cada uno de estos métodos.

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Busque y haga clic en **Red virtual**, compruebe que **Resource Manager** está seleccionado como modo de implementación y, finalmente, haga clic en **Crear**.

   ![creación de una red virtual](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Rellene el formulario de la red virtual con la información solicitada de forma similar a la que se ve en la siguiente captura de pantalla:

   ![formulario de creación de una red virtual](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Haga clic en **Create**(Crear).

   El espacio de direcciones y la subred se especifican en la notación CIDR. 

   > [!IMPORTANT]
   > Los valores predeterminados crean una subred que toma todo el espacio de direcciones de red virtual. Si elige esta opción, no puede crear ningún otro recurso dentro de la red virtual que no sea la instancia administrada. 

   El enfoque recomendado es el siguiente: 
   - Calcule el tamaño de la subred siguiendo la información expuesta en la sección [Determinación del tamaño de subred para instancias administradas](#determine-the-size-of-subnet-for-managed-instances)  
   - Evalúe las necesidades para el resto de la red virtual 
   - Rellene los intervalos de direcciones de red virtual y subred según corresponda 

   Asegúrese de que la opción Puntos de conexión de servicio está **Deshabilitada**. 

   ![formulario de creación de una red virtual](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>Creación de la tabla de rutas necesaria y su asociación

1. Inicie sesión en el Portal de Azure.  
2. Busque una **tabla de rutas** y haga clic en ella y, después, haga clic en **Crear** en la página Tabla de rutas.

   ![formulario de creación de la tabla de rutas](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Cree una ruta de Internet de próximo salto 0.0.0.0/0, de forma similar a la que se ve en las capturas de pantalla siguientes:

   ![incorporación de tabla de rutas](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![ruta](./media/sql-database-managed-instance-tutorial/route.png)

4. Asocie esta ruta a la subred para la instancia administrada, de forma similar a la que se ve en las capturas de pantalla siguientes:

    ![subred](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![establecimiento de la tabla de rutas](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![establecimiento de la tabla de rutas: guardar](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


Una vez creada la red virtual, está preparado para crear la instancia administrada.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Modificación de una red virtual existente para instancias administradas 

Las preguntas y respuestas de esta sección muestran cómo agregar una instancia administrada a una red virtual existente. 

**¿Está usando un modelo de implementación clásico o de Resource Manager para la red virtual existente?** 

Una Instancia administrada solo se puede crear en redes virtuales de Resource Manager. 

**¿Desea crear una subred nueva para la instancia administrada de SQL o usar una existente?**

Si desea crear una nueva: 

- Calcule el tamaño de la subred siguiendo las directrices de la sección [Determinación del tamaño de subred para instancias administradas](#determine-the-size-of-subnet-for-managed-instances).
- Siga los pasos indicados en [Incorporación, cambio o eliminación de una subred de red virtual](../virtual-network/virtual-network-manage-subnet.md). 
- Cree una tabla de rutas que contenga una única entrada, **0.0.0.0/0**, como el próximo salto Internet y asóciela a la subred para la instancia administrada.  

En caso de que desee crear una instancia administrada dentro de una subred existente: 
- Compruebe si la subred está vacía: no se puede crear una instancia administrada en una subred que contiene otros recursos incluida la subred de puerta de enlace 
- Calcule el tamaño de la subred siguiendo las directrices de la sección [Determinación del tamaño de subred para instancias administradas](#determine-the-size-of-subnet-for-managed-instances) y compruebe que tiene el tamaño apropiado. 
- Compruebe que los puntos de conexión de servicio no están habilitados en la subred.
- Asegúrese de que no hay ningún grupo de seguridad de red asociado a la subred 

**¿Tiene configurado un servidor DNS personalizado?** 

Si es así consulte [Configuración de un DNS personalizado](sql-database-managed-instance-custom-dns.md). 

- Cree la tabla de rutas necesaria y asóciela: consulte [Creación de la tabla de rutas necesaria y su asociación](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Pasos siguientes

- Para información general, consulte [¿Qué es Instancia administrada de SQL Database (versión preliminar)?](sql-database-managed-instance.md)
- Para obtener un tutorial que muestre cómo crear una red virtual, crear una instancia administrada y restaurar una base de datos desde una copia de seguridad de base de datos, consulte [Creación de una Instancia administrada de Azure SQL Database](sql-database-managed-instance-tutorial-portal.md).
- Para problemas DNS, consulte [Configuración de un DNS personalizado](sql-database-managed-instance-custom-dns.md)
