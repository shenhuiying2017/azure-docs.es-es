---
title: Administración de zonas DNS con CLI | Microsoft Docs
description: Puede administrar zonas DNS con CLI de Azure. Actualización, eliminación y creación de zonas DNS en DNS de Azure
services: dns
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: cherylmc

---
# Cómo administrar zonas DNS con CLI
> [!div class="op_single_selector"]
> * [CLI de Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)
> 
> 

Esta guía explica le mostrará cómo administrar los recursos de zona DNS mediante la CLI de Azure multiplataforma.

En estas instrucciones se usa la CLI de Microsoft Azure. Asegúrese de actualizar a la CLI de Azure más reciente (0.9.8 o posterior) para utilizar los comandos de DNS de Azure. Escriba `azure -v` para comprobar qué versión de la CLI de Azure está instalada en su equipo. Puede instalar la CLI de Azure para Windows, Linux o MAC. Puede consultar mas información disponible en [Instalación de la CLI de Azure](../xplat-cli-install.md).

DNS de Azure es un servicio exclusivo del Administrador de recursos de Azure. No tiene ninguna API de ASM. Debe asegurarse de que la CLI de Azure está configurada para utilizar el modo de Resource Manager. Puede hacerlo si utiliza el comando `azure config mode arm`.<BR> Si ve el mensaje "*error: 'dns' is not an azure command*", lo más probable es que se deba a que está usando CLI de Azure en modo ASM, no en el modo de Resource Manager.

## Creación de una zona DNS
Para crear una nueva zona DNS para hospedar un dominio, consulte [Creación de una zona DNS de Azure con CLI](dns-getstarted-create-dnszone-cli.md).

## Recuperación de una zona DNS
Para recuperar una zona DNS, use `azure network dns zone show`:

    azure network dns zone show myresourcegroup contoso.com

La operación devuelve una zona DNS con su Id., el número de conjuntos de registros y las etiquetas.

## Enumeración de zonas DNS
Para recuperar las zonas DNS dentro de un grupo de recursos, use `azure network dns zone list`:

    azure network dns zone list myresourcegroup

## Actualización de una zona DNS
Los cambios en los recursos de una zona DNS se pueden realizar con `azure network dns zone set`. Con esta operación no se actualizan los conjuntos de registros DNS de la zona (consulte [Administración de registros DNS](dns-operations-recordsets.md)). Solo se utiliza para actualizar las propiedades de los recursos de la zona. Esto se limita actualmente a las “etiquetas” del Administrador de recursos de Azure para los recursos de la zona. Consulte [Etags y etiquetas](dns-getstarted-create-dnszone.md#tagetag) para obtener más información.

    azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Eliminación de una zona DNS
Las zonas DNS se pueden eliminar mediante `azure network dns zone delete`. Esta operación tiene un modificador opcional *-q* que suprime el mensaje para confirmar que desea eliminar la zona DNS.

Antes de eliminar una zona DNS de DNS de Azure, deberá eliminar todos los conjuntos de registros, salvo los registros NS y SOA de la raíz de la zona que se crearon automáticamente cuando se creó la zona.

    azure network dns zone delete myresourcegroup contoso.com 



## Pasos siguientes
Después de crear una zona DNS, cree [conjuntos de registros y registros](dns-getstarted-create-recordset-cli.md) para iniciar la resolución de nombres para el dominio de Internet.

<!---HONumber=AcomDC_0817_2016-->