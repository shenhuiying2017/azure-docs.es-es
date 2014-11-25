<properties urlDisplayName="How to create and deploy" pageTitle="Creación e implementación de un servicio en la nube - Azure" metaKeywords="Creación de servicios en la nube de Azure, eliminación de servicio en la nube" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />





#Creación e implementación de un servicio en la nube

El Portal de administración de Azure ofrece dos formas de crear e implementar un servicio en la nube: **Quick Create** y **Custom Create**. 

En este tema se explica cómo usar el método Quick Create para crear un servicio en la nube y, a continuación, usar **Upload** para cargar e implementar un paquete de servicio en la nube en Azure. Cuando utiliza este método, el Portal de administración de Azure pone a su disposición los vínculos pertinentes para completar todos los requisitos que vaya necesitando sobre la marcha. Si está listo para implementar su servicio en la nube una vez creado, puede hacer las dos cosas a la vez usando **Custom Create**. 

**Nota:** si tiene pensado publicar su servicio en la nube desde los servicios de Team Foundation (TFS) de Windows, use Quick Create y, a continuación, configure la publicación TFS desde **Quick Start** o el panel. Para obtener más información, consulte [Entrega continua a Azure usando Visual Studio Online][TFSTutorialForCloudService] o la ayuda de la página **Inicio rápido**.

##Tabla de contenido##

