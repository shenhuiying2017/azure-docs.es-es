---
title: "Implementación de App Service en un entorno sin conexión: Azure Stack | Microsoft Docs"
description: "Guía detallada sobre cómo implementar App Service en un entorno de Azure Stack desconectado protegido por AD FS."
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: b733851d4459ead1ae437604a27ca7720e2a3a87
ms.contentlocale: es-es
ms.lasthandoff: 09/15/2017

---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Incorporación de un proveedor de recursos de App Service a un entorno de Azure Stack desconectado protegido por AD FS

Se debe agregar un [proveedor de recursos de Azure App Service](azure-stack-app-service-overview.md) a la implementación de Azure Stack:

* Si se ejecuta Azure Stack en un entorno aislado protegido por los Servicios de federación de Active Directory (AD FS). 
* Si quiere proporcionar a los inquilinos la capacidad de crear aplicaciones web, móviles y de API (y aplicaciones de Azure Functions) con su suscripción de Azure Stack. 

Para ello, siga los pasos de este artículo.

## <a name="download-the-required-components"></a>Descarga de los componentes necesarios

1. Descargue el [instalador de la versión preliminar de App Service en Azure Stack](http://aka.ms/appsvconmasrc1installer).

2. Descargue los [scripts de la aplicación auxiliar para la implementación de App Service en Azure Stack](http://aka.ms/appsvconmasrc1helper).

3. Extraiga los archivos del archivo ZIP de scripts de la aplicación auxiliar. Aparecen los archivos y la estructura de carpetas siguientes:

   - Create-AppServiceCerts.ps1
   - Create-IdentityApp.ps1
   - Módulos
      - AzureStack.Identity.psm1
      - GraphAPI.psm1

## <a name="create-an-offline-installation-package"></a>Creación de un paquete de instalación sin conexión

Para implementar App Service en un entorno aislado, se debe crear un paquete de instalación sin conexión desde una máquina que se conecte a Internet.

1. Ejecute el instalador de la versión preliminar de App Service en Azure Stack (AppService.exe) desde una máquina que esté conectada a Internet.

2. Haga clic en la pestaña **Avanzado** y haga clic en **Crear paquete de instalación sin conexión**.

    ![Creación de un paquete de instalación sin conexión de App Service en Azure Stack][1]

3. El instalador de App Service crea un paquete de instalación sin conexión, muestra la ruta de acceso y ofrece una opción para abrir la carpeta.

   ![Paquete de instalación sin conexión de App Service en Azure Stack][2]

4. Copie el instalador de la versión preliminar de App Service en Azure Stack (AppService.exe) y el paquete de instalación sin conexión en el equipo host de Azure Stack.

## <a name="create-certificates-required-by-app-service-on-azure-stack"></a>Creación de certificados necesarios para App Service en Azure Stack

Este primer script funciona con la entidad de certificación de Azure Stack para crear tres certificados que necesita App Service. Ejecute el script en el host de Azure Stack y asegúrese de que está ejecutando PowerShell como azurestack\administrator.

1. En una sesión de PowerShell que se ejecuta como azurestack\administrator, ejecute el script **Create-AppServiceCerts.ps1** desde la ubicación donde extrajo los scripts de la aplicación auxiliar.  El script crea tres certificados en la misma carpeta que el script de creación de certificados que App Service necesita.

2. Escriba una contraseña para proteger los archivos .pfx y anótela. Debe escribirla en App Service en el instalador de Azure Stack.

### <a name="create-appservicecertsps1-parameters"></a>Parámetros de Create-AppServiceCerts.ps1

| Parámetro | Obligatorio/opcional | Valor predeterminado | Descripción |
| --- | --- | --- | --- |
| pfxPassword | Obligatorio | Null | Contraseña usada para proteger la clave privada del certificado |
| DomainName | Obligatorio | local.azurestack.external | Región y sufijo de dominio de Azure Stack |
| CertificateAuthority | Obligatorio | AzS-CA01.azurestack.local | Punto de conexión de entidad de certificación |

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Realización de la instalación sin conexión de App Service en Azure Stack

> [!NOTE]
> Se *debe* usar una cuenta con privilegios elevados (administrador local o de dominio) para ejecutar el instalador. Si se inicia sesión como azurestack\azurestackuser, se pedirán credenciales con privilegios elevados.

1. Ejecute appservice.exe como azurestack\administrator.

2. Haga clic en la pestaña **Avanzado** y haga clic en **Completar la instalación sin conexión**.

    ![Realización de la instalación sin conexión de App Service en Azure Stack][3]

3. Especifique la ubicación del paquete de instalación sin conexión previamente creado y haga clic en **Siguiente**.

    ![Ubicación del paquete de instalación sin conexión de App Service en Azure Stack][4]

4. Revise y acepte los términos de licencia de la versión preliminar del software de Microsoft y haga clic en **Siguiente**.

5. Revise y acepte los términos de licencia de terceros y haga clic en **Siguiente**.

6. Revise la información de configuración de la nube de App Service y haga clic en **Siguiente**.

    ![Configuración de la nube de App Service en Azure Stack][5]

    > [!NOTE]
    > El instalador de App Service en Azure Stack proporciona los valores predeterminados para la instalación de Azure Stack de un nodo. Si se han personalizado opciones al implementar Azure Stack (por ejemplo, el sufijo de dominio), se deben editar los valores de esta ventana según corresponda. Por ejemplo, si se usa el sufijo de dominio mycloud.com, el punto de conexión de Azure Resource Manager del administrador debe cambiarse a adminmanagement.[región].mycloud.com.

7. Haga clic en el botón **Conectar** situado junto al cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack). Escriba la cuenta de administrador, por ejemplo, azurestackadmin@azurestack.local. Escriba la contraseña y haga clic en **Iniciar sesión**.

8. Seleccione su suscripción en el cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack).

