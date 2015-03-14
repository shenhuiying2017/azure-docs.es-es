<properties 
	pageTitle="Implementación de Node.js con Cloud9 - Tutorial de Azure" 
	description="Aprenda a utilizar Cloud9 IDE para desarrollar, compilar e implementar una aplicación Node.js en Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Implementación de una aplicación de Azure desde Cloud9

En este tutorial se describe cómo usar Cloud9 IDE para desarrollar, compilar e
implementar una aplicación Node.js en Azure.

En este tutorial, aprenderá a:

-   Crear un proyecto de Cloud9 IDE
-   Implementar el proyecto en Azure
-   Actualizar una implementación existente de Azure
-   Mover proyectos entre las implementaciones de ensayo y producción

[Cloud9 IDE][] proporciona un entorno de desarrollo entre plataformas basado en el
explorador. Una de las características que Cloud9 admite para los proyectos de Node.js es
que puede implementarlo directamente en Azure desde IDE.
Cloud9 también se integra con los servicios de repositorio GitHub y BitBucker,
por lo que compartir su proyecto con otros usuarios es sencillo.

Con Cloud9, puede desarrollar e implementar una aplicación en Azure
desde muchos exploradores y sistemas operativos modernos, sin la necesidad de
instalar herramientas de desarrollo adicionales o SDK de manera local. En los siguientes pasos
se ha utilizado Google Chrome en Mac.

## Registro

Para usar Cloud9, primero necesita visitar su sitio web y [registrarse para obtener una
suscripción a ][Cloud9 IDE]. Puede iniciar sesión con una cuenta
existente de GitHub o BitBucket o puede crear una cuenta de Cloud9. Hay disponible
una oferta de suscripción gratuita, además de otra de pago que
proporciona más características. Para obtener más información, consulte [Cloud9 IDE][].

## Creación de un proyecto Node.js

1.  Inicie sesión en Cloud9, haga clic en el símbolo **+** junto a **Mis proyectos** y,
    a continuación, seleccione **Crear nuevo proyecto**.

	![create new Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png)

2.  En el cuadro de diálogo **Crear nuevo proyecto**, escriba un nombre de proyecto,
    acceso y tipo de proyecto. Haga clic en **Crear** para crear el proyecto.

	![create new project dialog Cloud9](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png)

	> [AZURE.NOTE] Algunas opciones requieren un plan de pago de Cloud9.
	   
	> [AZURE.NOTE] No se utiliza el nombre del proyecto de su proyecto de Cloud9 cuando se implementa en Azure.

3.  Una vez creado el proyecto, haga clic en **Iniciar edición**. Si esta es la primera vez que utiliza Cloud9 IDE, se le ofrecerá la opción de realizar un recorrido por el servicio. Si desea omitir el recorrido y verlo después, seleccione **Solo el editor**.

	![start editing the Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png)

4.  Para crear una nueva aplicación Node, seleccione **Archivo** y luego **Nuevo
    archivo**.

	![create new file in the Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png)

5.  Aparecerá una pestaña nueva con el título **Untitled1**. Escriba el
    siguiente código en la pestaña **Untitled1** para crear el nodo de
    aplicación:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);
	
	> [AZURE.NOTE] El uso de process.env.PORT asegura que la aplicación seleccione el puerto correcto si se ejecuta en el depurador de Cloud9 o si se implementa en Azure.

6.  Para guardar el código, seleccione **Archivo** y, a continuación, **Guardar como**. En el cuadro de diálogo
    **Guardar como**, escriba **server.js** como el nombre del archivo y, a continuación,
    haga clic en **Guardar**.


	> [AZURE.NOTE] Es posible que vea un símbolo de advertencia que indica que la variable req no se ha usado. Puede ignorar esta advertencia de forma segura.

	![save the server.js file](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png)

## Ejecución de la aplicación

> [AZURE.NOTE] Aun cuando los pasos que se proporcionan en esta sección son suficientes para una aplicación Hello World, es posible que para las aplicaciones que usan módulos externos tenga que seleccionar una versión específica de Node.js para el entorno de depuración. Para hacerlo, seleccione **Configurar...** en el menú desplegable de depuración y, a continuación, seleccione la versión específica de Node.js. Por ejemplo, es posible que reciba errores de autenticación al usar el módulo 'azure' si no tiene seleccionado Node.js 0.6.x.


1.  Haga clic en **Depurar** para ejecutar la aplicación en el depurador de Cloud9.
	
	![run in the debugger](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png)

2.  Aparece una ventana de salida. Haga clic en la dirección URL que aparece para
    tener acceso a la aplicación a través de una ventana del explorador.

	![output window](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png)

	La aplicación resultante tendrá el aspecto que se muestra a continuación:

	![application running in browser](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png)

3.  Para detener la depuración de la aplicación, haga clic en **detener**.

## Creación de una cuenta de Azure

Necesita una cuenta para implementar la aplicación en Azure. Si
todavía no dispone de una cuenta de Azure, puede registrarse para obtener una
prueba gratuita con estos pasos:

[AZURE.INCLUDE [create-azure-account](../includes/create-azure-account.md)]


## Creación de una implementación

