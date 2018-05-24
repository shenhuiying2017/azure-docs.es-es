---
title: Implementación de una aplicación en Azure Stack | Microsoft Docs
description: Aprenda a implementar aplicaciones en Azure y Azure Stack con una canalización de CI/CD híbrida.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 483122f5d2e39c8595d0f28a6b937772c4ea2e50
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258355"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Tutorial: Implementación de aplicaciones en Azure y Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Una canalización híbrida de integración continua/entrega continua (CI/CD) le permite crear, probar e implementar una aplicación en varias nubes.  En este tutorial, creará un entorno de ejemplo para:
 
> [!div class="checklist"]
> * Iniciar una nueva compilación en función de las confirmaciones de código en el repositorio de Visual Studio Team Services (VSTS).
> * Implementar automáticamente el código recién compilado en Azure global para llevar a cabo las pruebas de aceptación del usuario.
> * Una vez que el código haya pasado las pruebas, se implementan automáticamente en Azure Stack.

### <a name="about-the-hybrid-delivery-build-pipe"></a>Acerca de la canalización de compilación de entrega híbrida

La continuidad, la seguridad y la confiabilidad de la implementación de las aplicaciones resultan fundamentales para su organización y vital para su equipo de desarrollo. Con una canalización de CI/CD híbrida, puede consolidar las canalizaciones en su entorno local y en la nube pública. Puede cambiar la ubicación sin cambiar de aplicación.

Este enfoque también permite mantener un conjunto coherente de herramientas de desarrollo. La coherencia de las herramientas entre la nube pública de Azure y el entorno local de Azure Stack significa que es más fácil implementar una práctica de desarrollo de CI/CD. Las aplicaciones y servicios implementados en Azure o Azure Stack son intercambiables y se puede ejecutar el mismo código en cada ubicación, de forma que se aprovechan las características y funcionalidades locales y de la nube pública.

