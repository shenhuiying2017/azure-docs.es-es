---
title: "Requisitos previos de implementación de Azure Stack Development Kit | Microsoft Docs"
description: Consulte el entorno y los requisitos de hardware de Azure Stack Development Kit (operador en la nube).
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/11/2017
ms.author: erikje
ms.openlocfilehash: 73e7efb7d789fe12846d68066c0927bb123831a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-deployment-prerequisites"></a>Requisitos previos de implementación de Azure Stack

*Se aplica a: Azure Stack Development Kit*

Antes de implementar [Azure Stack Development Kit](azure-stack-poc.md), asegúrese de que el equipo cumple los requisitos siguientes:


## <a name="hardware"></a>Hardware
| Componente | Mínima | Recomendado |
| --- | --- | --- |
| Unidades de disco: sistema operativo |Un disco del sistema operativo con un mínimo de 200 GB disponibles para la partición del sistema (SSD o HDD) |Un disco del sistema operativo con un mínimo de 200 GB disponibles para la partición del sistema (SSD o HDD) |
| Unidades de disco: datos generales del kit de desarrollo* |Cuatro discos. Cada disco proporciona un mínimo de 140 GB de capacidad (SSD o HDD). Se utilizarán todos los discos disponibles. |Cuatro discos. Cada disco proporciona un mínimo de 250 GB de capacidad (SSD o HDD). Se utilizarán todos los discos disponibles. |
| Proceso: CPU |Socket dual: 12 núcleos físicos (total) |Socket dual: 16 núcleos físicos (total) |
| Proceso: memoria |96 GB de RAM |128 GB de RAM (Este es el mínimo para admitir proveedores de recursos de PaaS.)|
| Proceso: BIOS |Hyper-V habilitado (con compatibilidad para SLAT) |Hyper-V habilitado (con compatibilidad para SLAT) |
| Red: NIC |Certificación de Windows Server 2012 R2 necesaria para NIC; no se necesitan características especializadas |Certificación de Windows Server 2012 R2 necesaria para NIC; no se necesitan características especializadas |
| Certificación del logotipo de hardware |[Certificado para Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificado para Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\*Necesitará más de esta capacidad recomendada si tiene previsto agregar muchos de los [elementos de Marketplace](azure-stack-download-azure-marketplace-item.md) desde Azure.

**Configuración de unidad de disco de datos:** todas las unidades de datos deben ser del mismo tipo (todas SAS o todas SATA) y capacidad. Si se usan unidades de disco SAS, las unidades de disco debe estar conectadas a través de una ruta de acceso única (no se proporciona compatibilidad con MPIO ni con rutas de acceso múltiples).

**Opciones de configuración HBA**

* (Opción preferida) HBA simple
* HBA RAID: el adaptador debe configurarse en modo "pass through"
* HBA de RAID: los discos deben configurarse como único disco, RAID-0

**Combinaciones de tipos de medios y bus admitidas**

* SATA HDD
* SAS HDD
* RAID HDD
* SSD RAID (si no se especifica el tipo de medios o es desconocido\*)
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD

\* Las controladoras RAID sin la funcionalidad pass-through no reconocen el tipo de medio. Estos controladores marcarán tanto HDD como SSD como No especificado. En ese caso, se usará una unidad SSD como almacenamiento persistente en lugar de dispositivos de almacenamiento en caché. Por lo tanto, puede implementar el kit de desarrollo en esas SSD.

**HBA de ejemplo**: LSI 9207 8i, LSI-9300-8i o LSI-9265-8i en modo Paso a través

Están disponibles configuraciones de OEM de ejemplo.

## <a name="operating-system"></a>Sistema operativos
|  | **Requisitos** |
| --- | --- |
| **Versión del SO.** |Windows Server 2012 R2 o posterior. La versión del sistema operativo no es crítica antes de iniciar la implementación, ya que podrá arrancar el equipo host en el disco duro virtual que se incluye en la instalación de Azure Stack. El sistema operativo y todas las revisiones necesarias ya están integradas en la imagen. No use ninguna clave para activar las instancias de Windows Server utilizadas en el kit de desarrollo. |

## <a name="deployment-requirements-check-tool"></a>Herramienta de comprobación de requisitos de implementación
Después de instalar el sistema operativo, puede usar el [Comprobador de implementación para Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar que el hardware cumple todos los requisitos.

## <a name="account-requirements"></a>Requisitos de cuenta
Normalmente, se implementa el kit de desarrollo con conectividad a Internet, donde puede conectarse a Microsoft Azure. En este caso, debe configurar una cuenta de Azure Active Directory (Azure AD) para implementar el kit de desarrollo.

Si el entorno no está conectado a Internet o no desea usar Azure AD, puede implementar Azure Stack mediante el uso de los Servicios de federación de Active Directory (AD FS). El kit de desarrollo incluye sus propias instancias de AD FS y Active Directory Domain Services. Si implementa mediante el uso de esta opción, no tiene que configurar cuentas de antemano.

>[!NOTE]
Si implementa mediante la opción de AD FS, debe volver a implementar Azure Stack para cambiar a Azure AD.

### <a name="azure-active-directory-accounts"></a>Cuentas de Azure Active Directory
Para implementar Azure Stack mediante una cuenta de Azure AD, debe preparar una cuenta de Azure AD antes de ejecutar el script de PowerShell de implementación. Esta cuenta se convierte en el administrador global para el inquilino de Azure AD. Se utiliza para aprovisionar y delegar aplicaciones y entidades de servicio para todos los servicios de Azure Stack que interactúan con Azure Active Directory y la API Graph. También se utiliza como el propietario de la suscripción de proveedor predeterminada (que puede cambiar más adelante). Puede iniciar sesión en el portal del administrador del sistema de Azure Stack mediante el uso de esta cuenta.

1. Cree una cuenta de Azure AD que sea el administrador de directorios de al menos una instancia de Azure AD. Si ya tiene una, puede usarla. En caso contrario, puede crear una de forma gratuita en [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (en China, visite <http://go.microsoft.com/fwlink/?LinkID=717821> en su lugar). Si tiene previsto más adelante [registrar Azure Stack en Azure](azure-stack-register.md), también debe tener una suscripción en la cuenta recién creada.
   
    Guarde estas credenciales para su uso en el paso 6 de [Implementación del kit de desarrollo](azure-stack-run-powershell-script.md#deploy-the-development-kit). Los *administradores de servicios* pueden configurar y administrar recursos en la nube, cuentas de usuario, planes de inquilinos, cuotas y precios. En el portal, pueden crear nubes de sitios web, nubes privadas de máquinas virtuales, crear planes y administrar suscripciones de usuario.
2. [Cree](azure-stack-add-new-user-aad.md) al menos una cuenta para que pueda iniciar sesión en el kit de desarrollo como un inquilino.
   
   | **Cuenta de Azure Active Directory** | **¿Admitida?** |
   | --- | --- |
   | Cuenta profesional o educativa con una suscripción de Azure pública válida |Sí |
   | Cuenta de Microsoft con una suscripción de Azure pública válida |Sí |
   | Cuenta profesional o educativa con una suscripción de Azure China válida |Sí |
   | Cuenta profesional o educativa con una suscripción de Azure Gobierno de Estados Unidos válida |Sí |

## <a name="network"></a>Red
### <a name="switch"></a>Switch
Un puerto disponible en un conmutador para la máquina del kit de desarrollo.  

La máquina del kit de desarrollo admite la conexión a un puerto de acceso de conmutador o puerto de enlace. No se requieren características especializadas en el conmutador. Si está usando un puerto de enlace o necesita configurar un identificador de VLAN, tendrá que proporcionar el identificador de VLAN como un parámetro de implementación. Puede ver ejemplos en la [lista de parámetros de implementación](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Subred
No conecte el equipo del kit de desarrollo a las subredes siguientes:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Estas subredes están reservadas para las redes internas dentro del entorno del kit de desarrollo.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Se admite solo IPv4. No se pueden crear redes IPv6.

### <a name="dhcp"></a>DHCP
Asegúrese de que hay un servidor DHCP disponible en la red al que se conecta la tarjeta NIC. Si DHCP no está disponible, debe preparar una red IPv4 estática adicional además de la usada por el host. Debe proporcionar esa dirección IP y la puerta de enlace como parámetro de implementación. Puede ver ejemplos en la [lista de parámetros de implementación](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Acceso a Internet
Azure Stack necesita acceso a Internet, ya sea directamente o a través de un proxy transparente. Azure Stack no admite la configuración de un proxy web para habilitar el acceso a Internet. Tanto la dirección IP del host como la nueva dirección IP asignada a MAS-BGPNAT01 (por DHCP o dirección IP estática) deben poder acceder a Internet. Se usan los puertos 80 y 443 en los dominios graph.windows.net y login.microsoftonline.com.

## <a name="telemetry"></a>Telemetría

La telemetría nos ayuda a dar forma a versiones futuras de Azure Stack. Nos permite responder rápidamente a los comentarios, proporcionar características nuevas y mejorar la calidad. Microsoft Azure Stack incluye Windows Server 2016 y SQL Server 2014. Ninguno de estos productos tienen cambios en la configuración predeterminada y ambos se describen en la declaración de privacidad de Microsoft Enterprise. Azure Stack también contiene software de código abierto que no se ha modificado para enviar datos de telemetría a Microsoft. Estos son algunos ejemplos de datos de telemetría de Azure Stack:

- información de registro de implementación
- cuándo se abre y se cierra una alerta
- el número de recursos de red

Para admitir el flujo de datos de telemetría, el puerto 443 (HTTPS) debe estar abierto en la red. El punto de conexión de cliente es https://vortex-win.data.microsoft.com.

Si no desea proporcionar datos de telemetría para Azure Stack, se puede desactivar en el host del kit de desarrollo y en las máquinas virtuales de infraestructura como se explica a continuación.

### <a name="turn-off-telemetry-on-the-development-kit-host-optional"></a>Desactivación de la telemetría en el host del kit de desarrollo (opcional)

>[!NOTE]
Si desea desactivar la telemetría para el host del kit de desarrollo, debe hacerlo antes de ejecutar el script de implementación.

Antes de [ejecutar el script asdk-installer.ps1]() para implementar el host del kit de desarrollo, inicie CloudBuilder.vhdx y ejecute el siguiente script en una ventana de PowerShell con privilegios elevados:
```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host 
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0'  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Establezca **AllowTelemetry** en 0 para desactivar la telemetría en la implementación de Windows y Azure Stack. Solo se envían eventos de seguridad críticos del sistema operativo. El valor controla la telemetría de Windows en todos los hosts y máquinas virtuales de infraestructura y se vuelve a aplicar a los nuevos nodos y las máquinas virtuales cuando se producen operaciones de escalado horizontal.


### <a name="turn-off-telemetry-on-the-infrastructure-virtual-machines-optional"></a>Desactivación de la telemetría en las máquinas virtuales de infraestructura (opcional)

Después de la correcta implementación, ejecute el siguiente script en una ventana de PowerShell con privilegios elevados (como el usuario AzureStack\AzureStackAdmin) en el host del kit de desarrollo:

```powershell
$AzSVMs= get-vm |  where {$_.Name -like "AzS-*"}
### Show current AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
### Set & Get updated AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {Set-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name "AllowTelemetry" -Value '0'}
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
```

Para configurar la telemetría de SQL Server, consulte [Cómo configurar SQL Server 2016](https://support.microsoft.com/en-us/help/3153756/how-to-configure-sql-server-2016-to-send-feedback-to-microsoft).

### <a name="usage-reporting"></a>Informes de uso

A través del registro, Azure Stack también está configurado para reenviar información de uso a Azure. Los informes de uso se controlan de forma independiente de la telemetría. Puede desactivar los informes de uso en el momento del [registro](azure-stack-register.md) mediante el uso del script de Github. Basta con establecer el parámetro **$reportUsage** en **$false**.

El formato de los datos de uso se detalla en [Informe de datos de uso de Azure Stack a Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-usage-reporting). No se cobra a los usuarios de Azure Stack Development Kit. Esta funcionalidad se incluye en el kit de desarrollo para que pueda probar el funcionamiento de los informes de uso. 


## <a name="next-steps"></a>Pasos siguientes
[Descarga del paquete de implementación de Azure Stack Development Kit](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Implementación de Azure Stack Development Kit](azure-stack-run-powershell-script.md)

