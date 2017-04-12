---
title: "Instalación de IIS en la primera máquina virtual Windows | Microsoft Docs"
description: "Experimente con la primera máquina virtual de Windows al instalar IIS y abrir el puerto 80 mediante Azure Portal."
keywords: 
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6334ea45-db6b-4e08-abb5-1f98927ebc34
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b11ce1eab0c26a802c31bc418cdf725cbc4fba30
ms.lasthandoff: 03/31/2017


---
# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>Experimentación con la instalación de un rol en la máquina virtual Windows
Con la primera máquina virtual activa y en funcionamiento, puede pasar a instalar el software y los servicios. Para este tutorial, vamos a utilizar el administrador del servidor en la máquina virtual de Windows Server para instalar IIS. A continuación, se crea un grupo de seguridad de red (NSG) mediante Azure Portal para abrir el puerto 80 al tráfico IIS. 

Si aún no ha creado su primera máquina virtual, debe volver a [Creación de la primera máquina virtual Windows en Azure Portal](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de continuar con este tutorial.

## <a name="make-sure-the-vm-is-running"></a>Asegúrese de que está ejecutando la máquina virtual
1. Abra el [Portal de Azure](https://portal.azure.com).
2. En el menú central, haga clic en **Máquinas virtuales**. Seleccione la máquina virtual en la lista.
3. Si el estado es **Detenido (desasignado)**, haga clic en el botón **Iniciar** de la hoja **Essentials** (Información básica) de la máquina virtual. Si el estado es **En ejecución**, puede continuar al paso siguiente.

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>Conexión a la máquina virtual e inicio de sesión
1. En el menú central, haga clic en **Máquinas virtuales**. Seleccione la máquina virtual en la lista.
2. En la hoja de la máquina virtual, haga clic en **Conectar**. Así se crea y se descarga un archivo de protocolo de escritorio remoto (archivo.rdp), que es como un acceso directo de conexión a la máquina. Puede guardar el archivo en el escritorio para facilitar el acceso. **Abra** este archivo para conectarse a la máquina virtual.
   
    ![Captura de pantalla del portal de Azure que muestra cómo conectarse a la máquina virtual](./media/hero-role/connect.png)
3. Aparece una advertencia que indica que el archivo .rdp procede de un editor desconocido. Esto es normal. En la ventana de Escritorio remoto, haga clic en **Conectar** para continuar.
   
    ![Captura de pantalla de una advertencia sobre un editor desconocido](./media/hero-role/rdp-warn.png)
4. En la ventana de seguridad de Windows, escriba el nombre de usuario y la contraseña de la cuenta local que generó al crear la máquina virtual. El nombre de usuario se escribe como *nombreDeMáquinaVirtual*&#92;*nombreDeUsuario*; después, haga clic en **Aceptar**.
   
    ![Captura de pantalla de la escritura del nombre de la máquina virtual, el nombre de usuario y la contraseña](./media/hero-role/credentials.png)
5. Aparece una advertencia que indica que no se puede comprobar el certificado. Esto es normal. Haga clic en **Sí** para comprobar la identidad de la máquina virtual y finalizar el inicio de sesión.
   
   ![Captura de pantalla que muestra un mensaje sobre la comprobación de la identidad de la máquina virtual](./media/hero-role/cert-warning.png)

Si surgen problemas al intentar conectarse, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="install-iis-on-your-vm"></a>Instalación de IIS en la máquina virtual
Ahora que ha iniciado sesión en la máquina virtual, se instalará un rol de servidor para que pueda realizar más experimentos.

1. Abra el **Administrador del servidor** si aún no está abierto. Haga clic en el menú **Inicio** y en **Administrador del servidor**.
2. En **Administrador del servidor**, seleccione **Servidor local** en el panel izquierdo. 
3. En el menú, seleccione **Administrar** > **Agregar roles y características**.
4. En el Asistente para agregar roles y características, en la página **Tipo de instalación**, elija **Instalación basada en características o en roles** y haga clic en **Siguiente**.
   
    ![Captura de pantalla que muestra la pestaña del Asistente para agregar roles y características para Tipo de instalación](./media/hero-role/role-wizard.png)
5. Seleccione la máquina virtual del grupo de servidores y haga clic en **Siguiente**.
6. En la página **Roles de servidor**, seleccione **Servidor web (IIS)**.
   
    ![Captura de pantalla que muestra la pestaña del Asistente para agregar roles y características para Roles de servidor](./media/hero-role/add-iis.png)
7. En el menú emergente acerca de cómo agregar características necesarias para IIS, asegúrese de que la opción **Incluir herramientas de administración** está seleccionada y, a continuación, haga clic en **Agregar características**. Cuando se cierre el elemento emergente, haga clic en **Siguiente** en el asistente.
   
    ![Captura de pantalla que muestra un elemento emergente para confirmar la adición del rol de IIS](./media/hero-role/confirm-add-feature.png)
8. En la página de características, haga clic en **Siguiente**.
9. En la página **Rol de servidor web (IIS)**, haga clic en **Siguiente**. 
10. En la página **Servicios de rol**, haga clic en **Siguiente**. 
11. En la página **Confirmación**, haga clic en **Instalar**. 
12. Una vez completada la instalación, haga clic en **Cerrar** en el asistente.

## <a name="open-port-80"></a>Apertura del puerto 80
Para que la máquina virtual acepte tráfico entrante en el puerto 80, debe agregar una regla de entrada al grupo de seguridad de red. 

1. Abra el [Portal de Azure](https://portal.azure.com).
2. En **Máquinas virtuales** , seleccione la máquina virtual que creó.
3. En la configuración de las máquinas virtuales, seleccione **Interfaces de red** y la interfaz de red existente.
   
    ![Captura de pantalla que muestra la configuración de máquina virtual para las interfaces de red](./media/hero-role/network-interface.png)
4. En **Essentials** (Información básica) de la interfaz de red, haga clic en el **grupo de seguridad de red**.
   
    ![Captura de pantalla que muestra la sección Essentials para la interfaz de red](./media/hero-role/select-nsg.png)
5. En la hoja **Essentials** (Información básica) del NSG, debe tener una regla de entrada predeterminada existente para **default-allow-rdp** que le permita iniciar sesión en la máquina virtual. Ahora, va a agregar otra regla de entrada para permitir el tráfico IIS. Haga clic en **Regla de seguridad de entrada**.
   
    ![Captura de pantalla que muestra la sección Essentials para el NSG](./media/hero-role/inbound.png)
6. En **Reglas de seguridad de entrada**, haga clic en **Agregar**.
   
    ![Captura de pantalla que muestra el botón para agregar una regla de seguridad](./media/hero-role/add-rule.png)
7. En **Reglas de seguridad de entrada**, haga clic en **Agregar**. Escriba **80** en el intervalo de puertos y asegúrese de que la opción **Permitir** está seleccionada. Cuando haya terminado, haga clic en **Aceptar**.
   
    ![Captura de pantalla que muestra el botón para agregar una regla de seguridad](./media/hero-role/port-80.png)

Para más información sobre los NSG y las reglas de entrada y de salida, consulte [Permitir el acceso externo a la máquina virtual mediante Azure Portal](nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="connect-to-the-default-iis-website"></a>Conexión al sitio web predeterminado de IIS
1. En el Portal de Azure, haga clic en **Máquinas virtuales** y, luego, seleccione la máquina virtual.
2. En la hoja **Essentials** (Información básica), copie su **dirección IP pública**.
   
    ![Captura de pantalla que muestra dónde encontrar la dirección IP pública de la máquina virtual](./media/hero-role/ipaddress.png)
3. Abra un explorador y, en la barra de direcciones, escriba la dirección IP pública así: http://<publicIPaddress> y haga clic en **Entrar** para ir a esa dirección.
4. El explorador debe abrir la página web IIS predeterminada. Se parece a lo siguiente:
   
    ![Captura de pantalla que muestra el aspecto de la página predeterminada de IIS en un explorador](./media/hero-role/iis-default.png)

## <a name="next-steps"></a>Pasos siguientes
* También puede probar a [conectar un disco de datos](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a la máquina virtual. Los discos de datos proporcionan más espacio de almacenamiento para la máquina virtual.