* [Conceptos](#concepts)
* [Preparación de la aplicación](#prepare)
* [Antes de empezar](#begin)
* [Direccionamiento del un servicio en la nube usando Quick Create](#quick)
* [Direccionamiento del un certificado para un servicio en la nube](#uploadcertificate)
* [Direccionamiento del un servicio en la nube](#deploy)


<h2><a id="concepts"></a>Conceptos</h2>
Se necesitan tres componentes para implementar una aplicación como servicio en la nube en Azure:

>- **archivo de definición de servicio**   El archivo de definición de servicio en la nube (.csdef) define el modelo de servicio, incluido el número de roles.

>- **archivo de configuración de servicio**   El archivo de configuración de servicio en la nube (.cscfg) proporciona opciones de configuración para los roles de servicio en la nube e individuales, incluyendo el número de instancias de rol.

>- **paquete de servicio**   El paquete de servicio (.cspkg) contiene el código de la aplicación y el archivo de definición de servicio.

<h2><a id="prepare"></a>Preparación de la aplicación</h2>
Antes de implementar un servicio en la nube, debe crear el paquete de servicio en la nube (.cspkg) desde su código de aplicación y un archivo de configuración de servicio en la nube (.cscfg). Cada paquete de servicio en la nube contiene archivos de aplicación y configuraciones. El archivo de configuración de servicio proporciona las opciones de configuración.

El SDK de Azure proporciona herramientas para preparar estos archivos de implementación necesarios. Puede instalar el SDK desde la página [Descargas de Azure](http://www.windowsazure.com/es-es/develop/downloads/), en el idioma en que prefiera implementar su código de aplicación.

Hay tres características del servicio en la nube que requieren configuraciones especiales antes de exportar un paquete de servicio:

- Si desea implementar un servicio en la nube que utilice Capa de sockets seguros (SSL) para el cifrado de datos, configure la aplicación para SSL. Para obtener más información, consulte [Configurar un certificado SSL en un extremo HTTPS](http://msdn.microsoft.com/es-es/library/windowsazure/ff795779.aspx).

- Si desea configurar una conexión a Escritorio remoto en instancias de rol, configure los roles para Escritorio remoto. Para obtener más información acerca de cómo preparar el archivo de definición de servicio para el acceso remoto, consulte [Establecer una conexión a Escritorio remoto para un rol de Azure](http://msdn.microsoft.com/es-es/library/hh124107.aspx).

- Si desea configurar una supervisión exhaustiva para su servicio en la nube, habilite Diagnósticos de Azure para el servicio en la nube. *Supervisión mínima* (nivel predeterminado de supervisión) usa contadores de rendimiento recopilados de los sistemas operativos host para las instancias de rol (máquinas virtuales). "La supervisión exhaustiva* recopila métricas adicionales basadas en los datos del rendimiento dentro de las instancias de rol para permitir un análisis más profundo de los problemas que se producen durante el procesamiento de la aplicación. Para obtener información acerca de cómo habilitar Diagnósticos de Azure, consulte [Habilitación de los diagnósticos en Azure](http://www.windowsazure.com/es-es/develop/net/common-tasks/diagnostics/).

- Para crear un servicio en la nube con implementaciones de roles web o roles de trabajo, debe crear el paquete de servicio. Para obtener más información acerca de los archivos relacionados con el paquete, consulte [Configurar un servicio en la nube para Azure](http://msdn.microsoft.com/es-es/library/hh124108.aspx). Para crear el archivo del paquete, consulte [Empaquetar una aplicación de Azure](http://msdn.microsoft.com/es-es/library/hh403979.aspx). Si usa Visual Studio para desarrollar la aplicación, consulte [Publicar un servicio en la nube mediante Azure Tools](http://msdn.microsoft.com/es-es/library/ff683672.aspx).

<h2><a id="begin"></a>Antes de empezar</h2>

- Si no ha instalado el SDK de Azure, haga clic en **Install Azure SDK** para abrir la [Página de descargas de Azure](http://www.windowsazure.com/es-es/develop/downloads/) y, a continuación, descargue el SDK en el idioma en que prefiera desarrollar su código. (Tendrá la oportunidad de hacer esto más tarde).

- Si hay instancias de rol que necesitan certificados, créelos. Los servicios en la nube requieren un archivo .pfx con una clave privada. Puede cargar los certificados en Azure mientras crea e implementa el servicio en la nube. Para obtener más información sobre los certificados, consulte [Administrar certificados](http://msdn.microsoft.com/es-es/library/gg981929.aspx).

- Si tiene pensado implementar el servicio en la nube en un grupo de afinidad, cree dicho grupo. Puede usar un grupo de afinidad para implementar su servicio en la nube y otros servicios de Azure en la misma ubicación de una región. Puede crear el grupo de afinidad en el área **Networks** del Portal de administración, en la página **Grupos de afinidad**. Para obtener más información, consulte [Crear un grupo de afinidad en el Portal de administración](http://msdn.microsoft.com/es-es/library/jj156209.aspx).


<h2><a id="quick"></a>Direccionamiento del un servicio en la nube usando Quick Create</h2>

1. En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Nuevo**>**Proceso**>**Servicio en la nube**>**Creación rápida**.

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. En **URL**, escriba el nombre del subdominio que vaya a usar en la URL pública para obtener acceso a su servicio en la nube en las implementaciones de producción. El formato de la URL para las implementaciones de producción es el siguiente: http://*myURL*.cloudapp.net.

3. En **Región o grupo de afinidad**, seleccione la zona geográfica o el grupo de afinidad en el que desea implementar el servicio en la nube. Seleccione un grupo de afinidad si desea implementar un servicio en la nube en la misma ubicación que los otros servicios de Azure de una región.

4. Haga clic en **Crear servicio en la nube**.

	![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

	Puede supervisar el estado del proceso en el área de mensajes situada en parte inferior de la ventana.

	El área **Servicios en la nube** se abre y en ella se muestran los servicios. Si el estado cambia a "Created", significa que la creación de servicios en la nube se ha completado correctamente.

	![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


<h2><a id="uploadcertificate"></a>Direccionamiento del un certificado para un servicio en la nube</h2>

1. En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**, haga clic en el nombre del servicio en la nube y, a continuación, haga clic en **Certificados**.

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. Haga clic en **Cargar un certificado** o **Cargar**.

3. En **File**, use **Browse** para seleccionar el certificado (archivo .pfx).

4. En **Password**, escriba la clave privada del certificado.

5. Haga clic en **OK** (marca de verificación).

	![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

	Puede ver el progreso de carga en el área de mensajes que se muestra a continuación. Cuando la carga termine, el certificado se agregará a la tabla. En el área de mensajes, haga clic en OK para cerrar el mensaje.

	![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

<h2><a id="deploy"></a>Direccionamiento del un servicio en la nube</h2>

1. En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**, haga clic en el nombre del servicio en la nube y, a continuación, haga clic en **Panel**.

	El panel se abre en el entorno de producción y, en este punto, puede seleccionar Staging para implementar la aplicación en el entorno de ensayo. Para obtener más información, consulte [Administrar implementaciones en Azure](http://msdn.microsoft.com/es-es/library/gg433027.aspx).

	 
2. Haga clic en **Cargue una nueva implementación de producción** o **Cargar**.

3. En **Etiqueta de implementación**, escriba un nombre para la nueva implementación; por ejemplo, MyCloudServicev4.

3. En **Package**, utilice **Browse** para seleccionar el archivo de paquete de servicio (.cspkg) que quiera usar.

4. En **Configuration**, utilice **Browse** para seleccionar el archivo de configuración de servicio (.cscfg) que quiera usar.

5. Si el servicio en la nube incluye roles con una sola instancia, active la casilla **Implementar aunque uno o varios roles contengan una sola instancia** para habilitar la implementación y continuar.

 Azure solo puede garantizar el 99,95 % de acceso al servicio en la nube durante el mantenimiento y las actualizaciones de servicio si cada rol tiene dos instancias como mínimo. Si procede, puede agregar instancias de rol adicionales en la página **Escala** después de implementar el servicio en la nube. Para obtener más información, consulte [Contratos de Nivel de Servicio](http://www.windowsazure.com/es-es/support/legal/sla/).

6. Haga clic en **OK** (marca de verificación) para iniciar la implementación del servicio en la nube.

	![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
 

	Puede supervisar el estado de la implementación en el área de mensajes. Haga clic en OK para ocultar el mensaje.

	![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

###Para comprobar que la implementación se haya completado correctamente###

1. Haga clic en **Panel**.

	El estado debe mostrar que el servicio está **En ejecución**.

2. Debajo de **quick glance**, haga clic en la URL del sitio para abrir su servicio en la nube en un explorador web.

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409

	![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)

