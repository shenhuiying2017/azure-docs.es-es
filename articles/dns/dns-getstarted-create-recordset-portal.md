---
title: "Creación de un conjunto de registros y registros para una zona DNS con Azure Portal | Microsoft Docs"
description: "Creación de registros de host para DNS de Azure y generación de registros y conjuntos de registros con el Portal de Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f93905f4-e82e-45db-b490-878d318e6aba
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: 194da45bade88c2866aaf6066d312d125f1ac2cb

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Creación de registros y conjuntos de registros de DNS mediante el Portal de Azure

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI de Azure](dns-getstarted-create-recordset-cli.md)

Este artículo le guiará a través de la creación de registros y conjuntos de registros usando el Portal de Azure. Después de crear la zona DNS, agregue los registros DNS para su dominio. Para ello, primero debe saber qué son los registros y los conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>Creación de un nuevo conjunto de registros y de un registro

En el ejemplo siguiente, se le guiará a través del proceso de creación de un conjunto de registros y un registro mediante el portal de Azure. Utilizaremos el tipo de registro DNS "A".

1. Inicie sesión en el portal.
2. Vaya a la hoja **Zona DNS** en la que quiera crear un conjunto de registros.
3. En la parte superior de la hoja **Zona DNS**, seleccione **Conjunto de registros** para abrir la hoja **Agregar conjunto de registros**.

    ![Nuevo conjunto de registros](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. En la hoja **Agregar conjunto de registros** , especifique un nombre para el conjunto de registros. Por ejemplo, podría llamar "**www**" a su conjunto de registros.

    ![Agregar conjunto de registros](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Seleccione el tipo de registro que quiere crear. Por ejemplo, seleccione **A**.
6. Establezca el **TTL**. El período de vida predeterminado en el portal es una hora.
7. Agregue las direcciones IP; una por línea. Cuando use el nombre sugerido del conjunto de registros y el tipo de registro descrito anteriormente, se agregarán direcciones IP de tipo IPv4 al registro **A** para el conjunto de registros www.
8. Cuando termine de agregar direcciones IP, seleccione **Aceptar** en la parte inferior de la hoja. Se creará el conjunto de registros de DNS.

## <a name="next-steps"></a>Pasos siguientes

Para administrar el conjunto de registros y los registros, consulte [Administración de registros y conjuntos de registros DNS mediante el Portal de Azure](dns-operations-recordsets-portal.md).

Para más información acerca de DNS de Azure, consulte la [Introducción a DNS de Azure](dns-overview.md).



<!--HONumber=Nov16_HO3-->


