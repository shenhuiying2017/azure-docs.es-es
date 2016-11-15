---
title: "Creación de un conjunto de registros y registros para una zona DNS con CLI | Microsoft Docs"
description: "Creación de registros host para DNS de Azure. Configuración de conjuntos de registros y registros mediante CLI"
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 40accee35eca32eefd4afc2315c70d51e0edcdcd


---
# <a name="create-dns-record-sets-and-records-by-using-cli"></a>Creación de conjuntos de registros y registros de DNS con CLI
> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI de Azure](dns-getstarted-create-recordset-cli.md)
> 
> 

Este artículo le guiará a través del proceso de creación de registros y los conjuntos de registros mediante la CLI. Después de crear la zona DNS, deberá agregar los registros DNS para su dominio. Para ello, primero debe saber qué son los registros y los conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>Creación de un registro y un conjunto de registros
En esta sección se mostrará cómo crear registros y un conjunto de registros. En este ejemplo, se creará un conjunto de registros que tiene un nombre relativo "www" en la zona DNS "contoso.com". El nombre completo de los registros será "www.contoso.com". El tipo de registro es "A" y el período de vida es de 60 segundos. Después de completar este paso, habrá creado un conjunto de registros vacío.

Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@",, incluidas las comillas. Esta es una convención común de DNS.

### <a name="1-create-a-record-set"></a>1. Creación de un conjunto de registros
Para crear un conjunto de registros, use `azure network dns record-set create`. Especifique el grupo de recursos, el nombre de zona, el nombre relativo del conjunto de registros, el tipo de registro y el período de vida. Si no se define el parámetro `--ttl` , el valor predeterminado es 4 (en segundos). Después de completar este paso, tendrá un conjunto de registros "www" vacío.

*Uso: network dns record-set show <grupo de recursos> <nombre de zona dns> <name> <type> <ttl>*

    azure network dns record-set create myresourcegroup  contoso.com  www A  60

### <a name="2-add-records"></a>2. Integración de registros
Para poder usar el conjunto de registros "www" recién creado, es necesario agregarle registros. Los registros se agregan a los conjuntos de registros mediante `azure network dns record-set add-record`.

Los parámetros para agregar registros a un conjunto de registros varían según el tipo de conjunto de registros. Por ejemplo, cuando se usa un conjunto de registros de tipo "A", solo se podrán especificar registros con el parámetro `-a <IPv4 address>`.

Puede agregar registros *A* de IPv4 al conjunto de registros "www" con el comando siguiente:

*Uso: network dns record-set add-record <grupo de recursos> <nombre de zona dns> <nombre del conjunto de registros> <type>*

    azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## <a name="additional-record-type-examples"></a>Ejemplos de tipo de registros adicionales
Los ejemplos siguientes muestran cómo crear un conjunto de registros de cada tipo de registro. Cada conjunto de registros contiene un único registro.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Para administrar los registros y los conjuntos de registros, consulte [Administración de registros y conjuntos de registros DNS mediante la CLI](dns-operations-recordsets-portal.md).

Para más información acerca de DNS de Azure, consulte la [Introducción a DNS de Azure](dns-overview.md).




<!--HONumber=Nov16_HO2-->


