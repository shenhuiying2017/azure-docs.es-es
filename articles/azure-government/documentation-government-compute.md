---
title: Proceso de Azure Government | Microsoft Docs
description: "Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: fb11f60c-5a70-46a9-82a0-abb2a4f4239b
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/18/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: ca01baebd755e92aff1ef080af1fcd4606b9a2ec
ms.openlocfilehash: 200caac4b480495b0bbb8f10cc89fdd73c42dbe2


---
# <a name="azure-government-compute"></a>Proceso de Azure Government
## <a name="virtual-machines"></a>Máquinas virtuales
Para más información sobre este servicio y cómo usarlo, consulte [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="variations"></a>Variaciones
Los productos (VM) están disponibles en Azure Government:

| Productos (VM) | US Gov VA | US Gov IA |
| --- | --- | --- |
| A0-A7 |Y |Y |
| Serie D |Y |N |
| Serie Dv2 |Y |Y |
| Serie DS |Y |N |
| Serie F |Y |Y |
| Serie Fs |N |N |
| Serie G |Y |N |
| Serie GS |Y |N |

### <a name="data-considerations"></a>Consideraciones de datos
La siguiente información identifica el límite de Azure Government para Máquinas virtuales de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
| --- | --- |
| Los datos especificados, almacenados y procesados dentro de una máquina virtual pueden contener datos controlados para exportación. Archivos binarios que se ejecutan dentro de Máquinas virtuales de Azure. Autenticadores estáticos, como contraseñas y PIN de smartcard para el acceso a componentes de la plataforma Azure. Claves privadas de certificados que se usan para administrar los componentes de la plataforma Azure. Cadenas de conexión SQL.  Otros secretos o información de seguridad, como certificados, claves de cifrado, claves maestras y claves de almacenamiento almacenadas en servicios de Azure. |Los metadatos no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su instancia de Máquinas virtuales de Azure.  No inserte datos regulados o controlados en los siguientes campos: nombres de roles de inquilino, grupos de recursos, nombres de implementación, nombres de recursos o etiquetas de recursos. |

## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>




<!--HONumber=Dec16_HO1-->


