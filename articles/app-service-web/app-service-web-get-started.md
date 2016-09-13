<properties
	pageTitle="Implementación de su primera aplicación web en Azure en 5 minutos | Microsoft Azure"
	description="Aprenda lo fácil que es ejecutar aplicaciones web en el Servicio de aplicaciones mediante la implementación de una aplicación de ejemplo en unos pocos pasos. Comience a realizar desarrollo real en cinco minutos y vea los resultados inmediatamente."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""
/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/12/2016"
	ms.author="cephalin"
/>

# Implementación de su primera aplicación web en Azure en 5 minutos

[AZURE.INCLUDE [pestañas](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial lo ayuda a implementar su primera aplicación web en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md). App Service se puede usar para crear Web Apps, [back-ends de Mobile Apps](/documentation/learning-paths/appservice-mobileapps/) y [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Con poca intervención por su parte, podrá:

- Implementar una aplicación web de ejemplo (elija entre ASP.NET, PHP, Node.js, Java o Python).
- Visualizar la aplicación en ejecución en cuestión de segundos.
- Actualizar la aplicación web del mismo modo que [insertaría confirmaciones de Git](https://git-scm.com/docs/git-push).

También echaremos un primer vistazo al [Portal de Azure](https://portal.azure.com) y a las características allí disponibles.

## Requisitos previos

- [Instalación de Git](http://www.git-scm.com/downloads).
- [Instalación de la CLI de Azure](../xplat-cli-install.md)
- Obtenga una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para una evaluación gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Vea una aplicación web en acción. [Pruebe App Service](http://go.microsoft.com/fwlink/?LinkId=523751) inmediatamente y cree una aplicación de inicio de corta duración (no se requiere tarjeta de crédito ni se establece ningún compromiso).

## Implementación de una aplicación sitio

Vamos a implementar una aplicación web en el Servicio de aplicaciones de Azure.

1. Abra un símbolo del sistema de Windows, una ventana de PowerShell, un shell de Linux o un terminal de OS X. Ejecute `git --version` y `azure --version` para comprobar que Git y la CLI de Azure estén instalados en el equipo.

    ![Prueba de la instalación de las herramientas de la CLI para su primera aplicación web en Azure](./media/app-service-web-get-started/1-test-tools.png)

    Si no ha instalado las herramientas, consulte [Requisitos previos](#Prerequisites) para obtener vínculos de descarga.

1. Cambie a un directorio de trabajo (`CD`) y clone la aplicación de ejemplo como se indica a continuación:

        git clone <github_sample_url>

    ![Clonación del código de ejemplo de aplicación de su primera aplicación web de Azure](./media/app-service-web-get-started/2-clone-sample.png)

    Para *&lt;github\_sample\_url>*, use una de las siguientes direcciones URL, en función del marco que le guste:

    - HTML+CSS+JS: [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
    - ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
    - PHP (CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
    - Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git)
    - Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
    - Python (Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

2. Cambie al repositorio de la aplicación de ejemplo. Por ejemplo:

        cd app-service-web-html-get-started

3. Inicie sesión en Azure como se indica a continuación:

        azure login

    Siga el mensaje de ayuda para continuar con el proceso de inicio de sesión.

    ![Inicio de sesión en Azure para crear su primera aplicación web](./media/app-service-web-get-started/3-azure-login.png)

4. Cree el recurso de aplicación de App Service en Azure con un nombre de aplicación único mediante el siguiente comando. Cuando se le solicite, especifique el número de la región deseada.

        azure site create --git <app_name>

    ![Creación del recurso de Azure para su primera aplicación web en Azure](./media/app-service-web-get-started/4-create-site.png)

    >[AZURE.NOTE] Si nunca ha configurado credenciales de implementación para su suscripción de Azure, se le solicitará que las cree. App Service usa estas credenciales, no las de su cuenta de Azure, solo en implementaciones de Git e inicios de sesión FTP.

    La aplicación se crea en Azure ahora. El directorio actual también se inicializa con Git y se conecta a la nueva aplicación del Servicio de aplicaciones como un Git remoto. Puede dirigirse a la dirección URL de la aplicación (http://&lt;app_name>.azurewebsites.net) para ver la bonita página HTML predeterminada, pero usemos ahora su propio código.

4. Implemente su código de ejemplo en la nueva aplicación de App Service igual que insertaría cualquier código con Git:

        git push azure master

    ![Inserción de código en su primera aplicación web de Azure](./media/app-service-web-get-started/5-push-code.png)

    Si usó uno de los marcos de lenguaje, verá un resultado diferente. Esto se debe a que `git push` no solo inserta código en Azure, sino que también desencadena tareas de implementación en el motor de implementación. Si tiene algún archivo package.json (Node.js) o requirements.txt (Python) en la raíz del proyecto (repositorio) o tiene un archivo packages.config en el proyecto ASP.NET, el script de implementación restaura automáticamente los paquetes necesarios. También puede [habilitar la extensión Composer](web-sites-php-mysql-deploy-use-git.md#composer) para procesar automáticamente los archivos composer.json en la aplicación PHP.

Enhorabuena, ha implementado la aplicación en el Servicio de aplicaciones de Azure.

## Visualización de la aplicación en ejecución

Para ver cómo la aplicación se ejecuta en Azure, ejecute este comando desde cualquier directorio del repositorio:

    azure site browse

## Realización de actualizaciones en la aplicación

Ahora puede usar Git para efectuar inserciones desde la raíz del proyecto (repositorio) con el fin de realizar una actualización en el sitio activo. Esto se hace del mismo modo que cuando implementó la aplicación en Azure por primera vez. Por ejemplo, cada vez que desee insertar un cambio nuevo que haya probado localmente, ejecute los siguientes comandos desde la raíz del proyecto (repositorio):

    git add .
    git commit -m "<your_message>"
    git push azure master

## Visualización de la aplicación en el portal de Azure

Ahora, vayamos al Portal de Azure para ver lo que ha creado:

1. Inicie sesión en el [portal de Azure](https://portal.azure.com) con la cuenta Microsoft que tenga su suscripción de Azure.

2. En la barra de la izquierda, haga clic en **Servicios de aplicaciones**.

3. Haga clic en la aplicación que acaba de crear para abrir su página en el portal (denominada [hoja](../azure-portal-overview.md)). Para su comodidad, también se abre la hoja **Configuración** de forma predeterminada.

    ![Vista del portal de su primera aplicación web de Azure](./media/app-service-web-get-started/portal-view.png)

La hoja del portal de la aplicación de App Service presenta un elevado número de opciones y herramientas que le ayudan a configurar, supervisar y proteger su aplicación, así como resolver cualquier problema que surja con ella. Dedique unos minutos a familiarizarse con esta interfaz mediante la realización de algunas tareas sencillas (el número de la tarea corresponde al número de la captura de pantalla).

1. Detenga la aplicación.
2. Reinicie la aplicación.
3. Haga clic en el vínculo **Grupo de recursos** para ver todos los recursos implementados en el grupo de recursos.
4. Haga clic en **Configuración** > **Propiedades** para ver otra información acerca de la aplicación.
5. Haga clic en **Herramientas** para acceder a herramientas útiles de supervisión y solución de problemas.

## Pasos siguientes

- Lleve su aplicación de Azure aún más lejos. Facilite su protección con autenticación. Escálela según la demanda. Configure algunas alertas de rendimiento. Todo ello con uno cuantos clics. Consulte [Incorporación de funcionalidad a su primera aplicación web](app-service-web-get-started-2.md).
- Aparte de Git y la CLI de Azure, existen otras formas de implementar aplicaciones web en Azure. Consulte [Documentación de implementación del Servicio de aplicaciones de Azure](../app-service-web/web-sites-deploy.md). Para ver los pasos de desarrollo e implementación preferidos para su marco de lenguaje, seleccione el marco en la parte superior del artículo.

<!---HONumber=AcomDC_0907_2016-->