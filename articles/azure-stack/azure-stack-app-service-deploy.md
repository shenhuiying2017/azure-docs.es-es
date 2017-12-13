---
title: "Implementación de App Services: Azure Stack | Microsoft Docs"
description: "Guía detallada para implementar App Service en Azure Stack"
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
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: 522e5a334b5165344b66524d03f0d85468b81332
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Incorporación de un proveedor de recursos de App Service a Azure Stack

Como operador en la nube de Azure Stack, puede ofrecer a los usuarios la capacidad de crear aplicaciones web y API. Para ello, primero debe agregar el [proveedor de recursos de App Service](azure-stack-app-service-overview.md) a la implementación de Azure Stack como se describe en este artículo. Después de haber instalado el proveedor de recursos de App Service, puede incluirlos en sus planes y ofertas. Los usuarios pueden suscribirse entonces para obtener el servicio y empezar a crear aplicaciones.

> [!IMPORTANT]
> Antes de ejecutar el instalador, asegúrese de que ha seguido las instrucciones de [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).
> 
>



## <a name="run-the-app-service-resource-provider-installer"></a>Ejecución del instalador del proveedor de recursos de App Service

La instalación del proveedor de recursos de App Service en su entorno de Azure Stack puede tardar hasta una hora. Durante este proceso, el instalador hará lo siguiente:

* Creerá un contenedor de blobs en la cuenta de almacenamiento de Azure Stack especificada.
* Creará una zona DNS y las entradas para App Service.
* Registrará el proveedor de recursos de App Service.
* Registrará los elementos de la galería de App Service.

Para implementar el proveedor de recursos de App Service, siga estos pasos:

1. Ejecute appservice.exe como administrador (azurestack\CloudAdmin).

2. Haga clic en **Implemente App Service en su nube de Azure Stack**.

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image01.png)

3. Revise y acepte los términos de licencia del software de Microsoft y haga clic en **Siguiente**.

4. Revise y acepte los términos de licencia de terceros y haga clic en **Siguiente**.

5. Asegúrese de que la información de configuración de nube de App Service es correcta. Si usó la configuración predeterminada durante la implementación de Azure Stack Development Kit, puede aceptar los valores predeterminados aquí. Sin embargo, si personalizó las opciones cuando implementó Azure Stack, debe editar los valores de esta ventana para reflejarlo. Por ejemplo, si usa el sufijo de dominio mycloud.com, el punto de conexión debe cambiar a management.mycloud.com. Después de confirmar su información, haga clic en **Siguiente**.

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image02.png)

6. En la página siguiente:
    1. Haga clic en el botón **Conectar** situado junto al cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack).
        - Si se usa Azure Active Directory (Azure AD), escriba la cuenta de administrador y la contraseña de Azure AD que proporcionó al implementar Azure Stack. Haga clic en **Iniciar sesión**.
        - Si se usan los Servicios de federación de Active Directory (AD FS), debe proporcionar la cuenta de administrador. Por ejemplo: cloudadmin@azurestack.local. Escriba la contraseña y haga clic en **Iniciar sesión**.
    2. En el cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack), seleccione su suscripción.
    3. En el cuadro **Azure Stack Locations** (Ubicaciones de Azure Stack), seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a implementar con Azure Stack Development Kit.
    4. Escriba un **Nombre de grupo de recursos** para la implementación de App Service. De forma predeterminada, se establece en **APPSERVICE\<REGION\>**.
    5. Escriba el **Nombre de cuenta de almacenamiento** que quiere que App Service cree como parte de la instalación. De forma predeterminada, se establece en **appsvclocalstor**.
    6. Haga clic en **Siguiente**.

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image03.png)

7. Escriba la información para el recurso compartido de archivos y, a continuación, haga clic en **Siguiente**. La dirección del recurso compartido de archivos debe utilizar el nombre de dominio completo del servidor de archivos, por ejemplo: \\\appservicefileserver.local.cloudapp.azurestack.external\websites o la dirección IP como, por ejemplo, \\\10.0.0.1\websites.

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image04.png)

