---
title: "Notas de la versión de Microsoft Azure Stack Development Kit | Microsoft Docs"
description: Mejoras, correcciones y problemas conocidos de Azure Stack Development Kit.
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: andredm
ms.openlocfilehash: 49d26defef56e6fc174cda57b24a126cd77227e4
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Notas de la versión de Azure Stack Development Kit

*Se aplica a: Azure Stack Development Kit*

Estas notas de la versión proporcionan información sobre las mejoras, correcciones y problemas conocidos en Azure Stack Development Kit. Si no está seguro de qué versión se está ejecutando, puede usar el [portal de administración](azure-stack-updates.md#determine-the-current-version).

## <a name="build-201711221"></a>Compilación 20171122.1

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

- Consulte la sección [Nuevas características y correcciones](azure-stack-update-1711.md#new-features-and-fixes) de las notas de versión de la actualización 1711 de Azure Stack para sistemas integrados de Azure Stack.

    > [!IMPORTANT]
    > Algunos de los elementos que aparecen en la sección **Nuevas características y correcciones** solo son importantes para los sistemas integrados de Azure Stack.

### <a name="known-issues"></a>Problemas conocidos
 
#### <a name="deployment"></a>Implementación
- Debe especificar un servidor de hora mediante una dirección IP durante la implementación.

#### <a name="infrastructure-management"></a>Administración de la infraestructura
- No habilite la copia de seguridad de la infraestructura en la hoja **Infrastructure backup** (Copia de seguridad de la infraestructura).
- La dirección IP del controlador de administración de placa base (BMC) y el modelo no se muestran en la información esencial de un nodo de unidad de escala. Este comportamiento es el esperado en Azure Stack Development Kit.

#### <a name="portal"></a>Portal
- Puede ver un panel en blanco en el portal. Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y, a continuación, seleccione **Restaurar configuración predeterminada**.
- El botón **Mover** está deshabilitado cuando se visualizan las propiedades de un grupo de recursos. Este comportamiento es normal. El movimiento de grupos de recursos entre suscripciones no se admite actualmente.
-  Para cualquier flujo de trabajo donde seleccione una suscripción, un grupo de recursos o una ubicación en una lista desplegable, puede experimentar uno o varios de los siguientes problemas:

   - Puede que vea una fila en blanco en la parte superior de la lista. Debería poder seleccionar un elemento según lo previsto.
   - Si la lista de elementos de la lista desplegable es breve, es posible que no se vean los nombres de los elementos.
   - Si tiene varias suscripciones de usuario, la lista desplegable de grupos de recursos podría estar vacía. 

   Para evitar los dos últimos problemas, puede escribir el nombre de la suscripción o el grupo de recursos (si lo conoce), o puede usar PowerShell en su lugar.

- Verá una altera de advertencia **Activación necesaria** que le informa de que debe registrar Azure Stack Development Kit. Este comportamiento es normal.
- Si se hace clic al vínculo **Componente** desde cualquier alerta de **rol de infraestructura**, la hoja **Introducción** resultante trata de cargarse y se produce un error. Además, en la hoja **Introducción** no se agota el tiempo de espera.
- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.
- No es posible ver los permisos para la suscripción con los portales de Azure Stack. Como solución alternativa, se pueden comprobar los permisos con PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Al intentar agregar elementos al Marketplace de Azure Stack mediante la opción **Add from Azure** (Agregar desde Azure), es posible que no todos los elementos estén visibles para su descarga.
- Los usuarios pueden examinar toda la plataforma Marketplace sin ninguna suscripción y ver elementos administrativos, como planes y ofertas. Estos elementos no funcionan para los usuarios.
 
#### <a name="compute"></a>Proceso
- Los usuarios tienen la opción de crear una máquina virtual con almacenamiento con redundancia geográfica. Esta configuración hace que no se puedan crear máquinas virtuales. 
- Puede configurar un conjunto de disponibilidad de la máquina virtual con un dominio de error de uno y un dominio de actualización de uno.
- No hay ninguna experiencia del Marketplace para crear conjuntos de escalado de máquinas virtuales. Puede crear un conjunto de escalado mediante una plantilla.
- Los valores de escalado para conjuntos de escalas de máquina virtual no están disponibles en el portal. Como alternativa, puede usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Debido a diferencias en la versión de PowerShell, debe usar el parámetro `-Name` en lugar de `-VMScaleSetName`.

#### <a name="networking"></a>Redes
- No puede crear un equilibrador de carga con una dirección IP pública mediante el portal. Como alternativa, puede usar PowerShell para crear el equilibrador de carga.
- Debe crear una regla de traducción de direcciones de red (NAT) cuando cree un equilibrador de carga de red. Si no lo hace, recibirá un error al intentar agregar una regla NAT después de crear el equilibrador de carga.
- En **Red**, si hace clic en **Conexión** para configurar una conexión VPN, **VNet a VNet** aparece como un tipo de conexión posible. No seleccione esta opción. Actualmente, solo se admite la opción **De sitio a sitio (IPsec)**.
- No se puede desasociar una dirección IP pública de una máquina virtual después de que la máquina virtual se haya creado y asociado a esa dirección IP. La desasociación parecerá funcionar, pero la dirección IP pública asignada previamente permanecerá asociada a la máquina virtual original. Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva. Actualmente, solo debe usar direcciones IP públicas nuevas para la creación de máquinas virtuales.
- Es posible que los operadores de Azure Stack no puedan implementar, eliminar o modificar las redes virtuales o los grupos de seguridad de red. Este problema se ve principalmente en los intentos de actualización posteriores del mismo paquete. Esto se debe a un problema de empaquetado con una actualización que esté investigándose.
- El equilibrio de carga interno (ILB) maneja incorrectamente las direcciones MAC de máquinas virtuales de back-end, lo que interrumpe las instancias de Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Puede pasar una hora antes de que los inquilinos puedan crear bases de datos en una nueva SKU SQL o MySQL. 
- La creación de elementos directamente en servidores de hospedaje SQL y MySQL que no realice el proveedor de recursos no se admite y puede provocar un estado de error de coincidencia.

#### <a name="app-service"></a>App Service
- Un usuario debe registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.
 
#### <a name="usage-and-billing"></a>Uso y facturación
- Los datos del medidor de uso de la dirección IP pública muestran el mismo valor *EventDateTime* para cada registro en lugar de la marca *TimeDate* que muestra cuándo se creó el registro. Actualmente, no puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.

#### <a name="identity"></a>Identidad

En entornos implementados de Servicios de federación de Active Directory (AD FS), la cuenta **azurestack\azurestackadmin** ya no es la propietaria de la suscripción de proveedor predeterminada. En lugar de iniciar sesión en el **portal de administración / punto de conexión adminmanagement** con **azurestack\azurestackadmin**, puede usar la cuenta **azurestack\cloudadmin**, de forma que puede administrar y usar la suscripción de proveedor predeterminada.

> [!IMPORTANT]
> Incluso la cuenta **azurestack\cloudadmin** es la propietaria de la suscripción de proveedor predeterminada en entornos implementados de ADFS, no tiene permisos para RDP en el host. Siga usando la cuenta **azurestack\azurestackadmin** o la cuenta de administrador local para iniciar sesión en el host, acceder a él y administrarlo según sea necesario.


## <a name="build-201710201"></a>Compilación 20171020.1

### <a name="improvements-and-fixes"></a>Mejoras y correcciones

Para ver la lista de mejoras y correcciones de la compilación 20171020.1, vea la sección [Mejoras y correcciones](azure-stack-update-1710.md#improvements-and-fixes) de las notas de la versión 1710 para los sistemas integrados de Azure Stack. Algunos de los elementos que aparecen en la sección "Correcciones y mejoras de calidad adicionales" solo son importantes para los sistemas integrados.

También se han realizado las siguientes correcciones:
- Se ha solucionado un problema en el que el proveedor de recursos de Compute mostraba un estado desconocido.
- Se ha corregido un problema en el que las cuotas podía no aparecer en el portal del administrador después de crearlas y luego intentar ver los detalles del plan.

### <a name="known-issues"></a>Problemas conocidos

#### <a name="powershell"></a>PowerShell
- La versión del módulo de PowerShell AzureRM 1.2.11 incluye una lista de los últimos cambios. Para información sobre cómo actualizar la versión 1.2.10, consulte la [guía de migración](https://aka.ms/azspowershellmigration).
 
#### <a name="deployment"></a>Implementación
- Debe especificar un servidor de hora mediante una dirección IP durante la implementación.

#### <a name="infrastructure-management"></a>Administración de la infraestructura
- No habilite la copia de seguridad de la infraestructura en la hoja **Infrastructure backup** (Copia de seguridad de la infraestructura).
- La dirección IP del controlador de administración de placa base (BMC) y el modelo no se muestran en la información esencial de un nodo de unidad de escala. Este comportamiento es el esperado en Azure Stack Development Kit.

#### <a name="portal"></a>Portal
- Puede ver un panel en blanco en el portal. Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y, a continuación, seleccione **Restaurar configuración predeterminada**.
- El botón **Mover** está deshabilitado cuando se visualizan las propiedades de un grupo de recursos. Este comportamiento es normal. El movimiento de grupos de recursos entre suscripciones no se admite actualmente.
-  Para cualquier flujo de trabajo donde seleccione una suscripción, un grupo de recursos o una ubicación en una lista desplegable, puede experimentar uno o varios de los siguientes problemas:

   - Puede que vea una fila en blanco en la parte superior de la lista. Debería poder seleccionar un elemento según lo previsto.
   - Si la lista de elementos de la lista desplegable es breve, es posible que no se vean los nombres de los elementos.
   - Si tiene varias suscripciones de usuario, la lista desplegable de grupos de recursos podría estar vacía. 

   Para evitar los dos últimos problemas, puede escribir el nombre de la suscripción o el grupo de recursos (si lo conoce), o puede usar PowerShell en su lugar.

- Verá una altera de advertencia **Activación necesaria** que le informa de que debe registrar Azure Stack Development Kit. Este comportamiento es normal.
- En los detalles de alerta de advertencia de **Activación necesaria**, no haga clic en el vínculo al componente **AzureBridge**. Si lo hace, la hoja **Información general** intentará cargarse sin éxito y no se agotará el tiempo de espera.
- En el portal de administrador, puede ver un error **Error al capturar los inquilinos** en el área **Notificaciones**. Puede omitir este error sin problemas.
- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.
- No es posible ver los permisos para la suscripción con los portales de Azure Stack. Como solución alternativa, se pueden comprobar los permisos con PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Al intentar agregar elementos al Marketplace de Azure Stack mediante la opción **Add from Azure** (Agregar desde Azure), es posible que no todos los elementos estén visibles para su descarga.
- Los usuarios pueden examinar toda la plataforma Marketplace sin ninguna suscripción y ver elementos administrativos, como planes y ofertas. Estos elementos no funcionan para los usuarios.
 
#### <a name="compute"></a>Proceso
- Los usuarios tienen la opción de crear una máquina virtual con almacenamiento con redundancia geográfica. Esta configuración hace que no se puedan crear máquinas virtuales. 
- Puede configurar un conjunto de disponibilidad de la máquina virtual con un dominio de error de uno y un dominio de actualización de uno.
- No hay ninguna experiencia del Marketplace para crear conjuntos de escalado de máquinas virtuales. Puede crear un conjunto de escalado mediante una plantilla.
- Los valores de escalado para conjuntos de escalas de máquina virtual no están disponibles en el portal. Como alternativa, puede usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Debido a diferencias en la versión de PowerShell, debe usar el parámetro `-Name` en lugar de `-VMScaleSetName`.

#### <a name="networking"></a>Redes
- No puede crear un equilibrador de carga con una dirección IP pública mediante el portal. Como alternativa, puede usar PowerShell para crear el equilibrador de carga.
- Debe crear una regla de traducción de direcciones de red (NAT) cuando cree un equilibrador de carga de red. Si no lo hace, recibirá un error al intentar agregar una regla NAT después de crear el equilibrador de carga.
- En **Red**, si hace clic en **Conexión** para configurar una conexión VPN, **VNet a VNet** aparece como un tipo de conexión posible. No seleccione esta opción. Actualmente, solo se admite la opción **De sitio a sitio (IPsec)**.
- No se puede desasociar una dirección IP pública de una máquina virtual después de que la máquina virtual se haya creado y asociado a esa dirección IP. La desasociación parecerá funcionar, pero la dirección IP pública asignada previamente permanecerá asociada a la máquina virtual original. Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva. Actualmente, solo debe usar direcciones IP públicas nuevas para la creación de máquinas virtuales.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Puede pasar una hora antes de que los inquilinos puedan crear bases de datos en una nueva SKU SQL o MySQL. 
- La creación de elementos directamente en servidores de hospedaje SQL y MySQL que no realice el proveedor de recursos no se admite y puede provocar un estado de error de coincidencia.

#### <a name="app-service"></a>App Service
- Un usuario debe registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.
 
#### <a name="usage-and-billing"></a>Uso y facturación
- Los datos del medidor de uso de la dirección IP pública muestran el mismo valor *EventDateTime* para cada registro en lugar de la marca *TimeDate* que muestra cuándo se creó el registro. Actualmente, no puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.

## <a name="build-201709283"></a>Compilación 20170928.3

### <a name="known-issues"></a>Problemas conocidos

#### <a name="powershell"></a>PowerShell
- La versión del módulo de PowerShell AzureRM 1.2.11 incluye una lista de los últimos cambios. Para información sobre cómo actualizar la versión 1.2.10, consulte la [guía de migración](https://aka.ms/azspowershellmigration).

#### <a name="deployment"></a>Implementación
- Debe especificar un servidor de hora mediante una dirección IP durante la implementación.

 #### <a name="infrastructure-management"></a>Administración de la infraestructura
- No habilite la copia de seguridad de la infraestructura en la hoja **Infrastructure backup** (Copia de seguridad de la infraestructura).
- El proveedor de recursos de Compute muestra un estado desconocido.
- La dirección IP del controlador de administración de placa base (BMC) y el modelo no se muestran en la información esencial de un nodo de unidad de escala. Este comportamiento es el esperado en Azure Stack Development Kit. 
   
#### <a name="portal"></a>Portal
- Puede ver un panel en blanco en el portal. Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y, a continuación, seleccione **Restaurar configuración predeterminada**.
- El botón **Mover** está deshabilitado cuando se visualizan las propiedades de un grupo de recursos. Este comportamiento es normal. El movimiento de grupos de recursos entre suscripciones no se admite actualmente.
- Verá una altera de advertencia **Activación necesaria** que le informa de que debe registrar Azure Stack Development Kit. Este comportamiento es normal.
- En los detalles de alerta de advertencia de **Activación necesaria**, no haga clic en el vínculo al componente **AzureBridge**. Si lo hace, la hoja **Información general** intentará cargarse sin éxito y no se agotará el tiempo de espera.
- Puede que las cuentas no se muestren en el portal del administrador después de crearlas e intentar ver luego los detalles del plan. Como solución alternativa, en **Servicios y cuotas**, haga clic en **Agregar**, y agregue una nueva entrada.
- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.
- No es posible ver los permisos para la suscripción con los portales de Azure Stack. Como solución alternativa, puede comprobar los permisos con PowerShell.
  
#### <a name="marketplace"></a>Marketplace
- Los usuarios pueden examinar toda la plataforma Marketplace sin ninguna suscripción y ver elementos administrativos, como planes y ofertas. Estos elementos no funcionan para los usuarios.
 
#### <a name="compute"></a>Proceso
- Los usuarios tienen la opción de crear una máquina virtual con almacenamiento con redundancia geográfica. Esta configuración hace que no se puedan crear máquinas virtuales.
- Puede configurar un conjunto de disponibilidad de la máquina virtual con un dominio de error de uno y un dominio de actualización de uno.
- No hay ninguna experiencia del Marketplace para crear conjuntos de escalado de máquinas virtuales. Puede crear un conjunto de escalado mediante una plantilla.

#### <a name="networking"></a>Redes
- No puede crear un equilibrador de carga con una dirección IP pública mediante el portal. Como alternativa, puede usar PowerShell para crear el equilibrador de carga.
- Debe crear una regla de traducción de direcciones de red (NAT) cuando cree un equilibrador de carga de red. Si no lo hace, recibirá un error al intentar agregar una regla NAT después de crear el equilibrador de carga.
- En **Red**, si hace clic en **Conexión** para configurar una conexión VPN, **VNet a VNet** aparece como un tipo de conexión posible. No seleccione esta opción. Actualmente, solo se admite la opción **De sitio a sitio (IPsec)**.
- No se puede desasociar una dirección IP pública de una máquina virtual después de que la máquina virtual se haya creado y asociado a esa dirección IP. La desasociación parecerá funcionar, pero la dirección IP pública asignada previamente permanecerá asociada a la máquina virtual original. Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que en ocasiones se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva. Actualmente, solo debe usar direcciones IP públicas nuevas para la creación de máquinas virtuales.

#### <a name="sqlmysql"></a>SQL/MySQL
- Puede pasar una hora antes de que los inquilinos puedan crear bases de datos en una nueva SKU SQL o MySQL. 
- La creación de elementos directamente en servidores de hospedaje SQL y MySQL que no realice el proveedor de recursos no se admite y puede provocar un estado de error de coincidencia.

#### <a name="app-service"></a>App Service
- Un usuario debe registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.
