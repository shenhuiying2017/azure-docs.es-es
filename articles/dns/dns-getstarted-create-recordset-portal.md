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
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 2752579b6277a27ca1e932fbd2c402ac3135e80c
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Creación de registros y conjuntos de registros de DNS mediante el Portal de Azure

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI de Azure 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [CLI de Azure 2.0](dns-getstarted-create-recordset-cli.md)

Este artículo le guiará a través de la creación de registros y conjuntos de registros usando el Portal de Azure. Para ello, primero debe saber qué son los registros y los conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Todos los ejemplos de esta página usan el tipo de registro DNS “A”. El proceso para otros tipos de registros es similar.

Si el nuevo registro tiene el mismo nombre y tipo que un registro existente, debe agregarlo al conjunto de registros existente&mdash;(consulte [Administración de registros y conjuntos de registros DNS mediante Azure Portal](dns-operations-recordsets-portal.md)). Si el nuevo registro tiene un nombre y un tipo diferente al de todos los registros existentes, debe crear un nuevo conjunto de registros, tal y como se explica más adelante.

## <a name="create-records-in-a-new-record-set"></a>Creación de registros en un conjunto de registros nuevo

En el ejemplo siguiente, se le guiará a través del proceso de creación de un conjunto de registros y un registro mediante el portal de Azure.

1. Inicie sesión en el portal.
2. Vaya a la hoja **Zona DNS** en la que quiera crear un conjunto de registros.
3. En la parte superior de la hoja **Zona DNS**, seleccione **+ Conjunto de registros** para abrir la hoja **Agregar conjunto de registros**.

    ![Nuevo conjunto de registros](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. En la hoja **Agregar conjunto de registros** , especifique un nombre para el conjunto de registros. Por ejemplo, podría llamar "**www**" a su conjunto de registros.

    ![Agregar conjunto de registros](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Seleccione el tipo de registro que quiere crear. Por ejemplo, seleccione **A**.
6. Establezca el **TTL**. El período de vida predeterminado en el portal es una hora.
7. Agregue los detalles de cada registro en el conjunto de registros. En este caso, puesto que el tipo de registro es "A", debe agregar las direcciones IP del registro A; una dirección IP por línea.
8. Cuando termine de agregar direcciones IP, seleccione **Aceptar** en la parte inferior de la hoja. Se creará el conjunto de registros de DNS.

### <a name="verify-name-resolution"></a>Comprobación de la resolución de nombres

Puede probar que los registros DNS están presentes en los servidores de nombres de Azure DNS con herramientas DNS tales como nslookup, DIG o el cmdlet de PowerShell [Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si aún no ha delegado el dominio para usar la nueva zona en Azure DNS, deberá [dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona](dns-getstarted-create-dnszone.md#test-name-servers). Asegúrese de sustituir los valores correctos de su zona de registros en el comando que aparece a continuación.

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [delegar su nombre de dominio a los servidores de nombres de Azure DNS](dns-domain-delegation.md).

Para administrar el conjunto de registros y los registros, consulte [Administración de registros y conjuntos de registros DNS mediante el Portal de Azure](dns-operations-recordsets-portal.md).