8. En la página siguiente:
    1. En el cuadro **Id. de la aplicación de identidad**, escriba el GUID de la aplicación que va a usar para la identidad (de Azure AD).
    2. En el cuadro **Identity Application certificate file** (Archivo del certificado de la aplicación de identidad) escriba (o vaya a) la ubicación del archivo del certificado.
    3. En el cuadro **Identity Application certificate password** (Contraseña del certificado de la aplicación de identidad), escriba la contraseña para el certificado. Esta contraseña es la que anotó cuando usó el script para crear los certificados.
    4. En el cuadro **Azure Resource Manager root certificate file** (Archivo del certificado raíz de Azure Resource Manager) escriba (vaya a) la ubicación del archivo del certificado.
    5. Haga clic en **Siguiente**.

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image05.png)

9. Para cada uno de los tres cuadros de archivo de certificado, haga clic en **Examinar** y navegue hasta el archivo de certificado adecuado. Debe proporcionar la contraseña de cada certificado. Estos certificados son los que creó en el [paso para crear los certificados necesarios](azure-stack-app-service-deploy.md#create-the-required-certificates). Haga clic en **Siguiente** después de escribir toda la información.

    | Box | Ejemplo de nombre de archivo de certificado |
    | --- | --- |
    | **Archivo de certificado SSL predeterminado de App Service**  | \_.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL de API de App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL del publicador de App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si usó un sufijo de dominio diferente cuando creó los certificados, los nombres de archivo de certificado no usan *local. AzureStack.external*. En su lugar, use la información del dominio personalizado.

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image06.png)    

10. Escriba los detalles de SQL Server para la instancia de servidor que se usa para hospedar las bases de datos del proveedor de recursos de App Service y, después, haga clic en **Siguiente**. El instalador valida las propiedades de conexión de SQL.

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image07.png)    

11. Revise las opciones de SKU y la instancia de rol. Los valores predeterminados se rellenan con el número mínimo de instancias y la SKU mínima de cada rol en una implementación de ASDK. Se proporciona un resumen de los requisitos de memoria y vCPU para ayudar a planear la implementación. Después de realizar las selecciones, haga clic en **Siguiente**.

    > [!NOTE]
    > Para las implementaciones de producción, siga las instrucciones que encontrará en [Planeamiento de la capacidad de los roles de servidor de Azure App Service en Azure Stack](azure-stack-app-service-capacity-planning.md).
    > 
    >

    | Rol | Número mínimo de instancias | SKU mínima | Notas |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A1: (1 vCPU, 1792 MB) | Administra y mantiene el estado de la nube de App Service. |
    | Administración | 1 | Standard_A2: (2 vCPU, 3584 MB) | Administra los puntos de conexión de API y Azure Resource Manager de App Service, las extensiones de portales (portal de Functions, administración e inquilino) y el servicio de datos. Para admitir la conmutación por error, se incrementan las instancias recomendadas a 2. |
    | Publicador | 1 | Standard_A1: (1 vCPU, 1792 MB) | Publica contenido a través de la implementación de web y FTP. |
    | FrontEnd | 1 | Standard_A1: (1 vCPU, 1792 MB) | Enruta las solicitudes a las aplicaciones de App Service. |
    | Trabajo compartido | 1 | Standard_A1: (1 vCPU, 1792 MB) | Hospeda aplicaciones de API o web y aplicaciones de Azure Functions. Puede ser conveniente agregar más instancias. Como operador, puede definir su oferta y elegir cualquier nivel de SKU. Los niveles deben tener una vCPU como mínimo. |

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image08.png)    

    > [!NOTE]
    > **Windows Server 2016 Core no es una imagen de plataforma compatible para su uso con Azure App Service en Azure Stack**.

12. En el cuadro **Select Platform Image** (Seleccionar imagen de plataforma), elija su imagen de máquina virtual Windows Server 2016 de implementación entre las que están disponibles en el proveedor de recursos de procesos para la nube de App Service. Haga clic en **Siguiente**.

13. En la página siguiente:
     1. Escriba el nombre de usuario y la contraseña del administrador de la máquina virtual con el rol de trabajo.
     2. Escriba el nombre de usuario y la contraseña del administrador de la máquina virtual con otros roles.
     3. Haga clic en **Siguiente**.

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image09.png)    

