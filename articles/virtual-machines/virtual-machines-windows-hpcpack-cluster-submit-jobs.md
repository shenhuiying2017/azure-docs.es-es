---
title: "Envío de trabajos a un clúster de HPC Pack en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo configurar un equipo local para enviar trabajos a un clúster de HPC Pack en Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 78f6833c-4aa6-4b3e-be71-97201abb4721
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 71c896673706fad3eb215f12893b65af7b697843
ms.lasthandoff: 11/17/2016


---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Envío de trabajos HPC desde un equipo local a un clúster de HPC Pack implementado en Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Configure un equipo cliente local para enviar trabajos a un clúster de [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) en Azure. En este artículo se muestra cómo configurar un equipo local con las herramientas de cliente al enviar un trabajo a través de HTTPS al clúster de Azure. De esta forma, varios usuarios de clúster pueden enviar trabajos a un clúster de HPC Pack en la nube, pero sin conectarse directamente a la máquina virtual del nodo principal o acceder a una suscripción de Azure.

![Enviar un trabajo a un clúster de Azure][jobsubmit]

## <a name="prerequisites"></a>Requisitos previos
* Un **nodo principal de HPC Pack implementado en una máquina virtual de Azure**: se recomienda usar herramientas automáticas, como una [plantilla de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/) o un [script de Azure PowerShell](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), para implementar el nodo principal y el clúster. Para completar los pasos de este artículo se necesitan el nombre DNS del nodo principal y las credenciales de un administrador de clústeres.
* **Equipo cliente**: se necesita un equipo cliente con Windows o Windows Server que pueda ejecutar utilidades de cliente de HPC Pack (consulte los [requisitos del sistema](https://technet.microsoft.com/library/dn535781.aspx)). Si solo desea usar el portal web de HPC Pack o la API de REST para enviar trabajos, puede usar el equipo cliente que prefiera.
* **Soporte de instalación de HPC Pack** : para instalar las utilidades de cliente de HPC Pack, el paquete de instalación gratuito de la versión más reciente de HPC Pack (HPC Pack 2012 R2) está disponible en el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Asegúrese de descargar la misma versión de HPC Pack que está instalada en la VM del nodo principal.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Paso 1: Instalar y configurar los componentes web en el nodo principal
Para habilitar una interfaz de REST para enviar trabajos al clúster a través de HTTPS, asegúrese de que los componentes web de HPC Pack están configurados en el nodo principal de HPC Pack. Si no están instalados, primero instale los componentes web, para lo que debe ejecutar el archivo de instalación HpcWebComponents.msi. A continuación, configure los componentes, para lo que debe ejecutar el script de HPC PowerShell **Set-HPCWebComponents.ps1**.

Para obtener información detallada sobre los procedimientos, consulte [Instalación de los componentes web de Microsoft HPC Pack](http://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Algunas plantillas de inicio rápido de Azure para HPC Pack instalan y configuran los componentes web automáticamente. Si usa el [script de implementación de IaaS de HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) para crear el clúster, puede instalar y configurar opcionalmente los componentes web como parte de la implementación.
> 
> 

**Para instalar los componentes web**

1. Conéctese a la máquina virtual del nodo principal con las credenciales de un administrador de clústeres.
2. En la carpeta de instalación de HPC Pack, ejecute HpcWebComponents.msi en el nodo principal.
3. Siga los pasos del asistente para instalar los componentes web

**Para configurar los componentes web**

1. En el nodo principal, inicie HPC PowerShell como administrador.
2. Para cambiar el directorio a la ubicación del script de configuración, escriba el siguiente comando:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Para configurar la interfaz de REST e iniciar el servicio web HPC, escriba el siguiente comando:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. Cuando se le pida que seleccione un certificado, elija el certificado que corresponda al nombre DNS público del nodo principal. Por ejemplo, si implementa la máquina virtual del nodo principal mediante el modelo de implementación clásico, el nombre del certificado es similar a este: CN=&lt;*HeadNodeDnsName&gt*&gt;.cloudapp.net. Si usa el modelo de implementación de Resource Manager, el nombre del certificado es como este CN=&lt;*HeadNodeDnsName*&gt;.&lt;*region*&gt;.cloudapp.azure.com.
   
   > [!NOTE]
   > Seleccione este certificado más adelante para enviar trabajos al nodo principal desde un equipo local. No seleccione ni configure un certificado que corresponda al nombre de equipo del nodo principal del dominio de Active Directory (por ejemplo, CN=*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
5. Para configurar el portal web para el envío de trabajos, escriba el siguiente comando:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Una vez completado el script, detenga y reinicie el servicio Scheduler de trabajos de HPC, para lo que debe escribir los siguientes comandos:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Paso 2: Instalar las utilidades de cliente de HPC Pack en un equipo local
Si desea instalar las utilidades de cliente de HPC Pack en el equipo, descargue los archivos de instalación de HPC Pack (instalación completa) del [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Al comenzar la instalación, elija la opción de instalación de las **utilidades de cliente de HPC Pack**.

Para usar las herramientas de cliente de HPC Pack para enviar trabajos a la máquina virtual del nodo principal, también será preciso que exporte un certificado del nodo principal y lo instale en el equipo cliente. El certificado debe estar en formato .CER.

**Para exportar el certificado desde el nodo principal**

1. En el nodo principal, agregue el complemento Certificados a Microsoft Management Console para la cuenta del equipo local. Para que conocer los pasos para agregar el complemento, consulte [Agregar el complemento Certificados a MMC](https://technet.microsoft.com/library/cc754431.aspx).
2. En el árbol de consola, expanda **Certificados – Equipo local** > **Personal** y, a continuación, haga clic en **Certificados**.
3. Busque el certificado que configuró para los componentes web de HPC Pack en [Paso 1: Instalar y configurar los componentes web en el nodo principal](#step-1:-install-and-configure-the-web-components-on-the-head-node) (por ejemplo, CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net).
4. Haga clic con el botón derecho en el certificado y haga clic en **Todas las tareas** > **Exportar**.
5. En el Asistente para exportar certificados, haga clic en **Siguiente** y asegúrese de que **No exportar la clave privada** está seleccionado.
6. Siga los restantes pasos del asistente para exportar el certificado en formato DER binario codificado X.509 (.CER).

**Para importar el certificado en el equipo cliente**

1. Copie el certificado que exportó desde el nodo principal a una carpeta del equipo cliente.
2. En el equipo cliente, ejecute certmgr.msc.
3. En el administrador de certificados, expanda **Certificados – Usuario actual** > **Entidades de certificación raíz de confianza**, haga clic con el botón derecho en **Certificados** y, después, en **Todas las tareas** > **Importar**.
4. En el Asistente para importar certificados, haga clic en **Siguiente** y siga los pasos necesarios para importar el certificado que exportó desde el nodo principal al almacén de entidades de certificación raíz de confianza.

> [!TIP]
> Puede ver una advertencia de seguridad, ya que el equipo cliente no reconoce la entidad de certificación del nodo principal. En las pruebas se puede ignorar esta advertencia y completar la importación del certificado.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Paso 3: Ejecutar trabajos de prueba en el clúster
Para comprobar la configuración, intente ejecutar trabajos en el clúster de Azure desde el equipo local. Por ejemplo, puede usar las herramientas de la GUI o los comandos de la línea de comandos de HPC Pack para enviar trabajos al clúster. También puede usar un portal web para enviar los trabajos.

**Para ejecutar comandos de envío de trabajos en el equipo cliente**

1. En un equipo cliente donde se instalan las utilidades de cliente de HPC Pack, inicie un símbolo del sistema.
2. Escriba un comando de ejemplo. Por ejemplo, para enumerar todos los trabajos del clúster, escriba un comando similar a uno de los siguientes, según el nombre DNS completo del nodo principal:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    o
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > Use el nombre DNS completo del nodo principal, no la dirección IP, en la dirección URL del programador. Si especifica la dirección IP, aparece un mensaje de error similar a "El certificado del servidor debe tener una cadena de confianza válida o colocarse en el almacén raíz de confianza".
   > 
   > 
3. Cuando se le solicite, escriba el nombre de usuario (con el formato &lt;DomainName&gt;\\&lt;UserName&gt;) y la contraseña del administrador de clústeres de HPC o cualquier otro usuario de clúster que haya configurado. Puede elegir almacenar las credenciales localmente para realizar más operaciones de trabajo.
   
    Aparece una lista de trabajos.

**Para usar el Administrador de trabajos de HPC en el equipo cliente**

1. Si previamente no almacenó las credenciales de dominio de un usuario de clúster al enviar un trabajo, puede agregarlas en el Administrador de credenciales.
   
    a. En el Panel de control del equipo cliente, inicie el Administrador de credenciales.
   
    b. Haga clic en **Credenciales de Windows** > **Agregar una credencial genérica**.
   
    c. Especifique la dirección de Internet (por ejemplo, https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler o https://&lt;HeadNodeDnsName&gt;.&lt;región&gt;.cloudapp.azure.com/HpcScheduler) y el nombre de usuario (&lt;DomainName&gt;\\&lt;UserName&gt;) y la contraseña de administrador de clústeres o de cualquier otro usuario del clúster que haya configurado.
2. En el equipo cliente, inicie el Administrador de trabajos de HPC.
3. En el cuadro de diálogo **Select Head Node** (Seleccionar nodo principal), escriba la dirección URL al nodo principal en Azure (por ejemplo, https://&lt;HeadNodeDnsName&gt;. cloudapp.net o https://&lt;HeadNodeDnsName&gt;.&lt;región&gt;. cloudapp.azure.com).
   
    El Administrador de trabajos de HPC se abre y muestra una lista de los trabajos del nodo principal.

**Para usar el portal web en el nodo principal**

1. Inicie un explorador web en el equipo cliente y escriba una de los siguientes direcciones, en función del nombre DNS completo del nodo principal:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    o
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. En el cuadro de diálogo de seguridad que aparece, escriba las credenciales de dominio del Administrador de clústeres de HPC. (También puede agregar otros usuarios de clúster en distintos roles. Consulte [Administración de usuarios de clúster](https://technet.microsoft.com/library/ff919335.aspx)).
   
    El portal web se abre en la vista de la lista de trabajos.
3. Para enviar un trabajo de ejemplo que devuelva una cadena “Hello World” desde el clúster, haga clic en **Nuevo trabajo** en el panel de navegación izquierdo.
4. En la página **New Job** (Trabajo nuevo), en **From submission pages** (Desde páginas de envío), haga clic en **HelloWorld**. Aparece la página de envío del trabajo.
5. Haga clic en **Submit**(Enviar). Si se le solicita, especifique las credenciales de dominio del administrador de clústeres de HPC. El trabajo se envía y su identificador aparece en la página **My Jobs** (Mis trabajos).
6. Para ver los resultados del trabajo que ha enviado, haga clic en su identificador y luego en **View Tasks** (Ver tareas) para ver el resultado del comando [en **Output** (Salida)].

## <a name="next-steps"></a>Pasos siguientes
* También es posible enviar trabajos al clúster de Azure con la [API de REST de HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Si desea enviar trabajos de clúster desde un cliente Linux, consulte el ejemplo de Python en el [SDK de HPC Pack 2012 R2 y el código de ejemplo](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png

