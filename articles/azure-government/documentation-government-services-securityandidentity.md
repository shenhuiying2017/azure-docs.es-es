---
title: Seguridad e identidad de Azure Government | Microsoft Docs
description: "Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: e2fe7983-5870-43e9-ae01-2d45d3102c8a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: cd01170c3c0a3f62024de3357d342af1f4f90c6c
ms.openlocfilehash: 27d447e8e3c336bbce2e1ca81d2c7c413b0360fc


---
# <a name="azure-government-security--identity"></a>Seguridad e identidad de Azure Government
## <a name="key-vault"></a>Key Vault
Para más información sobre este servicio y cómo usarlo, consulte la [documentación pública de Azure Key Vault](../key-vault/index.md).

### <a name="data-considerations"></a>Consideraciones de datos
La información siguiente identifica el límite de Azure Government para el Almacén de claves de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
| --- | --- |
| Todos los datos cifrados con una clave del Almacén de claves de Azure pueden contener datos regulados o controlados. |Los metadatos del Almacén de claves de Azure no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener el Almacén de claves.  No inserte datos regulados o controlados en los siguientes campos: nombres de grupos de recursos, nombres del Almacén de claves o nombres de suscripciones. |

El Almacén de claves está disponible en Azure Government con carácter general. Como es público, no hay ninguna extensión, por lo que solo está disponible mediante PowerShell y la CLI.

## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>




<!--HONumber=Nov16_HO3-->


