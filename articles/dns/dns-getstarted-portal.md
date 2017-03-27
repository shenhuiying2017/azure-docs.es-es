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
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 0fee02286f6d5dd4614a933590cdab43cc69237f
ms.lasthandoff: 03/11/2017

---

# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Introducción a DNS de Azure con Azure Portal

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [CLI de Azure 1.0](dns-getstarted-cli-nodejs.md)
> * [CLI de Azure 2.0](dns-getstarted-cli.md)

Este artículo lo guiará por los pasos necesarios para crear su primera zona y su primer registro DNS con Azure Portal. También puede llevar a cabo estos pasos con Azure PowerShell o la CLI de Azure multiplataforma.

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para iniciar el hospedaje de su dominio en DNS de Azure, debe crear una zona DNS para ese nombre de dominio. Cada registro DNS del dominio se crea luego en esta zona DNS. Finalmente, para publicar la zona DNS en Internet, debe configurar los servidores de nombres para el dominio. A continuación, se describen cada uno de estos pasos.

## <a name="create-a-dns-zone"></a>Creación de una zona DNS

1. Inicie sesión en el Portal de Azure.
2. En el menú Concentrador, haga clic en **Nuevo > Redes >** y, luego, en **Zona DNS** para abrir la hoja Crear zona DNS.

    ![Zona DNS](./media/dns-getstarted-portal/openzone650.png)

4. En la hoja **Crear zona DNS** , asígnele un nombre a la zona DNS. Por ejemplo, *contoso.com*.
 
    ![Crear zona](./media/dns-getstarted-portal/newzone250.png)

5. A continuación, especifique el grupo de recursos que desea usar. Puede crear un grupo de recursos o seleccionar uno ya existente. Si decide crear un nuevo grupo de recursos, use la lista desplegable **Ubicación** para especificar la ubicación del grupo de recursos. Tenga en cuenta que esta configuración se refiere a la ubicación del grupo de recursos y no tiene efecto alguno sobre la zona DNS. La ubicación de la zona DNS siempre es "global" y no se muestra.

6. Puede dejar activada la casilla **Anclar al panel** si desea ubicar fácilmente la zona nueva en el panel. A continuación, haga clic en **Crear**.

    ![Anclar al panel](./media/dns-getstarted-portal/pindashboard150.png)

7. Una vez que hace clic en Crear, verá la nueva zona configurada en el panel.

    ![Creating](./media/dns-getstarted-portal/creating150.png)

8. Una vez que se crea la zona nueva, en el panel se abrirá la hoja correspondiente a esta zona nueva.


## <a name="create-a-dns-record"></a>Creación de un registro de DNS

En el ejemplo siguiente, se le guiará a través del proceso de creación de un registro A. Para crear otros tipos de registros y modificar los que ya existan, consulte [Administración de registros y conjuntos de registros DNS mediante Azure Portal](dns-operations-recordsets-portal.md). 


1. En la parte superior de la hoja **Zona DNS**, seleccione **+ Conjunto de registros** para abrir la hoja **Agregar conjunto de registros**.

    ![Nuevo conjunto de registros](./media/dns-getstarted-portal/newrecordset500.png)

4. En la hoja **Agregar conjunto de registros** , especifique un nombre para el conjunto de registros. Por ejemplo, podría llamar "**www**" a su conjunto de registros.

    ![Agregar conjunto de registros](./media/dns-getstarted-portal/addrecordset500.png)

5. Seleccione el tipo de registro que quiere crear. En este ejemplo, seleccione **A**.
6. Establezca el **TTL**. El período de vida predeterminado es una hora.
7. Agregue la dirección IP del registro.
8. Haga clic en **Aceptar** en la parte inferior de la hoja para crear el registro DNS.


## <a name="view-records"></a>Visualización de los registros

En la parte inferior de la hoja Zona DNS, puede consultar los registros correspondientes a la zona DNS. Debería ver los registros SOA y NS predeterminados, que se crean en cada zona, además de todos los nuevos que haya creado.

![zona](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>Actualización de los servidores de nombres

Una vez haya comprobado que la zona y los registros DNS se han configurado correctamente, tiene que configurar el nombre de dominio para usar los servidores de nombres DNS de Azure. De esta forma, otros usuarios en Internet podrán encontrar los registros DNS.

Los servidores de nombres de su zona se proporcionan en Azure Portal:

![zona](./media/dns-getstarted-portal/viewzonens500.png)

Estos servidores de nombres deben configurarse con el registrador de nombres de dominio (donde adquirió el nombre de dominio). El registrador ofrece la opción de configurar los servidores de nombres para el dominio. Si quiere obtener más información, consulte [Delegación del dominio en DNS de Azure](dns-domain-delegation.md).


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Azure DNS, lea [Introducción a DNS de Azure](dns-overview.md).

Para aprender a administrar registros DNS en Azure DNS, consulte [Administración de registros y conjuntos de registros DNS mediante Azure Portal](dns-operations-recordsets-portal.md).


