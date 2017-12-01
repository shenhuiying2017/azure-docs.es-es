---
title: "Clúster de HPC Pack con Azure Active Directory | Microsoft Docs"
description: "Obtenga información sobre cómo integrar un clúster de Microsoft HPC Pack 2016 en Azure con Azure Active Directory"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: jeconnoc
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/16/2017
ms.author: danlep
ms.openlocfilehash: bb0e878c4e987d111a535603cede25c639087ca7
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2017
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Administrar un clúster de HPC Pack en Azure con Azure Active Directory
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) admite la integración con [Azure Active Directory](../../active-directory/index.md) (Azure AD) para los administradores que implementación un clúster de HPC Pack en Azure.



Siga los pasos descritos en este artículo para las siguientes tareas alto nivel: 
* Integración manual del clúster de HPC Pack con el inquilino de Azure AD
* Administración y programación de trabajos en el clúster de HPC Pack en Azure 

La integración de una solución de clúster de HPC Pack con Azure AD sigue los pasos estándar para integrar otras aplicaciones y servicios. En este artículo se da por supuesto que está familiarizado con la administración básica de usuarios en Azure AD. Para más información y contexto, consulte la [documentación de Azure Active Directory](../../active-directory/index.md) y la siguiente sección.

## <a name="benefits-of-integration"></a>Ventajas de la integración


Azure Active Directory (Azure AD) es un directorio basado en la nube multiinquilino y el servicio de administración de identidades que proporciona acceso de inicio de sesión único (SSO) a soluciones en la nube.

La integración de un clúster de HPC Pack con Azure AD puede ayudarle a lograr los objetivos siguientes:

