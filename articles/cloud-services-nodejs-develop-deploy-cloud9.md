<properties urlDisplayName="Deploying with Cloud9" pageTitle="Implementaci&oacute;n de Node.js con Cloud9 - Tutorial de Azure" metaKeywords="Cloud9 IDE Azure, Azure node.js, Azure node apps" description="Aprenda a utilizar Cloud9 IDE para desarrollar, compilar e implementar una aplicaci&oacute;n Node.js en Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Implementaci&oacute;n de una aplicaci&oacute;n de Azure desde Cloud9" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />

# Implementación de una aplicación de Azure desde Cloud9

En este tutorial se describe cómo usar Cloud9 IDE para desarrollar, compilar e
implementar una aplicación Node.js en Azure.

En este tutorial, aprenderá a:

-   Crear un proyecto de Cloud9 IDE
-   Implementar el proyecto en Azure
-   Actualizar una implementación existente de Azure
-   Mover proyectos entre las implementaciones de ensayo y producción

[Cloud9 IDE][Cloud9 IDE] proporciona un entorno de desarrollo entre plataformas basado en
el explorador. Una de las características que Cloud9 admite para los proyectos Node.js es la
posibilidad de implementar directamente en Azure desde el IDE.
Cloud9 se integra también con los servicios de repositorio de
GitHub y BitBucket, por lo que es fácil compartir su proyecto con otros.

Al usar Cloud9, puede desarrollar e implementar una aplicación en Azure
desde muchos exploradores y sistemas operativos modernos, sin necesidad de
instalar herramientas de desarrollo adicionales o SDK de manera local. En los siguientes pasos se ha utilizado
Google Chrome en Mac.

## Registro

Para usar Cloud9, primero necesita visitar su página web y [registrarse para obtener una
suscripción][Cloud9 IDE]. Puede iniciar sesión con una cuenta
de GitHub o BitBucket existente o crear una cuenta de Cloud9. Hay disponible una
oferta de suscripción gratuita, además de otra de pago que
proporciona más características. Para obtener más información, consulte [Cloud9 IDE][Cloud9 IDE].

## Creación de un proyecto Node.js

1.  Inicie sesión en Cloud9, haga clic en el símbolo **+** al lado de **My Proyectos**
     y seleccione **Crear un nuevo proyecto**.

    ![Creación de un proyecto nuevo en Cloud9][Creación de un proyecto nuevo en Cloud9]

2.  En el cuadro de diálogo **Crear un nuevo proyecto**, especifique un nombre de proyecto,
    acceso y tipo de proyecto. Haga clic en **Create** para crear el proyecto.

    ![Cuadro de diálogo de creación de proyecto nuevo en Cloud9][Cuadro de diálogo de creación de proyecto nuevo en Cloud9]

    <div class="dev-callout">

    **Nota:**
    Algunas opciones requieren un plan de pago de Cloud9.

    </div>

    <div class="dev-callout">

    **Nota:**
    No se utiliza el nombre del proyecto de su proyecto de Cloud9 cuando se implementa en Azure.

    </div>

3.  Después de que se haya creado el proyecto, haga clic en **Start Editing**. Si esta es la primera vez que utiliza Cloud9 IDE, se le ofrecerá la opción de realizar un recorrido por el servicio. Si desea omitir el recorrido y verlo después, seleccione **Just the editor,please**.

    ![Comienzo de edición del proyecto de Cloud9][Comienzo de edición del proyecto de Cloud9]

4.  Para crear una nueva aplicación Node, seleccione **Archivo** y luego **Nuevo
    Archivo**.

    ![Creación de un archivo nuevo en el proyecto de Cloud9][Creación de un archivo nuevo en el proyecto de Cloud9]

5.  Aparecerá una pestaña nueva con el título **Untitled1**. Escriba el siguiente
    código en la pestaña **Untitled1** para crear la aplicación
    Node:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);

    <div class="dev-callout">

    **Nota:**
    El uso de process.env.PORT asegura que la aplicación seleccione el puerto correcto si se ejecuta en el depurador de Cloud9 o si se implementa en Azure.

    </div>

6.  Para guardar el código, seleccione **File** y, a continuación, **Save as**. En el cuadro de diálogo
    **Guardar como**, escriba **server.js** como el nombre del archivo y
    haga clic en**Guardar**.

    <div class="dev-callout">

    **Nota:**
    Es posible que vea un símbolo de advertencia que indica que la variable req no se ha usado. Puede ignorar esta advertencia de forma segura.

    </div>

    ![Guardar el archivo server.js][Guardar el archivo server.js]