Más información sobre:
 - [What is Continuous Integration?](https://www.visualstudio.com/learn/what-is-continuous-integration/) (¿Qué es la integración continua?)
 - [What is Continuous Delivery?](https://www.visualstudio.com/learn/what-is-continuous-delivery/) (¿Qué es la entrega continua?)


## <a name="prerequisites"></a>requisitos previos

Necesitará contar con algunos componentes para crear una canalización de CI/CD híbrida. Estos componentes pueden tardar un tiempo en prepararse.
 
 - Un asociado de hardware u OEM de Azure puede implementar una instancia de Azure Stack de producción y todos los usuarios pueden implementar un Kit de desarrollo de Azure Stack (ASDK). 
 - Además, un operador de Azure Stack debe implementar la instancia de App Service, crear planes y ofertas, crear una suscripción de inquilino y agregar la imagen de Windows Server 2016.

Si ya tiene algunos de estos componentes, asegúrese de que cumplen los requisitos antes de comenzar.

En este tema también se da por supuesto que tiene algunos conocimientos de Azure y Azure Stack. Si desea más información antes de continuar, asegúrese de empezar con estos temas:


En este tutorial también se da por supuesto que tiene algunos conocimientos de Azure y Azure Stack. 

Si quiere más información antes de continuar, puede empezar con estos temas:
 - [Introducción a Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Conceptos clave de Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

 - Cree una [aplicación web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) en Azure. Tome nota de la dirección URL de la nueva aplicación web, ya que se utiliza más adelante.

Azure Stack
 - Use un sistema integrado de Azure Stack o implemente el Kit de desarrollo de Azure Stack (ASDK) mediante este vínculo:
    - También puede encontrar instrucciones detalladas sobre cómo implementar el ASDK en "[Tutorial: Implementación del ASDK con el instalador](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)"
    - Muchos de los pasos posteriores a la implementación del ASDK se pueden automatizar con el siguiente script de PowerShell, [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ).

    > [!Note]  
    > La instalación del ASDK tarda siete horas en completarse, así que realice los preparativos oportunos.

 - Implemente servicios PaaS de [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) para Azure Stack. 
 - Cree [planes u ofertas](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dentro del entorno de Azure Stack. 
 - Cree una [suscripción de inquilino](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dentro del entorno de Azure Stack. 
 - Cree una aplicación web dentro de la suscripción de inquilino. Anote la nueva dirección URL de aplicación web, ya que la usará más adelante.
 - Implemente la máquina virtual de VSTS, aún dentro de la suscripción de inquilino.
 - Se requiere una máquina virtual Windows Server 2016 con .NET 3.5. Esta máquina virtual se compila en la instancia de Azure Stack como el agente de compilación privada. 

### <a name="developer-tools"></a>Herramientas para desarrolladores

 - Cree un [área de trabajo de VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). El proceso de registro crea un proyecto denominado **MyFirstProject**.
 - [Instale Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [inicie sesión en VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
 - Conéctese al proyecto y [realice la clonación localmente](https://www.visualstudio.com/docs/git/gitquickstart).
 
 > [!Note]  
 > Necesitará Azure Stack con las imágenes adecuadas sindicadas para ejecutar (Windows Server y SQL) y tener implementado App Service.
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Preparación de la compilación privada y el agente de versión para la integración de Visual Studio Team Services

### <a name="prerequisites"></a>requisitos previos

Visual Studio Team Services (VSTS) se autentica en Azure Resource Manager mediante una entidad de servicio. Para que VSTS pueda aprovisionar los recursos en una suscripción de Azure Stack, necesita el estado de colaborador.

Estos son los pasos de alto nivel que deben configurarse para habilitar dicha autenticación:

1. Se debe crear una entidad de servicio o usarse una ya existente.
2. Se deben crear las claves de autenticación para la entidad de servicio.
3. La suscripción de Azure Stack se debe validar mediante el control de acceso basado en rol para permitir que el SPN forme parte del rol de colaborador.
4. Se debe crear una nueva definición de servicio en VSTS mediante los puntos de conexión de Azure Stack y la información de SPN.

### <a name="service-principal-creation"></a>Creación de la entidad de servicio

Para crear una entidad de servicio y elegir Web App/API como tipo de aplicación, consulte las instrucciones [Creación de una entidad de servicio](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

### <a name="access-key-creation"></a>Creación de las claves de acceso

Una entidad de servicio requiere una clave para la autenticación; siga los pasos descritos en esta sección para generar una clave.


1. En **Registros de aplicaciones**, en Azure Active Directory, seleccione su aplicación.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  Anote el valor del **identificador de aplicación**. Este valor se usará al configurar el punto de conexión de servicio en VSTS.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Para generar una clave de autenticación, seleccione **Configuración**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Para generar una clave de autenticación, seleccione **Claves**.
 
    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Proporcione una descripción de la clave y una duración. Cuando haya terminado, seleccione **Guardar**.
 
    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Después de guardar la clave, se muestra el valor de la clave. Copie este valor porque no podrá recuperarlo más adelante. Proporcionará el **valor de la clave** con el identificador de aplicación para iniciar sesión con la aplicación. Guarde el valor de clave donde la aplicación pueda recuperarlo.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>Obtención del identificador de inquilino

Como parte de la configuración del punto de conexión de servicio, VSTS requiere el **identificador de inquilino** que corresponde al directorio de AAD en el que se implementó la marca de Azure Stack. Siga los pasos de esta sección para obtener el identificador de inquilino.

1. Seleccione **Azure Active Directory**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Para obtener el identificador de inquilino, seleccione **Propiedades** en el inquilino de Azure AD.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. Copie el **id. de directorio**. Este valor es el id. de inquilino.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Concesión de derechos de entidad de servicio para implementar recursos en la suscripción de Azure Stack 

Para acceder a los recursos de la suscripción, debe asignarle a la aplicación un rol. Decida qué rol representa los permisos adecuados para la aplicación. Para obtener más información sobre los roles disponibles, vea [RBAC: Roles integrados](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Puede establecer el ámbito en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, el hecho de agregar una aplicación al rol Lector para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contenga.

1. Desplácese hasta el nivel de ámbito al que desea asignar la aplicación. Por ejemplo, para asignar un rol en el ámbito de suscripción, seleccione **Suscripciones**. También puede seleccionar un grupo de recursos o un recurso.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. Seleccione la **suscripción** específica (grupo de recursos o recurso) a la que asignar la aplicación.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Seleccione **Access Control (IAM)**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Seleccione **Agregar**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. Seleccione el rol que quiere asignar a la aplicación. En la imagen siguiente se muestra el rol **Propietario**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. De manera predeterminada, las aplicaciones de Azure Active Directory no se muestran en las opciones disponibles. Para buscar la aplicación, debe **proporcionar el nombre** en el campo de búsqueda. Selecciónelo.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Haga clic en **Guardar** para finalizar la asignación del rol. Verá la aplicación en la lista de usuarios asignados a un rol para ese ámbito.

### <a name="role-based-access-control"></a>Control de acceso basado en roles

El control de acceso basado en rol (RBAC) de Azure permite realizar una administración pormenorizada del acceso para Azure. Con RBAC, puede conceder únicamente el grado de acceso que los usuarios necesiten para realizar sus trabajos. Para más información sobre el control de acceso basado en rol, consulte [Administración del acceso a los recursos de suscripción de Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>Grupos de agentes de VSTS

En lugar de administrar cada agente de forma individual, los agentes se organizan en grupos. Un grupo de agentes define el límite de uso compartido de todos los agentes de ese grupo. En VSTS, los grupos de agentes tienen como ámbito la cuenta de VSTS; por lo que puede compartir un grupo de agentes entre proyectos de equipo. Para más información sobre cómo crear grupos de agentes de VSTS y ver un tutorial al respecto, consulte [Create Agent Pools and Queues](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts) (Creación de grupos de agentes y colas).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Adición de un token de acceso personal (PAT) a Azure Stack

1. Inicie sesión en su cuenta de VSTS y seleccione el nombre de perfil de su cuenta.
2. Seleccione **Administrar seguridad** para acceder a la página de creación de tokens de acceso.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Copie el token.
    
    > [!Note]  
    > Obtenga la información del token. Después de salir de esta pantalla, no se volverá a mostrar. 
    
    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Instalación del agente de compilación de VSTS en el servidor de compilación hospedado de Azure Stack

1.  Conéctese a su servidor de compilación que implementó en el host de Azure Stack.

2.  Descargue e instale el agente de compilación como un servicio mediante su token de acceso personal (PAT) y ejecútelo como la cuenta de administrador de la máquina virtual.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Vaya a la carpeta del agente de compilación extraído. Ejecute el archivo **run.cmd** desde un símbolo del sistema con privilegios elevados. 

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  Finalizada la ejecución, la carpeta con el contenido extraído tendrá este aspecto:

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Ahora puede ver al agente en la carpeta de VSTS.

## <a name="endpoint-creation-permissions"></a>Permisos de creación de puntos de conexión

Los usuarios pueden crear puntos de conexión para que las compilaciones de VSTO puedan implementar aplicaciones de servicios de Azure en la pila. VSTS se conecta con el agente de compilación, que, a su vez, se conecta con Azure Stack. 

![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. En el menú **Configuración**, seleccione **Seguridad**.
2. En la lista **Grupos de VSTS** de la izquierda, seleccione **Creadores de extremos**. 

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. En la pestaña **Miembros**, seleccione **+ Agregar**. 

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. Escriba un nombre de usuario y seleccione ese usuario de la lista.
5. Haga clic en **Guardar cambios**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. En la lista **Grupos de VSTS** de la izquierda, seleccione **Administradores de extremos**.
7. En la pestaña **Miembros**, seleccione **+ Agregar**.
8. Escriba un nombre de usuario y seleccione ese usuario de la lista.
9. Haga clic en **Guardar cambios**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    El agente de compilación de Azure Stack obtiene instrucciones de VSTS, el cual transmite la información de punto de conexión para la comunicación con Azure Stack. 
    
    La conexión de VSTS a Azure Stack ya está lista.

## <a name="develop-your-application"></a>Desarrollo de la aplicación

Configure la canalización de CI/CD híbrida para implementar la aplicación web en Azure y Azure Stack e insertar automáticamente los cambios en ambas nubes.

> [!Note]  
> Necesitará Azure Stack con las imágenes adecuadas sindicadas para ejecutar (Windows Server y SQL) y tener implementado App Service. Revise la sección "Requisitos previos" de la documentación de App Service para conocer los requisitos del operador de Azure Stack.

### <a name="add-code-to-vsts-project"></a>Adición de código al proyecto de VSTS

1. Inicie sesión en Visual Studio con una cuenta que tenga derechos de creación de proyectos en Azure Stack.

    La canalización de CI/CD híbrida se puede aplicar al código de aplicación y al código de infraestructura. Use [plantillas de Azure Resource Manager como código de aplicación web ](https://azure.microsoft.com/resources/templates/) de VSTS a ambas nubes.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Clone el repositorio**; para ello, cree y abra la aplicación web predeterminada.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Creación de una implementación de aplicación web autocontenida para App Services en ambas nubes

1. Edite el archivo **WebApplication.csproj**: seleccione **Runtimeidentifier** y agregue `win10-x64.` Para más información, consulte la documentación [Implementaciones autocontenidas](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd).

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Compruebe el código en VSTS mediante Team Explorer.

3. Confirme que el código de aplicación se ha insertado en el repositorio de Visual Studio Team Services. 

### <a name="create-the-build-definition"></a>Creación de la definición de compilación

1. Inicie sesión en VSTS para confirmar la posibilidad de crear definiciones de compilación.

2. Agregue el código **-r win10-x64**. Esto es necesario para activar una implementación autocontenida con .Net Core. 

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. Ejecute la compilación. El proceso de [compilación de implementación autocontenida](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) publicará los artefactos que se pueden ejecutar en Azure y Azure Stack.

### <a name="using-an-azure-hosted-agent"></a>Uso de un agente hospedado de Azure

El uso de un agente hospedado en VSTS es una opción adecuada para compilar e implementar aplicaciones web. Microsoft Azure realiza automáticamente el mantenimiento y las actualizaciones, lo que permite el desarrollo, la prueba y la implementación de forma continua e ininterrumpida.

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>Administración y configuración del proceso de implementación continua (CD)

Visual Studio Team Services (VSTS) y Team Foundation Server (TFS) proporcionan una canalización con una gran capacidad de configuración y administración para versiones de varios entornos, como desarrollo, ensayo, control de calidad (QA) y producción, lo que incluye las aprobaciones necesarias en etapas específicas.

### <a name="create-release-definition"></a>Creación de la definición de versión

![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. Seleccione el símbolo **\[ + ]** para agregar una nueva versión en la pestaña **Versiones** de la página Compilación y versión de VSO.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\102.png)

2. Aplique la plantilla **Implementación de Azure App Service**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\103.png)

3. En el menú desplegable Agregar artefacto, **agregue el artefacto** para la aplicación de compilación de nube de Azure.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\104.png)

4. En la pestaña Canalización, seleccione el vínculo **Fase**, **Tarea** del entorno y establezca los valores del entorno de nube de Azure.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\105.png)

5. Establezca el **nombre del entorno** y seleccione la **suscripción** de Azure como el punto de conexión de la nube de Azure.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\106.png)

6. En Nombre del entorno, establezca el **nombre del servicio de aplicación de Azure** necesario.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\107.png)

7. Escriba **Hospedado VS2017** en Cola de agentes para el entorno hospedado de la nube de Azure.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\108.png)

8. En el menú de implementación de Azure App Service, seleccione el **paquete o carpeta** válidos para el entorno. Seleccione **Aceptar** para la **ubicación de carpeta**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\110.png)

9. Guarde todos los cambios y vuelva a la **canalización de versión**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\111.png)

10. Agregue un **nuevo artefacto**mediante la selección de la compilación para la aplicación de Azure Stack.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\112.png)

11. Agregue un entorno más; para ello, aplique la plantilla **Implementación de Azure App Service**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\113.png)

12. Asigne al nuevo entorno el nombre **Azure Stack**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\114.png)

13. Busque el entorno de Azure Stack en la pestaña **Tarea**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\115.png)

14. Seleccione la **suscripción** para el punto de conexión de Azure Stack.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\116.png)