* Quite el controlador de dominio de Active Directory tradicional del clúster de HPC Pack. Esto puede ayudar a reducir los costos de mantenimiento del clúster, si no es necesario para su empresa, y a acelerar el proceso de implementación.
* Aproveche las ventajas siguientes que proporciona Azure AD:
    *   Inicio de sesión único 
    *   Uso de una identidad de AD local para el clúster de HPC Pack en Azure 

    ![Entorno de Azure Active Directory](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Requisitos previos
* **Clúster de HPC Pack 2016 implementado en máquinas virtuales de Azure**: Para conocer los pasos, consulte [Deploy an HPC Pack 2016 cluster in Azure](hpcpack-2016-cluster.md) (Implementación de un clúster de HPC Pack 2016 en Azure). Para completar los pasos de este artículo se necesitan el nombre DNS del nodo principal y las credenciales de un administrador de clústeres.

  > [!NOTE]
  > La integración con Azure Active Directory no se admite en versiones de HPC Pack antes a HPC Pack 2016.



* **Equipo cliente**: Se necesita un equipo cliente con Windows o Windows Server que pueda ejecutar utilidades de cliente de HPC Pack. Si solo desea usar el portal web de HPC Pack o la API de REST para enviar trabajos, puede usar el equipo cliente que prefiera.

* **Utilidades de cliente de HPC Pack**: Instale las utilidades de cliente de HPC Pack en el equipo cliente, con el paquete de instalación gratis disponible en el Centro de descarga de Microsoft.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Paso 1: Registrar el servidor de clúster HPC con el inquilino de Azure AD
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Si la cuenta proporciona acceso a más de uno, haga clic en la cuenta en la esquina superior derecha. Después, establezca la sesión del portal para el inquilino deseado. Debe tener permiso para tener acceso a recursos en el directorio. 
3. Haga clic en **Azure Active Directory** en el panel de navegación Servicios en la izquierda, haga clic en **Usuarios y grupos** y asegúrese de que hay cuentas de usuario ya creadas o configuradas.
4. En **Azure Active Directory**, haga clic en **Registros de aplicaciones** > **Nuevo registro de aplicaciones**. Escriba la siguiente información:
    * **Nombre**: HPCPackClusterServer
    * **Tipo de aplicación**: seleccione **Aplicación web o API**.
    * **Dirección URL de inicio de sesión**: Dirección URL base para el ejemplo, que de forma predeterminada. es `https://hpcserver`.
    * Haga clic en **Crear**.
5. Después de agregar la aplicación, selecciónela en la lista **Registros de aplicaciones**. Después, haga clic en **Configuración** > **Propiedades**. Escriba la siguiente información:
    * Seleccione **Sí** en **Multiinquilino**.
    * Cambie **URI de id. de aplicación** a `https://<Directory_name>/<application_name>`. Reemplace `<Directory_name`> por el nombre completo del inquilino de Azure AD, por ejemplo, `hpclocal.onmicrosoft.com` y reemplace `<application_name>` por el nombre elegido anteriormente.
6. Haga clic en **Guardar**. Cuando finalice de proceso de guardado, en la página de aplicación, haga clic en **Manifiesto**. Edite el manifiesto localizando la configuración `appRoles` y agregando el siguiente rol de aplicación y, después, haga clic en **Guardar**:

  ```json
  "appRoles": [
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "displayName": "HpcAdminMirror",
     "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "description": "HpcAdminMirror",
     "value": "HpcAdminMirror"
     },
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "description": "HpcUsers",
     "displayName": "HpcUsers",
     "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "value": "HpcUsers"
     }
  ],
  ```
7. En **Azure Active Directory**, haga clic en **Aplicaciones empresariales** > **Todas las aplicaciones**. Seleccione **HPCPackClusterServer** en la lista.
8. Haga clic en **Propiedades** y cambie **Asignación de usuarios necesaria** a **Sí**. Haga clic en **Guardar**.
9. Haga clic en **Usuarios y grupos** > **Agregar usuario**. Seleccione un usuario y un rol y después haga clic en **Asignar**. Asigne uno de los roles disponibles (HpcUsers o HpcAdminMirror) al usuario. Repita este paso con otros usuarios del directorio. Para información general sobre de los usuarios de clúster, consulte [Managing Cluster Users](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx) (Administrar usuarios de clústeres).


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Paso 2: Registrar el cliente de clúster HPC con el inquilino de Azure AD

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Si la cuenta proporciona acceso a más de uno, haga clic en la cuenta en la esquina superior derecha. Después, establezca la sesión del portal para el inquilino deseado. Debe tener permiso para tener acceso a recursos en el directorio. 
3. En **Azure Active Directory**, haga clic en **Registros de aplicaciones** > **Nuevo registro de aplicaciones**. Escriba la siguiente información:

    * **Nombre**: HPCPackClusterClient    
    * **Tipo de aplicación**: seleccione **Nativo**.
    * **URI de redirección** - `http://hpcclient`
    * Haga clic en **Crear**

4. Después de agregar la aplicación, selecciónela en la lista **Registros de aplicaciones**. Copie el valor de **Id. de aplicación** y guárdelo. Lo necesitará más adelante cuando configure la aplicación.

5. Haga clic en **Configuración** > **Permisos necesarios** > **Agregar** > **Seleccionar una API**. Busque y seleccione la aplicación HpcPackClusterServer (creada en el paso 1).

6. En la página **Habilitar acceso**, seleccione **Acceder a HpcClusterServer**. A continuación, haga clic en **Hecho**.


## <a name="step-3-configure-the-hpc-cluster"></a>Paso 3: Configurar el clúster HPC

1. Conectar con el nodo principal de HPC Pack 2016 en Azure.

2. Inicie PowerShell HPC.

3. Ejecute el siguiente comando:

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcPackClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    donde

    * `AADTenant` especifica el nombre del inquilino de Azure AD, como `hpclocal.onmicrosoft.com`
    * `AADClientAppId` especifica el identificador de la aplicación creada en el paso 2.

4. Realice una de las siguientes acciones, según la configuración del nodo principal:

    * En un clúster de HPC Pack con un único nodo principal, reinicie el servicio HpcScheduler.

    * En un clúster HPC Pack con varios nodos principales, ejecute los siguientes comandos de PowerShell en el nodo principal para reiniciar el servicio HpcSchedulerStateful:

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName "fabric:/HpcApplication/SchedulerStatefulService"

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Paso 4: Administrar y enviar trabajos desde el cliente

Para instalar las utilidades de cliente de HPC Pack en el equipo, descargue los archivos de instalación de HPC Pack 2016 (instalación completa) del Centro de descarga de Microsoft. Al comenzar la instalación, elija la opción de instalación de las **utilidades de cliente de HPC Pack**.

Para preparar el equipo cliente, instale el certificado que se usa durante la [instalación del clúster HPC](hpcpack-2016-cluster.md) en el equipo cliente. Utilice los procedimientos estándar de administración de certificados de Windows para instalar el certificado público para el almacén **Certificados – Usuario actual** > **Entidades de certificación raíz de confianza**. 

Ahora puede ejecutar los comandos de HPC Pack o usar la GUI del administrador de trabajos de HPC Pack para enviar y administrar trabajos del clúster mediante la cuenta de Azure AD. Para opciones de envío de trabajos, consulte [Envío de trabajos HPC desde un equipo local a un clúster de HPC Pack implementado en Azure](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> Cuando intente conectarse al clúster de HPC Pack en Azure por primera vez, aparecerá una ventana emergente. Escriba sus credenciales de Azure AD para iniciar sesión. El token se almacenará entonces en caché. Las conexiones posteriores con el clúster en Azure usan el token almacenado en caché a menos que los cambios de autenticación o el almacenado en caché se borre.
>
  
Por ejemplo, después de completar los pasos anteriores, puede consultar trabajos desde un cliente local de la siguiente manera:

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Cmdlets útiles para el envío de trabajos con integración de Azure AD 

### <a name="manage-the-local-token-cache"></a>Administración de la memoria caché de tokens local

HPC Pack 2016 proporciona los siguientes cmdlets de HPC PowerShell para administrar la memoria caché de tokens local. Estos cmdlets son útiles para enviar trabajos de una manera no interactiva. Consulte el ejemplo siguiente:

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Establecimiento de credenciales para enviar trabajos con la cuenta de Azure AD 

En ocasiones, puede que prefiera ejecutar el trabajo bajo el usuario del clúster HPC (para un clúster HPC unido a un dominio, ejecútelo como usuario del dominio; para un clúster HPC no unido a un dominio, ejecútelo como usuario local en el nodo principal).

1. Use los siguientes comandos para establecer las credenciales:

    ```powershell
    $localUser = "<username>"

    $localUserPassword="<password>"

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Después, envíe el trabajo como se indica a continuación. El trabajo o la tarea se ejecuta en $localUser en los nodos de proceso.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Si `–Credential` no se especifica con `Submit-HpcJob`, el trabajo o la tarea se ejecuta bajo un usuario local asignado como cuenta de Azure AD. (El clúster de HPC crea un usuario local con el mismo nombre que la cuenta de Azure AD para ejecutar la tarea.)
    
3. Establezca los datos extendidos para la cuenta de Azure AD. Esto es útil cuando se ejecuta un trabajo de MPI en nodos de Linux con la cuenta de Azure AD.

   * Establezca los datos extendidos para propia cuenta de Azure AD.

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Establezca los datos extendidos y realice la ejecución como usuario del clúster HPC.
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