## Ejecución de la aplicación

<div class="dev-callout">

**Nota:**
Aun cuando los pasos que se proporcionan en esta sección son suficientes para una aplicación Hello World, es posible que para las aplicaciones que usan módulos externos tenga que seleccionar una versión específica de Node.js para el entorno de depuración. Para hacer esto, seleccione **Configure...** desde la ventana desplegable de depuración y, a continuación, seleccione la versión específica de Node.js. Por ejemplo, es posible que reciba errores de autenticación al usar el módulo "azure" si no selecciona Node.js 0.6.x.

</div>

1.  Haga clic en **Debug** para ejecutar la aplicación en el depurador de Cloud9.

    ![Ejecución en el depurador][Ejecución en el depurador]

2.  Aparece una ventana de salida. Haga clic en la URL que se menciona
    para tener acceso a su aplicación a través de la ventana del explorador.

    ![Ventana de salida][Ventana de salida]

    La aplicación resultante tendrá el aspecto que se muestra a continuación:

    ![Aplicación que se ejecuta en el explorador][Aplicación que se ejecuta en el explorador]

3.  Para detener la depuración de la aplicación, haga clic en **stop**.

## Creación de una cuenta de Azure

Necesita una cuenta para implementar la aplicación en Azure. Si no tiene una
cuenta de Azure, puede suscribirse para una evaluación gratuita
mediante los siguientes pasos:

[WACOM.INCLUDE [create-azure-account](../includes/create-azure-account.md)]

## Creación de una implementación

1.  Para crear una implementación nueva, seleccione **Deploy** y, a continuación, haga clic en **+** para crear un servidor de implementación.

    [creación de una nueva implementación][creación de una nueva implementación]

2.  En el cuadro de diálogo **Add a deploy target**, escriba un nombre de implementación y luego seleccione **Azure** en la lista **Choose type**. El nombre de la implementación que especifique se usará para identificar la implementación dentro de Cloud9; no corresponderá a un nombre de implementación dentro de Azure.

3.  Si esta es la primera vez que ha creado una implementación de Cloud9 que usa Azure, debe configurar sus ajustes de publicación de Azure. Lleve a cabo los siguientes pasos para descargar e instalar esta configuración en Cloud9:

    1.  Haga clic en **Download Azure Settings**.

        ![Descarga de configuración de publicación][Descarga de configuración de publicación]

        Esta acción abre el Portal de administración de Azure y le solicita descargar la configuración de publicación de Azure. Se le pedirá que inicie sesión en su cuenta de Azure para comenzar.

    2.  Guarde el archivo de configuración de publicación en su unidad local.

    3.  En el cuadro de diálogo **Agregar destino de implementación**, seleccione **Elegir archivo**
         y luego seleccione el archivo que descargó en el paso anterior.

    4.  Después de seleccionar el archivo, haga clic en **Upload**.

4.  Haga clic en **+ Create new** para crear un servicio hospedado nuevo. Un *servicio hospedado* es el contenedor en el que su aplicación se hospeda cuando se implementa en Azure. Para obtener más información, consulte [Overview of Creating a Hosted Service for Azure][Overview of Creating a Hosted Service for Azure].

    ![Creación de una implementación][Creación de una implementación]

5.  Se le solicitará el nombre del nuevo servicio hospedado y las opciones de configuración, como la cantidad de instancias, el sistema operativo del host y el centro de datos. El nombre de la implementación especificado se usará como el nombre del servicio hospedado en Azure. Este nombre debe ser único dentro del sistema Azure.

    ![Creación de un servicio hospedado][Creación de un servicio hospedado]

    <div class="dev-callout">

    **Nota:**
    En el cuadro de diálogo **Add a deploy target**, todo servicio hospedado de Azure existente se mencionará en la sección **Choose existing deployment**; la selección de un servicio hospedado existente tiene como resultado que este proyecto se implemente en ese servicio.

    </div>

    <div class="dev-callout">

    **Nota:**
    La selección de **Enable RDP** y la especificación de un nombre de usuario y contraseña habilitará un escritorio remoto para su implementación.

    </div>

## Implementación en el entorno de producción de Azure

1.  Seleccione la implementación que creó en los pasos anteriores. Aparecerá un
    cuadro de diálogo que proporciona información sobre esta implementación,
    además de la URL de producción que se usará después de la implementación en Windows
    Azure.

    ![Selección de una implementación][Selección de una implementación]

2.  Seleccione **Deploy to Production environment**.

3.  Haga clic en **Deploy** para comenzar la implementación.

