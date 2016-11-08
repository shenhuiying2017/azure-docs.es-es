---
title: Implementación de DocumentDB y aplicaciones web del Servicio de aplicaciones de Azure mediante una plantilla del Administrador de recursos de Azure | Microsoft Docs
description: Aprenda a implementar una cuenta de DocumentDB, aplicaciones web del Servicio de aplicaciones de Azure y una aplicación web de ejemplo mediante una plantilla del Administrador de recursos de Azure.
services: documentdb, app-service\web
author: h0n
manager: jhubbard
editor: monicar
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2016
ms.author: hawong

---
# <a name="deploy-documentdb-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Implementación de DocumentDB y aplicaciones web de servicio de aplicación de Azure mediante una plantilla del Administrador de recursos de Azure
En este tutorial se muestra cómo usar una plantilla de Azure Resource Manager para implementar e integrar [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), una aplicación web de [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) y una aplicación web de ejemplo.

Con las plantillas de Azure Resource Manager, puede automatizar fácilmente la implementación y la configuración de sus recursos de Azure.  Este tutorial muestra cómo implementar una aplicación web y configurar automáticamente la información de conexión de la cuenta de DocumentDB.

Después de completar este tutorial, podrá responder a las siguientes preguntas:  

* ¿Cómo puedo usar una plantilla del Administrador de recursos de Azure para implementar e integrar una cuenta de DocumentDB y una aplicación web en el Servicio de aplicaciones de Azure?
* ¿Cómo puedo usar una plantilla del Administrador de recursos de Azure para implementar e integrar una cuenta de DocumentDB, una aplicación web de Servicio de aplicaciones de Azure y una aplicación Webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Requisitos previos
> [!TIP]
> Aunque en este tutorial no se exige experiencia con plantillas de Azure Resource Manager o JSON, si quiere modificar las plantillas o las opciones de implementación a las que se hace referencia, entonces sí es necesario tener conocimientos sobre cada una de estas áreas.
> 
> 

Antes de seguir las instrucciones de este tutorial, asegúrese de contar con lo siguiente:

* Una suscripción de Azure. Azure es una plataforma basada en suscripción.  Para más información sobre cómo obtener una suscripción, consulte [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/), [Ofertas para miembros](https://azure.microsoft.com/pricing/member-offers/) o [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="<a-id="createdb"></a>step-1:-download-the-template-files"></a><a id="CreateDB"></a>Paso 1: Descarga de los archivos de plantilla
Para comenzar, vamos a descargar los archivos de plantilla que usaremos en este tutorial.

1. Descargue la plantilla [Create a DocumentDB account, Web Apps, and deploy a demo application sample](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) en una carpeta local (por ejemplo, C:\DocumentDBTemplates). Con esta plantilla se implementará una cuenta de DocumentDB, una aplicación web del Servicio de aplicaciones y una aplicación web.  También se configurará automáticamente la aplicación web para conectar con la cuenta de DocumentDB.
2. Descargue la plantilla [Create a DocumentDB account and Web Apps sample](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) en una carpeta local (por ejemplo, C:\DocumentDBTemplates). Con esta plantilla se implementará una cuenta de DocumentDB y una aplicación web del Servicio de aplicaciones y se modificará la configuración de aplicación del sitio para exponer fácilmente información de conexión de DocumentDB. No se incluye una aplicación web.  

<a id="Build"></a>

## <a name="step-2:-deploy-the-documentdb-account,-app-service-web-app-and-demo-application-sample"></a>Paso 2: Implementación de la cuenta de DocumentDB, la aplicación web del Servicio de aplicaciones y el ejemplo de aplicación de demostración
Ahora vamos a implementar nuestra primera plantilla.

> [!TIP]
> La plantilla no valida que el nombre de la aplicación web y el nombre de la cuenta de DocumentDB especificados a continuación sean a) válidos y b) estén disponibles.  Es muy recomendable comprobar la disponibilidad de los nombres que planea suministrar antes de enviar la implementación.
> 
> 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com), haga clic en Nuevo y busque "Implementación de plantillas".
    ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment1.png)