15. Establezca el nombre de la aplicación web de Azure Stack como el **nombre del servicio de aplicación**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\117.png)

16. Seleccione el **agente de Azure Stack**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\118.png)

17. En la sección de implementación de Azure App Service, seleccione el **paquete o carpeta** válidos para el entorno. Seleccione Aceptar para la **ubicación de carpeta**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\120.png)

18. En la pestaña Variable, agregue una variable llamada **VSTS_ARM_REST_IGNORE_SSL_ERRORS**, establezca su valor en **true**, y el ámbito como **Azure Stack**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\121.png)

19. Seleccione el icono del desencadenador de implementación **Continuo** en ambos artefactos y habilite el desencadenador de implementación continua.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\122.png)

20. Seleccione el icono de condiciones **previas a la implementación** en el entorno de Azure Stack y establezca el desencadenador en **After release** (Tras el lanzamiento).

21. Guarde todos los cambios.

> [!Note]  
> Algunos valores de configuración de las tareas se han definido automáticamente como [variables de entorno](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) cuando se creó una definición de versión desde una plantilla. Estos valores de configuración no se pueden modificar en la configuración de tareas; para ello, debe seleccionar el elemento del entorno principal.

## <a name="create-a-release"></a>Creación de una versión

Ahora que ha completado las modificaciones a la definición de versión, es hora de iniciar la implementación. Para ello, cree una versión a partir de la definición de versión. Una versión se puede crear automáticamente; por ejemplo, el desencadenador de implementación continua se establece en la definición de versión. Esto significa que al modificar el código fuente se iniciará una nueva compilación y, a partir de ella, una nueva versión. Sin embargo, en esta sección creará una nueva versión manualmente.

