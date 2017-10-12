---
title: "Solución de problemas y errores de la replicación de Azure a Azure en Azure Site Recovery| Microsoft Docs"
description: "Solución de problemas y errores al replicar máquinas virtuales de Azure para la recuperación ante desastres"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/10/2017
ms.author: sujayt
ms.openlocfilehash: 4e4e8b097fbab3ddce551eb93945d0880f8c457f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Solución de problemas de replicación de máquinas virtuales de Azure a Azure

En este artículo se describen los problemas comunes de Azure Site Recovery al replicar y recuperar máquinas virtuales de Azure de una región a otra y se explica cómo solucionarlos. Para más información sobre las configuraciones admitidas, consulte la [matriz de compatibilidad para la replicación de máquinas virtuales de Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de cuota de recursos de Azure (código de error 150097)
La suscripción debe estar habilitada para crear máquinas virtuales de Azure en la región de destino que se va a utilizar como región de recuperación ante desastres. Además, la suscripción debe tener habilitada suficiente cuota para crear máquinas virtuales de un tamaño específico. De forma predeterminada, Site Recovery adopta el mismo tamaño en la máquina virtual de destino que en la de origen. Si el tamaño coincidente no está disponible, se selecciona automáticamente el tamaño más aproximado posible. Si no hay ningún tamaño coincidente que admita la configuración de la máquina virtual de origen, aparece este mensaje de error:

**Código de error** | **Causas posibles:** | **Recomendación**
--- | --- | ---
150097<br></br>**Mensaje**: No se pudo habilitar la replicación para la máquina virtual VmName. | -El identificador de suscripción no puede habilitarse para crear ninguna máquina virtual en la ubicación de la región de destino.</br></br>-El identificador de suscripción no puede habilitarse o no tiene cuota suficiente para crear tamaños de máquina virtual específicos en la ubicación de la región de destino.</br></br>-No se encuentra un tamaño adecuado de máquina virtual de destino que coincida con el número de tarjetas NIC de la máquina virtual de origen (2) para el id. de suscripción en la ubicación de la región de destino.| Para habilitar la creación de máquinas virtuales con el tamaño adecuado en la ubicación de destino de la suscripción, póngase en contacto con el [soporte de facturación de Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Una vez habilitada, vuelva a intentar la operación con error.

### <a name="fix-the-problem"></a>Corrección del problema
Para permitir que su suscripción cree máquinas virtuales de los tamaños necesarios en la ubicación de destino, puede ponerse en contacto con el [soporte de facturación de Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Si la ubicación de destino tiene restricciones de capacidad, deshabilite la replicación y habilítela en una ubicación diferente donde su suscripción tenga cuota suficiente para crear máquinas virtuales con los tamaños necesarios.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificados raíz de confianza (código de error 151066)

Si los certificados raíz de confianza más recientes no están presentes en la máquina virtual, el trabajo de habilitar la replicación podría dar error. Sin los certificados, la autenticación y la autorización de las llamadas al servicio de Site Recovery desde la máquina virtual darán error. Aparece el mensaje de error al habilitar la replicación para el trabajo de Site Recovery:

**Código de error** | **Causa posible** | **Recomendaciones**
--- | --- | ---
151066<br></br>**Mensaje**: error de configuración de Site Recovery. | Los certificados raíz de confianza necesarios usados para la autorización y la autenticación no están presentes en la máquina. | -En máquinas virtuales que ejecutan el sistema operativo Windows, asegúrese de que los certificados raíz de confianza estén presentes en la máquina. Para más información, consulte [Configurar raíces de confianza y certificados no permitidos](https://technet.microsoft.com/library/dn265983.aspx).<br></br>-En máquinas virtuales que ejecutan el sistema operativo Linux, siga las instrucciones relativas a los certificados raíz de confianza publicados por el distribuidor de versiones de dicho sistema operativo.

### <a name="fix-the-problem"></a>Corrección del problema
**Windows**

Instale las actualizaciones de Windows más recientes en la máquina virtual para que todos los certificados raíz de confianza están presentes en la máquina. Si se encuentra en un entorno desconectado, siga el proceso de actualización estándar de Windows en su organización para obtener los certificados. Si los certificados necesarios no están presentes en la máquina virtual, las llamadas al servicio de Site Recovery darán error por razones de seguridad.

Para obtener los certificados raíz más recientes y la lista actualizada de revocación de certificados en las máquinas virtuales, siga el proceso habitual de administración de actualizaciones de certificados o de administración de actualizaciones de Windows.

Para comprobar que el problema se haya resuelto, vaya a login.microsoftonline.com en su máquina virtual mediante un explorador.

**Linux**

Para obtener los certificados raíz de confianza y la lista de revocación de certificados más recientes en la máquina virtual, siga las instrucciones proporcionadas por su distribuidor de Linux.

Como SuSE Linux usa vínculos simbólicos para mantener una lista de certificados, siga estos pasos:

1.  Inicie sesión con un usuario raíz.

2.  Ejecute este comando:

      ``# cd /etc/ssl/certs``

3.  Para ver si el certificado de CA raíz de Symantec está presente o no, ejecute este comando:

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4.  Si no se encuentra el archivo, ejecute estos comandos:

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

      ``# c_rehash``

5.  Para crear un vínculo simbólico con b204d74a.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem, ejecute este comando:

      ``# ln -s  VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

6.  Compruebe si este comando tiene la siguiente salida. En caso contrario, deberá crear un vínculo simbólico:

      ``# ls -l | grep Baltimore
      -rw-r--r-- 1 root root   1303 Apr  7  2016 Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 04:47 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 05:01 653b494a.0 -> Baltimore_CyberTrust_Root.pem``

7. Si el vínculo simbólico 653b494a.0 no está presente, use este comando para crearlo:

      ``# ln -s Baltimore_CyberTrust_Root.pem 653b494a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividad saliente para direcciones URL o intervalos IP de Site Recovery (código de error 151037 o 151072)

Para que la replicación de Site Recovery funcione, la máquina virtual debe disponer de conectividad saliente a direcciones URL o intervalos IP específicos. Si la máquina virtual está detrás de un firewall o usa reglas de grupo de seguridad de red (NSG) para controlar la conectividad saliente, puede aparecer uno de estos mensajes de error:

**Códigos de error** | **Causas posibles:** | **Recomendaciones**
--- | --- | ---
151037<br></br>**Mensaje**: No se pudo registrar la máquina virtual de Azure en Site Recovery. | -Usa NSG para controlar el acceso de salida en la máquina virtual y los intervalos IP necesarios no están incluidos en la lista de permitidos para dicho acceso.</br></br>-Usa herramientas de firewall de terceros y los intervalos IP o las direcciones URL no están incluidos en lista de permitidos.</br>| - Si usa un proxy de firewall para controlar la conectividad de red de salida en la máquina virtual, asegúrese de que las direcciones URL o los intervalos IP de centro de datos, que son requisito previo, estén incluidos en la lista de permitidos. Para más información, consulte las [instrucciones sobre el proxy de firewall](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>-- Si usa reglas de NSG para controlar la conectividad de red de salida en la máquina virtual, asegúrese de que los intervalos IP de centro de datos, que son requisito previo, estén incluidos en la lista de permitidos. Para más información, consulte la [instrucciones para los grupos de seguridad de red](https://aka.ms/a2a-nsg-guidance).
151072<br></br>**Mensaje**: error de configuración de Site Recovery. | No se puede establecer la conexión a los puntos de conexión del servicio de Site Recovery. | - Si usa un proxy de firewall para controlar la conectividad de red de salida en la máquina virtual, asegúrese de que las direcciones URL o los intervalos IP de centro de datos, que son requisito previo, estén incluidos en la lista de permitidos. Para más información, consulte las [instrucciones sobre el proxy de firewall](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>-- Si usa reglas de NSG para controlar la conectividad de red de salida en la máquina virtual, asegúrese de que los intervalos IP de centro de datos, que son requisito previo, estén incluidos en la lista de permitidos. Para más información, consulte la [instrucciones para los grupos de seguridad de red](https://aka.ms/a2a-nsg-guidance).

### <a name="fix-the-problem"></a>Corrección del problema
Para incluir en la lista de permitidos [las direcciones URL necesarias](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-urls) o los [intervalos IP necesarios](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-ip-ranges), siga los pasos del [documento de instrucciones para redes](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disco no encontrado en la máquina (código de error 150039)

Se debe inicializar un nuevo disco asociado a la máquina virtual.

**Código de error** | **Causas posibles:** | **Recomendaciones**
--- | --- | ---
150039<br></br>**Mensaje**: El disco de datos (DiskName) (DiskUri) con el número de unidad lógica (LUN) (LUNValue) no se ha asignado a un disco correspondiente que se notifica desde la máquina virtual como que tiene el mismo valor de LUN. | -Un nuevo disco de datos se asoció a la máquina virtual, pero no se ha inicializado.</br></br>-El disco de datos dentro de la máquina virtual no está notificando correctamente el valor LUN con el que el disco se asoció a la máquina virtual.| Asegúrese de que se inicializan los discos de datos y, a continuación, vuelva a intentar la operación.</br></br>En Windows: [Adjunte e inicialice un disco nuevo](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-portal#option-1-attach-and-initialize-a-new-disk).</br></br>En Linux: [Inicialice un nuevo disco de datos en Linux](https://docs.microsoft.com/azure/virtual-machines/linux/classic/attach-disk#initialize-a-new-data-disk-in-linux).

### <a name="fix-the-problem"></a>Corrección del problema
Asegúrese de que se hayan inicializado los discos de datos y, a continuación, vuelva a intentar la operación:

- En Windows: [Adjunte e inicialice un disco nuevo](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-portal#option-1-attach-and-initialize-a-new-disk).
- En Linux: [Inicialice un nuevo disco de datos en Linux](https://docs.microsoft.com/azure/virtual-machines/linux/classic/attach-disk#initialize-a-new-data-disk-in-linux).

Si el problema persiste, póngase en contacto con el soporte técnico.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>No se puede ver la máquina virtual de Azure para seleccionarla en "Habilitar la replicación"

Puede que no vea su máquina virtual de Azure para seleccionarla en [Habilitar la replicación: paso 2](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines). Este problema podría ser debido a que se ha dejado una configuración de Site Recovery obsoleta en la máquina virtual de Azure. Esta situación podría darse en los siguientes casos:

- Habilitó la replicación para la máquina virtual de Azure mediante Site Recovery y luego eliminó el almacén de Site Recovery sin deshabilitar explícitamente la replicación en la máquina virtual.
- Habilitó la replicación para la máquina virtual de Azure mediante Site Recovery y luego eliminó el grupo de recursos que contenía el almacén de Site Recovery sin deshabilitar explícitamente la replicación en la máquina virtual.

### <a name="fix-the-problem"></a>Corrección del problema

Puede usar el artículo sobre cómo [quitar el script de configuración de ASR obsoleto](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) y quitar la configuración de Site Recovery obsoleta en la máquina virtual de Azure. Después de quitar la configuración obsoleta, verá la máquina virtual en [Habilitar la replicación: paso 2](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines).


## <a name="next-steps"></a>Pasos siguientes
[Replicación de máquinas virtuales de Azure](site-recovery-replicate-azure-to-azure.md)
