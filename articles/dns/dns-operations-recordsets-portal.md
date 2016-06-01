<properties 
   pageTitle="Administración de conjuntos de registros y registros de DNS mediante el Portal de Azure | Microsoft Azure" 
   description="Administración de conjuntos de registros y registros de DNS al hospedar el dominio en DNS de Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmon" 
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# Administración de registros y conjuntos de registros DNS mediante el Portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](dns-operations-recordsets-portal.md)
- [CLI de Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Este artículo explica cómo administrar conjuntos de registros y registros de zonas DNS mediante el Portal de Azure.

Es importante comprender la diferencia entre los conjuntos de registros de DNS y los registros DNS individuales. Un conjunto de registros es una colección de registros de una zona con el mismo nombre y el mismo tipo. Para más información, consulte [Descripción de los registros y los conjuntos de registros](dns-getstarted-create-recordset-portal.md).

## Creación de un nuevo conjunto de registros y un registro

Para crear un conjunto de registros en el Portal de Azure, consulte [Create DNS records using the Azure portal (Creación de registros de DNS mediante el Portal de Azure)](dns-getstarted-create-recordset-portal.md).


## Visualización de un conjunto de registros

1. En el Portal de Azure, vaya a la hoja de zona DNS.

2. Puede buscar el conjunto de registros y seleccionarlo en los elementos mostrados. Haga clic en el conjunto de registros para seleccionarlo. Esto abrirá las propiedades del conjunto de registros.

	![buscar conjunto de registros](./media/dns-operations-recordsets-portal/searchset500.png)


## Incorporación de un nuevo registro a un conjunto de registros

Puede agregar hasta 20 registros a cualquier conjunto de registros. Un conjunto de registros no puede contener dos registros idénticos. Se pueden crear conjuntos de registros vacíos (sin ningún registro), pero no aparecen en los servidores de nombres DNS de Azure. Los conjuntos de registros de tipo CNAME pueden contener, como máximo, un registro.


1. En la hoja **Propiedades del conjunto de registros** de la zona DNS, haga clic en el conjunto de registros al que desea agregar un registro.

	![seleccionar conjunto](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especifique la configuración del registro rellenando los campos.

	![agregar registro](./media/dns-operations-recordsets-portal/addrecord500.png)

2. Haga clic en **Guardar** en la parte superior de la hoja para guardar la configuración antes de cerrar la hoja.

3. En la esquina, verá que el registro se está guardando.

	![guardar registro](./media/dns-operations-recordsets-portal/saving150.png)

4. Una vez que se ha guardado el registro, los valores del conjunto de registro de la hoja DNS reflejarán el nuevo registro.


## Actualización de un registro

Cuando se actualiza un registro en un conjunto de registros existente, los campos disponibles que puede actualizar dependen del tipo de registro con el que está trabajando.

1. En la hoja **Propiedades del conjunto de registros** correspondiente al conjunto de registros, busque el registro.

2. Modifique el registro. Cuando se modifica un registro, puede cambiar la configuración disponible para dicho registro. En el ejemplo siguiente, se hizo clic en el campo de dirección IP y la dirección IP se encuentra en proceso de modificación.

	![modificar registro](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Haga clic en **Guardar** en la parte superior de la hoja para guardar la configuración. En la esquina superior derecha verá la notificación de que el registro se ha guardado.+
	
	![guardar registro](./media/dns-operations-recordsets-portal/saved150.png)


3. Una vez que se ha guardado el registro, los valores del conjunto de registros de la hoja DNS reflejarán el registro actualizado.


## Eliminación de un registro de un conjunto de registros

Puede usar el Portal de Azure para quitar registros de un conjunto de registros. Tenga en cuenta que al eliminar el último registro de un conjunto de registros no se elimina el conjunto de registros.

1. En la hoja **Propiedades del conjunto de registros** correspondiente al conjunto de registros, busque el registro.

2. Haga clic en el registro que desea quitar. Después, haga clic en **Quitar**.

	![quitar registro](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Haga clic en **Guardar** en la parte superior de la hoja para guardar la configuración.

3. Una vez quitado el registro, los valores del conjunto de registros de la hoja DNS reflejarán que el registro se quitó.


## <a name="delete"></a>Eliminación de un conjunto de registros

1. En la hoja **Propiedades del conjunto de registros** correspondiente al conjunto de registros, haga clic en **Eliminar**. 

	![eliminar conjunto de registros](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Aparecerá un mensaje preguntándole si desea eliminar el conjunto de registros 'nombre\_del\_conjunto\_de\_registros'.

3. Compruebe que el nombre coincide con el conjunto de registros que desea eliminar y después haga clic en **Sí**.

4. En la hoja de la zona DNS, puede comprobar que el conjunto de registros ya no está visible.


## Trabajo con registros NS y SOA

Los registros NS y SOA creados automáticamente se administran de forma diferente de otros tipos de registros.

### Modificación de registros SOA

No puede agregar ni eliminar registros del conjunto de registros SOA creado automáticamente en el ápice de zona (nombre = “@”), pero puede modificar cualquiera de los parámetros del registro SOA (excepto 'Host') y del TTL del conjunto de registros.

### Modificación de registros NS en el vértice de zona

No puede agregar, eliminar ni modificar los registros en el conjunto de registros NS creado automáticamente en el ápice de zona (nombre = “@”). El único cambio permitido es modificar el TTL del conjunto de registros.

### Eliminación de conjuntos de registros SOA o NS

No se pueden eliminar conjuntos de registros SOA ni NS en el ápice de zona (nombre = “@”) que se crean automáticamente cuando se crea la zona. Se eliminarán automáticamente al eliminar la zona.

## Pasos siguientes

Para más información acerca de DNS de Azure, consulte la [Introducción a DNS de Azure](dns-overview.md). Para más información acerca de la automatización de DNS, consulte [Creación de conjuntos de registros y zonas DNS con el SDK de .NET](dns-sdk.md).


Si desea trabajar con registros DNS inversos, consulte [Administración de registros de DNS inversos para los servicios mediante la CLI de Azure](dns-reverse-dns-record-operations-ps.md).
 

<!---HONumber=AcomDC_0518_2016-->