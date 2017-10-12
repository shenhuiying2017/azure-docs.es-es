---
title: "Introducción a DNS de Azure con Azure Portal | Microsoft Docs"
description: "Obtenga información sobre cómo crear una zona y un registro DNS en Azure DNS. Esta es una guía paso a paso para crear y administrar su primera zona y su primer registro DNS con Azure Portal."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
ms.openlocfilehash: 93b24e3d9fbb3fbb3ea995271fd63d1e82eb9c9e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Introducción a DNS de Azure con Azure Portal

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [CLI de Azure 1.0](dns-getstarted-cli-nodejs.md)
> * [CLI de Azure 2.0](dns-getstarted-cli.md)

Este artículo lo guiará por los pasos necesarios para crear su primera zona y su primer registro DNS con Azure Portal. También puede llevar a cabo estos pasos con Azure PowerShell o la CLI de Azure multiplataforma.

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para iniciar el hospedaje de su dominio en DNS de Azure, debe crear una zona DNS para ese nombre de dominio. Cada registro DNS del dominio se crea luego en esta zona DNS. Finalmente, para publicar la zona DNS en Internet, debe configurar los servidores de nombres para el dominio. En los procedimientos siguientes se describen todos estos pasos.

## <a name="create-a-dns-zone"></a>Creación de una zona DNS

1. Inicie sesión en el Portal de Azure.
2. En el menú Concentrador, haga clic en **Nuevo > Redes >** y, luego, en **Zona DNS** para abrir la hoja Crear zona DNS.

    ![Zona DNS](./media/dns-getstarted-portal/openzone650.png)

4. En la hoja **Crear zona DNS**, escriba los valores siguientes y haga clic en **Crear**:


   | **Configuración** | **Valor** | **Detalles** |
   |---|---|---|
   |**Name**|contoso.com|El nombre de la zona DNS|
   |**Suscripción**|[Su suscripción]|Seleccione la suscripción en la que se creará la zona DNS.|
   |**Grupos de recursos**|**Crear nuevo:** contosoDNSRG|Cree un grupo de recursos. El nombre del grupo de recursos debe ser único dentro de la suscripción seleccionada. Para más información sobre los grupos de recursos, lea el artículo [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Ubicación**|Oeste de EE. UU.||

> [!NOTE]
> El grupo de recursos se refiere a la ubicación del grupo de recursos y no tiene efecto alguno sobre la zona DNS. La ubicación de la zona DNS siempre es "global" y no se muestra.

## <a name="create-a-dns-record"></a>Creación de un registro de DNS

En el ejemplo siguiente, se le guiará a través del proceso de creación de un registro A. Para crear otros tipos de registros y modificar los que ya existan, consulte [Administración de registros y conjuntos de registros DNS mediante Azure Portal](dns-operations-recordsets-portal.md). 

1. Con la zona DNS creada, en el panel **Favoritos** de Azure Portal, haga clic en **Todos los recursos**. Haga clic en la zona DNS **contoso.com** en la hoja Todos los recursos. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir **contoso.com** en el cuadro **Filtrar por nombre...** para acceder fácilmente a la zona DNS.

1. En la parte superior de la hoja **Zona DNS**, seleccione **+ Conjunto de registros** para abrir la hoja **Agregar conjunto de registros**.

1. En la hoja **Agregar conjunto de registros**, escriba los valores siguientes y haga clic en **Aceptar**. En este ejemplo, va a crear un registro A.

   |**Configuración** | **Valor** | **Detalles** |
   |---|---|---|
   |**Name**|www|Nombre del registro|
   |**Tipo**|Una | El tipo de registro DNS que se va a crear, los valores aceptables son A, AAAA, CNAME, MX, NS, SRV, TXT y PTR.  Para más información sobre los tipos de registro, visite [Información general sobre zonas y registros de DNS](dns-zones-records.md)|
   |**TTL**|1|Período de vida de la solicitud DNS.|
   |**Unidad de TTL**|Horas|Medición de tiempo para el valor de TTL.|
   |**Dirección IP**|ipAddressValue| Este valor es la dirección IP que resuelve el registro DNS.|

## <a name="view-records"></a>Visualización de los registros

En la parte inferior de la hoja Zona DNS, puede consultar los registros correspondientes a la zona DNS. Debería ver los registros SOA y DNS predeterminados, que se crean en cada zona, además de todos los nuevos que haya creado.

![zona](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>Actualización de los servidores de nombres

Una vez haya comprobado que la zona y los registros DNS se han configurado correctamente, tiene que configurar el nombre de dominio para usar los servidores de nombres DNS de Azure. De esta forma, otros usuarios en Internet podrán encontrar los registros DNS.

Los servidores de nombres de su zona se proporcionan en Azure Portal:

![zona](./media/dns-getstarted-portal/viewzonens500.png)

Estos servidores de nombres deben configurarse con el registrador de nombres de dominio (donde adquirió el nombre de dominio). El registrador ofrece la opción de configurar los servidores de nombres para el dominio. Si quiere obtener más información, consulte [Delegación del dominio en DNS de Azure](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Eliminación de todos los recursos

Para eliminar todos los recursos creados en este artículo, complete los pasos siguientes:

1. En el panel **Favoritos** de Azure Portal, haga clic en **Todos los recursos**. Haga clic en el grupo de recursos **MyResourceGroup** en la hoja Todos los recursos. Si la suscripción que seleccionó ya tiene varios recursos en ella, escriba **MyResourceGroup** en el cuadro **Filtrar por nombre...** para acceder fácilmente al grupo de recursos.
1. En la hoja **MyResourceGroup**, haga clic en el botón **Eliminar**.
1. El portal requiere que escriba el nombre del grupo de recursos para confirmar que desea eliminarlo. Haga clic en **Eliminar**, escriba *MyResourceGroup* para el nombre de grupo de recursos y haga clic en **Eliminar**. Al eliminarse un grupo de recursos, se eliminan todos los recursos que contiene, por lo que siempre debe asegurarse de comprobar su contenido antes de eliminarlo. El portal elimina todos los recursos incluidos en el grupo de recursos y, después, el grupo de recursos en sí. Este proceso tarda varios minutos.


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Azure DNS, lea [Introducción a DNS de Azure](dns-overview.md).

Para aprender a administrar registros DNS en Azure DNS, consulte [Administración de registros y conjuntos de registros DNS mediante Azure Portal](dns-operations-recordsets-portal.md).