1. Abra la lista desplegable **Versión** y elija **Crear versión**.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. Escriba una descripción para la versión, compruebe que se seleccionan los artefactos correctos y, a continuación, elija **Crear**. Transcurridos unos instantes, aparece un mensaje emergente que indica que se ha creado la nueva versión. Elija el vínculo (el nombre de la versión).

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. Se abre la página de resumen de versiones que muestra los detalles de la versión. En la sección **Entornos**, verá que el estado de implementación del entorno "QA" cambia de "EN CURSO" a "CORRECTO" y, en ese momento, aparece un mensaje emergente que indica que la versión está a la espera de aprobación. Cuando una implementación en un entorno está pendiente o ha dado error, se muestra un icono de información (i) azul. Seleccione esta opción para ver una ventana emergente que contiene la razón.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\202.png)

Otras vistas, como la lista de versiones, también muestran un icono que indica que la aprobación está pendiente. El icono muestra una ventana emergente que contiene el nombre del entorno y más detalles cuando lo selecciona. Esto permite que un administrador pueda ver fácilmente que versiones están a la espera de aprobación así como el progreso general de todas las versiones.

### <a name="monitor-and-track-deployments"></a>Supervisión y seguimiento de implementaciones

En esta sección, verá cómo puede supervisar y realizar el seguimiento de las implementaciones, en este ejemplo, en dos sitios web de Azure App Services, a partir de la versión que creó en la sección anterior.

