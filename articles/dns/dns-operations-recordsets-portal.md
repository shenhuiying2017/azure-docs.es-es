---
title: "Administración del conjunto de registros de DNS y registros con Azure DNS | Microsoft Docs"
description: Azure DNS proporciona la funcionalidad de administrar registros y conjuntos de registros de DNS al hospedar un dominio.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 18ed44a1-7bfe-454f-964e-922ad978264a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: 001b80ccba43beab44f6a598f820df65a85a345f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Administración de registros y conjuntos de registros DNS mediante el Portal de Azure

> [!div class="op_single_selector"]
> * 
            [Azure Portal](dns-operations-recordsets-portal.md)
> * [CLI de Azure 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [CLI de Azure 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

En este artículo se explica cómo administrar conjuntos de registros y registros para la zona DNS mediante el Portal de Azure.

Es importante comprender la diferencia entre los conjuntos de registros de DNS y los registros DNS individuales. Un conjunto de registros es una colección de registros de una zona con el mismo nombre y el mismo tipo. Para más información, consulte [Creación de registros y conjuntos de registros de DNS mediante el Portal de Azure](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Creación de un nuevo conjunto de registros y un registro

Para crear un conjunto de registros en el Portal de Azure, consulte [Creación de registros y conjuntos de registros de DNS mediante el Portal de Azure](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Visualización de un conjunto de registros

1. En el Portal de Azure, vaya a la hoja **Zona DNS** .
2. Busque el conjunto de registros y selecciónelo. De este modo se abrirán las propiedades del conjunto de registros.

    ![Buscar un conjunto de registros](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Incorporación de un nuevo registro a un conjunto de registros

Puede agregar hasta 20 registros a cualquier conjunto de registros. Un conjunto de registros no puede contener dos registros idénticos. Se pueden crear conjuntos de registros vacíos (sin ningún registro), pero no aparecen en los servidores de nombres DNS de Azure. Los conjuntos de registros de tipo CNAME pueden contener, como máximo, un registro.

1. En la hoja **Record set properties** (Propiedades del conjunto de registros) de la zona DNS, haga clic en el conjunto de registros al que desea agregar un registro.

    ![Seleccionar un conjunto de registros](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especifique las propiedades del conjunto de registros rellenando los campos.

    ![Incorporación de un registro](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Haga clic en **Guardar** en la parte superior de la hoja para guardar la configuración. A continuación, cierre la hoja.
4. En la esquina, verá que el registro se está guardando.

    ![Guardar el conjunto de registros](./media/dns-operations-recordsets-portal/saving150.png)

Una vez que se ha guardado el registro, los valores de la hoja **Zona DNS** reflejarán el nuevo registro.

## <a name="update-a-record"></a>Actualización de un registro

Cuando se actualiza un registro en un conjunto de registros existente, los campos que puede actualizar dependen del tipo de registro con el que está trabajando.

1. En la hoja **Record set properties** (Propiedades del conjunto de registros) correspondiente al conjunto de registros, busque el registro.
2. Modifique el registro. Cuando se modifica un registro, puede cambiar la configuración disponible para dicho registro. En el ejemplo siguiente, se hizo clic en el campo **Dirección IP** y la dirección IP se encuentra en proceso de modificación.

    ![Modificar un registro](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Haga clic en **Guardar** en la parte superior de la hoja para guardar la configuración. En la esquina superior derecha verá la notificación de que el registro se ha guardado.

    ![Agregar conjunto de registros](./media/dns-operations-recordsets-portal/saved150.png)

Una vez que se ha guardado el registro, los valores del conjunto de registros de la **Hoja DNS** reflejarán el registro actualizado.

## <a name="remove-a-record-from-a-record-set"></a>Eliminación de un registro de un conjunto de registros

Puede usar el Portal de Azure para quitar registros de un conjunto de registros. Tenga en cuenta que al eliminar el último registro de un conjunto de registros no se elimina el conjunto de registros.

1. En la hoja **Record set properties** (Propiedades del conjunto de registros) correspondiente al conjunto de registros, busque el registro.
2. Haga clic en el registro que desea quitar. A continuación, seleccione **Quitar**.

    ![Quitar un registro](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Haga clic en **Guardar** en la parte superior de la hoja para guardar la configuración.
4. Una vez quitado el registro, los valores del registro de la hoja **Zona DNS** reflejarán la eliminación.

## <a name="delete"></a>Eliminación de un conjunto de registros

1. En la hoja **Record set properties** (Propiedades del conjunto de registros) correspondiente al conjunto de registros, haga clic en **Eliminar**.

    ![Eliminación de un conjunto de registros](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Aparece un mensaje en el que se pregunta si desea eliminar el conjunto de registros.
3. Compruebe que el nombre coincide con el conjunto de registros que desea eliminar y después haga clic en **Sí**.
4. En la hoja de **Zona DNS** , compruebe que el conjunto de registros ya no está visible.

## <a name="work-with-ns-and-soa-records"></a>Trabajo con registros NS y SOA

Los registros NS y SOA creados automáticamente se administran de forma diferente de otros tipos de registros.

### <a name="modify-soa-records"></a>Modificación de registros SOA

No puede agregar ni eliminar registros del conjunto de registros SOA creado automáticamente en el vértice de zona (nombre = "@"). Sin embargo, puede modificar cualquiera de los parámetros del registro SOA (excepto "Host") y del conjunto de registros TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modificación de los registros NS en el vértice de zona

El conjunto de registros NS en el vértice de zona se crea automáticamente con cada zona DNS. Este conjunto de registros contiene los nombres de los servidores de nombres de Azure DNS asignados a la zona.

Puede agregar más servidores de nombres a este conjunto de registros NS, para admitir dominios de hospedaje conjunto con más de un proveedor DNS. También puede modificar el TTL y los metadatos de este conjunto de registros. Sin embargo, no puede quitar ni modificar los servidores de nombres de Azure DNS rellenados previamente.

Tenga en cuenta que esto solo se aplica al conjunto de registros NS en el vértice de zona. Otros conjuntos de registros NS de su zona (como los que se usan para delegar zonas secundarias) se pueden modificar sin restricciones.

### <a name="delete-soa-or-ns-record-sets"></a>Eliminación de conjuntos de registros SOA o NS

No puede eliminar conjuntos de registros SOA ni NS en el vértice de zona (nombre = @) que se crean automáticamente cuando se crea la zona. Se eliminan automáticamente al eliminar la zona.

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de DNS de Azure, consulte la [Introducción a DNS de Azure](dns-overview.md).
* Para más información acerca de la automatización de DNS, consulte [Creación de conjuntos de registros y zonas DNS con el SDK de .NET](dns-sdk.md).
* Para más información acerca de los registros de DNS inversos, consulte [Introducción a DNS inverso y compatibilidad en Azure](dns-reverse-dns-overview.md).