4.  Si esta es la primera vez que ha implementado este proyecto en Azure, recibirá un error de **'No web.config found'**. Seleccione **Yes** para crear el archivo. De esta manera se agregará un archivo 'Web.cloud.config' a su proyecto.

    ![Mensaje de que no se encontró el archivo web.config][Mensaje de que no se encontró el archivo web.config]

5.  Si esta es la primera vez que ha implementado este proyecto en Azure, recibirá un error de **'No 'csdef' file present'**. Seleccione **Yes** para crear el archivo .csdef. Esa acción agregará un archivo 'ServiceDefinition.csdef' a su proyecto. ServiceDefinition.csdef es un archivo específico de Azure necesario para publicar su aplicación. Para obtener más información, consulte [Overview of Creating a Hosted Service for Azure][Overview of Creating a Hosted Service for Azure].

6.  Se le solicitará que seleccione el tamaño de la instancia para esta aplicación. Seleccione **Small** y, a continuación, haga clic en **Create**. Para obtener más detalles sobre los tamaños de la máquina virtual de Azure, consulte [Configuración de tamaños de la máquina virtual][Configuración de tamaños de la máquina virtual].

    ![Especificación de los valores del archivo csdef][Especificación de los valores del archivo csdef]

7.  La entrada de implementación mostrará el estado del proceso de implementación. Una vez completa, la implementación aparecerá como **Active**.

    ![Estado de la implementación][Estado de la implementación]

    <div class="dev-callout">

    **Nota:**
    A los proyectos que se implementan mediante Cloud 9 IDE se les asigna una GUID como nombre de implementación en Azure.

    </div>

8.  El cuadro de diálogo de la implementación incluye un vínculo a la URL de producción. Cuando se complete la implementación, haga clic en la URL para dirigirse a su aplicación que se ejecuta en Azure.

    ![Vínculo URL de producción de Azure][Vínculo URL de producción de Azure]

## Actualización de la aplicación

Cuando realiza cambios en su aplicación, puede usar Cloud9 para implementar la aplicación actualizada en el mismo servicio hospedado de Azure.

1.  En el archivo server.js, actualice su código de modo que se imprima "hello azure v2" en la pantalla. Puede reemplazar el código existente por el siguiente código actualizado:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  Para guardar el código, seleccione **File** y, a continuación, **Save**.

## Implementación de la actualización en el entorno de ensayo de Azure

1.  Seleccione **Deploy to Staging**.

2.  Haga clic en **Deploy** para comenzar la implementación.

    Cada servicio hospedado de Azure admite dos entornos, ensayo y producción. El entorno de ensayo es exactamente igual al entorno de producción, salvo que solo puede tener acceso a la aplicación de ensayo con una URL confusa basada en GUID que se genera mediante Azure. Puede usar el entorno de ensayo para probar su aplicación y, después de verificar los cambios, puede mover la versión de ensayo la producción mediante un intercambio de IP virtual (VIP), como se describe posteriormente en este tutorial.

3.  Cuando su aplicación se implementa en ensayo, la URL de ensayo basada en GUID aparecerá en la salida de la consola, como se muestra en la captura de pantalla continuación. Haga clic en la URL para abrir su aplicación de ensayo en un explorador.

    ![Salida de la consola que muestra la URL de ensayo][Salida de la consola que muestra la URL de ensayo]

## Actualización a producción con el intercambio de VIP

Cuando se implementa un servicio en los entornos de producción o
ensayo, se asigna una dirección IP virtual (VIP) al servicio
de ese entorno. Cuando desee mover un servicio del entorno de
ensayo al entorno de producción, podrá hacerlo sin necesidad de volver a implementar mediante la
realización de un intercambio VIP, que intercambia las implementaciones de
ensayo y producción. Un intercambio VIP pone su aplicación probada y ensayada en
producción sin tiempo de inactividad en el entorno de producción. Para obtener más
detalles, consulte [Información general sobre la administración de implementaciones en Azure][Información general sobre la administración de implementaciones en Azure].

1.  En el cuadro de diálogo de implementación, haga clic en el vínculo**Abrir portal** para abrir el
    Portal de administración de Azure.

    [Vínculo desde el cuadro de diálogo de implementación al Portal de administración de Azure][Vínculo desde el cuadro de diálogo de implementación al Portal de administración de Azure]

2.  Inicie sesión en el portal con sus credenciales.

3.  En el lado izquierdo de la página web, seleccione **Servicios hospedados, cuentas de almacenamiento
    y CDN**, y haga clic en **Servicios hospedados**.

    [Portal de administración de Azure][Portal de administración de Azure]

    El panel de resultados muestra el servicio hospedado con el nombre que especificó en Cloud9, y dos implementaciones, una con el valor de **Entorno** **Ensayo**, el segundo **Producción**.

