---
title: "Solución de problemas de Azure Disk Encryption | Microsoft Docs"
description: "En este artículo se ofrecen sugerencias para solucionar problemas de Microsoft Azure Disk Encryption para máquinas virtuales IaaS con Windows y Linux."
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: devtiw
ms.openlocfilehash: 618e5e6d159a8f0d4610d6d652c21e121a93a5e0
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guía de solución de problemas de Azure Disk Encryption

Esta guía está destinada a profesionales de tecnologías de la información (TI), analistas de seguridad de la información y administradores de la nube cuyas organizaciones utilizan Azure Disk Encryption y necesitan orientación para solucionar problemas relacionados con el cifrado de discos.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Solución de problemas del cifrado de discos con el SO Linux

El cifrado de discos con el sistema operativo Linux debe desmontar la unidad del sistema operativo antes de pasar por el proceso de cifrado de disco completo. Si no puede desmontar la unidad, es probable que el mensaje de error "no se pudo desmontar después de..." aparezca.

Este error suele ocurrir cuando se intenta el cifrado del disco del sistema operativo en un entorno de máquina virtual de destino que se ha modificado o cambiado desde su imagen de la galería admitida. Estos son algunos ejemplos de desviaciones de la imagen admitida, que pueden interferir con la capacidad de la extensión de desmontar la unidad del sistema operativo, por ejemplo:
- Imágenes personalizadas que ya no coinciden con un sistema de archivos o esquema de particiones compatibles.
- Se instalan aplicaciones grandes, como SAP, MongoDB o Apache Casandra, y se ejecutan en el sistema operativo antes del cifrado. La extensión no puede cerrar correctamente estas aplicaciones. Si estas aplicaciones mantienen abiertos controladores de archivo en la unidad del sistema operativo, la unidad no se puede desmontar, lo que provoca un error.
- Se ejecutan scripts personalizados casi al mismo tiempo que se deshabilita el cifrado, o si se hace algún otro cambio en la máquina virtual durante el proceso de cifrado. Este conflicto puede ocurrir cuando una plantilla de Azure Resource Manager define varias extensiones para ejecutarse al mismo tiempo o cuando una extensión de script personalizada u otra acción se ejecutan simultáneamente que el cifrado del disco. La serialización y el aislamiento de estos pasos podría resolver el problema.
- Linux con seguridad mejorada (SELinux) no se ha deshabilitado antes de habilitar el cifrado, de modo que se produce un error en el paso de desmontaje. SELinux puede habilitarse de nuevo después de completarse el cifrado.
- El disco del sistema operativo utiliza un esquema de administrador de volúmenes lógicos (LVM). Aunque hay compatibilidad limitada con los discos de datos LVM, no se admite el disco del sistema operativo LVM.
- No se cumplen los requisitos mínimos de memoria (se recomiendan 7 GB para el cifrado del disco de sistema operativo).
- Las unidades de datos se han montado recursivamente en el directorio /mnt/ o entre sí (por ejemplo, /mnt/datos1, /mnt/datos2, /datos3 + /datos3/datos4).
- No se cumplen otros [requisitos previos](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) de Azure Disk Encryption para Linux.

## <a name="unable-to-encrypt"></a>No se puede cifrar

En algunos casos, parece que el cifrado del disco de Linux se atasca en el mensaje que indica que se inició el cifrado de disco del sistema operativo, y SSH se deshabilita. El proceso de cifrado puede tardar entre 3 y 16 horas en completarse en una imagen de la galería de existencias. Si se agregan discos de datos de varios terabytes, el proceso puede tardar días.

La secuencia de cifrado del disco del sistema operativo Linux desmonta la unidad del sistema operativo temporalmente. A continuación, realiza el cifrado de bloque a bloque de todo el disco del sistema operativo, antes de que vuelva a montarse en su estado cifrado. A diferencia de Azure Disk Encryption en Windows, el cifrado del disco de Linux no permite el uso simultáneo de la máquina virtual mientras el cifrado está en curso. Las características de rendimiento de la máquina virtual pueden suponer una diferencia significativa en el tiempo necesario para el cifrado completo. Estas características incluyen el tamaño del disco y si la cuenta de almacenamiento es almacenamiento premium (SSD) o estándar.