9. En el cuadro **Azure Stack Locations** (Ubicaciones de Azure Stack), seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local**. Haga clic en **Siguiente**.

    ![Selección de la suscripción de App Service en Azure Stack][6]

10. Escriba el **Nombre de grupo de recursos** para la implementación de App Service. De forma predeterminada, se establece en **APPSERVICE-LOCAL**.

11. Escriba el **Nombre de cuenta de almacenamiento** que quiere que App Service cree como parte de la instalación. De forma predeterminada, se establece en **appsvclocalstor**.

12. Escriba los detalles de SQL Server para la instancia que se usa para hospedar las bases de datos del proveedor de recursos de App Service. Haga clic en **Siguiente**; el instalador validará las propiedades de conexión de SQL.

13. Haga clic en el botón **Examinar** situado junto al cuadro **App Service default SSL certificate file** (Archivo de certificado SSL predeterminado de App Service). Vaya al certificado **_.appservice.local.AzureStack.external** [creado anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps). Si especificó una ubicación y un sufijo de dominio diferentes al crear el certificado, seleccione el certificado correspondiente.

14. Escriba la contraseña que estableció cuando creó el certificado.

15. Haga clic en el botón **Examinar** situado junto al cuadro **Resource provider SSL certificate file** (Archivo de certificado SSL del proveedor de recursos). Vaya al certificado **api.appservice.local.AzureStack.external** [creado anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps). Si especificó una ubicación y un sufijo de dominio diferentes al crear el certificado, seleccione el certificado correspondiente.

16. Escriba la contraseña que estableció cuando creó el certificado.

17. Haga clic en el botón **Examinar** situado junto al cuadro **Resource provider root certificate file** (Archivo de certificado raíz del proveedor de recursos). Vaya al certificado **AzureStackCertificationAuthority** [creado anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).

18. Haga clic en **Siguiente**. El instalador comprueba la contraseña del certificado proporcionada.

    ![Detalles del certificado de App Service en Azure Stack][8]

19. Revise la configuración del rol de App Service. Los valores predeterminados se rellenan con las SKU de instancia mínimas recomendadas para cada rol. Se proporciona un resumen de los requisitos de memoria y núcleo para ayudar a planear la implementación. Después de realizar las selecciones, haga clic en **Siguiente**.

    - **Controlador**: de forma predeterminada, se selecciona una instancia de Estándar A1. Es lo mínimo que se recomienda. El rol de controlador es responsable de administrar y mantener el estado de la nube de App Service.
    - **Administración**: de forma predeterminada, se selecciona una instancia de Estándar A2. Para proporcionar una conmutación por error, se recomiendan dos instancias. El rol de administración es responsable de los puntos de conexión de API y Azure Resource Manager de App Service, las extensiones de portales (portal de Functions, administración e inquilino) y el servicio de datos.
    - **Publicador**: de forma predeterminada, se selecciona una instancia de Estándar A1. Es lo mínimo que se recomienda. El rol de publicador es responsable de la publicación de contenido a través de la implementación web y FTP.
    - **FrontEnd**: de forma predeterminada, se selecciona una instancia de Estándar A1. Es lo mínimo que se recomienda. El rol de FrontEnd es responsable de enrutar las solicitudes a aplicaciones de App Service.
    - **Trabajo compartido**: de forma predeterminada, se selecciona una instancia de Estándar A1, pero es posible que se quieran agregar más. Como administrador, puede definir su oferta y elegir cualquier nivel de SKU. Los niveles deben tener un núcleo como mínimo. El rol de trabajo compartido es responsable del hospedaje de aplicaciones web, móviles o de API y aplicaciones de Azure Functions.

    ![Configuración del rol de App Service en Azure Stack][9]

    > [!NOTE]
    > En las versiones preliminares técnicas, el instalador del proveedor de recursos de App Service también implementa una instancia de Estándar A1 para que funcione como servidor de archivos simple para la compatibilidad con Azure Resource Manager. Esto se conserva para el punto de contacto de un único nodo. Para las cargas de trabajo de producción de disponibilidad general, el instalador de App Service permite el uso de un servidor de archivos de alta disponibilidad.

