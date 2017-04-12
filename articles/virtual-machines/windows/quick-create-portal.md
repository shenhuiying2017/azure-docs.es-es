---
title: "Inicio rápido de Azure: creación de máquinas virtuales Windows con el Portal | Microsoft Docs"
description: "Inicio rápido de Azure: creación de máquinas virtuales Windows con el Portal"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 17fb538b33a4a4a2b333ff501e6e729f6000f623
ms.lasthandoff: 04/06/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Cree una máquina virtual Windows en Azure Portal.

Las máquinas virtuales de Azure pueden crearse a través de Azure Portal. Este método proporciona una interfaz de usuario basada en el explorador para crear y configurar máquinas virtuales y todos los recursos asociados. Este inicio rápido le ayuda a crear una máquina virtual mediante Azure Portal. Una vez completada la implementación, nos conectamos al servidor e instalamos IIS.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-virtual-machine"></a>Create virtual machine

2. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

3. Seleccione **Proceso** en la hoja **Nuevo**, seleccione **Windows Server 2016 Datacenter** en la hoja **Proceso** y, luego, haga clic en el botón **Crear**.

4. Rellene el formulario **Datos básicos** de la máquina virtual. El nombre de usuario y la contraseña que especifique aquí se usarán para iniciar sesión en la máquina virtual. En **Grupo de recursos**, cree uno. Un grupo de recursos es un contenedor lógico en el que se administran y crean los recursos de Azure. Cuando haya terminado, haga clic en **Aceptar**.

    ![Especificación de la información básica de la máquina virtual en la hoja del Portal](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

5. Elija un tamaño de máquina virtual y haga clic en **Seleccionar**.

6. En la hoja Configuración, seleccione **Sí** en **Usar discos administrados**, conserve los valores predeterminados en el resto de la configuración y haga clic en **Aceptar**.

7. En la página Resumen, haga clic en **Aceptar** para iniciar la implementación de máquina virtual.

8. Para supervisar el estado de implementación, haga clic en la máquina virtual. Encontrará la máquina virtual en el panel de Azure Portal, o bien seleccionando **Máquinas virtuales** en el menú izquierdo. Cuando se ha creado la máquina virtual, el estado cambia de **Deploying** (Implementación) a **Running** (En ejecución).

## <a name="open-port-80-for-web-traffic"></a>Apertura del puerto 80 para el tráfico web 

Para permitir el tráfico para IIS, debe abrir el puerto 80 al tráfico web. En este paso se explica cómo crear una regla de grupo de seguridad de red (NSG) para permitir conexiones entrantes en el puerto 80.

1. En la hoja de la máquina virtual, en la sección **Essentials**, haga clic en el nombre del **Grupo de recursos**.
2. En la hoja del grupo de recursos, en la lista de recursos, haga clic en el **Grupo de seguridad de red**. El nombre del NSG debe ser el nombre de la máquina virtual con - nsg anexado al final.
3. Haga clic en el encabezado **Reglas de seguridad de entrada** para abrir la lista de reglas de entrada. Debería ver una regla para RDP ya en la lista.
4. Haga clic en **+Agregar** para abrir la hoja **Agregar regla de seguridad de entrada**.
5. En **Nombre**, escriba **IIS** y asegúrese de que el valor de **Intervalo de puertos** es 80 y el valor de **Acción** es **Permitir**; a continuación, haga clic en **Aceptar**.


## <a name="connect-to-virtual-machine"></a>Conexión a la máquina virtual

Una vez finalizada la implementación, cree una conexión de Escritorio remoto con la máquina virtual.

1. Haga clic en el botón **Conectar** está disponible en la hoja de la máquina virtual. Se crea y se descarga un archivo de Protocolo de Escritorio remoto (archivo .rdp).

    ![Portal 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le solicite, haga clic en **Conectar**. En un equipo Mac, necesita un cliente RDP como este [Cliente de Escritorio remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) de Mac App Store.

3. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual y, a continuación, haga clic en **Aceptar**.

4. Recibirá una advertencia de certificado durante el proceso de inicio de sesión. Haga clic en **Sí** o **Conectar** para continuar con la conexión.


## <a name="install-iis-using-powershell"></a>Instalación de IIS mediante PowerShell

Ahora que ha iniciado sesión en la máquina virtual de Azure, puede usar una sola línea de PowerShell para instalar IIS y habilitar la regla de firewall local para permitir el tráfico web.  Abra una ventana de PowerShell y ejecute el siguiente comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Página principal de IIS

Con IIS instalado y el puerto 80 abierto en la máquina virtual desde Internet, puede usar el explorador web que elija para ver la página principal de IIS. Obtenga la **Dirección IP pública** en la hoja de la máquina virtual y úsela para visitar la página web predeterminada. 

![Sitio predeterminado de IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Eliminación de máquinas virtuales

Cuando ya no los necesite, se puede usar el comando siguiente para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="delete-virtual-machine"></a>Eliminación de máquinas virtuales

Cuando ya no los necesite, elimine el grupo de recursos, la máquina virtual y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la hoja de la máquina virtual y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

[Instalación de un rol y configuración del tutorial de firewall](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Ejemplos de la CLI de implementación de máquinas virtuales](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

