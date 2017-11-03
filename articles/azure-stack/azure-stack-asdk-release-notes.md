---
title: "Notas de la versión de Microsoft Azure Stack Development Kit | Microsoft Docs"
description: 
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: helaw
ms.openlocfilehash: 5ee2f8164d13e61f9a43abcbda729298f2168518
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Notas de la versión de Azure Stack Development Kit

*Se aplica a: Azure Stack Development Kit*

Estas notas de la versión proporcionan información sobre las nuevas características y los problemas conocidos en el kit de desarrollo de Azure Stack. Si no está seguro de qué versión se está ejecutando, puede usar el [portal de administración](azure-stack-updates.md#determine-the-current-version).

## <a name="release-build-201709283"></a>Compilación de versión 20170928.3

### <a name="known-issues"></a>Problemas conocidos

#### <a name="powershell"></a>PowerShell

- La versión del módulo de PowerShell AzureRM 1.2.11 incluye una lista de los últimos cambios. Para información sobre cómo actualizar la versión 1.2.10, consulte la guía de migración en [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).


#### <a name="deployment"></a>Implementación
* Debe especificar un servidor de hora mediante una dirección IP durante la implementación.  


#### <a name="portal"></a>Portal
* Puede ver un panel en blanco en el portal.  Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y seleccione "Restaurar configuración predeterminada".
* Los inquilinos podrán examinar toda la plataforma Marketplace sin ninguna suscripción y verán elementos administrativos como planes y ofertas.  Estos elementos no funcionan para los inquilinos.
* El botón "Mover" está deshabilitado en el Grupo de recursos.  El comportamiento esperado es el botón deshabilitado, porque el movimiento de grupos de recursos entre suscripciones no se admite actualmente.
* No es posible ver los permisos para la suscripción con los portales de Azure Stack.  Como solución alternativa, se pueden comprobar los permisos con Powershell.
* Verá una alerta que avisa de que registre el kit de desarrollo de Azure Stack.  El comportamiento esperado es esta alerta.  
  

#### <a name="services"></a>Services
* No hay ninguna experiencia de Marketplace para crear conjuntos de escalado de máquinas virtuales, aunque se pueden crear mediante una plantilla.
* No es posible crear un equilibrador de carga con una dirección IP pública mediante el portal.  Como alternativa, puede usar PowerShell para crear el equilibrador de carga.
* Los conjuntos de disponibilidad de la máquina virtual solo pueden configurarse con un dominio de error de uno y un dominio de actualización de uno.  
* Un inquilino debe registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.
* La eliminación de las suscripciones del inquilino da como resultado recursos huérfanos.  Como alternativa, elimine primero los recursos del inquilino o todo grupo de recursos y, a continuación, elimine las suscripciones del inquilino. 
* Debe crear una regla NAT cuando crea un equilibrador de carga de red. Si no lo hace, recibirá un error al intentar agregar una regla NAT después de crear el equilibrador de carga.
* Los inquilinos tienen la opción de crear una máquina virtual con almacenamiento con redundancia geográfica.  Esta configuración hace que no se puedan crear máquinas virtuales.
* Puede pasar una hora antes de que los inquilinos puedan crear bases de datos en una nueva SKU SQL o MySQL. 
* La creación de elementos directamente en servidores de hospedaje SQL y MySQL que no realice el proveedor de recursos no se admite y puede provocar un estado de error de coincidencia.
* No debe usarse la hoja de copia de seguridad de la infraestructura.


#### <a name="fabric"></a>Fabric
* El proveedor de recursos de Compute muestra un estado desconocido.
* La dirección IP y el modelo de BMC no se muestran en la información esencial de un nodo de unidad de escala.  Este comportamiento es el esperado en Azure Stack Development Kit.