2. Seleccione el elemento de implementación de plantilla y haga clic en **Crear**
    ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment2.png)
3. Haga clic en **Editar plantilla**, pegue el contenido del archivo de plantilla DocDBWebsiteTodo.json y haga clic en **Guardar**.
   ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment3.png)
4. Haga clic en **Editar parámetros**, proporcione valores para cada uno de los parámetros obligatorios y haga clic en **Aceptar**.  Los parámetros son los siguientes:
   
   1. SITENAME: especifica el nombre de la aplicación web del Servicio de aplicaciones y se usa para crear la URL que se empleará para el acceso a la aplicación web (por ejemplo, si especifica "mydemodocdbwebsite", la URL mediante la que tendrá acceso a la aplicación web será mydemodocdbwebsite.azurewebsites.net).
   2. HOSTINGPLANNAME: especifica el nombre del plan de hospedaje del Servicio de aplicaciones que se creará.
   3. LOCATION: especifica la ubicación de Azure en la que se crearán los recursos de DocumentDB y de la aplicación web.
   4. DATABASEACCOUNTNAME: especifica el nombre de la cuenta de DocumentDB que se creará.   
      
      ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment4.png)
5. Elija un grupo de recursos existente o proporcione un nombre para crear uno nuevo y elija una ubicación para él.
    ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment5.png)
6. Haga clic en **Revisar los términos legales**, **Compra** y luego haga clic en **Crear** para comenzar la implementación.  Seleccione **Anclar al panel** de modo que la implementación resultante se pueda ver fácilmente en la página principal del Portal de Azure.
   ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment6.png)
7. Cuando finalice la implementación, se abrirá la hoja Grupo de recursos.
   ![Captura de pantalla de la hoja Grupo de recursos](./media/documentdb-create-documentdb-website/TemplateDeployment7.png)  
8. Para usar la aplicación, vaya simplemente a la URL de la aplicación web (en el ejemplo anterior, la URL sería http://mydemodocdbwebsite.azurewebsites.net).  Verá la siguiente aplicación web:
   
   ![Aplicación de tareas pendientes de ejemplo](./media/documentdb-create-documentdb-website/image2.png)
9. Siga adelante y cree un par de tareas en la aplicación web, luego vuelva a la hoja Grupo de recursos en el Portal de Azure. Haga clic en el recurso de la cuenta de DocumentDB en la lista Recursos y luego haga clic en el **Explorador de consultas**.
    ![Captura de pantalla del modo de resumen con la aplicación web myotherdocumentdbwebapp resaltada](./media/documentdb-create-documentdb-website/TemplateDeployment8.png)  
10. Ejecute la consulta predeterminada, "SELECT * FROM c" e inspeccione los resultados.  Observe que la consulta ha recuperado la representación JSON de los elementos todo creados en el paso 7 anterior.  No dude en experimentar con las consultas; por ejemplo, intente ejecutar SELECT * FROM c WHERE c.isComplete = true para devolver todos los elementos marcados como completos.
    
    ![Captura de pantalla de las hojas del Explorador de consultas y Resultados en la que se muestran los resultados de la consulta](./media/documentdb-create-documentdb-website/image5.png)
11. No dude en explorar la experiencia del portal de DocumentDB o modificar la aplicación Todo de ejemplo.  Cuando esté preparado, implementaremos otra plantilla.

<a id="Build"></a> 

## <a name="step-3:-deploy-the-document-account-and-web-app-sample"></a>Paso 3: Implementación de la cuenta de documentos y del ejemplo de aplicación web
Ahora implementaremos nuestra segunda plantilla.  Esta plantilla es útil para mostrar cómo insertar información de conexión de DocumentDB, por ejemplo, el punto de conexión y la clave maestra en una aplicación web, como configuración de la aplicación o como cadena de conexión personalizada. Por ejemplo, si tiene su propia aplicación web, quizás prefiera realizar la implementación con una cuenta de DocumentDB y que la información de conexión se rellene automáticamente durante dicha operación.

> [!TIP]
> La plantilla no valida que el nombre de la aplicación web y el nombre de la cuenta de DocumentDB especificados a continuación sean a) válidos y b) estén disponibles.  Es muy recomendable comprobar la disponibilidad de los nombres que planea suministrar antes de enviar la implementación.
> 
> 

