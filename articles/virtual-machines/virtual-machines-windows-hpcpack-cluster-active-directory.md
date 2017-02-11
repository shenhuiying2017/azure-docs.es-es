---
title: "Clúster de HPC Pack con Azure Active Directory | Microsoft Docs"
description: "Obtenga información sobre cómo integrar un clúster de HPC Pack 2016 en Azure con Azure Active Directory"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/14/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: bdc23f0e54c8bd30f3c082e9038d005d3595d429
ms.openlocfilehash: dd789f76a0fee69aabc894c33b4682253f7c617d


---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Administrar un clúster de HPC Pack en Azure con Azure Active Directory
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) admite la integración con [Azure Active Directory](../active-directory/index.md) (Azure AD) para los administradores que implementación un clúster de HPC Pack en Azure.



Siga los pasos descritos en este artículo para las siguientes tareas alto nivel: 
* Integración manual del clúster de HPC Pack con el inquilino de Azure AD
* Administración y programación de trabajos en el clúster de HPC Pack en Azure 

La integración de una solución de clúster de HPC Pack con Azure AD sigue los pasos estándar para integrar otras aplicaciones y servicios. En este artículo se da por supuesto que está familiarizado con la administración básica de usuarios en Azure AD. Para más información y contexto, consulte la [documentación de Azure Active Directory](../active-directory/index.md) y la siguiente sección.

## <a name="benefits-of-integration"></a>Ventajas de la integración


Azure Active Directory (Azure AD) es un directorio basado en la nube multiinquilino y el servicio de administración de identidades que proporciona acceso de inicio de sesión único (SSO) a soluciones en la nube.

La integración de un clúster de HPC Pack con Azure AD puede ayudarle a lograr los objetivos siguientes:

* Quite el controlador de dominio de Active Directory tradicional del clúster de HPC Pack. Esto puede ayudar a reducir los costos de mantenimiento del clúster, si no es necesario para su empresa, y a acelerar el proceso de implementación.
* Aproveche las ventajas siguientes que proporciona Azure AD:
    *   Inicio de sesión único 
    *   Uso de una identidad de AD local para el clúster de HPC Pack en Azure 

    ![Entorno de Azure Active Directory](./media/virtual-machines-windows-hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Requisitos previos
* **Clúster de HPC Pack 2016 implementado en máquinas virtuales de Azure**: Para conocer los pasos, consulte [Deploy an HPC Pack 2016 cluster in Azure](virtual-machines-windows-hpcpack-2016-cluster.md) (Implementación de un clúster de HPC Pack 2016 en Azure). Para completar los pasos de este artículo se necesitan el nombre DNS del nodo principal y las credenciales de un administrador de clústeres.

  > [!NOTE]
  > La integración con Azure Active Directory no se admite en versiones de HPC Pack antes a HPC Pack 2016.



* **Equipo cliente**: Se necesita un equipo cliente con Windows o Windows Server que pueda ejecutar utilidades de cliente de HPC Pack. Si solo desea usar el portal web de HPC Pack o la API de REST para enviar trabajos, puede usar el equipo cliente que prefiera.

* **Utilidades de cliente de HPC Pack**: Instale las utilidades de cliente de HPC Pack en el equipo cliente, con el paquete de instalación gratis disponible en el Centro de descarga de Microsoft.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Paso 1: Registrar el servidor de clúster HPC con el inquilino de Azure AD
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Active Directory** en el menú de la izquierda y luego en el directorio que desee de su suscripción. Debe tener permiso para tener acceso a recursos en el directorio.
3. Haga clic en **Usuarios** y asegúrese de que ya hay cuentas de usuario creadas o configuradas.
4. Haga clic en **Aplicaciones** > **Agregar** y luego en **Agregar una aplicación que mi organización está desarrollando**. Escriba la siguiente información en el asistente:
    * **Nombre**: HPCPackClusterServer
    * **Tipo**: Seleccione **Aplicación web y/o API web**
    * **Dirección URL de inicio de sesión**: Dirección URL base para el ejemplo, que de forma predeterminada. es `https://hpcserver`.
    * **URI de id. de aplicación** - `https://<Directory_name>/<application_name>`. Reemplace `<Directory_name`> por el nombre completo del inquilino de Azure AD, por ejemplo, `hpclocal.onmicrosoft.com` y reemplace `<application_name>` por el nombre elegido anteriormente.

5. Después de agregar la aplicación, haga clic en **Configurar**. Configure las siguientes propiedades:
    * Seleccione **Sí** en **La aplicación es multiempresa**.
    * Seleccione **Sí** en **Asignación de usuario necesaria para acceder a la aplicación**.

6. Haga clic en **Save**. Cuando se complete el almacenamiento, haga clic en **Administrar manifiesto**. Esta acción descarga la notación de objetos JavaScript (JSON) del manifiesto de la aplicación. Edite el manifiesto descargado localizando la configuración `appRoles` y agregando el siguiente rol de aplicación:
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
7. Guarde el archivo . A continuación, en el portal, haga clic en **Administrar manifiesto** > **Cargar manifiesto**. Después puede cargar el manifiesto editado.
8. Haga clic en **Usuarios**, seleccione un usuario y luego haga clic en **Asignar**. Asigne uno de los roles disponibles (HpcUsers o HpcAdminMirror) al usuario. Repita este paso con otros usuarios del directorio. Para información general sobre de los usuarios de clúster, consulte [Managing Cluster Users](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx) (Administrar usuarios de clústeres).

   > [!NOTE] 
   > Para administrar usuarios, se recomienda usar la hoja de vista previa de Azure Active Directory de [Azure Portal](https://portal.azure.com).
   >


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Paso 2: Registrar el cliente de clúster HPC con el inquilino de Azure AD

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Active Directory** en el menú de la izquierda y luego en el directorio que desee de su suscripción. Debe tener permiso para tener acceso a recursos en el directorio.
3. Haga clic en **Aplicaciones** > **Agregar** y luego en **Agregar una aplicación que mi organización está desarrollando**. Escriba la siguiente información en el asistente:

    * **Nombre**: HPCPackClusterClient
    * **Tipo**: Seleccione **Aplicación de cliente nativo**
    * **URI de redirección** - `http://hpcclient`

4. Después de agregar la aplicación, haga clic en **Configurar**. Copia el valor de **Id. de cliente** valor y guárdelo. Lo necesitará más adelante cuando configure la aplicación.

5. En **Permisos para otras aplicaciones**, haga clic en **Agregar aplicación**. Busque y agregue la aplicación HpcPackClusterServer (creada en el paso 1).

6. En la lista desplegable **Permisos delegados**, seleccione **Acceder a HpcClusterServer**. A continuación, haga clic en **Guardar**.


## <a name="step-3-configure-the-hpc-cluster"></a>Paso 3: Configurar el clúster HPC

1. Conectar con el nodo principal de HPC Pack 2016 en Azure.

2. Inicie PowerShell HPC.

3. Ejecute el siguiente comando:

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    donde

    * `AADTenant` especifica el nombre del inquilino de Azure AD, como `hpclocal.onmicrosoft.com`
    * `AADClientAppId` especifica el identificador de cliente para la aplicación creada en el paso 2.

4. Reinicie el servicio HpcSchedulerStateful.

    En un clúster con varios nodos principales, puede ejecutar los siguientes comandos de PowerShell en el nodo principal para cambiar la réplica principal para el servicio HpcSchedulerStateful:

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName “fabric:/HpcApplication/SchedulerStatefulService”

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Paso 4: Administrar y enviar trabajos desde el cliente

Para instalar las utilidades de cliente de HPC Pack en el equipo, descargue los archivos de instalación de HPC Pack 2016 (instalación completa) del Centro de descarga de Microsoft. Al comenzar la instalación, elija la opción de instalación de las **utilidades de cliente de HPC Pack**.

Para preparar el equipo cliente, instale el certificado que se usa durante la [instalación del clúster HPC](virtual-machines-windows-hpcpack-2016-cluster.md) en el equipo cliente. Utilice los procedimientos estándar de administración de certificados de Windows para instalar el certificado público para el almacén **Certificados – Usuario actual** > **Entidades de certificación raíz de confianza**. 

Ahora puede ejecutar los comandos de HPC Pack o usar la GUI del administrador de trabajos de HPC Pack para enviar y administrar trabajos del clúster mediante la cuenta de Azure AD. Para opciones de envío de trabajos, consulte [Envío de trabajos HPC desde un equipo local a un clúster de HPC Pack implementado en Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> Cuando intente conectarse al clúster de HPC Pack en Azure por primera vez, aparecerá una ventana emergente. Escriba sus credenciales de Azure AD para iniciar sesión. El token se almacenará entonces en caché. Las conexiones posteriores con el clúster en Azure usan el token almacenado en caché a menos que los cambios de autenticación o el almacenado en caché se borre.
>
  
Por ejemplo, después de completar los pasos anteriores, puede consultar trabajos desde un cliente local de la siguiente manera:

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Cmdlets útiles para el envío de trabajos con integración de Azure AD 

### <a name="manage-the-local-token-cache"></a>Administración de la memoria caché de tokens local

HPC Pack 2016 proporciona dos nuevos cmdlets de HPC PowerShell para administrar la memoria caché de tokens local. Estos cmdlets son útiles para enviar trabajos de una manera no interactiva. Consulte el ejemplo siguiente:

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Establecimiento de credenciales para enviar trabajos con la cuenta de Azure AD 

En ocasiones, puede que prefiera ejecutar el trabajo bajo el usuario del clúster HPC (para un clúster HPC unido a un dominio, ejecútelo como usuario del dominio; para un clúster HPC no unido a un dominio, ejecútelo como usuario local en el nodo principal).

1. Use los siguientes comandos para establecer las credenciales:

    ```powershell
    $localUser = “<username>”

    $localUserPassword=”<password>”

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




<!--HONumber=Dec16_HO2-->


