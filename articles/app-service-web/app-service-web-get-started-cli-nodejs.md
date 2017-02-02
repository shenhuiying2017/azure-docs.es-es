---
title: "Implementación de su primera aplicación web en Azure en&5; minutos | Microsoft Docs"
description: "Aprenda lo fácil que es ejecutar aplicaciones web en App Service mediante la implementación de una aplicación de ejemplo. Para empezar, realice un desarrollo real rápidamente y vea los resultados inmediatamente."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 65c9bdd9-8763-4c56-8e15-f790992e951e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 2c750583212d7ed13e3bb5f4d0770d52aa9610b2


---
# <a name="deploy-your-first-web-app-to-azure-in-five-minutes"></a>Implementación de su primera aplicación web en Azure en&5; minutos
Este tutorial le ayudará a implementar su primera aplicación web en [Azure App Service](../app-service/app-service-value-prop-what-is.md).
App Service se puede usar para crear aplicaciones web, [back-ends de aplicaciones móviles](/documentation/learning-paths/appservice-mobileapps/) y [aplicaciones de API](../app-service-api/app-service-api-apps-why-best-platform.md).

Podrá: 

* Crear una aplicación web en Azure App Service.
* Implementar ejemplo de código (elija entre ASP.NET, PHP, Node.js, Java o Python).
* Ver la ejecución del código en directo en producción.
* Actualizar la aplicación web del mismo modo que [insertaría confirmaciones de Git](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](app-service-web-get-started-cli-nodejs.md): la CLI para los modelos de implementación clásico y de Resource Manager
- [CLI de Azure 2.0 (versión preliminar)](app-service-web-get-started.md): la CLI de última generación para el modelo de implementación de Resource Manager

## <a name="prerequisites"></a>Requisitos previos
* [Git](http://www.git-scm.com/downloads).
* [Azure CLI](../xplat-cli-install.md).
* Una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> También puede [probar App Service](https://azure.microsoft.com/try/app-service/) sin una cuenta de Azure. Cree una aplicación de inicio y juegue con ella durante una hora como máximo; no se requiere ninguna tarjeta de crédito ni ningún compromiso.
> 
> 

## <a name="deploy-a-web-app"></a>Implementación de una aplicación sitio
Vamos a implementar una aplicación web en el Servicio de aplicaciones de Azure.

1. Abra un símbolo del sistema de Windows, una ventana de PowerShell, un shell de Linux o un terminal de OS X. Ejecute `git --version` y `azure --version` para comprobar que Git y la CLI de Azure estén instalados en el equipo.
   
    ![Prueba de la instalación de las herramientas de la CLI para su primera aplicación web en Azure](./media/app-service-web-get-started/1-test-tools.png)
   
    Si no ha instalado las herramientas, consulte [Requisitos previos](#Prerequisites) para obtener vínculos de descarga.
2. Inicie sesión en Azure como se indica a continuación:
   
        azure login
   
    Siga el mensaje de ayuda para continuar con el proceso de inicio de sesión.
   
    ![Inicio de sesión en Azure para crear su primera aplicación web](./media/app-service-web-get-started/3-azure-login.png)
3. Cambie la CLI de Azure al modo ASM y establezca el usuario de implementación para App Service. Va a implementar código mediante las credenciales posteriormente.
   
        azure config mode asm
        azure site deployment user set --username <username> --pass <password>

4. Cambie a un directorio de trabajo (`CD`) y clone la aplicación de ejemplo como se indica a continuación:
   
        git clone <github_sample_url>
   
    ![Clonación del código de ejemplo de aplicación de su primera aplicación web de Azure](./media/app-service-web-get-started/2-clone-sample.png)
   
    Para *&lt;github_sample_url>*, use una de las siguientes direcciones URL, en función del marco que le guste:
   
   * HTML+CSS+JS: [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
   * ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
   * PHP (CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
   * Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git)
   * Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
   * Python (Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

5. Cambie al repositorio de la aplicación de ejemplo. Por ejemplo:
   
        cd app-service-web-html-get-started

6. Cree el recurso de la aplicación de App Service en Azure con un nombre de aplicación único y el usuario de implementación que configuró anteriormente. Cuando se le solicite, especifique el número de la región deseada.
   
        azure site create <app_name> --git --gitusername <username>
   
    ![Creación del recurso de Azure para su primera aplicación web en Azure](./media/app-service-web-get-started/4-create-site.png)
   
    La aplicación se crea en Azure ahora. El directorio actual también se inicializa con Git y se conecta a la nueva aplicación del Servicio de aplicaciones como un Git remoto.
    Puede dirigirse a la dirección URL de la aplicación (http://&lt;nombre_aplicación>.azurewebsites.net) para ver la bonita página HTML predeterminada, pero usemos ahora su código.

7. Implemente su código de ejemplo en la nueva aplicación de Azure igual que insertaría cualquier código con Git. Cuando se le pida, use la contraseña que configuró anteriormente.
   
        git push azure master
   
    ![Inserción de código en su primera aplicación web de Azure](./media/app-service-web-get-started/5-push-code.png)
   
    Si usó uno de los marcos de lenguaje, verá un resultado diferente. `git push` no solo inserta código en Azure, sino que también desencadena tareas de implementación en el motor de implementación. Si tiene algún archivo package.json (Node.js) o requirements.txt (Python) en la raíz del proyecto (repositorio) o tiene un archivo packages.config en el proyecto ASP.NET, el script de implementación restaura automáticamente los paquetes necesarios. También puede [habilitar la extensión Composer](web-sites-php-mysql-deploy-use-git.md#composer) para procesar automáticamente los archivos composer.json en la aplicación PHP.

Enhorabuena, ha implementado la aplicación en el Servicio de aplicaciones de Azure.

## <a name="see-your-app-running-live"></a>Visualización de la aplicación en ejecución
Para ver cómo la aplicación se ejecuta en Azure, ejecute este comando desde cualquier directorio del repositorio:

    azure site browse

## <a name="make-updates-to-your-app"></a>Realización de actualizaciones en la aplicación
Ahora puede usar Git para efectuar inserciones desde la raíz del proyecto (repositorio) con el fin de realizar una actualización en el sitio activo. Hágalo igual que cuando implementó el código por primera vez. Por ejemplo, cada vez que quiera insertar un nuevo cambio que ha probado localmente, solo tiene que ejecutar los siguientes comandos desde la raíz del proyecto (repositorio):

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Pasos siguientes
Busque los pasos de desarrollo e implementación preferidos para su plataforma de lenguaje:

> [!div class="op_single_selector"]
> * [.NET](web-sites-dotnet-get-started.md)
> * [PHP](app-service-web-php-get-started-cli-nodejs.md)
> * [Node.js](app-service-web-nodejs-get-started-cli-nodejs.md)
> * [Python](web-sites-python-ptvs-django-mysql.md)
> * [Java](web-sites-java-get-started.md)
> 
> 

También puede hacer más cosas con su primera aplicación web. Por ejemplo:

* Pruebe [otras formas de implementar el código en Azure](web-sites-deploy.md). Por ejemplo, para implementar desde uno de los repositorios de GitHub, simplemente seleccione **GitHub** en lugar de **Repositorio de Git local** en **Opciones de implementación**.
* Lleve su aplicación de Azure aún más lejos. Autentique los usuarios. Escálela según la demanda. Configure algunas alertas de rendimiento. Todo ello con unos cuantos clics. Consulte [Incorporación de funcionalidad a su primera aplicación web](app-service-web-get-started-2.md).




<!--HONumber=Jan17_HO3-->