1. En el [Portal de Azure](https://portal.azure.com), haga clic en Nuevo y busque "Implementación de plantillas".
    ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment1.png)
2. Seleccione el elemento de implementación de plantilla y haga clic en **Crear**
    ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment2.png)
3. Haga clic en **Editar plantilla**, pegue el contenido del archivo de plantilla DocDBWebSite.json y haga clic en **Guardar**.
   ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment3.png)
4. Haga clic en **Editar parámetros**, proporcione valores para cada uno de los parámetros obligatorios y haga clic en **Aceptar**.  Los parámetros son los siguientes:
   
   1. SITENAME: especifica el nombre de la aplicación web del Servicio de aplicaciones y se usa para crear la URL que se empleará para el acceso a la aplicación web (por ejemplo, si especifica "mydemodocdbwebsite", la URL mediante la que tendrá acceso a la aplicación web será mydemodocdbwebsite.azurewebsites.net).
   2. HOSTINGPLANNAME: especifica el nombre del plan de hospedaje del Servicio de aplicaciones que se creará.
   3. LOCATION: especifica la ubicación de Azure en la que se crearán los recursos de DocumentDB y de la aplicación web.
   4. DATABASEACCOUNTNAME: especifica el nombre de la cuenta de DocumentDB que se creará.   
      
      ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment4.png)
5. Elija un grupo de recursos existente o proporcione un nombre para crear uno nuevo y elija una ubicación para él.
    ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment5.png)
6. Haga clic en **Revisar los términos legales**, **Compra** y luego haga clic en **Crear** para comenzar la implementación.  Seleccione **Anclar al panel** de modo que la implementación resultante se pueda ver fácilmente en la página principal del Portal de Azure.
   ![Captura de pantalla de la interfaz de usuario de implementación de plantillas](./media/documentdb-create-documentdb-website/TemplateDeployment6.png)
7. Cuando finalice la implementación, se abrirá la hoja Grupo de recursos.
   ![Captura de pantalla de la hoja Grupo de recursos](./media/documentdb-create-documentdb-website/TemplateDeployment7.png)  
8. Haga clic en el recurso Web App en la lista Recursos y luego haga clic en **Configuración de la aplicación**
    ![Captura de pantalla del grupo de recursos](./media/documentdb-create-documentdb-website/TemplateDeployment9.png)  
9. Tenga en cuenta que existen valores de configuración de aplicación para el punto de conexión de DocumentDB y para cada una de las claves maestras de DocumentDB.
    ![Captura de pantalla de configuración de la aplicación](./media/documentdb-create-documentdb-website/TemplateDeployment10.png)  
10. Explore libremente el Portal de Azure o siga uno de nuestros [ejemplos](http://go.microsoft.com/fwlink/?LinkID=402386) de DocumentDB para crear su propia aplicación de DocumentDB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Pasos siguientes
¡Enhorabuena! Ha implementado DocumentDB, una aplicación web del Servicio de aplicaciones y una aplicación web de ejemplo mediante plantillas del Administrador de recursos de Azure.

* Para obtener más información sobre DocumentDB, haga clic [aquí](http://azure.com/docdb).
* Para obtener más información acerca de aplicaciones de servicio web de la aplicación de Azure, haga clic en [aquí](http://go.microsoft.com/fwlink/?LinkId=325362).
* Para obtener más información sobre las plantillas del Administrador de recursos de Azure, haga clic [aquí](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="what's-changed"></a>Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkId=529715)

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

<!--HONumber=Oct16_HO2-->


