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
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 31eeaa3df41065b65d6202f00c01ad2f706e230a
ms.contentlocale: es-es
ms.lasthandoff: 07/28/2017

---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guía de solución de problemas de Azure Disk Encryption

Esta guía está destinada a profesionales de tecnologías de la información (TI), analistas de seguridad de la información y administradores de la nube cuyas organizaciones utilizan Azure Disk Encryption y necesitan orientación para solucionar problemas relacionados con el cifrado de discos.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Solución de problemas del cifrado de discos con el SO Linux

El cifrado de discos con el sistema operativo Linux debe desmontar la unidad del sistema operativo antes de pasar por el proceso de cifrado de disco completo.   Si no es posible, es probable que el mensaje de error "no se pudo desmontar después de..." aparezca.

Suele ocurrir cuando se intenta el cifrado del disco del sistema operativo en un entorno de máquina virtual de destino que se ha modificado o cambiado desde su imagen de la galería admitida.  Estos son algunos ejemplos de desviaciones de la imagen admitida, que pueden interferir con la capacidad de la extensión de desmontar la unidad del sistema operativo:
- Imágenes personalizadas que ya no coinciden con un sistema de archivos o esquema de partición compatibles.
- Cuando se instalan aplicaciones grandes, como SAP, MongoDB o Apache Casandra, y se ejecutan en el sistema operativo antes del cifrado.  La extensión no puede cerrarlas correctamente y, si mantienen identificadores de archivos abiertos en la unidad de sistema operativo, la unidad no se puede desmontar, lo que ocasiona el error.
- Cuando se ejecutan scripts personalizados casi al mismo tiempo que se deshabilita el cifrado, o si se hace algún otro cambio en la máquina virtual durante el proceso de cifrado.   Esto puede ocurrir cuando una plantilla de Resource Manager define varias extensiones para ejecutarse al mismo tiempo o cuando una extensión de script personalizada u otra acción se ejecutan simultáneamente que el cifrado del disco.   La serialización y el aislamiento de estos pasos pueden resolver el problema.
- Cuando no se ha deshabilitado SELinux antes de habilitar el cifrado, se produce un error en el paso de desmontaje.  SELinux puede habilitarse de nuevo después de completarse el cifrado.
- Cuando el disco del sistema operativo usa un esquema de LVM (aunque hay compatibilidad limitada con los discos de datos LVM, no se admite el disco del sistema operativo LVM)
- Cuando no se cumplen los requisitos mínimos de memoria (se recomiendan 7GB para el cifrado del disco de sistema operativo)
- Cuando las unidades de datos se han montado recursivamente en el directorio /mnt/ o entre sí (por ejemplo, /mnt/datos1, /mnt/datos2, /datos3 + /datos3/datos4, etc.)
- Cuando no se cumplen otros [requisitos previos](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) de Azure Disk Encryption para Linux

## <a name="unable-to-encrypt"></a>No se puede cifrar

En algunos casos, parece que el cifrado del disco de Linux se atasca en el mensaje que indica que se inició el cifrado de disco del sistema operativo, y SSH se deshabilita. Este proceso puede tardar entre 3 y 16 horas en completarse y puede necesitar más tiempo.  La secuencia de cifrado del disco de sistema operativo Linux desmonta temporalmente la unidad de sistema operativo y realiza el cifrado bloque a bloque de todo el disco de sistema operativo, antes de volver a montarlo cifrado.   A diferencia de Azure Disk Encryption en Windows, el cifrado del disco de Linux no permite el uso simultáneo de la máquina virtual mientras el cifrado está en curso.  Las características de rendimiento de la máquina virtual, incluido el tamaño del disco y si la cuenta de almacenamiento está respaldada por el almacenamiento Standard o Premium (SSD), pueden influir en gran medida en el tiempo necesario para completar el cifrado.

Para comprobar el estado, puede sondearse el campo ProgressMessage devuelto por el comando [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus).   Mientras se cifra la unidad del sistema operativo, la máquina virtual entra en estado de mantenimiento y SSH también se deshabilita para evitar cualquier interrupción en el proceso en curso.  Durante el cifrado, la mayoría de las veces se mostrará el mensaje EncryptionInProgress, seguido, varias horas después, del mensaje VMRestartPending que pregunta si se reinicia la máquina virtual.  Por ejemplo:


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

Una vez que se le pida que reinicie la máquina virtual, tras reiniciarla y transcurridos entonces de dos a tres minutos para el reinicio y para los pasos finales en el destino, el mensaje de estado indicará que el cifrado se ha completado finalmente.   Cuando este mensaje esté disponible, es de esperar que la unidad de sistema operativo cifrada y la máquina virtual se puedan utilizar de nuevo.

Si no se ha seguido esta secuencia o si se muestran otros mensajes de progreso o información de inicio o bien indicadores de error que informan de que el cifrado del sistema operativo ha fracasado en mitad de este proceso (por ejemplo, si ve el error "no se pudo desmontar" descrito en esta guía), se recomienda restaurar la máquina virtual a la instantánea o con la copia de seguridad realizadas inmediatamente antes del cifrado.  Antes de volver a intentarlo, se recomienda volver a evaluar las características de la máquina virtual y asegurarse de que se cumplan todos los requisitos previos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Solución de problemas de Azure Disk Encryption detrás de un firewall
Cuando la conectividad está limitada por un firewall, la configuración del grupo de seguridad de red (NSG) o el requisito de proxy, puede que se pierda la capacidad de la extensión para realizar las tareas necesarias.   Esto puede dar lugar a mensajes de estado similares a "El estado de extensión no está disponible en la máquina virtual" y podría ocurrir que el proceso no se pueda concluir.  En las secciones siguientes se enumeran algunos problemas comunes del firewall que puede investigar.

### <a name="network-security-groups"></a>Grupos de seguridad de red
Parte de la configuración del grupo de seguridad de red aplicada debe permitir todavía que el punto de conexión cumpla con los [requisitos previos](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) documentados de la configuración de red para el cifrado del disco.

### <a name="azure-keyvault-behind-firewall"></a>Azure Key Vault detrás del firewall
La máquina virtual debe poder tener acceso al almacén de claves. Consulte la documentación sobre el acceso al almacén de claves detrás de un firewall mantenido por el equipo de [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall).

### <a name="linux-package-management-behind-firewall"></a>Administración de paquetes de Linux detrás del firewall
En tiempo de ejecución, Azure Disk Encryption para Linux se basa en el sistema de administración de paquetes de la distribución de destino para instalar los componentes de requisitos previos necesarios antes de habilitar el cifrado.  Si la configuración de firewall impide que la máquina virtual pueda descargar e instalar estos componentes, es previsible que produzcan errores.    Los pasos para configurar esta opción pueden variar la distribución.  En Red Hat, cuando se requiere un proxy, es vital asegurarse de que el administrador de la suscripción y yum están configurados correctamente.  Consulte [este artículo](https://access.redhat.com/solutions/189533) de soporte técnico de Red Hat sobre este tema.  

## <a name="see-also"></a>Otras referencias
En este documento, aprendió más acerca de algunos problemas comunes de Azure Disk Encryption y cómo solucionarlos. Para más información acerca de este servicio y su funcionalidad, lea:

- [Aplicación de cifrado de discos en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Cifrado de una máquina virtual de Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Cifrado en reposo de datos de Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
