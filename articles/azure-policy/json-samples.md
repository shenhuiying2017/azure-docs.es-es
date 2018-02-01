---
title: Ejemplos de plantillas de directivas | Microsoft Docs
description: Ejemplos de JSON para Azure Policy
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: na
ms.topic: samples
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 01/17/2018
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 19641f3b1f2781cf23955743bf65172ff84fb544
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="templates-for-azure-policy"></a>Plantillas para Azure Policy

En la tabla siguiente se incluyen vínculos a las plantillas de JSON para Azure Policy. Estos ejemplos se encuentran en el [repositorio de ejemplos de Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Proceso**||
| [Imágenes de máquina virtual aprobadas](scripts/allowed-custom-images.md) | Requiere que solo las imágenes personalizadas aprobadas se puedan implementar en su entorno. Se especifica una matriz de identificadores de las imágenes aprobadas. |
| [Auditar cuando la máquina virtual no utiliza un disco administrado](scripts/create-vm-managed-disk.md) | Audita cuando se crea una máquina virtual que no usa discos administrados.|
| [Auditar si la extensión no existe](scripts/audit-ext-not-exist.md) | Audita si una extensión no se implementa con una máquina virtual. Se especifica el tipo y el publicador de la extensión para comprobar si se implementó. |
| [Permitir una imagen de máquina virtual personalizada desde un grupo de recursos](scripts/allow-custom-vm-image.md) |  Requiere que las imágenes personalizadas provengan de un grupo de recursos aprobado. Se especifica el nombre del grupo de recursos aprobado. |
| [Denegar la ventaja para uso híbrido](scripts/deny-hybrid-use.md) | Prohíbe el uso de la Ventaja para uso híbrido de Azure (AHUB). Se usa cuando no se quiere permitir que se usen las licencias locales. |
| [Extensiones de máquina virtual no permitidas](scripts/not-allowed-vm-ext.md) | Prohíbe el uso de las extensiones especificadas. Se especifica una matriz que contiene los tipos de extensiones prohibidos. |
| [Permitir solo cierta imagen de plataforma de máquina virtual](scripts/allow-certain-vm-image.md) | Requiere que las máquinas virtuales usen una versión específica de UbuntuServer. |
| [Crear una máquina virtual con un disco administrado](scripts/use-managed-disk-vm.md) | Requiere que las máquinas virtuales usen discos administrados.|
|**Supervisión**||
| [Auditar la configuración de diagnóstico](scripts/audit-diag-setting.md) | Audita si la configuración de diagnóstico no está habilitada para tipos de recursos especificados. Se especifica una matriz de tipos de recursos para comprobar si la configuración de diagnóstico está habilitada. |
|**Convenciones de nombre y texto**||
| [Permitir varios patrones de nombre](scripts/allow-multiple-name-patterns.md) | Permite utilizar uno de varios patrones de nombre para los recursos. |
| [Requerir patrón Like](scripts/enforce-like-pattern.md) | Asegúrese de que los nombres de recursos cumplan la condición Like para un patrón. |
| [Requerir patrón de coincidencia](scripts/enforce-match-pattern.md) | Asegúrese de que los nombres de recursos coincidan con el patrón de nomenclatura. |
| [Requerir patrón de coincidencia de etiqueta](scripts/enforce-tag-match-pattern.md) | Asegúrese de que un valor de etiqueta coincida con un patrón de texto. |
|**Red**||
| [SKU permitidas de Application Gateway](scripts/allowed-app-gate-sku.md) | Requiere que Application Gateways use una SKU aprobada. Se especifica una matriz de las SKU aprobadas. |
| [Audita si Network Watcher no está habilitado para la región](scripts/net-watch-not-enabled.md) | Audita si Network Watcher no está habilitado para una región especificada. Se especifica el nombre de la región para comprobar si Network Watcher está habilitado. |
| [NSG X en cada NIC](scripts/nsg-on-nic.md) | Requiere que se use un grupo de seguridad de red específico con cada interfaz de red virtual. Se especifica el identificador del grupo de seguridad de red que se va a usar. |
| [NSG X en cada subred](scripts/nsg-on-subnet.md) | Requiere que se use un grupo de seguridad de red específico con cada subred virtual. Se especifica el identificador del grupo de seguridad de red que se va a usar. |
| [Ancho de banda permitido de ExpressRoute](scripts/allowed-er-band.md) | Requiere que ExpressRoute use un conjunto de anchos de banda especificados. Se especifica una matriz de las SKU que se pueden especificar para ExpressRoute. |
| [Ubicación de emparejamiento permitida para ExpressRoute](scripts/allowed-peering-er.md) | Requiere que ExpressRoute use ubicaciones de emparejamiento especificadas. Se especifica una matriz de ubicaciones de emparejamiento permitidas. |
| [SKU permitidas de ExpressRoute](scripts/allowed-er-skus.md) | Requiere que ExpressRoute use una SKU aprobada. Se especifica una matriz de las SKU permitidas. |
| [SKU permitidas de Load Balancer](scripts/allowed-lb-skus.md) | Requiere que Load Balancer use una SKU aprobada. Se especifica una matriz de las SKU permitidas. |
| [Sin emparejamiento de red con la red de ER](scripts/no-peering-er-net.md) | Prohíbe que se asocie un emparejamiento de red con una red de un grupo de recursos especificado. Se usa para evitar la conexión con la infraestructura de red administrada central. Se especifica el nombre del grupo de recursos para evitar la asociación. |
| [Sin tabla de rutas definida por el usuario](scripts/no-user-def-route-table.md)  |Prohíbe que se implementen redes virtuales con una tabla de rutas definida por el usuario. |
| [SKU permitidas de Puerta de enlace de red virtual](scripts/allowed-vn-gate-sku.md) | Requiere que las puertas de enlace de red virtual usen un tipo de puerta de enlace y SKU aprobadas. Se especifica una matriz de SKU aprobadas y una matriz de tipos de puertas de enlace aprobados. |
| [Usar subred aprobada para las interfaces de red de máquinas virtuales](scripts/use-approved-subnet-vm-nics.md) | Requiere que las interfaces de red usen una subred aprobada. Se especifica el identificador de la subred aprobada. |
| [Usar red virtual aprobada para las interfaces de red de máquinas virtuales](scripts/use-approved-vnet-vm-nics.md) | Requiere que las interfaces de red usen una red virtual aprobada. Se especifica el identificador de la red virtual aprobada. |
|**Etiquetas**||
| [Iniciativa de directiva de etiquetas de facturación](scripts/billing-tags-policy-init.md) | Requiere valores de etiqueta especificados para el nombre de producto y el centro de costos. Usa directivas integradas para aplicar las etiquetas requeridas. Se especifican los valores requeridos para las etiquetas.  |
| [Aplicar etiqueta y su valor en grupos de recursos](scripts/enforce-tag-rg.md) | Requiere una etiqueta y un valor en un grupo de recursos. Se especifica el valor y el nombre de etiqueta que se requieren.  |
|**SQL**||
| [Auditar la configuración de auditoría en el nivel de SQL DB](scripts/audit-sql-db-audit-setting.md) | Audita la configuración de auditoría de SQL Database si esa configuración no coincide con una configuración especificada. Se especifica un valor que indica si la configuración de auditoría debe estar habilitada o deshabilitada.  |
| [Auditar el estado del cifrado de datos transparente](scripts/audit-trans-data-enc-status.md) | Audita el cifrado de datos transparente de SQL Database si no está habilitado.  |
| [Auditar la configuración de detección de amenazas en el nivel de base de datos](scripts/audit-db-threat-det-setting.md) | Audita las directivas de alertas de seguridad de SQL Database si no están establecidas en un estado especificado. Se especifica un valor que indica si la detección de amenazas está habilitada o deshabilitada.  |
| [Auditar la configuración de auditoría en el nivel de SQL Server](scripts/audit-sql-ser-leve-audit-setting.md) | Audita la configuración de auditoría de SQL Server si esa configuración no coincide con una configuración especificada. Se especifica un valor que indica si la configuración de auditoría debe estar habilitada o deshabilitada. |
| [Auditar la configuración de detección de amenazas en el nivel de servidor](scripts/audit-sql-ser-threat-det-setting.md) | Audita las directivas de alertas de seguridad de SQL Database si no están establecidas en un estado especificado. Se especifica un valor que indica si la detección de amenazas está habilitada o deshabilitada.  |
| [Auditar que no haya un administrador de Azure Active Directory](scripts/audit-no-aad-admin.md) | Audite cuándo no hay administrador de Azure Active Directory asignado al servidor SQL. |
| [SKU permitidas de SQL DB](scripts/allowed-sql-db-skus.md) | Requiere que las bases de datos de SQL Database usen una SKU aprobada. Se especifica una matriz de los identificadores de las SKU permitidas o una matriz de los nombres de las SKU que se permiten. |
|**Storage**||
| [SKU permitidas para cuentas de almacenamiento y máquinas virtuales](scripts/allowed-skus-storage.md) | Requiere que las cuentas de almacenamiento y las máquinas virtuales usen las SKU aprobadas. Se usan directivas integradas para garantizar SKU aprobadas. Se especifica una matriz de las SKU de máquinas virtuales aprobadas y una matriz de las SKU de cuentas de almacenamiento aprobadas. |
| [Asegurar el tráfico HTTPS solo para una cuenta de almacenamiento](scripts/ensure-https-stor-acct.md) | Requiere que las cuentas de almacenamiento usen el tráfico HTTPS.  |
| [Denegar el nivel de almacenamiento de acceso esporádico para las cuentas de almacenamiento](scripts/deny-cool-access-tiering.md) | Prohíbe el uso del nivel de almacenamiento de acceso esporádico para las cuentas de Blob Storage.  |
| [Asegurar el cifrado de archivos de almacenamiento](scripts/ensure-store-file-enc.md) | Requiere que el cifrado de archivos esté habilitado para las cuentas de almacenamiento.  |
|**Directiva integrada**||
| [Ubicaciones permitidas](scripts/allowed-locs.md) | Requiere que todos los recursos se implementen en las ubicaciones aprobadas. Se especifica una matriz de ubicaciones aprobadas.  |
| [Tipos de recursos permitidos](scripts/allowed-res-types.md) | Garantiza que solo se implementen los tipos de recursos aprobados. Se especifica una matriz de los tipos de recursos permitidos.  |
| [SKU permitidas de cuentas de almacenamiento](scripts/allowed-stor-acct-skus.md) | Requiere que las cuentas de almacenamiento usen una SKU aprobada. Se especifica una matriz de las SKU aprobadas. |
| [Aplicar etiqueta y su valor predeterminado](scripts/apply-tag-def-val.md) | Anexa un nombre y valor de etiqueta especificado, si no se proporciona esa etiqueta. Se especifica el valor y el nombre de la etiqueta que se va a aplicar.  |
| [Aplicar una etiqueta y su valor](scripts/enforce-tag-val.md) | Requiere un valor y nombre de etiqueta especificada. Se especifica el valor y el nombre de la etiqueta que se va a aplicar.  |
| [Tipos de recursos no permitidos](scripts/not-allowed-res-type.md) | Prohíbe la implementación de tipos de recursos especificados. Se especifica una matriz de los tipos de recurso que se bloquearán.  |
| [Requerir SQL Server 12.0](scripts/req-sql-12.md) | Requiere que los servidores de SQL Server usen la versión 12.0.  |
| [Requerir el cifrado de la cuenta de almacenamiento](scripts/req-store-acct-enc.md) | Requiere que la cuenta de almacenamiento use el cifrado de blobs.  |