20. Elija su imagen de VM **Windows Server 2016** de implementación entre las que están disponibles en el proveedor de recursos de procesos para la nube de App Service. Haga clic en **Siguiente**. 

    ![Selección de la imagen de VM de App Service en Azure Stack][10]

21. Escriba un nombre de usuario y una contraseña para los roles de trabajo configurados en la nube de App Service. Escriba un nombre de usuario y una contraseña para todos los demás roles de App Service. Haga clic en **Siguiente**.

    ![Entrada de credenciales de App Service en Azure Stack][11]

22. En la pantalla de resumen, compruebe las selecciones realizadas. Para realizar cambios, regrese a través de las pantallas y modifique sus selecciones. Si la configuración es de su agrado, seleccione la casilla. Para iniciar la implementación, haga clic en **Siguiente**. 

    ![Resumen de la selección de App Service en Azure Stack][12]

23. Realice un seguimiento del progreso de la instalación. App Service en Azure Stack tarda entre 45 y 60 minutos en realizar la implementación según las selecciones predeterminadas.

    ![Progreso de la instalación de App Service en Azure Stack][13]

24. Después de que el instalador finalice correctamente, haga clic en **Salir**.

## <a name="configure-an-ad-fs-service-principal-for-virtual-machine-scale-set-integration-on-worker-tiers-and-sso-for-the-azure-functions-portal-and-advanced-developer-tools"></a>Configuración de una entidad de servicio de AD FS para la integración del conjunto de escalado de máquinas virtuales en niveles de trabajo y SSO para el portal de Azure Functions y las herramientas avanzadas de desarrollador

>[!NOTE]
> Estos pasos se aplican solo a entornos de Azure Stack seguros de AD FS.

Los administradores necesitan configurar SSO para:

* Configurar a una entidad de servicio para la integración del conjunto de escalado de máquinas virtuales en los niveles de trabajo.
* Habilitar las herramientas avanzadas de desarrollador en App Service (Kudu).
* Habilitar el uso de la experiencia del portal de Azure Functions. 

Siga estos pasos:

1. Abra una instancia de PowerShell como azurestack\azurestackadmin.

