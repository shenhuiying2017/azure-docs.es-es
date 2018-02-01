---
title: "Introducción a Azure Automation | Microsoft Docs"
description: "En este artículo se proporciona una introducción al servicio de Azure Automation. Revisa los detalles de diseño e implementación como preparación para incorporar la oferta de Azure Marketplace."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: d6ee5c35ce9866f6106c7b5dbc51599b666c3eb1
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2018
---
# <a name="get-started-with-azure-automation"></a>Introducción a Azure Automation

Este artículo presenta los conceptos básicos relacionados con la implementación de Azure Automation. Si no está familiarizado con Azure Automation o si tiene experiencia con software de flujo de trabajo de automatización como System Center Orchestrator, puede aprender a preparar e incorporar Automation. Después de leer este artículo, estará preparado para empezar a desarrollar runbooks para satisfacer las necesidades de automatización del proceso. 


## <a name="automation-architecture-overview"></a>Información general sobre la arquitectura de Automation

![Información general sobre Azure Automation](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure Automation es una aplicación de software como servicio (SaaS) que proporciona un entorno de varios inquilinos escalable y de confianza en el que puede usar runbooks para automatizar procesos. Puede utilizar Desired State Configuration (DSC) en Azure, otros servicios en la nube o en un entorno local para administrar los cambios de configuración en los sistemas Windows y Linux. Las entidades de la cuenta de Automation, como runbooks, recursos y cuentas de ejecución, están aisladas de otras cuentas de Automation dentro de la suscripción y de otras suscripciones.  

Los runbooks que ejecuta en Azure se ejecutan en espacios aislados de Automation. Los espacios aislados se alojan en máquinas virtuales de plataforma como servicio (PaaS) de Azure. 

Los espacios aislados de Automation proporcionan aislamiento de inquilinos para todos los aspectos de la ejecución del runbook, como módulos, almacenamiento, memoria, comunicación de red y flujos de trabajo. Este rol se administra mediante el servicio. No se puede acceder al rol o administrarlo desde la cuenta de Automation o Azure.         

Para automatizar la implementación y administración de los recursos en su centro de datos local o de otros servicios en la nube, después de crear una cuenta de Automation, puede designar una o varias máquinas virtuales para ejecutar el rol [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Cada rol Hybrid Runbook Worker necesita una cuenta de Automation y que se instale Microsoft Management Agent. El agente debe tener una conexión a un área de trabajo de Azure Log Analytics. Puede usar Log Analytics para arrancar la instalación, mantener Microsoft Management Agent y supervisar la funcionalidad del rol Hybrid Runbook Worker. Azure Automation realiza la entrega de los runbooks y la instrucción para ejecutarlos.

Puede implementar varios roles Hybrid Runbook Worker. Utilice roles Hybrid Runbook Worker para proporcionar alta disponibilidad para los runbooks y para el equilibrio de carga de los trabajos de runbook. En algunos casos, puede dedicar los trabajos de runbook a cargas de trabajo o entornos específicos. En el rol Hybrid Runbook Worker, Microsoft Monitoring Agent inicia la comunicación con el servicio Automation a través del puerto TCP 443. Los roles Hybrid Runbook Worker no tiene ningún requisito de firewall de entrada.  

Quizá quiera que un runbook que se está ejecutando en un rol Hybrid Runbook Worker realice tareas de administración en otros equipos o servicios de su entorno. En ese escenario, el runbook también podría necesitar acceder a otros puertos. Si las directivas de seguridad de las tecnologías de la información no permiten que los equipos de su red se conecten a Internet, revise la [puerta de enlace de OMS](../log-analytics/log-analytics-oms-gateway.md). La puerta de enlace de Operations Management Suite (OMS) actúa como proxy para Hybrid Runbook Worker. Recopila el estado del trabajo y recibe la información de configuración desde la cuenta de Automation.

Los runbooks que se ejecutan en un rol Hybrid Runbook Worker se ejecutan en el contexto de la cuenta de sistema local en el equipo. Se recomienda un contexto de seguridad al realizar acciones administrativas en la máquina Windows local. Si desea que el runbook ejecute tareas en recursos que están fuera de la máquina local, podría necesitar definir recursos de credenciales seguras en la cuenta de Automation. Puede acceder a recursos de credenciales seguras desde el runbook y usarlos para autenticarse con el recurso externo. Puede usar los recursos [Credenciales](automation-credentials.md), [Certificado](automation-certificates.md) y [Conexión](automation-connections.md) de su runbook. Utilice los recursos con cmdlets que puede usar para especificar las credenciales para autenticarse en ellos.

Se pueden aplicar configuraciones de DSC que se almacenan en Azure Automation para máquinas virtuales. Otras máquinas físicas y virtuales pueden solicitar configuraciones del servidor de extracción de DSC de Automatización. No es necesario implementar una infraestructura para ayudar al servidor de extracción de DSC de Automatización a administrar las configuraciones de los sistemas locales físicos o virtuales de Windows y Linux. Solo necesita acceso a Internet de salida desde cada sistema que administrará con DSC de Automatización. La comunicación se produce a través del puerto TCP 443 hacia el servicio OMS.   

## <a name="prerequisites"></a>requisitos previos

### <a name="automation-dsc"></a>DSC de Automation
Puede usar DSC de Automatización para administrar estas máquinas:

* Máquinas virtuales de Azure (clásico) con Windows o Linux.
* Máquinas virtuales de Azure con Windows o Linux.
* Máquinas virtuales de Amazon Web Services (AWS) con Windows o Linux.
* Equipos físicos y virtuales con Windows, que se encuentran en un entorno local o en una nube que no sea Azure ni AWS.
* Equipos físicos y virtuales con Linux, que se encuentran en un entorno local o en una nube que no sea Azure ni AWS.

Para las máquinas Windows, tiene que estar instalada la versión más reciente de Windows Management Framework (WMF) 5. Para las máquinas Linux, tiene que estar instalada la versión más reciente del [agente DSC de PowerShell para Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150). El agente DSC de PowerShell usa WMF 5 para comunicarse con Automation. 

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
Al designar un equipo para ejecutar trabajos de runbooks híbridos, el equipo debe cumplir los siguientes requisitos previos:

* Windows Server 2012 o posterior.
* Windows PowerShell 4.0 o posterior. Para mayor confiabilidad, se recomienda Windows PowerShell 5.0. Puede [descargar la nueva versión](https://www.microsoft.com/download/details.aspx?id=50395) en el Centro de descargas de Microsoft.
* .NET Framework 4.6.2 o posterior.
* Dos núcleos como mínimo.
* Un mínimo de 4 GB de RAM.

### <a name="permissions-required-to-create-an-automation-account"></a>Permisos necesarios para crear una cuenta de Automation
Para crear o actualizar una cuenta de Automation y completar las tareas que se describen en este artículo, debe tener los siguientes privilegios y permisos:   
 
* Para crear una cuenta de Automation, la cuenta de usuario de Azure Active Directory (Azure AD) debe agregarse a un rol con permisos equivalentes al rol de propietario para los recursos **Microsoft.Automation**. Para más información, consulte [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md).  
* En Azure Portal, en **Azure Active Directory** > **ADMINISTRAR** > **Registros de aplicaciones**, si **Registros de aplicaciones** está establecido en **Sí**, los usuarios sin derechos administrativos del inquilino de Azure AD pueden [registrar aplicaciones de Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Si **Registros de aplicaciones** está establecido en **No**, el usuario que realice esta acción debe ser administrador global de Azure AD. 

Si no es miembro de la instancia de Active Directory de la suscripción antes de que le agreguen al rol de administrador global/coadministrador de esta última, se le agregará a Active Directory como invitado. En este escenario, verá este mensaje en la página **Agregar cuenta de Automation**: "No tiene permisos para crear". 

Si un usuario se agrega primero al rol de administrador global/coadministrador, puede quitarlo de la instancia de Active Directory de la suscripción y volver a agregarlo para convertirlo en un usuario completo en Active Directory.

Para comprobar los roles de usuario:
1. En Azure Portal, vaya al panel **Azure Active Directory**.
2. Seleccione **Usuarios y grupos**.
3. Seleccione **Todos los usuarios**. 
4. Después de seleccionar un usuario específico, seleccione **Perfil**. El valor del atributo **Tipo de usuario** del perfil de los usuarios no debería ser **Invitado**.

## <a name="authentication-planning"></a>Planeamiento de la autenticación
En Azure Automation, puede automatizar las tareas en recursos que están en Azure, en un entorno local y en otros servicios en la nube. Para que un runbook realice las acciones necesarias, debe tener permisos para acceder de forma segura a los recursos. Debe tener los derechos mínimos necesarios en la suscripción.  

### <a name="what-is-an-automation-account"></a>¿Qué es una cuenta de Automation? 
Todas las tareas de automatización que realice en los recursos mediante los cmdlets de Azure Automation se autentican en Azure con una autenticación basada en credenciales de identidad organizativa de Azure AD.  La cuenta de Automation es independiente de la cuenta que usa para iniciar sesión en el portal para configurar y usar los recursos de Azure. 

Los siguientes recursos se incluyen junto con una cuenta de Automation:

* **Certificados**. Contiene un certificado utilizado para la autenticación desde un runbook o configuración de DSC. También puede agregar certificados.
* **Conexiones**. Contiene la información de autenticación y configuración necesaria para conectarse a un servicio externo o a una aplicación desde un runbook o una configuración de DSC.
* **Credenciales**. Contiene un objeto **PSCredential** que tiene credenciales de seguridad, como un nombre de usuario y una contraseña. Se necesitan las credenciales para autenticarse desde un runbook o configuración de DSC.
* **Módulos de integración**. Módulos de PowerShell que se incluyen junto con una cuenta de Automation. Utilice los módulos de PowerShell para ejecutar cmdlets en runbooks y configuraciones de DSC.
* **Programaciones**. Contiene programaciones que inician o detienen un runbook en un momento especificado, incluidas las frecuencias de repetición.
* **Variables**. Contiene valores que están disponibles desde un runbook o configuración de DSC.
* **Configuraciones de DSC**. Scripts de PowerShell que describen cómo configurar una característica o valor del sistema operativo o instalar una aplicación en un equipo Windows o Linux.  
* **Runbooks**. Conjunto de tareas que realizan algún proceso automatizado en Automation basado en Windows PowerShell.    

Los recursos de automatización para cada cuenta de Automation están asociados a una sola región de Azure. Sin embargo, puede usar las cuentas de Automation para administrar todos los recursos de su suscripción. Cree cuentas de Automation en distintas regiones si tiene directivas que requieren que los datos y recursos se aíslen en una región específica.

Al crear una cuenta de Automation en Azure Portal, se crean automáticamente dos entidades de autenticación:

* **Cuenta de ejecución**. Esta cuenta hace las siguientes tareas:
  - Crea una entidad de servicio en Azure AD.
  - Crea un certificado.
  - Asigna el control de acceso basado en rol (RBAC) del colaborador, que administra recursos de Azure Resource Manager mediante runbooks.
* **Cuenta de ejecución clásica**. Esta cuenta carga un certificado de administración. El certificado se usa para administrar los recursos clásicos mediante runbooks.

RBAC está disponible con Resource Manager para conceder acciones permitidas a una cuenta de ejecución y una cuenta de usuario de Azure AD. También puede utilizar RBAC para autenticar esa entidad de servicio. Para más información y ayuda para desarrollar su modelo de administración de permisos de Automation, consulte el artículo [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Métodos de autenticación
La tabla siguiente resume los métodos de autenticación que puede usar para cada entorno admitido por Azure Automation.

| Método | Environment 
| --- | --- | 
| Cuenta de ejecución y cuenta de ejecución de Azure clásico |Implementación de Azure Resource Manager e implementación clásica |  
| Cuenta de usuario de Azure AD |Implementación de Azure Resource Manager e implementación clásica |  
| Autenticación de Windows |Centro de datos local u otro proveedor de servicios en la nube con el rol Hybrid Runbook Worker |  
| Credenciales de Amazon Web Services |Amazon Web Services |  

Los siguientes artículos proporcionan una introducción y pasos de implementación para configurar la autenticación de esos entornos. Se describe en los artículos mediante una cuenta nueva y una existente que se dedica a ese entorno. 
* [Creación de una cuenta independiente de Azure Automation](automation-create-standalone-account.md)
* [Autenticación de runbooks con la implementación clásica de Azure y Resource Manager](automation-create-aduser-account.md)
* [Autenticación de runbooks con Amazon Web Services](automation-config-aws-account.md)
* [Actualización de una cuenta de ejecución de Automation](automation-create-runas-account.md)

Para la cuenta de ejecución de Azure y la cuenta de ejecución de Azure clásica, [Actualización de una cuenta de ejecución de Automation](automation-create-runas-account.md) describe cómo actualizar la cuenta de Automation existente con las cuentas de ejecución desde el portal. También describe cómo usar PowerShell si la cuenta de Automation no se configuró originalmente con una cuenta de ejecución o de ejecución clásica. Puede crear una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado emitido por su entidad de certificación (CA) empresarial. Revise [Actualización de una cuenta de ejecución de Automation](automation-create-runas-account.md) para aprender a crear las cuentas mediante esta configuración.     
 
## <a name="network-planning"></a>Planeamiento de la red
Para que el rol Hybrid Runbook Worker se conecte a OMS y se registre, debe tener acceso al número de puerto y a las direcciones URL que se describen en esta sección. Y esto aparte de los [puertos y las direcciones URL necesarios para que Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md) se conecte a OMS. 

Si se usa un servidor proxy para realizar la comunicación entre el agente y el servicio OMS, asegúrese de que es posible acceder a los recursos adecuados. Si usa un firewall para restringir el acceso a Internet, tendrá que configurarlo para que permita el acceso.

Los siguientes puertos y direcciones URL son necesarios para que el rol Hybrid Runbook Worker se comunique con Automation:

* Puerto: solo se requiere el puerto TCP 443 para el acceso a Internet.
* URL global: *.azure-automation.net.

Si tiene una cuenta de Automation definida para una región específica, puede restringir la comunicación con ese centro de datos regional. En la tabla siguiente se proporciona el registro de DNS para cada región.

| **Región** | **Registro de DNS** |
| --- | --- |
| Centro-Sur de EE. UU |scus-jobruntimedata-prod-su1.azure-automation.net |
| Este de EE. UU. 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Centro occidental de EE.UU. | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Europa occidental |we-jobruntimedata-prod-su1.azure-automation.net |
| Europa del Norte |ne-jobruntimedata-prod-su1.azure-automation.net |
| Centro de Canadá |cc-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste de Asia |sea-jobruntimedata-prod-su1.azure-automation.net |
| India Central |cid-jobruntimedata-prod-su1.azure-automation.net |
| Este de Japón |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste de Australia |ase-jobruntimedata-prod-su1.azure-automation.net |
| Sur del Reino Unido 2 | uks-jobruntimedata-prod-su1.azure-automation.net |
| Gobierno de EE. UU. - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us |

Para obtener una lista de direcciones IP de regiones en lugar de nombres de regiones, descargue el archivo XML [Direcciones IP de los centros de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) desde el Centro de descarga de Microsoft. 

> [!NOTE]
> El archivo XML de direcciones IP de los centros de datos de Azure enumera los intervalos de direcciones IP que se usan en los centros de datos de Microsoft Azure. En el archivo se incluyen Compute, SQL y los intervalos de almacenamiento. 
>
>Semanalmente, se publica un archivo actualizado. El archivo refleja los intervalos implementados actualmente y los próximos cambios en los intervalos IP. Los nuevos intervalos que aparecen en el archivo no se utilizan en los centros de datos durante al menos una semana. 
>
> Descargar el archivo XML nuevo cada semana es una buena idea. A continuación, actualice el sitio para identificar correctamente los servicios que se ejecutan en Azure. Los usuarios de Azure ExpressRoute deberían observar que este archivo se usa para actualizar la publicidad de Border Gateway Protocol (BGP) del espacio de Azure la primera semana de cada mes. 
> 

## <a name="creating-an-automation-account"></a>Crear una cuenta de Automation

La tabla siguiente presenta métodos para crear una cuenta de Automation en Azure Portal. La tabla describe cada tipo de experiencia de implementación y las diferencias entre ellas.  

|Método | DESCRIPCIÓN |
|-------|-------------|
| Seleccione **Automation & Control** en Azure Marketplace | Una oferta de Azure Marketplace crea una cuenta de Automation y un área de trabajo de OMS vinculadas entre sí en el mismo grupo de recursos y región. La integración con OMS también ofrece la ventaja de utilizar Log Analytics para supervisar y analizar el estado de los trabajos de runbook y las secuencias de trabajos en el tiempo. También puede usar las características avanzadas de Log Analytics para escalar o investigar los problemas. La oferta implementa las soluciones **Change Tracking** y **Update Management**, que están habilitadas de forma predeterminada. |
| Seleccione **Automation** en Marketplace | Este método crea una cuenta de Automation en un grupo de recursos nuevos o existentes que no está vinculado a un área de trabajo de OMS. No incluye ninguna solución disponible desde la oferta **Automation & Control**. Este método consiste en una configuración básica que le hace una introducción sobre Automation. Puede ayudarle a aprender a escribir runbooks y configuraciones de DSC y a aprender cómo utilizar las funcionalidades del servicio. |
| Seleccione soluciones de **Administración** | Si selecciona una solución de **Administración**, que incluya [Update Management](../operations-management-suite/oms-solution-update-management.md), [Start/Stop VMs during off hours](automation-solution-vm-management.md) o [Change Tracking](../log-analytics/log-analytics-change-tracking.md), la solución le pedirá que seleccione una cuenta de Automation existente y un área de trabajo de OMS. La solución le ofrece la opción de crear una cuenta de Automation y un área de trabajo de OMS, según sea necesario para la solución, para implementarse en su suscripción. |

### <a name="create-an-automation-account-thats-integrated-with-oms"></a>Creación de una cuenta de Automation integrada con OMS
Para incorporar Automation, le recomendamos que seleccione la oferta **Automation & Control** en Marketplace. Con este método se crea una cuenta de Automation y se establece la integración con un área de trabajo de OMS. Cuando utiliza este método, también tiene la opción de instalar las soluciones de administración que están disponibles con la oferta.  

[Creación de una cuenta independiente de Automation](automation-create-standalone-account.md) le mostrará paso a paso el proceso de creación de una cuenta Automation y un área de trabajo de OMS mediante la incorporación de la oferta **Automation & Control**. Puede aprender cómo crear una cuenta independiente de Automation para hacer pruebas u obtener una versión preliminar del servicio.  

Para crear una cuenta de Automation y un área de trabajo OMS mediante la oferta de Marketplace de **Automation & Control**:

1. Inicie sesión en Azure Portal con una cuenta que sea miembro del rol Administradores de la suscripción y coadministrador de la suscripción.
2. Seleccione **Nuevo**.<br><br> ![Seleccione Nuevo en Azure Portal](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Busque **Automation**. En los resultados de la búsqueda, seleccione **Automation & Control**.<br><br> ![Busque y seleccione Automation & Control en Azure Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   
4. Revise la descripción de la oferta y, a continuación, seleccione **Crear**.  
5. En **Automation & Control**, seleccione **Área de trabajo de OMS**. En **Áreas de trabajo de OMS**, seleccione un área de trabajo de OMS que esté vinculada a la suscripción de Azure en la que se encuentra la cuenta de Automation. Si no tiene ninguna área de trabajo de OMS, seleccione **Crear área de trabajo nueva**. En **Área de trabajo de OMS**: 
  1. Para **Área de trabajo de OMS**, escriba un nombre para la nueva área de trabajo.
  2. En **Suscripción**, seleccione una suscripción a la que se vinculará. Si la selección predeterminada no es la suscripción que desea usar, seleccione la suscripción en la lista desplegable.
  3. En **Grupo de recursos**, puede crear un grupo de recursos o seleccionar uno existente.  
  4. En **Ubicación**, seleccione una región. Para más información, consulte en qué [regiones está disponible Azure Automation](https://azure.microsoft.com/regions/services/). Las soluciones se ofrecen en dos niveles: gratis y nivel de pago por nodo (OMS). El nivel gratis tiene limitada la cantidad de datos que se recopila diariamente, el período de retención y los minutos de tiempo de ejecución de los trabajos de runbook. El nivel de pago por nodos (OMS) no tiene ningún límite en la cantidad de datos que se recopila a diario.  
  5. Seleccione **Cuenta de Automation**.  Si crea una nueva área de trabajo de OMS, debe crear también una cuenta de Automation que esté asociada con ella. Incluya su suscripción de Azure, el grupo de recursos y la región. 
    1. Seleccione **Crear una cuenta de Automation**.
    2. En **Cuenta de Automation**, en el campo **Nombre**, escriba el nombre de la cuenta de Automation.
    Las restantes opciones se rellenan automáticamente en función del área de trabajo de OMS seleccionado. No se pueden modificar estas opciones. 
    3. Una cuenta de ejecución de Azure es el método de autenticación predeterminado de la oferta. Después de seleccionar **Aceptar**, se validan las opciones de configuración y se crea la cuenta de Automation. Para realizar el seguimiento de su progreso, seleccione **Notificaciones** en el menú. 
    4. En caso contrario, seleccione una cuenta de ejecución de Automation existente. La cuenta que seleccione no puede estar vinculada a otra área de trabajo de OMS. Si es así, aparece un mensaje de notificación. Si la cuenta está ya vinculada a un área de trabajo de OMS, seleccione una cuenta de ejecución de Automation diferente o cree una.
    5. Después de escribir o seleccionar la información necesaria, seleccione **Crear**. Se comprueba la información y se crean las cuentas de Automation y de ejecución. Se le redirigirá al panel **Área de trabajo de OMS** automáticamente.  
6. Después de escribir o seleccionar la información necesaria en el panel **Área de trabajo de OMS**, seleccione **Crear**.  Se comprueba la información y se crea el área de trabajo. Para realizar el seguimiento de su progreso, seleccione **Notificaciones** en el menú. Volverá al panel **Agregar solución**.  
7. En la configuración **Automation & Control**, confirme que desea instalar las soluciones recomendadas preseleccionadas. Si cambia cualquiera de las opciones predeterminadas, puede instalar las soluciones de forma individual más adelante.  
8. Seleccione **Crear** para continuar con la incorporación de Automation y de un área de trabajo de OMS. Todas las configuraciones se validan y, a continuación, Azure intenta implementar la oferta en la suscripción. Este proceso podría tardar varios segundos. Para realizar el seguimiento de su progreso, seleccione **Notificaciones** en el menú. 

Después de que se incorpore la oferta, puede realizar las tareas siguientes:
* Empiece a crear runbooks.
* Trabaje con las soluciones de administración que ha habilitado.
* Implemente un rol [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Empiece a trabajar con [Log Analytics](https://docs.microsoft.com/azure/log-analytics) para recopilar datos generados por recursos en su entorno de nube o local.   

## <a name="next-steps"></a>pasos siguientes
* Compruebe que la nueva cuenta de Automation puede autenticarse en recursos de Azure. Para más información, consulte [Comprobación de la autenticación con la cuenta de ejecución de Azure Automation](automation-verify-runas-authentication.md).
* Aprenda cómo comenzar a crear runbooks y otras consideraciones relacionadas antes de empezar a crear. Para más información, consulte [Tipos de runbooks de Automation](automation-runbook-types.md).