Para comprobar el estado de cifrado, sondee el campo **ProgressMessage** devuelto por el comando [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). Mientras se cifra la unidad del sistema operativo, la máquina virtual entra en estado de mantenimiento y deshabilita SSH para evitar cualquier interrupción en el proceso en curso. El mensaje **EncryptionInProgress** aparece la mayoría del tiempo mientras el cifrado está en curso. Varias horas después, se muestra un mensaje **VMRestartPending** que le pide que reinicie la máquina virtual. Por ejemplo:


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Tras reiniciar la máquina virtual cuando se le pida, debe esperar de dos a tres minutos para el reinicio y para que se realicen los pasos finales en el destino. El mensaje de estado cambia cuando el cifrado se completa finalmente. Cuando este mensaje esté disponible, es de esperar que la unidad de sistema operativo cifrada y la máquina virtual estén listas para usarse de nuevo.

En los casos siguientes, se recomienda que restaure la máquina virtual con la instantánea o la copia de seguridad realizada inmediatamente antes del cifrado:
   - Si la secuencia de reinicio descrita anteriormente no se produce.
   - Si la información de inicio, el mensaje de progreso u otro indicador informa de que se ha producido un error en el cifrado del sistema operativo en mitad de este proceso. Un ejemplo de mensaje es el error "no se pudo desmontar" que se describe en esta guía.

Antes de volver a intentarlo, vuelva a evaluar las características de la máquina virtual y asegúrese de que se cumplan todos los requisitos previos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Solución de problemas de Azure Disk Encryption detrás de un firewall
Cuando la conectividad está limitada por un firewall, la configuración del grupo de seguridad de red (NSG) o el requisito de proxy, podría perderse la capacidad de la extensión para realizar las tareas necesarias. Esto puede dar lugar a mensajes de estado similares a "El estado de extensión no está disponible en la máquina virtual". En los escenarios esperados, el cifrado no puede finalizar. En las secciones siguientes se enumeran algunos problemas comunes del firewall que podría investigar.

### <a name="network-security-groups"></a>Grupos de seguridad de red
Parte de la configuración del grupo de seguridad de red que se aplica debe permitir todavía que el punto de conexión cumpla con los [requisitos previos](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) documentados de la configuración de red para el cifrado del disco.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault detrás de un firewall
La máquina virtual debe poder tener acceso al almacén de claves. Consulte la documentación sobre el acceso al almacén de claves desde detrás de un firewall que el equipo de [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) mantiene.

### <a name="linux-package-management-behind-a-firewall"></a>Administración de paquetes de Linux detrás de un firewall

En el entorno de ejecución, Azure Disk Encryption para Linux se basa en el sistema de administración de paquetes de la distribución de destino para instalar los componentes de requisitos previos necesarios antes de habilitar el cifrado. Si la configuración de firewall impide que la máquina virtual pueda descargar e instalar estos componentes, es previsible que se produzcan errores. Los pasos para configurar este sistema de administración de paquetes pueden variar según la distribución. En Red Hat, cuando se requiere un proxy, debe asegurarse de que el administrador de la suscripción y yum están configurados correctamente. Para obtener más información, consulte [cómo solucionar problemas de administrador de suscripción y yum](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Solución de problemas de Windows Server 2016 Server Core

En Windows Server 2016 Server Core, el componente bdehdcfg no está disponible de forma predeterminada. y Azure Disk Encryption necesita dicho componente. Se utiliza para dividir el volumen del sistema del volumen del sistema operativo, lo que se realiza una única vez en toda la duración de la máquina virtual. Estos archivos binarios no son necesarios durante las operaciones de cifrado posteriores.

Para solucionar este problema, copie los cuatro archivos siguientes de una máquina virtual del centro de datos de Windows Server 2016 en la misma ubicación de Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. Escriba el comando siguiente:

   ```
   bdehdcfg.exe -target default
   ```

   3. Este comando crea una partición de sistema de 550 MB. Reinicie el sistema.

   4. Use DiskPart para comprobar los volúmenes y, a continuación, siga.  

Por ejemplo:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió más acerca de algunos problemas comunes de Azure Disk Encryption y cómo solucionarlos. Para más información acerca de este servicio y su funcionalidad, consulte los artículos siguientes:

- [Aplicación de cifrado de discos en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Cifrado de una máquina virtual de Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Cifrado de datos en reposo de Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
