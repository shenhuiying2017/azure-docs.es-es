---
title: "Implementación de una aplicación en Azure Stack | Microsoft Docs"
description: "Aprenda a implementar aplicaciones en Azure y Azure Stack con una canalización de CI/CD híbrida."
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: helaw
ms.custom: mvc
ms.openlocfilehash: 83bb401d5d65cd2c34015a1a14673363aeee81d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Implementación de aplicaciones en Azure y Azure Stack
*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Una canalización de [integración continua](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[entrega continua](https://www.visualstudio.com/learn/what-is-continuous-delivery/) (CI/CD) híbrida le permite crear, probar e implementar una aplicación en varias nubes.  En este tutorial, se crea un entorno de ejemplo para aprender el modo en que una canalización de CI/CD híbrida puede ayudarle a:
 
> [!div class="checklist"]
> * Iniciar una nueva compilación en función de las confirmaciones de código en el repositorio de Visual Studio Team Services (VSTS).
> * Implementar automáticamente el código recién compilado en Azure para llevar a cabo las pruebas de aceptación del usuario.
> * Una vez que el código haya pasado las pruebas, se implementan automáticamente en Azure Stack. 


## <a name="prerequisites"></a>Requisitos previos
Se requieren algunos componentes para crear una canalización de CI/CD híbrida y pueden tardar algún tiempo en prepararse.  Si ya tiene algunos de estos componentes, asegúrese de que cumplen los requisitos antes de comenzar.

En este tema también se da por supuesto que tiene algunos conocimientos de Azure y Azure Stack. Si desea más información antes de continuar, asegúrese de empezar con estos temas:

- [Introducción a Azure](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Conceptos clave de Azure Stack](../azure-stack-key-features.md)

### <a name="azure"></a>Azure
 - Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
 - Cree una [aplicación web](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md) y configúrela para [publicación FTP](../../app-service/app-service-deploy-ftp.md).  Tome nota de la dirección URL de la nueva aplicación web, ya que se utiliza más adelante.


### <a name="azure-stack"></a>Azure Stack
 - [Implementación de Azure Stack](../azure-stack-run-powershell-script.md).  La instalación suele tardar unas horas, de modo que planéela en consecuencia.
 - Implemente servicios PaaS de [App Service](../azure-stack-app-service-deploy.md) para Azure Stack.
 - Cree una aplicación web y configúrela para [publicación FTP](../azure-stack-app-service-enable-ftp.md).  Tome nota de la dirección URL de la nueva aplicación web, ya que se utiliza más adelante.  

### <a name="developer-tools"></a>Herramientas para desarrolladores
 - Cree un [área de trabajo de VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).  El proceso de registro crea un proyecto denominado "MyFirstProject".  
 - [Instale Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [inicie sesión en VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio)
 - Conéctese al proyecto y [realice la clonación localmente](https://www.visualstudio.com/docs/git/gitquickstart).
 - Cree un [grupo de agentes](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues) en VSTS.
 - Instale Visual Studio e implemente un [agente de compilación de VSTS](https://www.visualstudio.com/docs/build/actions/agents/v2-windows) para una máquina virtual en Azure Stack. 
 

## <a name="create-app--push-to-vsts"></a>Creación de la aplicación e inserción en VSTS

### <a name="create-application"></a>Creación de la aplicación
En esta sección, va a crear una aplicación ASP.NET sencilla y la insertará en VSTS.  Estos pasos representan el flujo de trabajo habitual del desarrollador y se pueden adaptar para los diferentes lenguajes y herramientas de desarrollo. 

1.  Abra Visual Studio.
2.  Desde el espacio de Team Explorer y el área **Soluciones...** , haga clic en **Nuevo**.
3.  Seleccione **Visual C#** > **Web** > **Aplicación web ASP.NET (.NET Framework)**.
4.  Asigne un nombre a la aplicación y haga clic en **Aceptar**.
5.  En la siguiente pantalla, conserve los valores predeterminados (formularios Web Forms) y haga clic en **Aceptar**.

### <a name="commit-and-push-changes-to-vsts"></a>Confirmación e inserción de los cambios en VSTS
1.  Con Team Explorer en Visual Studio, seleccione la lista desplegable y haga clic en **Cambios**.
2.  Agregue un mensaje de confirmación y seleccione **Confirmar todo**. Puede que se le pida que guarde el archivo de solución; haga clic en Sí para guardar todo.
3.  Una vez haya confirmado, Visual Studio ofrece sincronizar los cambios en el proyecto. Seleccione **Sincronizar**.

    ![imagen que muestra la pantalla de confirmación una vez completada la confirmación](./media/azure-stack-solution-pipeline/image1.png)

4.  En la pestaña de sincronización, en *Saliente*, verá la nueva confirmación.  Seleccione **Insertar** para sincronizar el cambio en VSTS.

    ![imagen que muestra los pasos de sincronización](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>Revisión del código en VSTS
Una vez haya confirmado un cambio y lo haya insertado en VSTS, compruebe el código desde el portal de VSTS.  Seleccione **Código** y, a continuación, **Archivos** en el menú desplegable.  Puede ver la solución que creó.

## <a name="create-build-definition"></a>Creación de la definición de compilación
El proceso de compilación define el modo en que la aplicación se compila y se empaqueta para implementarse cada vez que se confirman los cambios en el código. En nuestro ejemplo, utilizamos la plantilla incluida para configurar el proceso de compilación de una aplicación ASP.NET, aunque esta configuración podría adaptarse según la aplicación.

1.  Inicie sesión en el área de trabajo de VSTS desde un explorador web.
2.  En el encabezado, seleccione **Compilación y versión** y, a continuación, **Compilaciones**.
3.  Haga clic en **+ Nueva definición**.
4.  En la lista de plantillas, seleccione **ASP.NET (versión preliminar)** y **Aplicar**.
5.  Modifique el campo *Argumentos de MSBuild* en el paso*Compilar solución* para:

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  Seleccione la pestaña **Opciones** y la cola del agente para el agente de compilación que implementó en una máquina virtual en Azure Stack. 
7.  Seleccione la pestaña **Desencadenadores** y habilite **Integración continua**.
7.  Haga clic en **Guardar y poner en cola** y, a continuación, seleccione **Guardar** en la lista desplegable. 

## <a name="create-release-definition"></a>Creación de la definición de versión
El proceso de creación de versiones define el modo en que las compilaciones derivadas del paso anterior se implementan en un entorno.  En este tutorial, publicamos nuestra aplicación ASP.NET con el protocolo FTP en una aplicación web de Azure. Para configurar una versión en Azure, siga estos pasos:

1.  En el encabezado de VSTS, seleccione **Compilación y versión** y, a continuación, **Versiones**.
2.  Haga clic en la opción **+ Nueva definición** en verde.
3.  Seleccione **Vacía** y haga clic en **Siguiente**.
4.  Active la casilla de *Implementación continua* y, a continuación, haga clic en **Crear**.

Ahora que ha creado una definición de versión vacía y vinculada a la compilación, agregamos los pasos para el entorno de Azure:

1.  Haga clic en el  **+ verde**  para agregar tareas.
2.  Seleccione **Todos** y, a continuación, en la lista, agregue **Carga por FTP** y seleccione **Cerrar**.
3.  Seleccione la tarea **Carga por FTP** recién agregada y configure los parámetros siguientes:
    
    | Parámetro | Valor |
    | ----- | ----- |
    |Método de autenticación| Escribir credenciales|
    |Dirección URL del servidor | Dirección URL de FTP de la aplicación web recuperada de Azure Portal |
    |Nombre de usuario | Nombre de usuario que configuró al crear las credenciales de FTP para la aplicación web |
    |Password | Contraseña que creó al establecer las credenciales FTP para la aplicación web|
    |Directorio de origen | $(System.DefaultWorkingDirectory)\**\ |
    |Directorio remoto | /site/wwwroot/ |
    |Mantener las rutas de acceso de los archivos | Habilitado (activado)|

4.  Haga clic en **Guardar**

Por último, configure la definición de la versión para usar el grupo de agentes que contiene al agente implementado con los pasos siguientes:
1.  Seleccione la definición de la versión y haga clic en **Editar**.
2.  Seleccione **Ejecutar en el agente** en la columna central.  En la columna de la derecha, seleccione la cola del agente que contiene el agente de compilación que se ejecuta en Azure Stack.  
    ![imagen que muestra la configuración de la definición de versión para usar una cola específica](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>Implementación de una aplicación en Azure
Este paso usa la canalización de CI/CD recién creada para implementar la aplicación ASP.NET en una aplicación web en Azure. 

1.  En el encabezado de VSTS, seleccione **Compilación y versión** y, a continuación, **Compilaciones**.
2.  Haga clic en **...**  en la definición de compilación creada anteriormente y, a continuación, seleccione **Poner nueva compilación en cola**.
3.  Acepte el valor predeterminado y haga clic en **Aceptar**.  La compilación comienza y se muestra el progreso.
4.  Una vez completada la compilación, puede realizar el seguimiento del estado seleccionando **Compilación y versión** y **Versiones**.
5.  Una vez completada la compilación, visite el sitio web utilizando la dirección URL que anotó al crear la aplicación web.    


## <a name="add-azure-stack-to-pipeline"></a>Incorporación de Azure Stack a la canalización
Ahora que ha probado la canalización de CI/CD implementándola en Azure, es momento de agregar Azure Stack a la canalización.  En los pasos siguientes, cree un nuevo entorno y agregue una tarea Carga por FTP para implementar la aplicación en Azure Stack.  También agrega un aprobador de versión, que actúa como medio de simular la "firma" en una versión de código para Azure Stack.  

1.  En la definición de la versión, seleccione **+ Agregar entorno** y **Crear nuevo entorno**.
2.  Seleccione **Vacío** y haga clic en **Siguiente**.
3.  Seleccione **Usuarios específicos** y especifique su cuenta.  Seleccione **Crear**.
4.  Cambie el nombre del entorno seleccionando el nombre actual y escribiendo *Azure Stack*.
5.  Ahora, seleccione el entorno de Azure Stack y después seleccione **Agregar tareas**.
6.  Seleccione la tarea **Carga por FTP** y **Agregar**; a continuación, seleccione **Cerrar**.


### <a name="configure-ftp-task"></a>Configuración de la tarea FTP
Ahora que ha creado una versión, configurará los pasos necesarios para publicarla en la aplicación web en Azure Stack.  Igual que ha configurado la tarea Carga por FTP para Azure, configurará la tarea para Azure Stack:

1.  Seleccione la tarea **Carga por FTP** recién agregada y configure los parámetros siguientes:
    
    | Parámetro | Valor |
    | -----     | ----- |
    |Método de autenticación| Escribir credenciales|
    |Dirección URL del servidor | Dirección URL de FTP de la aplicación web recuperada de Azure Stack Portal |
    |Nombre de usuario | Nombre de usuario que configuró al crear las credenciales de FTP para la aplicación web |
    |Password | Contraseña que creó al establecer las credenciales FTP para la aplicación web|
    |Directorio de origen | $(System.DefaultWorkingDirectory)\**\ |
    |Directorio remoto | /site/wwwroot/|
    |Mantener las rutas de acceso de los archivos | Habilitado (activado)|

2.  Haga clic en **Guardar**

Por último, configure la definición de la versión para usar el grupo de agentes que contiene al agente implementado con los pasos siguientes:
1.  Seleccione la definición de la versión y haga clic en **Editar**.
2.  Seleccione **Ejecutar en el agente** en la columna central. En la columna de la derecha, seleccione la cola del agente que contiene el agente de compilación que se ejecuta en Azure Stack.  
    ![imagen que muestra la configuración de la definición de versión para usar una cola específica](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>Implementación de nuevo código
Ahora puede probar la canalización de CI/CD híbrida, con el paso final de la publicación en Azure Stack.  En esta sección, modificará el pie de página del sitio y empezará la implementación a través de la canalización.  Una vez haya terminado, verá los cambios implementados en Azure para revisarlos y, cuando apruebe la versión, se publicarán en Azure Stack.

1. En Visual Studio, abra el archivo *site.master* y cambie esta línea:
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    a este:

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  Confirme los cambios y sincronice en VSTS.  
4.  Desde el área de trabajo de VSTS, compruebe el estado de la compilación seleccionando **Compilar y versión** > **Compilar**
5.  Verá una compilación en curso.  Haga doble clic en el estado y podrá ver el progreso de la compilación.  Una vez que vea "Compilación finalizada" en la consola, compruebe la versión en **Compilar y versión** > **Versión**.  Haga doble clic en la versión.
6.  Recibirá una notificación que indica que una versión requiere ser revisada. Compruebe la dirección URL de la aplicación web y que los nuevos cambios están presentes.  Apruebe la versión en VSTS.
    ![imagen que muestra la configuración de la definición de versión para usar una cola específica](./media/azure-stack-solution-pipeline/image4.png)
    
7.  Compruebe que la publicación en Azure Stack se ha completado visitando el sitio web con la dirección URL que anotó al crear la aplicación web.
    ![imagen que muestra la aplicación ASP.NET con el pie de página cambiado](./media/azure-stack-solution-pipeline/image5.png)


Ahora puede usar la nueva canalización de CI/CD híbrida como un bloque de creación para otros modelos de nube híbrida.

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, aprendió a crear una canalización de CI/CD híbrida que:

> [!div class="checklist"]
> * Inicia una nueva compilación en función de las confirmaciones de código en el repositorio de Visual Studio Team Services (VSTS).
> * Implementa automáticamente el código recién compilado en Azure para llevar a cabo las pruebas de aceptación del usuario.
> * Una vez que el código ha pasado las pruebas, se implementan automáticamente en Azure Stack. 

Ahora que tiene una canalización de CI/CD híbrida, continúe aprendiendo a desarrollar aplicaciones para Azure Stack.

> [!div class="nextstepaction"]
> [Desarrollo para Azure Stack](azure-stack-developer.md)


