---
title: "Asignación de servicios a medidores para la cuenta gratuita: Azure | Microsoft Docs"
description: "Comprenda la asignación de servicios a medidores para los servicios incluidos con la cuenta gratuita."
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 9d7e355e755f2bac8929ab16f7f71aa3b0702658
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="understand-free-service-to-meter-mapping"></a>Descripción de la asignación de servicios a medidores

Todos los servicios de Azure emiten la utilización a los medidores, que el sistema de facturación de Azure utiliza para cobrar a los usuarios por los servicios. Para entender mejor el uso de los servicios gratuitos, echemos un vistazo a la asignación de servicios a medidores para estos servicios. Para información sobre cómo crear servicios gratuitos, consulte [Create free services with Azure free account](billing-create-free-services-included-free-account.md) (Creación de servicios gratuitos con la cuenta gratuita de Azure).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Asignación de servicios a medidores para servicios idóneos de la cuenta gratuita 

|    Servicio   | Nombre de medidor en Azure Portal | Nombre de medidor en la API o archivo de utilización | Id. de medidor |
| ------------ | -------------------------- | -------------------------| -------- |
| VM Linux B1S | Horas de proceso - VM B1 estándar | Horas de proceso - Gratis | 8260cba2-4437-47d1-a31e-2561cd370f50
| VM Windows B1S | Horas de proceso - VM B1 estándar (Windows) | Horas de proceso - Gratis | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S VM - Direcciones IP públicas  | Horas de dirección IP - Direcciones IP públicas | Horas de dirección IP - Gratis | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Almacenamiento (GB) - Cosmos DB | Almacenamiento (GB) - Gratis | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 unidades de solicitud (horas) - Cosmos DB | 100 unidades de solicitud (horas) - Gratis | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | E/S estándar - Archivos (GB) - Redundancia local | E/S estándar - Archivos (GB) - Gratis | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | E/S estándar - Unidades de operaciones de lectura de archivos (en decenas de millar) | E/S estándar - Unidades de operación de lectura de archivos (en decenas de millar) - Gratis | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | E/S estándar - Unidades de operación de escritura de archivos (en decenas de millar) | E/S estándar - Unidades de operaciones de escritura de archivos (en decenas de millar) - Gratis | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | E/S estándar - Unidades de operaciones de protocolo de archivo (en decenas de millar) | E/S estándar - Unidades de operación de protocolo de archivo (en decenas de millar) - Gratis | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | E/S estándar - Unidades de operaciones de lista de archivos (en decenas de millar) | E/S estándar - Unidades de operación de lista de archivos (en decenas de millar) - Gratis | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Almacenamiento de blobs en bloque de acceso frecuente | E/S estándar - Operaciones de lectura de blobs en bloque de acceso frecuente (en decenas de millar) | E/S estándar - Operaciones de lectura de blobs en bloque de acceso frecuente (en decenas de millar) - Gratis |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Almacenamiento de blobs en bloque de acceso frecuente | E/S estándar - Blob en bloques de acceso frecuente (GB) - Redundancia local | E/S estándar - Blob en bloques de acceso frecuente (GB) - Gratis | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Almacenamiento de blobs en bloque de acceso frecuente | E/S estándar - Operaciones de escritura de blobs en bloques de acceso frecuente (en decenas de millar) | E/S estándar - Operaciones de escritura de blobs en bloques de acceso frecuente (en decenas de millar) Gratis | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Almacenamiento de blobs en bloque de acceso frecuente  | E/S estándar - Operaciones lista y de escritura de blobs en bloques de acceso frecuente (en decenas de millar) | E/S estándar - Operaciones lista y de escritura de blobs en bloques de acceso frecuente (en decenas de millar) - Gratis | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Disco administrado *  | Disco administrado estándar/instantáneas (GB) - Redundancia local | Disco administrado estándar/instantáneas (GB) - Gratis | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Disco administrado *  | Operaciones de disco administrado estándar (en decenas de millar) | Operaciones de disco administrado estándar (en decenas de millar) - Gratis | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Disco administrado *  | Almacenamiento premium - Blob en páginas/P6 (unidades) - Redundancia local | Almacenamiento premium - Blob en páginas/P6 (unidades) - Gratis | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Días de base de datos S0 estándar - SQL Database | Días de base de datos S0 estándar - Gratis | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Compartido - Ancho de banda ** | Transferencia de datos de salida (GB) | Transferencia de datos de salida (GB) - Gratis | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Si crea una máquina virtual Windows y elige disco administrado, consumirá el medidor de disco administrado como parte de la máquina virtual.

\** Los medidores compartidos se pueden consumir a través de varios servicios. Por ejemplo, ambas máquinas virtuales y el almacenamiento emiten la utilización al medidor Transferencia de datos de salida (GB).





## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si necesita ayuda, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