14. En la página de resumen:
    1. Compruebe las selecciones realizadas. Para realizar cambios, use los botones **Anterior** para visitar las páginas anteriores.
    2. Si las configuraciones son correctas, active la casilla.
    3. Para iniciar la implementación, haga clic en **Siguiente**.

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image10.png)    

15. En la página siguiente:
    1. Realice un seguimiento del progreso de la instalación. App Service en Azure Stack tarda aproximadamente 60 minutos en realizar la implementación según las selecciones predeterminadas.
    2. Después de que el instalador finalice correctamente, haga clic en **Salir**.

    ![Instalador de App Service](media/azure-stack-app-service-deploy/image11.png)    


## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validación de la instalación de App Service en Azure Stack

1. En el Portal de administración de Azure Stack, vaya a **Administración - App Service**.

2. En la información general del estado, compruebe que en **Estado** se muestra **Todos los roles están listos**.

    ![Administración de App Service](media/azure-stack-app-service-deploy/image12.png)    

## <a name="test-drive-app-service-on-azure-stack"></a>Prueba de App Service en Azure Stack

Después de implementar y registrar el proveedor de recursos de App Service, pruébelo para asegurarse de que los usuarios pueden implementar aplicaciones web y de API.

> [!NOTE]
> Debe crear una oferta que tenga el espacio de nombres Microsoft.Web dentro del plan. A continuación, debe tener una suscripción de inquilino que suscriba esta oferta. Para más información, consulte [Create offer](azure-stack-create-offer.md) (Creación de una oferta) y [Create plan](azure-stack-create-plan.md) (Creación de un plan).
>
Se *debe* tener una suscripción de inquilino para crear aplicaciones que usen App Service en Azure Stack. Las únicas funcionalidades que un administrador del servicio puede completar en el portal de administración están relacionadas con la administración del proveedor de recursos de App Service. Entre estas funcionalidades se incluyen la adición de capacidad, la configuración de orígenes de implementación y la adición de niveles de trabajo y SKU.
>
Para crear aplicaciones web, de API y Azure Functions, se debe usar el portal de inquilino y tener una suscripción de inquilino.

1. En el portal de inquilino de Azure Stack, haga clic en **Nuevo** > **Web y móvil** > **Aplicación web**.

2. En la hoja **Aplicación web**, escriba un nombre en el cuadro **Aplicación web**.

3. En **Grupo de recursos**, haga clic en **Nuevo**. Escriba un nombre en el cuadro **Grupo de recursos**.

4. Haga clic en **Plan de App Service/Ubicación** > **Create New** (Crear nuevo).

5. En la hoja **Plan de App Service**, escriba un nombre en el cuadro **Plan de App Service**.

6. Haga clic en **Plan de tarifa** > **Free-Shared** (Gratis - Compartido) o **Shared-Shared** (Compartido - Compartido) > **Seleccionar** > **Aceptar** > **Crear**.

7. En menos de un minuto, aparecerá un icono para la nueva aplicación web en el panel. Haga clic en el icono.

8. En la hoja **Aplicación web**, haga clic en **Examinar** para ver el sitio web predeterminado para esta aplicación.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementación de un sitio web de WordPress, DNN o Django (opcional)

1. En el portal de inquilino de Azure Stack, haga clic en **+**, vaya a Azure Marketplace, implemente un sitio web de Django y espere a que se complete correctamente. La plataforma web de Django usa una base de datos basada en el sistema de archivos. No requiere ningún proveedor de recursos adicional, como SQL o MySQL.

2. Si también implementó un proveedor de recursos de MySQL, puede implementar un sitio web de WordPress en Marketplace. Cuando se le pidan los parámetros de la base de datos, escriba el nombre de usuario como *User1@Server1*, con el nombre de usuario y el nombre del servidor de su elección.

3. Si también implementó un proveedor de recursos de SQL Server, puede implementar un sitio web de DNN en Marketplace. Cuando se le pidan los parámetros de la base de datos, elija una base de datos del equipo que ejecute la instancia de SQL Server que está conectado a su proveedor de recursos.

## <a name="next-steps"></a>Pasos siguientes

También puede probar otros [servicios de Plataforma como servicio (PaaS)](azure-stack-tools-paas-services.md).

- [Proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
