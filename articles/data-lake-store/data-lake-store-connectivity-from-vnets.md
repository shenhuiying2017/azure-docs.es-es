---
title: "Conexión a Azure Data Lake Store desde redes virtuales | Microsoft Docs"
description: "Conexión a Azure Data Lake Store desde redes virtuales de Azure"
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 029939eabc7283d7591576fa1b14731a9e626a88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>Acceso a Azure Data Lake Store desde máquinas virtuales de una red virtual de Azure
Azure Data Lake Store es un servicio de PaaS que se ejecuta en direcciones IP de Internet pública. Normalmente, cualquier servidor que puede conectarse a la Internet pública también puede conectarse a los puntos de conexión de Azure Data Lake Store. De forma predeterminada, todas las máquinas virtuales que se encuentran en redes virtuales de Azure pueden acceder a Internet y, por tanto, a Azure Data Lake Store. Sin embargo, se puede configurar que las máquinas virtuales de una red virtual no tengan acceso a Internet. Estas máquinas virtuales tampoco pueden acceder a Azure Data Lake Store. Puede bloquear el acceso a la Internet pública a las máquinas virtuales de las redes virtuales de Azure con cualquiera de los siguientes enfoques.

* Configurando grupos de seguridad de red (NSG)
* Configurando rutas definidas por el usuario (UDR)
* Intercambiando rutas a través de BGP (protocolo de enrutamiento dinámico de normalización industrial) cuando utilice ExpressRoute para bloquear el acceso a Internet

En este artículo, aprenderá a habilitar el acceso a Azure Data Lake Store en las máquinas virtuales de Azure cuyo acceso a los recursos se ha restringido mediante uno de los tres métodos mencionados anteriormente.

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>Habilitación de la conectividad a Azure Data Lake Store desde máquinas virtuales con conectividad restringida
Para acceder a Azure Data Lake Store desde máquinas virtuales, debe configurarlas para que tengan acceso a la dirección IP en la que está disponible la cuenta de Azure Data Lake Store. Puede identificar las direcciones IP de las cuentas de Data Lake Store resolviendo los nombres DNS de sus cuentas (`<account>.azuredatalakestore.net`). Para ello, puede usar herramientas como **nslookup**. Abra un símbolo del sistema en el equipo y ejecute el siguiente comando.

    nslookup mydatastore.azuredatalakestore.net

La salida debe ser similar a la siguiente. El valor de la propiedad **Address** es la dirección IP asociada con la cuenta de Data Lake Store.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Habilitación de la conectividad en las máquinas virtuales restringidas mediante NSG
Cuando una regla de NSG se utiliza para bloquear el acceso a Internet, puede crear otro NSG que permita el acceso a la dirección de IP de Data Lake Store. Obtenga más información sobre las reglas de NSG en la página donde se explica qué es [un grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md). Para obtener instrucciones sobre cómo crear NSG, consulte [How to manage NSGs using the Azure portal](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) (Administración de NSG mediante Azure Portal).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Habilitación de la conectividad en las máquinas virtuales restringidas mediante UDR o ExpressRoute
Cuando se utilizan rutas, UDR o rutas intercambiables a través de BGP, para bloquear el acceso a Internet, debe configurarse una ruta especial para que las máquinas virtuales de estas subredes puedan acceder a los puntos de conexión de Data Lake Store. Para obtener más información, consulte el artículo donde se explica [qué son las rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md). Para obtener instrucciones sobre cómo crear UDR, consulte el artículo sobre cómo [crear UDR en Resource Manager](../virtual-network/virtual-network-create-udr-arm-ps.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Habilitación de la conectividad en las máquinas virtuales restringidas mediante ExpressRoute
Cuando se configura un circuito ExpressRoute, los servidores locales pueden acceder a Data Lake Store a través de pares públicos. Para obtener más información sobre cómo configurar ExpressRoute para pares públicos, consulte las [preguntas más frecuentes de ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Otras referencias
* [Información general del Almacén de Azure Data Lake](data-lake-store-overview.md)
* [Protección de los datos almacenados en Azure Data Lake Store](data-lake-store-security-overview.md)

