---
title: "Introducción a Azure Automation | Microsoft Docs"
description: "Este artículo proporciona una introducción al servicio Azure Automation, y se revisan el diseño y los detalles de implementación como preparación para incorporar las ofertas de Azure Marketplace."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: fad13053895c5d6e3c41835fea3cf0bdd3380cd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-automation"></a>Introducción a Azure Automation

Esta guía de introducción presenta los conceptos básicos relacionados con la implementación de Azure Automation. Si no está familiarizado con Azure Automation o si tiene experiencia con software de flujo de trabajo de automatización como System Center Orchestrator, esta guía le ayudará a comprender cómo preparar e incorporar Automation.  Después, estará preparado para empezar a desarrollar runbooks que ayuden en las necesidades de automatización del proceso. 


## <a name="automation-architecture-overview"></a>Información general sobre la arquitectura de Automation

![Información general sobre Azure Automation](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure Automation es una aplicación de software como servicio (SaaS) que proporciona un entorno multiinquilino escalable y confiable para automatizar procesos con runbooks y administrar los cambios de configuración en sistemas Windows y Linux mediante la configuración del estado deseado (DSC) en Azure, otros servicios en la nube, o locales. Las entidades contenidas dentro de la cuenta de Automation, como runbooks, recursos y cuentas de ejecución están aisladas de otras cuentas de Automation dentro de la suscripción y de otras suscripciones.  

Los runbooks que ejecuta en Azure se ejecutan en espacios aislados de Automation que se hospedan en las máquinas virtuales de la plataforma como servicio (PaaS) de Azure.  Los espacios aislados de Automation proporcionan aislamiento de inquilinos para todos los aspectos de la ejecución del runbook: módulos, almacenamiento, memoria, comunicación de red, flujos de trabajo, etc. El servicio administra este rol, al que no puede acceder desde su cuenta de Azure o Azure Automation para controlarlo.         

Para automatizar la implementación y administración de los recursos en su centro de datos local o de otros servicios en la nube, después de crear una cuenta de Automation, puede designar una o varias máquinas para ejecutar el rol de [Hybrid Runbook Worker (HRW)](automation-hybrid-runbook-worker.md).  Cada HRW requiere el Agente de administración de Microsoft con una conexión a un área de trabajo de Log Analytics y una cuenta de Automation.  Log Analytics se utiliza para arrancar la instalación, mantener el Agente de administración de Microsoft y supervisar la funcionalidad de HRW.  Azure Automation realiza la entrega de los runbooks y la instrucción para ejecutarlos.

Puede implementar varios HRW para proporcionar alta disponibilidad para sus runbooks, equilibrar la carga de los trabajos de runbook y, en algunos casos, dedicarlos a cargas de trabajo o entornos determinados.  El agente Microsoft Monitoring Agent de HRW permite iniciar la comunicación con el servicio Automation a través del puerto TCP 443 y no hay ningún requisito de firewall de entrada.  Una vez que el runbook se esté ejecutando en un HRW dentro del entorno y si desea que el runbook realice tareas de administración en otros equipos o servicios de este, puede que el runbook necesite tener acceso a otros puertos.  Si las directivas de seguridad de TI no permiten que los equipos de su red se conecten a Internet, consulte el artículo [Puerta de enlace de OMS](../log-analytics/log-analytics-oms-gateway.md), que actúa como un proxy para que el HRW pueda recopilar el estado del trabajo y recibir información de configuración desde la cuenta de Automation.

Los runbooks que se ejecutan en un HRW, se ejecutan en el contexto de la cuenta del sistema local del equipo, el cual es el contexto de seguridad recomendado cuando se realizan actividades administrativas en la máquina Windows local. Si desea que el runbook ejecute tareas en recursos fuera de la máquina local, puede que necesite definir recursos de credenciales seguros en la cuenta de Automation a la que puede acceder desde el runbook y los use para autenticarse con el recurso externo. Puede usar los recursos [Credencial](automation-credentials.md), [Certificado](automation-certificates.md) y [Conexión](automation-connections.md) en el runbook con cmdlets que le permitan especificar las credenciales, para que así pueda autenticarlos.

Las configuraciones de DSC almacenadas en Azure Automation pueden aplicarse directamente a máquinas virtuales de Azure. Otras máquinas físicas y virtuales pueden solicitar las configuraciones del servidor de extracción de DSC de Azure Automation.  Para administrar las configuraciones de sus sistemas locales de Windows y Linux físicos o virtuales, no es necesario implementar ninguna infraestructura que admita el servidor de extracción de DSC de Automation, solo un acceso de salida a Internet desde cada sistema que se administrará mediante el DSC de Automation, con comunicación a través del puerto TCP 443 al servicio OMS.   

## <a name="prerequisites"></a>Requisitos previos

### <a name="automation-dsc"></a>DSC de Automation
El DSC de Azure Automation puede usarse para administrar diversas máquinas:

* Máquinas virtuales de Azure (modelo clásico) con Windows o Linux
* Máquinas virtuales de Azure con Windows o Linux
* Máquinas virtuales de Amazon Web Services (AWS) con Windows o Linux
* Equipos físicos o virtuales con Windows, locales o en una nube que no sea Azure ni AWS
* Equipos físicos o virtuales con Linux, locales o en una nube que no sea Azure ni AWS

La versión más reciente de WMF 5 debe estar instalada para el agente DSC de PowerShell para que Windows pueda comunicarse con Azure Automation. La versión más reciente del [agente DSC de PowerShell para Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) debe estar instalada para que sea posible la comunicación con el servicio Azure Automation.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
Al designar un equipo para que ejecute trabajos híbridos de runbook, debe tener lo siguiente:

* Windows Server 2012 o superior
* Windows PowerShell 4.0 o posterior.  Se recomienda instalar Windows PowerShell 5.0 en los equipos para una mayor confiabilidad. Puede descargar la nueva versión en el [Centro de descargas de Microsoft](https://www.microsoft.com/download/details.aspx?id=50395).
* Dos núcleos como mínimo
* 4 GB de RAM como mínimo

### <a name="permissions-required-to-create-automation-account"></a>Permisos necesarios para crear la cuenta de Automation
Para crear o actualizar una cuenta de Automation, debe tener los siguientes privilegios y permisos, necesarios para completar este tema.   
 
* Para crear una cuenta de Automation, debe agregar la cuenta de usuario de AD a un rol con los privilegios equivalentes a los del rol Propietario para los recursos de Microsoft Automation, como se resalta en el artículo [Control de acceso basado en roles en Azure Automation](automation-role-based-access-control.md).  
* Los usuarios sin privilegios de administrador en el inquilino de Azure AD pueden **registrar aplicaciones de AD** si los registros de aplicaciones se configuran en [Sí](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Si se establecen en **No**, el usuario que realice esta acción debe ser administrador global de Azure AD. 

Si no es miembro de la instancia de Active Directory de la suscripción antes de que le agreguen al rol de administrador global/coadministrador de esta última, se le agregará a Active Directory como invitado. En este caso, recibirá una advertencia tipo "No tiene permisos para crear..." en la hoja **Agregar cuenta de Automation**. Los usuarios que primero se agregaron al rol de administrador global/coadministrador se pueden quitar de la instancia de Active Directory de la suscripción y volverse a agregar para convertirlos en usuarios completos en Active Directory. Para comprobar esta situación, en el panel de **Azure Active Directory** de Azure Portal, seleccione **Usuarios y grupos**, **All Users** y, después de seleccionar el usuario específico, seleccione **Perfil**. El valor del atributo **Tipo de usuario** del perfil de los usuarios no debería ser **Invitado**.

## <a name="authentication-planning"></a>Planeamiento de la autenticación
Azure Automation le permite automatizar tareas en recursos de Azure, locales y de otros proveedores de servicios en la nube.  Para que un runbook realice las acciones necesarias, debe tener permiso de acceso seguro a los recursos con los derechos mínimos necesarios en la suscripción.  

### <a name="what-is-an-automation-account"></a>¿Qué es una cuenta de Automation? 
Todas las tareas de automatización que realice con recursos mediante los cmdlets de Azure en Azure Automation deben autenticarse en Azure con una autenticación basada en credenciales de identidad organizativa de Azure Active Directory.  La cuenta de Automation es independiente de la cuenta que usa para iniciar sesión en el portal para configurar y usar los recursos de Azure.  Los recursos de Automation que se incluyen con una cuenta son los siguientes:

* **Certificados**: contiene un certificado utilizado para la autenticación desde un runbook o configuración de DSC o para agregarlos.
* **Conexiones**: contiene la información de autenticación y configuración necesaria para conectarse a un servicio externo o a una aplicación desde un runbook o una configuración de DSC.
* **Credenciales**: es un objeto PSCredential que contiene credenciales de seguridad, como el nombre de usuario y contraseña necesarios para autenticarse desde un runbook o una configuración de DSC.
* **Módulos de integración**: son módulos de PowerShell incluidos con una cuenta de Azure Automation para hacer uso de cmdlets en runbooks y configuraciones de DSC.
* **Programaciones**: contiene programaciones que inician o detienen un runbook en un momento especificado, incluidas las frecuencias de repetición.
* **Variables**: contiene valores que están disponibles desde un runbook o una configuración de DSC.
* **Configuraciones DSC**: son scripts de PowerShell que describen cómo configurar una característica u opción del sistema operativo o instalar una aplicación en un equipo Windows o Linux.  
* **Runbooks**: son un conjunto de tareas que realizan algún proceso automatizado en Azure Automation basado en Windows PowerShell.    

Los recursos de Automation de cada cuenta de Automation están asociados con una sola región de Azure, pero las cuentas de Automation pueden administrar recursos en su suscripción. Cree cuentas de Automation en distintas regiones si tiene directivas que requieren que los datos y recursos se aíslen en una región específica.

> [!NOTE]
> A las cuentas de automatización y los recursos que contienen, que se crean en Azure Portal, no se puede acceder desde el Portal de Azure clásico. Si desea administrar estas cuentas o sus recursos con Windows PowerShell, debe usar los módulos del Administrador de recursos de Azure.
> 

Al crear una cuenta de Automation en Azure Portal, crea automáticamente dos entidades de autenticación:

* Una cuenta de ejecución. Esta cuenta crea una entidad de servicio en Azure Active Directory (Azure AD) y un certificado. También asigna el control de acceso basado en rol (RBAC) del colaborador, que administra recursos de Resource Manager mediante runbooks.
* Una cuenta de ejecución clásica. Esta cuenta carga un certificado de administración, que se usa para administrar recursos clásicos mediante runbooks.

El control de acceso basado en rol está disponible en Azure Resource Manager para conceder las acciones permitidas a una cuenta de usuario de Azure AD y a una cuenta de ejecución, y para autenticar dicha entidad de servicio.  Para más información que le ayude a desarrollar su modelo de administración de permisos de Automation, consulte el [artículo Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Métodos de autenticación
La tabla siguiente resume los diferentes métodos de autenticación para cada entorno admitido por Azure Automation.

| Método | Environment 
| --- | --- | 
| Cuenta de ejecución y cuenta de ejecución de Azure clásico |Implementación de Azure Resource Manager e implementación clásica |  
| Cuenta de usuario de Azure AD |Implementación de Azure Resource Manager e implementación clásica |  
| Autenticación de Windows |Centro de datos local u otro proveedor en la nube con Hybrid Runbook Worker |  
| Credenciales de AWS |Amazon Web Services |  

En la sección **Procedimientos\Autenticación y seguridad** puede encontrar artículos de ayuda que proporcionan un resumen y pasos de implementación para configurar la autenticación para esos entornos, ya sea con una cuenta existente o una nueva dedicada para ese entorno.  Para la cuenta de ejecución de Azure y la cuenta de ejecución de Azure clásica, el tema [Actualización de una cuenta de ejecución de Automation](automation-create-runas-account.md) describe cómo actualizar la cuenta de Automation existente con las cuentas de ejecución desde el portal o mediante PowerShell si no se configuró originalmente con una cuenta de ejecución o una cuenta de ejecución de Azure clásica. Si desea crear una cuenta de ejecución y una cuenta de ejecución de Azure clásica con un certificado emitido por la entidad de certificación (CA), revise este artículo para obtener información sobre cómo crear cuentas con esta configuración.     
 
## <a name="network-planning"></a>Planeamiento de red
Para que la instancia de Hybrid Runbook Worker se conecte a Microsoft Operations Management Suite (OMS) y se registre en él, debe tener acceso al número de puerto y a las direcciones URL que se describen a continuación.  Y esto aparte de los [puertos y las direcciones URL necesarios para que Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agents.md#network) se conecte a OMS. Si se usa un servidor proxy para realizar la comunicación entre el agente y el servicio de OMS, debe asegurarse de que es posible tener acceso a los recursos adecuados. Si usa un firewall para restringir el acceso a Internet, deberá configurarlo para que permita el acceso.

A continuación se indican los puertos y las direcciones URL que son necesarios para que Hybrid Runbook Worker se comunique con Automation.

* Puerto: solo se requiere el puerto TCP 443 para el acceso a Internet
* URL global: *.azure-automation.net

Si tiene una cuenta de Automation definida para una región específica y quiere restringir la comunicación con ese dentro de datos regional, en la tabla siguiente se proporciona el registro DNS para cada región.

| **Región** | **Registro DNS** |
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

Para obtener una lista de direcciones IP en lugar de nombres, descargue y revise el archivo XML [Intervalos de direcciones IP de los centros de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653) desde el Centro de descarga de Microsoft. 

> [!NOTE]
> Este archivo contiene los intervalos de direcciones IP (incluidos los intervalos de Compute, SQL y Storage) utilizados en los centros de datos de Microsoft Azure. Semanalmente, se publica un archivo actualizado que refleja los intervalos implementados actualmente y los próximos cambios en los intervalos de direcciones IP. Los nuevos intervalos que aparecen en el archivo no se utilizarán en los centros de datos durante al menos una semana. Descargue el nuevo archivo XML cada semana y realizar los cambios necesarios en su sitio para identificar correctamente los servicios que se ejecutan en Azure. Los usuarios de Express Route observarán que este archivo se usa para actualizar la publicidad de BGP del espacio de Azure en la primera semana de cada mes. 
> 

## <a name="creating-an-automation-account"></a>Creación de una cuenta de Automation

Hay varias formas de crear una cuenta de Automation en Azure Portal.  La tabla siguiente presenta cada tipo de experiencia de implementación y las diferencias entre ellas.  

|Método | Descripción |
|-------|-------------|
| Seleccionar Automation and Control en Marketplace | Una oferta que crea una cuenta de Automation y un área de trabajo de OMS vinculadas entre sí en el mismo grupo de recursos y región.  La integración con OMS también incluye la ventaja de utilizar Log Analytics para supervisar y analizar el estado de los trabajos de runbook y las secuencias de trabajos en el tiempo y utilizar características avanzadas para remitir o investigar los problemas. La oferta también implementa las soluciones de seguimiento de cambios y administración de actualizaciones, que están habilitadas de forma predeterminada. |
| Seleccionar Automation en Marketplace | Crea una cuenta de Automation en un grupo de recursos nuevos o ya existente que no está vinculada a un área de trabajo de OMS y no incluye ninguna solución disponible de la oferta de Automation and Control. Se trata de una configuración básica que le sirve de introducción a Automation y puede ayudarle a aprender a escribir runbooks, configuraciones de DSC y a utilizar las funcionalidades del servicio. |
| Soluciones de administración seleccionadas | Si selecciona una solución como **[Administración de actualizaciones](../operations-management-suite/oms-solution-update-management.md)**, **[Iniciar/Detener las VM fuera de las horas de trabajo](automation-solution-vm-management.md)** o **[Seguimiento de cambios](../log-analytics/log-analytics-change-tracking.md)** se le pedirá que seleccione un área de trabajo de Automation y OMS existente o se le ofrecerá la opción de crear ambas según sea necesario para que la solución se implemente en su suscripción. |

Este tema explica cómo crear una cuenta de Automation y un área de trabajo de OMS mediante la incorporación de la oferta de Automation and Control.  Para crear una cuenta de Automation independiente de prueba o para obtener una vista previa del servicio, revise el artículo siguiente sobre [Creación de una cuenta independiente de Automation](automation-create-standalone-account.md).  

### <a name="create-automation-account-integrated-with-oms"></a>Creación de una cuenta de Automation integrada con OMS
Es el método recomendado para incorporar Automation seleccionando la oferta de Automation and Control de Marketplace.  Esto crea una cuenta de Automation y establece la integración con un área de trabajo de OMS, incluida la opción para instalar las soluciones de administración que están disponibles con la oferta.  

1. Inicie sesión en Azure Portal con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.

2. Haga clic en **Nuevo**.<br><br> ![Seleccione la opción Nuevo en Azure Portal](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. Busque **Automation** y, a continuación, en los resultados de la búsqueda seleccione **Automation and Control***.<br><br> ![Busque y seleccione Automation and Control en Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   

4. Después de leer la descripción de la oferta, haga clic en **Crear**.  

5. En la hoja de configuración de **Automation and Control**, seleccione **Área de trabajo de OMS**.  En la hoja **Áreas de trabajo de OMS**, seleccione un área de trabajo de OMS que esté vinculada a la misma suscripción de Azure en la que se encuentra la cuenta de Automation o cree un área de trabajo de OMS.  Si no tiene un área de trabajo de OMS, puede seleccionar **Crear área de trabajo nueva** y en la hoja **Área de trabajo de OMS** realizar estos pasos: 
   - Especifique un nombre para el nuevo **espacio de trabajo de OMS**.
   - Seleccione una **suscripción** a la que vincularlo en la lista desplegable si la opción predeterminada seleccionada no es adecuada.
   - Para **Grupo de recursos**, puede crear un grupo de recursos o seleccionar uno existente.  
   - Seleccione una **ubicación**.  Para más información, consulte qué [regiones de Azure Automation están disponibles en](https://azure.microsoft.com/regions/services/).  Las soluciones se ofrecen en dos niveles: gratuito y nivel de pago por nodo (OMS).  El nivel gratis tiene un límite sobre la cantidad de datos recopilados diariamente, el período de retención y los minutos de tiempo de ejecución del trabajo de Runbook.  El nivel de pago por nodos (OMS) no tiene ningún límite en la cantidad de datos recopilados a diario.  
   - Seleccione **Cuenta de Automation**.  Si va a crear una nueva área de trabajo de OMS, será necesario crear también una nueva cuenta de Automation que se asociará con la nueva área de trabajo de OMS especificada anteriormente, lo que incluye la suscripción, el grupo de recursos y la región de Azure.  Puede seleccionar **Crear una cuenta de Automation** y, en la hoja **Cuenta de Automation**, proporcionar la siguiente información: 
  - En el campo **Nombre**, escriba el nombre de la cuenta de Automation.

    Todas las demás opciones se rellenan automáticamente en función del espacio de trabajo de OMS seleccionado y no se pueden modificar.  Una cuenta de ejecución de Azure es el método de autenticación predeterminado de la oferta.  Después de hacer clic en **Aceptar**, se validan las opciones de configuración y se crea la cuenta de Automation.  Puede realizar un seguimiento de su progreso en **Notificaciones** en el menú. 

    En caso contrario, seleccione una cuenta de ejecución de Automation existente.  La cuenta seleccionada no puede estar vinculada ya a otra área de trabajo de OMS ya que, en caso contrario, aparecerá un mensaje de notificación en la hoja.  Si ya está vinculada, deberá seleccionar una cuenta de ejecución de Automation diferente o crear una.

    Después de rellenar la información necesaria, haga clic en **Crear**.  Se comprueba la información y se crean las cuentas de Automation y de ejecución.  Se le redirigirá a la hoja **Área de trabajo de OMS** automáticamente.  

6. Después de proporcionar la información necesaria en la hoja **Área de trabajo de OMS**, haga clic en **Crear**.  Mientras se comprueba la información y se crea el espacio de trabajo, puede realizar un seguimiento de su progreso en **Notificaciones** en el menú.  Volverá a la hoja **Agregar solución**.  

7. En la hoja de configuración **Automation and Control**, confirme que desea instalar las soluciones recomendadas preseleccionadas. Si anula la selección de alguna de ellas puede instalarlas individualmente más adelante.  

8. Haga clic en **Crear** para continuar con la incorporación de Automation y de un área de trabajo de OMS. Todas las configuraciones se validan y se intenta implementar la oferta en la suscripción.  Este proceso puede tardar varios segundos en completarse y puede realizar el seguimiento de su progreso en **Notificaciones** en el menú. 

Después de incorporar la oferta, puede empezar a crear runbooks, trabajar con las soluciones de administración que ha habilitado, implementar un rol de [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) o comenzar a trabajar con [Log Analytics](https://docs.microsoft.com/azure/log-analytics) para recopilar los datos generados por los recursos de su entorno en la nube o local.   

## <a name="next-steps"></a>Pasos siguientes
* Puede confirmar la nueva cuenta de Automation para autenticarse con recursos de Azure mediante la revisión del artículo [Comprobación de la autenticación con la cuenta de ejecución de Azure Automation](automation-verify-runas-authentication.md).
* Para empezar a trabajar con la creación de runbooks, revise primero los [tipos de runbook de Automation](automation-runbook-types.md) compatibles y otras consideraciones relacionadas antes de empezar a crear.