1.  Para crear una nueva implementación, seleccione **Implementar** y, a continuación, haga clic en **+** para crear un servidor de implementación.

    ![create a new deployment][create a new deployment]

2.  En el cuadro de diálogo **Agregar un destino de implementación**, escriba un nombre de implementación y luego seleccione **Azure** en la lista **Elegir tipo**. El nombre de la implementación que especifique se usará para identificar la implementación dentro de Cloud9; no corresponderá a un nombre de implementación dentro de Azure.

3.  Si esta es la primera vez que ha creado una implementación de Cloud9 que usa Azure, debe configurar sus ajustes de publicación de Azure. Lleve a cabo los siguientes pasos para descargar e instalar esta configuración en Cloud9:

    1.  Haga clic en **Descargar configuración de Azure**.

        ![download publish settings](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png)

        Esta acción abre el Portal de administración de Azure y le solicita descargar la configuración de publicación de Azure. Se le pedirá que inicie sesión en su cuenta de Azure para comenzar.

    2.  Guarde el archivo de configuración de publicación en su unidad local.

    3.  En el cuadro de diálogo **Agregar un destino de implementación**, seleccione **Elegir archivo** y,
        a continuación, seleccione el archivo descargado en el paso anterior.

    4.  Después de seleccionar el archivo, haga clic en **Cargar**.

4.  Haga clic en **+ Crear nuevo** para crear un servicio hospedado nuevo. Un *hosted service* es el contenedor en el que su aplicación se hospeda cuando se implementa en Azure. Para obtener más información, consulte [Información general de la creación de un servicio hospedado para Azure][].

	![create a new deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png)

5.  Se le solicitará el nombre del nuevo servicio hospedado y las opciones de configuración, como la cantidad de instancias, el sistema operativo del host y el centro de datos. El nombre de la implementación especificado se usará como el nombre del servicio hospedado en Azure. Este nombre debe ser único dentro del sistema Azure.
	
	![create a new hosted service](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png)

	> [AZURE.NOTE] En el cuadro de diálogo **Agregar un destino de implementación**, todo servicio hospedado de Azure existente se mencionará en la sección **Elegir implementación existente**; la selección de un servicio hospedado existente tiene como resultado que este proyecto se implemente en ese servicio.

	> [AZURE.NOTE] La selección de **Habilitar RDP** y la especificación de un nombre de usuario y contraseña habilitará un escritorio remoto para su implementación.


## Implementación en el entorno de producción de Azure

1.  Seleccione la implementación que creó en los pasos anteriores. Aparecerá un
    cuadro de diálogo que proporciona información acerca de esta implementación, así como
    la dirección URL de producción que se usará después de la implementación en Microsoft
    Azure.

	![select a deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png)

2.  Seleccione **Implementar en entorno de producción**.

3.  Haga clic en **Implementar** para comenzar la implementación.

4.  Si esta es la primera vez que ha implementado este proyecto en Azure, recibirá un error de **"No web.config found"**. Seleccione **Sí** para crear el archivo. Esta acción agregará un archivo  'Web.cloud.config' al proyecto.
	
	![no web.config file found message](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png)

5.  Si esta es la primera vez que ha implementado este proyecto en Azure, recibirá un error de **"No 'csdef' file present"**. Seleccione **Sí** para crear el archivo .csdef. Esa acción agregará un archivo 'ServiceDefinition.csdef' a su proyecto.    ServiceDefinition.csdef es un archivo específico de Azure necesario para publicar su aplicación. Para obtener más información, consulte [Información general de la creación de un servicio hospedado para Azure][].

6.  Se le solicitará que seleccione el tamaño de la instancia para esta aplicación. Seleccione **Pequeña** y, a continuación, haga clic en **Crear**. Para obtener más detalles sobre los tamaños de la máquina virtual de Azure, consulte [Configuración de tamaños de la máquina virtual][].

	![specify csdef file values](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png)

7.  La entrada de implementación mostrará el estado del proceso de implementación. Una vez completa, la implementación aparecerá como **Activa**.

	![deployment status](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png)

	> [AZURE.NOTE] A los proyectos que se implementan mediante Cloud 9 IDE se les asigna una GUID como nombre de implementación en Azure.

8.  El cuadro de diálogo de la implementación incluye un vínculo a la URL de producción. Cuando se complete la implementación, haga clic en la URL para dirigirse a su aplicación que se ejecuta en Azure.

	![Azure production URL link](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png)

## Actualización de la aplicación

Cuando realiza cambios en su aplicación, puede usar Cloud9 para implementar la aplicación actualizada en el mismo servicio hospedado de Azure.

1.  En el archivo server.js, actualice su código de modo que se imprima "hello azure v2" en la pantalla. Puede reemplazar el código existente por el siguiente código actualizado:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  Para guardar el código, seleccione **Archivo** y, a continuación, **Guardar**.

## Implementación de la actualización en el entorno de ensayo de Azure

1.  Seleccione **Implementar en entorno de ensayo**.