1. En la página de resumen de versiones, elija el vínculo **Registros**. Mientras tiene lugar la implementación, en esta página se muestra el registro activo del agente y, en el panel izquierdo, una indicación del estado de cada operación en el proceso de implementación de cada entorno.

    Elija el icono en la columna **Acción** correspondiente a una aprobación previa o posterior a la implementación para ver detalles de quién aprobó (o rechazó) la implementación y el mensaje que proporcionó el usuario.

2. Una vez completada la implementación, el archivo de registro entero se muestra en el panel derecho. Seleccione cualquiera de los **pasos del proceso** en el panel izquierdo para mostrar solo el contenido del archivo de registro correspondiente a ese paso. Así resulta más fácil realizar el seguimiento y la depuración de partes individuales de la implementación general. Como alternativa, descargue los archivos de registro individuales o un archivo ZIP de todos los archivos de registro, desde los iconos y vínculos de la página.

    ![Texto alternativo](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. Abra la pestaña **Resumen** para ver los detalles generales de la versión. Se muestran detalles de la compilación y los entornos en los que se implementó, junto con el estado de implementación y otra información sobre la versión.

4. Seleccione cada uno de lo **vínculos de entorno** para ver más detalles sobre las implementaciones existentes y pendientes en ese entorno específico. Puede usar estas páginas para comprobar que la misma compilación se implementó en ambos entornos.

5. Abra la **aplicación de producción implementada** en el explorador. Por ejemplo, en el caso de un sitio web de Azure App Services, desde la dirección URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](https://docs.microsoft.com/azure/architecture/patterns).
