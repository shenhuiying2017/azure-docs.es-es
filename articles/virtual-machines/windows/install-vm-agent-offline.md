---
title: "Instalación del agente de máquina virtual de Azure en modo sin conexión | Microsoft Docs"
description: "Aprenda a instalar al agente de máquina virtual de Azure en modo sin conexión."
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: 3770cc3338c89a9bf88e2cf9ec3faa37e2ff109b
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2018
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instalación del agente de máquina virtual de Azure en modo sin conexión 

El agente de máquina virtual de Azure (agente de VM) proporciona características útiles, como el restablecimiento de contraseña de administrador local y la inserción de scripts. En este artículo se muestra cómo instalar al agente de máquina virtual para una máquina virtual (VM) de Windows sin conexión. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Cuándo usar el agente de máquina virtual en modo sin conexión

Instale al agente de máquina virtual en modo sin conexión en los escenarios siguientes:

- La máquina virtual de Azure implementada no tiene instalado el agente de máquina virtual o el agente no funciona.
- Olvidó la contraseña de administrador de la máquina virtual o no puede acceder a ella.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Instalación del agente de máquina virtual en modo sin conexión

Use los pasos siguientes para instalar al agente de máquina virtual en modo sin conexión.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Paso 1: Asociación del disco del sistema operativo de la máquina virtual a otra máquina virtual como disco de datos

1.  Elimine la máquina virtual. Asegúrese de seleccionar la opción **Keep the disks** (Mantener los discos) al eliminar la máquina virtual.

2.  Asocie el disco del sistema operativo como un disco de datos a otra máquina virtual (lo que se conoce como máquina virtual de _solucionador de problemas_). Para más información, consulte [Cómo conectar un disco de datos administrado a una VM con Windows en Azure Portal](attach-managed-disk-portal.md).

3.  Conéctese a la máquina virtual de solucionador de problemas. Abra **Administración de equipos** > **Administración de discos**. Confirme que el disco del sistema operativo está en línea y que las letras de unidad están asignadas a las particiones de disco.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Paso 2: Modificación del disco del sistema operativo para instalar al agente de máquina virtual de Azure

1.  Realice una conexión de Escritorio remoto a la máquina virtual de solucionador de problemas.

2.  En el disco del sistema operativo que ha asociado, vaya a la carpeta \windows\system32\config. Realice una copia de seguridad de todos los archivos de esta carpeta, por si fuera necesaria una reversión.

3.  Inicie el **Editor del Registro** (regedit.exe).

4.  Seleccione la clave **HKEY_LOCAL_MACHINE**. En el menú, seleccione **Archivo** > **Cargar subárbol**:

    ![Carga del subárbol](./media/install-vm-agent-offline/load-hive.png)

5.  Vaya a la carpeta \windows\system32\config\SYSTEM en el disco del sistema operativo que ha asociado. Como nombre de la sección, escriba **BROKENSYSTEM**. El nuevo subárbol del Registro se muestra bajo la clave **HKEY_LOCAL_MACHINE**.

6.  Vaya a la carpeta \windows\system32\config\SOFTWARE en el disco del sistema operativo que ha asociado. Como nombre del software del subárbol, escriba **BROKENSOFTWARE**.

7.  Si el agente de máquina virtual no funciona, realice una copia de seguridad de la configuración actual.

    >[!NOTE]
    >Si la máquina virtual no tiene instalado el agente, vaya al paso 8. 
      
    1. Cambie el nombre de la carpeta \windowsazure por \windowsazure.old.

    2. Exporte los registros siguientes:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Use los archivos existentes en la maquina virtual de solucionador de problemas como repositorio para la instalación del agente de máquina virtual. Complete los siguientes pasos:

    1. En la máquina virtual de solucionador de problemas, exporte las siguientes subclaves en el formato del Registro (. reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

        ![Exportación de las subclaves del Registro](./media/install-vm-agent-offline/backup-reg.png)

    2. Edite los archivos de Registro. En cada archivo, cambie el valor de entrada **SYSTEM** por **BROKENSYSTEM** (como se muestra en las siguientes imágenes) y guarde el archivo.

        ![Cambio de los valores de subclave del Registro](./media/install-vm-agent-offline/change-reg.png)

    3. Importe los archivos del Registro en el repositorio haciendo doble clic en cada uno de ellos.

    4. Confirme que las siguientes tres subclaves se han importado correctamente en el subárbol **BROKENSYSTEM**:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

9.  Copie la carpeta del agente de máquina virtual de C:\windowsazure\packages al &lt;disco duro del sistema operativo que asoció&gt;:\windowsazure\packages.

    ![Copia de los archivos del agente de máquina virtual en el disco del sistema operativo](./media/install-vm-agent-offline/copy-package.png)
      
    >[!NOTE]
    >No copie la carpeta **logs**. Una vez iniciado el servicio, se generan nuevos registros.

10.  Seleccione **BROKENSYSTEM**. En el menú, seleccione **Archivo** > **Descargar subárbol**.

11.  Seleccione **BROKENSOFTWARE**. En el menú, seleccione **Archivo** > **Descargar subárbol**.

12.  Desasocie el disco del sistema operativo y, a continuación, úselo para volver a crear la máquina virtual.

13.  Acceda a la máquina virtual. Tenga en cuenta que se está ejecutando RdAgent y que se están generando los registros.

Si ha creado la máquina virtual mediante el modelo de implementación clásica, ha terminado.


### <a name="use-the-provisionguestagent-property-for-vms-created-with-azure-resource-manager"></a>Uso de la propiedad ProvisionGuestAgent para máquinas virtuales creadas con Azure Resource Manager

Si ha creado la máquina virtual mediante el modelo de implementación de Resource Manager, use el módulo de Azure PowerShell para actualizar la propiedad **ProvisionGuestAgent**. La propiedad informa a Azure de que la máquina virtual tiene instalado el agente de máquina virtual.

Para establecer la propiedad **ProvisionGuestAgent**, ejecute los siguientes comandos en Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

A continuación, ejecute el comando `Get-AzureVM` . Tenga en cuenta que la propiedad **GuestAgentStatus** está ahora rellena con datos:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>pasos siguientes

- [Información general del agente de máquina virtual de Azure](agent-user-guide.md)
- [Características y extensiones de las máquinas virtuales para Windows](extensions-features.md)