2.  Haga clic en **Implementar** para comenzar la implementación.

	Cada servicio hospedado de Azure admite dos entornos, ensayo y producción. El entorno de ensayo es exactamente igual al entorno de producción, salvo que solo puede tener acceso a la aplicación de ensayo con una URL confusa basada en GUID que se genera mediante Azure. Puede usar el entorno de ensayo para probar su aplicación y, después de verificar los cambios, puede mover la versión de ensayo la producción mediante un intercambio de IP virtual (VIP), como se describe posteriormente en este tutorial.

3.  Cuando su aplicación se implementa en ensayo, la URL de ensayo basada en GUID aparecerá en la salida de la consola, como se muestra en la captura de pantalla continuación. Haga clic en la URL para abrir su aplicación de ensayo en un explorador.

	![console output showing staging URL](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png)

## Actualización a producción con el intercambio de VIP

Cuando un servicio se implementa en el entorno de producción o en el entorno de
ensayo, se asigna una dirección IP virtual (VIP) al servicio
en ese entorno. Si desea transferir un servicio desde el entorno de
ensayo al entorno de producción, puede hacerlo sin
volver a implementarlo a través de un intercambio de VIP, que intercambia las implementaciones en ensayo y
producción. Un intercambio de VIP pone su aplicación de ensayo en
producción sin tiempo de inactividad en el entorno de producción. Para obtener más
detalles, consulte [Información general sobre la administración de implementaciones en Azure.][]

1.  En el cuadro de diálogo, haga clic en el vínculo **Abrir portal** para abrir el
    Portal de administración de Azure.

	![Link from deploy dialog to Azure Management Portal][Link from deploy dialog to Azure Management Portal]

2.  Inicie sesión en el portal con sus credenciales.

3.  A la izquierda de la página web, seleccione **Servicios hospedados, cuentas de
    almacenamiento y CDN** y, a continuación, haga clic en **Servicios hospedados**.

	![Azure Management Portal][Azure Management Portal]

	El panel de resultados muestra el servicio hospedado con el nombre que especificó en Cloud9 y dos implementaciones, una con el valor    **Environment** como **Staging** y el segundo como **Production**.

4.  Para realizar el intercambio de VIP, seleccione el servicio hospedado y, a continuación, haga clic en **Intercambiar VIP** en la cinta.

	![VIP SWAP](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png)

5.  Haga clic en **Aceptar** en el cuadro de diálogo Intercambiar VIP que aparece.

6.  Diríjase a su aplicación de producción. Podrá ver que la versión de la aplicación que se implementó anteriormente para ensayo se encuentra ahora en producción.

	![Production application running on Azure](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png)

## Uso del escritorio remoto

Si habilitó RDP y especificó un nombre de usuario y contraseña al crear su implementación, puede usar Escritorio remoto para conectarse a su servicio hospedado al seleccionar una instancia específica y, a continuación, seleccionar Conectar en
la cinta.

![Connect to an instance](./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png)

Cuando haga clic en Connect, se le solicitará abrir o descargar un archivo .RDP. Este archivo contiene la información necesaria para conectarse a su sesión de escritorio remoto. La ejecución de este archivo en un sistema Windows le solicitará el nombre de usuario y la contraseña que escribió al crear su implementación y, posteriormente, lo conectará al escritorio de la instancia
seleccionada.

> [AZURE.NOTE] El archivo .RDP para conectarse a la instancia hospedada de su aplicación solo funcionará con la aplicación Escritorio remoto de
Windows.

## Detención y eliminación de la aplicación

Azure factura las instancias de rol por hora de tiempo de servidor utilizado, y el tiempo de servidor se consume mientras la aplicación se implementa, aunque las instancias no se estén ejecutando y estén detenidas. Además,
el tiempo de servidor se consume a través de las implementaciones de producción y ensayo.

Cloud9 se centra en proporcionar un IDE y no proporciona un método directo para detener o eliminar una aplicación una vez que se haya implementado en Azure. Para eliminar una aplicación hospedada en Azure, realice los siguientes pasos:

1.  En el cuadro de diálogo de implementación, haga clic en el vínculo **Abrir portal** para abrir el Portal de administración de Azure.

	![Link from deploy dialog to Azure Management Portal](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png)

2.  Inicie sesión en el portal con sus credenciales.

3.  A la izquierda de la página web, seleccione **Servicios hospedados, cuentas de almacenamiento y CDN** y, a continuación, haga clic en **Servicios hospedados**.

4.  Seleccione la implementación de ensayo (indicada por el valor **Environment**). Haga clic en **Eliminar** en la cinta para eliminar la aplicación.

	![delete the deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png)

5.  Seleccione la implementación de producción y, a continuación, haga clic en **Eliminar** para eliminar también esa aplicación.

## Recursos adicionales

-   [Documentación de Cloud9][]


  [Cloud9 IDE]: http://cloud9ide.com/ 
  [Información general de la creación de un servicio hospedado para Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
  [Configuración de tamaños de la máquina virtual]: http://msdn.microsoft.com/library/windowsazure/ee814754.aspx
  [Información general de administración de implementaciones en Azure]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
  [Información general sobre la administración de implementaciones en Azure.]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
  [Documentación de Cloud9]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409

<!--HONumber=45--> 