4.  Para realizar el intercambio VIP, seleccione el servicio hospedado y, a continuación, haga clic en **Intercambiar VIP** en la cinta.

    ![INTERCAMBIO DE VIP][INTERCAMBIO DE VIP]

5.  Haga clic en **OK** en el cuadro de diálogo de intercambio de VIP que aparece.

6.  Diríjase a su aplicación de producción. Podrá ver que la versión de la aplicación que se implementó anteriormente para ensayo se encuentra ahora en producción.

    ![Aplicación de producción que se ejecuta en Azure][Aplicación de producción que se ejecuta en Azure]

## Uso del escritorio remoto

Si habilitó RDP y especificó un nombre usuario y contraseña al crear su implementación, puede usar Escritorio remoto para conectarse a su servicio hospedado al seleccionar una instancia específica y, a continuación, seleccionar Connect en
la cinta.

![Conexión a una instancia][Conexión a una instancia]

Cuando haga clic en Connect, se le solicitará abrir o descargar un archivo .RDP. Este archivo contiene la información necesaria para conectarse a su sesión de escritorio remoto. La ejecución de este archivo en un sistema Windows le solicitará el nombre usuario y la contraseña que escribió al crear su implementación y, posteriormente, lo conectará al escritorio de la
instancia seleccionada.

<div class="dev-callout">

**Nota:**
El archivo .RDP para conectarse a la instancia hospedada de su aplicación solo funcionará con la aplicación Escritorio remoto de Windows.

</div>

## Detención y eliminación de la aplicación

Azure factura las instancias de rol por hora de tiempo de servidor utilizado, y el tiempo de servidor se consume mientras la aplicación se implementa, aunque las instancias no se estén ejecutando y estén detenidas. Además, el tiempo de
servidor se consume a través de las implementaciones de producción y ensayo.

Cloud9 se centra en proporcionar un IDE y no proporciona un método directo para detener o eliminar una aplicación una vez que se haya implementado en Azure. Para eliminar una aplicación hospedada en Azure, realice los siguientes pasos:

1.  En el cuadro de diálogo de implementación, haga clic en el vínculo **Open portal** para abrir el Portal de administración de Azure.

    ![Vínculo desde el cuadro de diálogo de implementación al Portal de administración de Azure][Vínculo desde el cuadro de diálogo de implementación al Portal de administración de Azure]

2.  Inicie sesión en el portal con sus credenciales.

3.  En el lado izquierdo de la página web, seleccione **Hosted Services, Storage Accounts & CDN** y, a continuación, haga clic en **Hosted Services**.

4.  Seleccione la implementación de ensayo (indicada por el valor **Environment**). Haga clic en **Delete** en la cinta para eliminar la aplicación.

    ![Eliminación de la implementación][Eliminación de la implementación]

5.  Seleccione la implementación de producción y haga clic en **Delete** para eliminar también esa aplicación.

## Recursos adicionales

-   [Cloud9 documentation][Cloud9 documentation]

  [Cloud9 IDE]: http://cloud9ide.com/
  [Creación de un proyecto nuevo en Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png
  [Cuadro de diálogo de creación de proyecto nuevo en Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png
  [Comienzo de edición del proyecto de Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png
  [Creación de un archivo nuevo en el proyecto de Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png
  [Guardar el archivo server.js]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png
  [Ejecución en el depurador]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png
  [Ventana de salida]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png
  [Aplicación que se ejecuta en el explorador]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png
  [Descarga de configuración de publicación]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png
  [Overview of Creating a Hosted Service for Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj155995.aspx
  [Creación de una implementación]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png
  [Creación de un servicio hospedado]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png
  [Selección de una implementación]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png
  [Mensaje de que no se encontró el archivo web.config]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png
  [Configuración de tamaños de la máquina virtual]: http://msdn.microsoft.com/es-es/library/windowsazure/ee814754.aspx
  [Especificación de los valores del archivo csdef]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png
  [Estado de la implementación]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png
  [Vínculo URL de producción de Azure]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png
  [Salida de la consola que muestra la URL de ensayo]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png
  [Información general sobre la administración de implementaciones en Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg433027.aspx
  [INTERCAMBIO DE VIP]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png
  [Aplicación de producción que se ejecuta en Azure]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png
  [Conexión a una instancia]: ./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png
  [Vínculo desde el cuadro de diálogo de implementación al Portal de administración de Azure]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png
  [Eliminación de la implementación]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png
  [Cloud9 documentation]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409
