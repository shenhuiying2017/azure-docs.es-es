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
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2bd93faf01c5d1790989a0231020ce8340eff57d
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="azure-stack-development-kit-release-notes"></a>Notas de la versión de Azure Stack Development Kit

*Se aplica a: Azure Stack Development Kit*

Estas notas de la versión proporcionan información sobre las nuevas características y los problemas conocidos.

## <a name="release-build-201706271"></a>Compilación de versión 20170627.1
A partir de la versión [20170627.1](azure-stack-updates.md#determine-the-current-version), se ha cambiado el nombre de la prueba de concepto de Azure Stack a Azure Stack Development Kit.  Igual que la prueba de concepto de Azure Stack, Azure Stack Development Kit está pensado para ser un entorno de desarrollo y evaluación, usado para explorar las características de Azure Stack y proporcionar una plataforma de desarrollo para Azure Stack.

### <a name="whats-new"></a>Novedades
- Ahora se puede usar CLI 2.0 para administrar los recursos de Azure Stack desde una línea de comandos en sistemas operativos populares.
- Los tamaños de máquina virtual DSV2 permiten la portabilidad de plantilla entre Azure y Azure Stack.
- Los operadores de la nube pueden obtener una vista previa de la experiencia con la administración de capacidad dentro de la hoja de administración de capacidad.
- Ahora se puede usar la extensión Azure Diagnostics para recopilar datos de diagnóstico de las máquinas virtuales.  La captura de estos datos resulta útil para analizar el rendimiento de la carga de trabajo y para investigar los problemas.
- Una nueva [experiencia de implementación](azure-stack-run-powershell-script.md) reemplaza los anteriores pasos generados por script para la implementación.  La nueva experiencia de implementación proporciona una interfaz gráfica común durante todo el ciclo de vida de la implementación.
- Ahora se admiten las cuentas Microsoft (MSA) durante la implementación.
- Ahora se admite Multi-Factor Authentication (MFA) durante la implementación.  Anteriormente, MFA debía deshabilitarse durante la implementación.

### <a name="known-issues"></a>Problemas conocidos
#### <a name="deployment"></a>Implementación
* Puede que observe que la implementación tarda más que en versiones anteriores. 
* Get-AzureStackLogs genera registros de diagnóstico, sin embargo, no registra el progreso en la consola.
* Se debe usar la nueva [experiencia de implementación](azure-stack-run-powershell-script.md) para implementar Azure Stack o es posible que se produzca un error en la implementación.
* Las implementaciones con el parámetro *PublicVLANID* generarán un error.

#### <a name="portal"></a>Portal
* Puede ver un panel en blanco en el portal.  Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y seleccione "Restaurar configuración predeterminada".
* Los inquilinos pueden examinar toda la plataforma Marketplace sin ninguna suscripción y verán elementos administrativos como planes y ofertas.  Estos elementos no funcionan para los inquilinos.
* Al seleccionar una instancia de rol de infraestructura, verá un error que muestra un error de referencia. Use la funcionalidad de actualización del explorador para actualizar el Portal de administración.
* El botón "Mover" está deshabilitado en la hoja Grupo de recursos.  Este es el comportamiento esperado, porque el movimiento de grupos de recursos entre suscripciones no se admite actualmente.
* Recibirá notificaciones repetidas para los elementos de Marketplace sindicados que hayan completado la descarga.
* No es posible ver los permisos para la suscripción con los portales de Azure Stack.  Como solución alternativa, se pueden comprobar los permisos con Powershell.
* Debe agregar `-TenantID` como marca al exportar una implementación completada como script de automatización desde el portal.

#### <a name="services"></a>Services
* Los servicios de Key Vault deben crearse desde el portal del inquilino o la API de inquilino.  Si se registra como administrador, asegúrese de usar el portal del inquilino para crear nuevos almacenes Key Vault, secretos y claves.
* No hay ninguna experiencia de Marketplace para crear conjuntos de escalado de máquinas virtuales, aunque se pueden crear mediante una plantilla.
* No se puede asociar un equilibrador de carga con una red de back-end a través del portal.  Esta tarea se puede completar con PowerShell o con una plantilla.
* Los conjuntos de disponibilidad de la máquina virtual solo pueden configurarse con un dominio de error de uno y un dominio de actualización de uno.  
* Un inquilino debe tener una cuenta de almacenamiento antes de crear una nueva función de Azure Functions.
* La máquina virtual puede producir un error y notificar "No se puede enlazar el argumento al parámetro 'adaptador de red de máquina virtual' porque es nulo".  La reimplementación de la máquina virtual se realiza correctamente.  
* La eliminación de las suscripciones del inquilino da como resultado recursos huérfanos.  Como alternativa, elimine primero los recursos del inquilino o todo grupo de recursos y, a continuación, elimine las suscripciones del inquilino. 
* Se debe crear una regla NAT al crear un equilibrador de carga de red o se recibirá un error al intentar agregar una regla NAT después de crear el equilibrador de carga.
* Los inquilinos pueden crear máquinas virtuales mayores de lo que permite la cuota.  Este comportamiento se debe a que las cuotas no se aplican.
* Los inquilinos tienen la opción de crear una máquina virtual con almacenamiento con redundancia geográfica.  Esta configuración hace que no se puedan crear máquinas virtuales.
* Puede pasar una hora antes de que los inquilinos puedan crear bases de datos en una nueva SKU SQL o MySQL. 
* La creación de elementos directamente en servidores de hospedaje SQL y MySQL que no realice el proveedor de recursos no se admite y puede provocar un estado de error de coincidencia.
* AzureRM PowerShell 1.2.10 requiere pasos de configuración adicionales:
    * Ejecútelo después de ejecutar Add-AzureRMEnvironment para las implementaciones de Azure AD.  Proporcione los valores de nombre y GraphAudience usando la salida de `Add-AzureRMEnvironment`.
      
      ```PowerShell
      Set-AzureRmEnvironment -Name <Environment Name> -GraphAudience <Graph Endpoint URL>
      ```
    * Ejecútelo después de ejecutar Add-AzureRMEnvironment para las implementaciones de AD FS.  Proporcione los valores de nombre y GraphAudience usando la salida de `Add-AzureRMEnvironment`.
      
      ```PowerShell
      Set-AzureRmEnvironment <Environment Name> -GraphAudience <Graph Endpoint URL> -EnableAdfsAuthentication:$true
      ```
    
    Por ejemplo, se usa lo siguiente para un entorno de Azure AD:

    ```PowerShell
      Set-AzureRmEnvironment AzureStack -GraphAudience https://graph.local.azurestack.external/
    ```

#### <a name="fabric"></a>Fabric
* El proveedor de recursos de Compute muestra un estado desconocido.
* La dirección IP y el modelo de BMC no se muestran en la información esencial de un nodo de unidad de escala.  Este comportamiento es el esperado en Azure Stack Development Kit.
* No debe usarse la acción de reinicio en el rol de infraestructura de controlador de Compute (instancia AzS XRP01).
* No debe usarse la hoja de copia de seguridad de la infraestructura.

