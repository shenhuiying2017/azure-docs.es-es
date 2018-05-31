---
title: Habilitación de la copia de seguridad de Azure Stack desde el portal de administración | Microsoft Docs
description: Habilite el servicio Infrastructure Backup con el portal de administración para que Azure Stack se pueda restaurar si se produce un error.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: 0ef8247eba4605d3c8e5ef0992ce97bce989002e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075279"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Habilitación de la copia de seguridad de Azure Stack desde el portal de administración
Habilite el servicio Infrastructure Backup con el portal de administración para que Azure Stack pueda generar copias de seguridad. Puede utilizar estas copias de seguridad para restaurar el entorno mediante recuperación en la nube si se produce un [error catastrófico](.\azure-stack-backup-recover-data.md). La finalidad de la recuperación en la nube es garantizar que los operadores y usuarios puedan volver a iniciar sesión en el portal una vez que se complete la recuperación. Los usuarios tendrán sus suscripciones restauradas, incluidos los permisos de acceso basado en roles y los roles, los planes originales, las ofertas, así como el proceso, el almacenamiento y las cuotas de red definidos previamente.

Sin embargo, el servicio Copia de seguridad de infraestructura no realiza copias de seguridad de las máquinas virtuales de IaaS, las configuraciones de red y los recursos de almacenamiento, como las cuentas de almacenamiento, los blobs, las tablas, etc., por lo que los usuarios que inicien sesión después de la recuperación en la nube no verán ninguno de sus recursos previamente existentes. El servicio también hace una copia de seguridad de los recursos y los datos de Plataforma como servicio (PaaS). 

Los administradores y los usuarios son los responsables de realizar copias de seguridad de los recursos de IaaS y PaaS y restaurar dichos recursos por separado a partir de los procesos de copia de seguridad de la infraestructura. Para información sobre la copia de seguridad de los recursos de IaaS y PaaS, vea los siguientes vínculos:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Antes de habilitar la copia de seguridad mediante la consola, debe configurar el servicio de copia de seguridad. Puede configurar el servicio de copia de seguridad mediante PowerShell. Para más información, consulte [Habilitación de la copia de seguridad de Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Habilitación de la copia de seguridad

1. Abra el portal de administración de Azure Stack en [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Seleccione **Más servicios** > **Infrastructure backup** (Copia de seguridad de infraestructura). Elija **Configuración** en la hoja **Copia de seguridad de infraestructura**.

    ![Azure Stack: configuración del controlador de copia de seguridad](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Escriba la ruta de acceso a la **ubicación de almacenamiento de la copia de seguridad**. Utilice una cadena de convención de nomenclatura universal (UNC) para la ruta de acceso de un recurso compartido de archivos hospedado en un dispositivo independiente. Una cadena UNC especifica la ubicación de recursos como archivos compartidos o dispositivos. Para el servicio, puede usar una dirección IP. Para garantizar la disponibilidad de los datos de copia de seguridad después de un desastre, el dispositivo debe estar en una ubicación independiente.
    > [!Note]  
    > Si el entorno admite la resolución de nombres de la red de infraestructura de Azure Stack para su entorno empresarial, puede usar un nombre de dominio completo en lugar de la dirección IP.
4. Escriba el **nombre de usuario** utilizando el dominio y el nombre de usuario. Por ejemplo, `Contoso\administrator`.
5. Escriba la **Contraseña** del usuario.
5. Escriba la contraseña de nuevo para **Confirmar la contraseña**.
6. Proporcione una clave precompartida en la casilla **Clave de cifrado**. Los archivos de copia de seguridad se cifran mediante esta clave. Asegúrese de almacenar esta clave en una ubicación segura. Una vez que configure esta clave por primera vez o la rote en el futuro, no podrá verla desde esta interfaz. Para obtener más instrucciones sobre cómo generar una clave precompartida, siga los scripts de [Habilitación de la copia de seguridad para Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Seleccione **Aceptar** para guardar la configuración del controlador de copia de seguridad.

Para ejecutar una copia de seguridad, debe descargar las herramientas de Azure Stack y, a continuación, ejecutar el cmdlet de PowerShell **Start-AzSBackup** en el nodo de administración de Azure Stack. Para obtener más información, consulte [Copia de seguridad de Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a ejecutar una copia de seguridad. Consulte [Copia de seguridad de Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Aprenda a comprobar que la copia de seguridad se ha ejecutado. Consulte [Confirmación de la copia de seguridad realizada en el portal de administración](azure-stack-backup-back-up-azure-stack.md).
