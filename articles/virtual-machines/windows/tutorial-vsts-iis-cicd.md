---
title: "Creación de una canalización de CI/CD en Azure con Team Services | Microsoft Docs"
description: "Obtenga información acerca de cómo crear una canalización de Visual Studio Team Services para integración y entrega continuas, que implementa una aplicación web en IIS en una máquina virtual Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 006cac5606c411c9d86b36d0069021094fcdb1db
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Creación de una canalización de integración continua con Visual Studio Team Services e IIS
Para automatizar las fases de creación, prueba e implementación del desarrollo de la aplicación, puede utilizar una canalización de integración e implementación continua (CI/CD). En este tutorial se creará una canalización CI/CD utilizando Visual Studio Team Services y una máquina virtual Windows en Azure que ejecuta IIS. Aprenderá a:

> [!div class="checklist"]
> * Publicar una aplicación web ASP.NET en un proyecto de Team Services
> * Crear una definición de compilación que se desencadena mediante confirmaciones de código
> * Instalar y configurar IIS en una máquina virtual de Azure
> * Agregar la instancia de IIS a un grupo de implementación de Team Services
> * Crear una definición de versión para publicar nuevos paquetes de implementación en IIS
> * Prueba de la canalización de CI/CD

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="create-project-in-team-services"></a>Crear el proyecto en Team Services
Visual Studio Team Services permite una colaboración y desarrollo sencillos sin mantener una solución de administración de código local. Team Services proporciona pruebas de código en la nube, compilación y visión de la aplicación. Puede elegir el repositorio de control de versiones y el IDE que mejor se adapten a su desarrollo de código. Para este tutorial, puede usar una cuenta gratuita para crear una aplicación web ASP.NET básica y la canalización de CI/CD. Si no dispone de una cuenta de Team Services, [cree una](http://go.microsoft.com/fwlink/?LinkId=307137).

Para administrar el proceso de confirmación de código, las definiciones de compilación y las definiciones de versión, cree un proyecto en Team Services del modo siguiente:

1. Abra el panel de Team Services en un explorador web y elija **Nuevo proyecto**.
2. Escriba *myWebApp* como **Nombre del proyecto**. Deje todos los demás valores predeterminados para usar el control de versiones *Git* y el proceso de elementos de trabajo *Agile*.
3. Elija la opción de **Compartir con** *Miembros del equipo* y, a continuación, seleccione **Crear**.
5. Una vez creado el proyecto, elija la opción **Inicializar con un archivo Léame o gitignore** y, a continuación, **Inicializar**.
6. En el proyecto nuevo, elija **Paneles** en la parte superior y, a continuación, seleccione **Abrir en Visual Studio**.


## <a name="create-aspnet-web-application"></a>Creación de una aplicación web ASP.NET
En el paso anterior, creó un proyecto en Team Services. El último paso abre el proyecto nuevo en Visual Studio. Las confirmaciones de código se administran en la ventana **Team Explorer**. Cree una copia local del nuevo proyecto y, a continuación, cree una aplicación web ASP.NET a partir de una plantilla, como se indica a continuación:

1. Seleccione **Clonar** para crear un repositorio de git local del proyecto de Team Services.
    
    ![Clonación del repositorio del proyecto de Team Services](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. En **Soluciones**, seleccione **Nuevo**.

    ![Creación de la solución de aplicación web](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Seleccione plantillas **Web** y, a continuación, elija la plantilla **Aplicación Web ASP.NET**.
    1. Escriba un nombre para la aplicación, como *myWebApp*y desactive la casilla **Crear directorio para la solución**.
    2. Si la opción está disponible, desactive la casilla para **Agregar Application Insights al proyecto**. Application Insights precisa que autorice la aplicación web con Azure Application Insights. Para simplificar en este tutorial, omita este proceso.
    3. Seleccione **Aceptar**.
4. Elija **MVC** en la lista de plantillas.
    1. Seleccione **Cambiar autenticación**, elija **Sin autenticación** y, a continuación, seleccione **Aceptar**.
    2. Seleccione **Aceptar** para crear la solución.
5. En la ventana **Team Explorer**, elija **Cambios**.

    ![Confirmación de los cambios locales en el repositorio de git de Team Services](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. En el cuadro de texto de confirmación, escriba un mensaje como *Confirmación inicial*. Elija **Confirmar todos y sincronizar** en el menú desplegable.


## <a name="create-build-definition"></a>Creación de la definición de compilación
En Team Services, se utiliza una definición de compilación para describir cómo se debe compilar la aplicación. En este tutorial, creamos una definición básica que toma el código fuente, compila la solución y, a continuación, crea un paquete de implementación web que podemos usar para ejecutar la aplicación web en un servidor IIS.

1. En el proyecto de Team Services, elija **Compilación y versión** en la parte superior y, a continuación, seleccione **Compilaciones**.
3. Seleccione **+ Nueva definición**.
4. Elija la plantilla **ASP.NET (versión preliminar)** y seleccione **Aplicar**.
5. Deje todos los valores predeterminados de la tarea. En **Obtener fuentes**, asegúrese de que el repositorio *myWebApp* y la bifurcación *master* están seleccionados.

    ![Creación de la definición de compilación en el proyecto de Team Services](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. En la pestaña **Desencadenadores**, mueva el control deslizante para **Habilitar este desencadenador** a *Habilitado*.
7. Guarde la definición de compilación y ponga en cola una compilación nueva seleccionando **Guardar y poner en cola** y, a continuación, **Guardar y poner en cola** de nuevo. Deje los valores predeterminados y seleccione **Poner en cola**.

Observe cómo la compilación se programa en un agente hospedado y después comienza la compilación. La salida es similar a la del ejemplo siguiente:

![Compilación correcta del proyecto de Team Services](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Create virtual machine
Para proporcionar una plataforma para ejecutar la aplicación web ASP.NET, necesita una máquina virtual Windows que ejecute IIS. Team Services utiliza un agente para interactuar con la instancia de IIS cuando confirma el código y se desencadenan compilaciones.

Cree una máquina virtual Windows Server 2016 mediante [este script de ejemplo](../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json). El script tarda unos minutos en ejecutarse y crear la máquina virtual. Una vez creada la máquina virtual, abra el puerto 80 para el tráfico web con [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.resources/new-azurermresourcegroup) como se indica a continuación:

```powershell
Get-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $resourceGroup `
  -Name "myNetworkSecurityGroup" | `
Add-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWeb" `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority "1001" `
  -SourceAddressPrefix "*" `
  -SourcePortRange "*" `
  -DestinationAddressPrefix "*" `
  -DestinationPortRange "80" `
  -Access "Allow" | `
Set-AzureRmNetworkSecurityGroup
```

Para conectarse a la máquina virtual, obtenga la dirección IP pública con [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) como se indica a continuación:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup | Select IpAddress
```

Cree una sesión de escritorio remoto a la máquina virtual:

```cmd
mstsc /v:<publicIpAddress>
```

En la máquina virtual, abra un símbolo del sistema **PowerShell de administrador**. Instale IIS y las características de .NET necesarias como se indica a continuación:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Creación de un grupo de implementación
Para enviar el paquete de implementación de la web al servidor IIS, defina un grupo de implementación en Team Services. Este grupo permite especificar los servidores que son el destino de nuevas compilaciones cuando confirme código en Team Services y las compilaciones se hayan completado.

1. En Team Services, elija **Compilación y versión** y, a continuación, seleccione **Grupos de implementación**.
2. Elija **Agregar grupo de implementación**.
3. Escriba un nombre para el grupo, como *myIIS* y, a continuación, seleccione **Crear**.
4. En la sección **Registrar máquinas**, asegúrese de que *Windows* está seleccionado y, a continuación, active la casilla para **Usar un token de acceso personal en el script para la autenticación**.
5. Seleccione **Copiar script en el portapapeles**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Incorporación de la máquina virtual de IIS al grupo de implementación
Con el grupo de implementación creado, agregue cada instancia de IIS al grupo. Team Services genera un script que se descarga y configura un agente en la máquina virtual que recibe los nuevos paquetes de implementación web y, a continuación, los aplica a IIS.

1. En la sesión de **PowerShell de administrador** en la máquina virtual, pegue y ejecute el script copiado desde Team Services.
2. Cuando se le solicite configurar etiquetas para el agente, elija *S* y escriba *web*.
3. Cuando se le solicite la cuenta de usuario, presione *Intro* para aceptar los valores predeterminados.
4. Espere a que termine el script con el mensaje *El servicio vstsagent.account.computername se inició correctamente*.
5. En la página **Grupos de implementación** del menú **Compilación y versión**, abra el grupo de implementación *myIIS*. Compruebe que aparece la máquina virtual en la pestaña **Máquinas**.

    ![Máquina virtual agregada correctamente al grupo de implementación de Team Services](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>Creación de la definición de versión
Para publicar las compilaciones, se crea una definición de versión en Team Services. Esta definición se desencadena automáticamente por una compilación correcta de la aplicación. Elija el grupo de implementación al que enviar el paquete de implementación web y defina la configuración de IIS adecuada.

1. Elija **Compilación y versión** y, a continuación, seleccione **Compilaciones**. Elija la definición de compilación que creó en un paso anterior.
2. En **Completados recientemente**, elija la compilación más reciente y luego seleccione **Versión**.
3. Elija **Sí** para crear una definición de versión.
4. Elija la plantilla **Vacía** y, a continuación, seleccione **Siguiente**.
5. Compruebe que el proyecto y la definición de compilación se rellenan con los datos del proyecto.
6. Seleccione la casilla de verificación **Implementación continua** y, a continuación, seleccione **Crear**.
7. Seleccione el cuadro de lista desplegable junto a **+ Agregar tareas** y elija *Agregar una fase de grupo de implementación*.
    
    ![Incorporación de la tarea a la definición de versión en Team Services](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Elija **Agregar** junto a **Implementación de aplicaciones web de IIS (versión preliminar)** y, a continuación, seleccione **Cerrar**.
9. Seleccione la tarea primaria **Ejecutar en el grupo de implementación**.
    1. Para **Grupo de implementación**, seleccione el grupo de implementación que creó anteriormente, como *myIIS*.
    2. En el cuadro de texto **Etiquetas de máquina**, seleccione **Agregar** y elija la etiqueta *web*.
    
    ![Tarea del grupo de implementación de la definición de versión en IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Seleccione la tarea **Implementar: implementación de aplicaciones web de IIS** para configurar la instancia de IIS del modo siguiente:
    1. En **Nombre del sitio web**, escriba *Sitio web predeterminado*.
    2. Deje el resto de los valores predeterminados.
12. Elija **Guardar** y, a continuación, seleccione **Aceptar** dos veces.


## <a name="create-a-release-and-publish"></a>Creación de una versión y publicación
Ahora puede enviar el paquete de implementación web como una nueva versión. Este paso comunica con el agente de cada instancia que forma parte del grupo de implementación, envía el paquete de implementación web y configura IIS para que ejecute la aplicación web actualizada.

1. En la definición de versión, seleccione **+ Versión** y, a continuación, elija **Crear versión**.
2. Compruebe que está seleccionada la última compilación en la lista desplegable, junto con **Implementación automatizada: después de crear la versión**. Seleccione **Crear**.
3. Aparecerá un pequeño banner en la parte superior de la definición de la versión, como *Se ha creado la versión 'Versión 1'*. Seleccione el enlace de la versión.
4. Abra la pestaña **Registros** para ver el progreso de la versión.
    
    ![Versión e inserción del paquete de implementación web de Team Services correctas](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Una vez completada la versión, abra un explorador web y escriba la dirección IP pública de la máquina virtual. Se está ejecutando la aplicación web ASP.NET.

    ![Aplicación web ASP.NET en ejecución en la máquina virtual de IIS](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Prueba de la canalización CI/CD
Con la aplicación web en ejecución en IIS, ahora se probará toda la canalización CI/CD. Después de realizar un cambio en Visual Studio y confirmar el código, se desencadena una compilación que a su vez desencadena una versión del paquete de implementación actualizada en IIS:

1. En Visual Studio, abra la ventana **Explorador de soluciones**.
2. Vaya a *myWebApp | Vistas | Inicio | Index.cshtml* y ábralo.
3. Edite la línea 6 así:

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Guarde el archivo .
5. Abra la ventana **Team Explorer**, seleccione el proyecto *myWebApp* y luego elija **Cambios**.
6. Escriba un mensaje de confirmación, como *Pruebas canalización CI/CD* y, a continuación, elija **Confirmar todos y sincronizar** en el menú desplegable.
7. En el área de trabajo de Team Services, se desencadena una nueva compilación desde la confirmación de código. 
    - Elija **Compilación y versión** y, a continuación, seleccione **Compilaciones**. 
    - Elija la definición de compilación y, a continuación, seleccione **En cola y en ejecución** para ver el progreso de la compilación.
9. Una vez que la compilación es correcta, se desencadena una nueva versión.
    - Elija **Compilación y versión** y, a continuación, seleccione **Compilaciones** para ver el paquete de implementación web enviado a la máquina virtual de IIS. 
    - Seleccione el icono **Actualizar** para actualizar el estado. Cuando la columna *Entornos* muestra una marca de verificación verde, la versión se ha implementado correctamente en IIS.
11. Para ver los cambios aplicados, actualice el sitio web IIS en un explorador.

    ![Aplicación web ASP.NET en ejecución en una máquina virtual de IIS desde la canalización CI/CD](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado una aplicación web ASP.NET en Team Services y configurado la compilación y definición de versión para que se implementen nuevos paquetes de implementación web en IIS en cada confirmación de código. Ha aprendido a:

> [!div class="checklist"]
> * Publicar una aplicación web ASP.NET en un proyecto de Team Services
> * Crear una definición de compilación que se desencadena mediante confirmaciones de código
> * Instalar y configurar IIS en una máquina virtual de Azure
> * Agregar la instancia de IIS a un grupo de implementación de Team Services
> * Crear una definición de versión para publicar nuevos paquetes de implementación en IIS
> * Prueba de la canalización de CI/CD

Avance hasta el siguiente tutorial para aprender a instalar una pila SQL&#92;IIS&#92;.NET en un par de máquinas virtuales Windows.

> [!div class="nextstepaction"]
> [Pila SQL&#92;IIS&#92;.NET](tutorial-iis-sql.md)