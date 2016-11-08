---
title: Documentación de Azure Government | Microsoft Docs
description: Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure
services: Azure-Government
cloud: gov
documentationcenter: ''
author: ryansoc
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 09/29/2016
ms.author: ryansoc

---
# <a name="azure-government-compute"></a>Proceso de Azure Government
## <a name="virtual-machines"></a>Máquinas virtuales
Para más información sobre este servicio y cómo usarlo, consulte [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Variaciones
Las siguientes SKU de máquina virtual están disponible con carácter general (GA) en Azure Government:

| SKU de la máquina virtual | US Gov VA | US Gov IA | Notas |
| --- | --- | --- | --- |
| Encontrará |GA |GA |None |
| Dv1 |GA |- |None |
| DSv1 |GA |- |None |
| Dv2 |GA |GA |15 próximamente |
| F |GA |GA |None |
| G |Planeado |- |None |

### <a name="data-considerations"></a>Consideraciones de datos
La siguiente información identifica el límite de Azure Government para Máquinas virtuales de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
| --- | --- |
| Los datos especificados, almacenados y procesados dentro de una máquina virtual pueden contener datos controlados para exportación. Archivos binarios que se ejecutan dentro de Máquinas virtuales de Azure. Autenticadores estáticos, como contraseñas y PIN de smartcard para el acceso a componentes de la plataforma Azure. Claves privadas de certificados que se usan para administrar los componentes de la plataforma Azure. Cadenas de conexión SQL.  Otros secretos o información de seguridad, como certificados, claves de cifrado, claves maestras y claves de almacenamiento almacenadas en servicios de Azure. |Los metadatos no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su instancia de Máquinas virtuales de Azure.  No inserte datos regulados o controlados en los siguientes campos: nombres de roles de inquilino, grupos de recursos, nombres de implementación, nombres de recursos o etiquetas de recursos. |

## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>

<!--HONumber=Oct16_HO2-->