2. Vaya a la ubicación de los scripts descargados y extraídos en el [paso de requisito previo](#Download-Required-Components).

3. [Instale](azure-stack-powershell-install.md) y [configure un entorno de PowerShell de Azure Stack](azure-stack-powershell-configure-admin.md).

4. En la misma sesión de PowerShell, ejecute el script **CreateIdentityApp.ps1**. Cuando se le pida el identificador de inquilino de Azure Active Directory (Azure AD), escriba **ADFS**.

5. En la ventana **Credencial**, escriba la cuenta y la contraseña de administrador del servicio de AD FS. Haga clic en **Aceptar**.

6. Escriba la contraseña y la ruta de acceso del archivo del [certificado creado anteriormente](# Create certificates to be used by App Service on Azure Stack). El certificado creado para este paso de forma predeterminada es sso.appservice.local.azurestack.external.pfx.

7. El script crea una nueva aplicación en Azure AD del inquilino y genera un nuevo script de PowerShell.

8. Copie el archivo de certificado de la aplicación de identidad y el script generado en **CN0-VM** mediante una sesión de escritorio remoto.

9. Vuelva a **CN0-VM**.

10. Abra una ventana de PowerShell como administrador y vaya al directorio donde se copiaron el archivo de script y el certificado en el paso 7.

11. Ejecute el archivo de script. El archivo de script accederá a las propiedades de la configuración de App Service en Azure Stack e iniciará una operación de reparación en todos los roles de front-end y de administración.

| Parámetro | Obligatorio/opcional | Valor predeterminado | Descripción |
| --- | --- | --- | --- |
| DirectoryTenantName | Obligatorio | Null | Use **ADFS** para el entorno de AD FS |
| TenantAzure Resource ManagerEndpoint | Obligatorio | management.local.azurestack.external | Punto de conexión de Azure Resource Manager del inquilino |
| AzureStackCredential | Obligatorio | Null | Cuenta de administrador del servicio de AD FS |
| CertificateFilePath | Obligatorio | Null | Ruta de acceso del archivo de certificado de la aplicación de identidad generado anteriormente |
| CertificatePassword | Obligatorio | Null | Contraseña usada para proteger la clave privada del certificado |
| DomainName | Obligatorio | local.azurestack.external | Región y sufijo de dominio de Azure Stack |
| AdfsMachineName | Opcional | Nombre de la máquina de AD FS, por ejemplo, AzS-ADFS01.azurestack.local |


## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validación de la instalación de App Service en Azure Stack

1. En el portal de administración de Azure Stack, vaya al grupo de recursos creado por el instalador. De forma predeterminada, este grupo es **APPSERVICE-LOCAL**.

2. Busque **CN0-VM**. Para conectarse a la máquina virtual, haga clic en **Conectar** en la hoja **Máquina virtual**.

3. En el escritorio de esta máquina virtual, haga doble clic en **Web Cloud Management Console** (Consola de administración de la nube web).

4. Vaya a **Servidores administrados**.

5. Cuando se muestre **Listo** en todas las máquinas para uno o varios trabajos, vaya al paso 6.

6. Cierre la máquina del escritorio remoto y vuelva a la máquina donde ejecutó el instalador de App Service.

    > [!NOTE]
    > No es necesario esperar a que se muestre **Listo** para uno o varios trabajos para completar la instalación de App Service en Azure Stack. Sin embargo, se necesita que un trabajo como mínimo esté listo para implementar una aplicación web, móvil o de API o Azure Functions.
    
    ![Estado de los servidores administrados de App Service en Azure Stack][14]

## <a name="test-drive-app-service-on-azure-stack"></a>Prueba de App Service en Azure Stack

Después de implementar y registrar el proveedor de recursos de App Service, pruébelo para asegurarse de que los inquilinos pueden implementar aplicaciones web, móviles y de API.

> [!NOTE]
> Debe crear una oferta que tenga el espacio de nombres Microsoft.Web dentro del plan. A continuación, debe tener una suscripción de inquilino que suscriba esta oferta. Para más información, consulte [Create offer](azure-stack-create-offer.md) (Creación de una oferta) y [Create plan](azure-stack-create-plan.md) (Creación de un plan).
>

Se *debe* tener una suscripción de inquilino para crear aplicaciones que usen App Service en Azure Stack. Las únicas funcionalidades que un administrador del servicio puede completar en el portal de administración están relacionadas con la administración del proveedor de recursos de App Service. Entre estas funcionalidades se incluyen la adición de capacidad, la configuración de orígenes de implementación y la adición de niveles de trabajo y SKU.

A partir de la tercera versión preliminar técnica, para crear aplicaciones web, móviles y de API se debe usar el portal de inquilino y tener una suscripción de inquilino.  

1. En el portal de inquilino de Azure Stack, haga clic en **Nuevo** > **Web y móvil** > **Aplicación web**.

2. En la hoja **Aplicación web**, escriba un nombre en el cuadro **Aplicación web**.

3. En **Grupo de recursos**, haga clic en **Nuevo**. A continuación, escriba un nombre en el cuadro **Grupo de recursos**.

4. Haga clic en **Plan de App Service/Ubicación** > **Create New** (Crear nuevo).

5. En la hoja **Plan de App Service**, escriba un nombre en el cuadro **Plan de App Service**.

6. Haga clic en **Plan de tarifa** > **Free-Shared** (Gratis - Compartido) o **Shared-Shared** (Compartido - Compartido) > **Seleccionar** > **Aceptar** > **Crear**.

7. En menos de un minuto, aparecerá un icono para la nueva aplicación web en el panel. Haga clic en el icono.

8. En la hoja **Aplicación web**, haga clic en **Examinar** para ver el sitio web predeterminado para esta aplicación.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementación de un sitio web de WordPress, DNN o Django (opcional)

1. En el portal de inquilino de Azure Stack, haga clic en **+**. Vaya a Azure Marketplace, implemente un sitio web de Django y espere a que se complete correctamente. La plataforma web de Django usa una base de datos basada en el sistema de archivos. No requiere ningún proveedor de recursos adicional, como SQL o MySQL.

2. Si también implementó un proveedor de recursos de MySQL, puede implementar un sitio web de WordPress en Marketplace. Cuando se le pidan los parámetros de la base de datos, escriba el nombre de usuario como * User1@Server1 *, con el nombre de usuario y el nombre del servidor de su elección.

3. Si también implementó un proveedor de recursos de SQL Server, puede implementar un sitio web de DNN en Marketplace. Cuando se le pidan los parámetros de la base de datos, elija una base de datos del equipo que ejecute el SQL Server que está conectado a su proveedor de recursos.

## <a name="next-steps"></a>Pasos siguientes

También puede probar otros [servicios de Plataforma como servicio (PaaS)](azure-stack-tools-paas-services.md).

- [Proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step1.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step2.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step3.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step4.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-cloud-configuration.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-subscription-location-populated.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-resource-group-SQL.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-certificates.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-role-configuration.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-vm-image-selection.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-role-credentials.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-selection-summary.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-installation-progress.png
[14]: ./media/azure-stack-app-service-deploy-offline/managed-servers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

