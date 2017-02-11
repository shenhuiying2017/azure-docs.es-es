---
title: "Creación y administración de una zona DNS en Azure Portal | Microsoft Docs"
description: "Obtenga información sobre cómo crear zonas DNS para Azure DNS. Esta es una guía paso a paso para crear y administrar su primera zona DNS con Azure Portal."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f44c5ea1-4c85-469e-888e-5f5b34451664
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: c9bebc8f54d3f732b3014f6885ee65a067e9d1d8

---

# <a name="create-a-dns-zone-in-the-azure-portal"></a>Creación de una zona DNS en el Portal de Azure

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI de Azure](dns-getstarted-create-dnszone-cli.md)

Este artículo le guiará por los pasos necesarios para crear una zona DNS con Azure Portal. También puede crear una zona DNS con PowerShell o la CLI.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="create-a-dns-zone"></a>Creación de una zona DNS

1. Inicie sesión en el Portal de Azure.
2. En el menú Concentrador, haga clic en **Nuevo > Redes >** y, luego, en **Zona DNS** para abrir la hoja Crear zona DNS.

    ![Zona DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

4. En la hoja **Crear zona DNS** , asígnele un nombre a la zona DNS. Por ejemplo, *contoso.com*.
 
    ![Crear zona](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

5. A continuación, especifique el grupo de recursos que desea usar. Puede crear un grupo de recursos o seleccionar uno ya existente. Si decide crear un nuevo grupo de recursos, use la lista desplegable **Ubicación** para especificar la ubicación del grupo de recursos. Tenga en cuenta que esta configuración se refiere a la ubicación del grupo de recursos y no tiene efecto alguno sobre la zona DNS. La ubicación de la zona DNS siempre es "global" y no se muestra.

6. Puede dejar activada la casilla **Anclar al panel** si desea ubicar fácilmente la zona nueva en el panel. A continuación, haga clic en **Crear**.

    ![Anclar al panel](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

7. Una vez que hace clic en Crear, verá la nueva zona configurada en el panel.

    ![Creating](./media/dns-getstarted-create-dnszone-portal/creating150.png)

8. Una vez que se crea la zona nueva, en el panel se abrirá la hoja correspondiente a esta zona nueva.

## <a name="view-records"></a>Visualización de los registros

Cuando se crea una zona DNS, también se crean los siguientes registros:

* El registro "Inicio de autoridad" (SOA). El SOA se encuentra en la raíz de cada zona DNS.
* Los registros de servidor de nombres (NS) autoritativo. Estos muestran qué servidores de nombres hospedan la zona. DNS de Azure usa un grupo de servidores de nombres, por lo que se pueden asignar diferentes servidores de nombres a zonas distintas en DNS de Azure. Consulte [Delegación de un dominio a DNS de Azure](dns-domain-delegation.md) para obtener más información.

En la parte inferior de la hoja Zona DNS, puede consultar los conjuntos de registros correspondientes a la zona DNS.

![zona](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test-name-servers"></a>Prueba de los servidores de nombres

Puede probar que la zona DNS esté presente en los servidores de nombres de Azure DNS con herramientas DNS tales como nslookup, DIG o el [cmdlet de PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si aún no ha delegado el dominio para usar la nueva zona en DNS en Azure, deberá dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona. Los servidores de nombres de su zona se proporcionan en Azure Portal:
    
![zona](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)

Asegúrese de sustituir el servidor de nombres correcto de su zona en el comando que aparece a continuación.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = azuredns-hostmaster.microsoft.com
            serial  = 1
            refresh = 3600 (1 hour)
            retry   = 300 (5 mins)
            expire  = 2419200 (28 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>Pasos siguientes

Después de crear una zona DNS, [cree registros y conjuntos de registros](dns-getstarted-create-recordset-portal.md) para crear los registros DNS para el dominio de Internet.




<!--HONumber=Dec16_HO2-->


