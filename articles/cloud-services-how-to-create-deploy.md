<properties linkid="manage-services-how-to-create-and-deploy-a-cloud-service" urlDisplayName="How to create and deploy" pageTitle="How to create and deploy a cloud service - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# Creación e implementación de un servicio en la nube

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

El Portal de administración de Azure ofrece dos formas de crear e implementar un servicio en la nube: **Quick Create** y **Custom Create**.

En este tema se explica cómo usar el método Quick Create para crear un servicio en la nube y, a continuación, usar **Upload** para cargar e implementar un paquete de servicio en la nube en Azure. Cuando utiliza este método, el Portal de administración de Azure pone a su disposición los vínculos pertinentes para completar todos los requisitos que vaya necesitando sobre la marcha. Si está listo para implementar su servicio en la nube una vez creado, puede hacer las dos cosas a la vez usando **Custom Create**.

**Nota:** si tiene pensado publicar su servicio en la nube desde los servicios de Team Foundation (TFS) de Windows, use Quick Create y, a continuación, configure la publicación TFS desde **Quick Start** o el panel. Para obtener más información, consulte [Entrega continua a Azure usando Visual Studio Online][TFSTutorialForCloudService] o la ayuda de la página **Inicio rápido**.

## Tabla de contenido

-   [Conceptos](#concepts)
-   [Preparación de la aplicación](#prepare)
-   [Antes de empezar](#begin)
-   [Direccionamiento del un servicio en la nube usando Quick Create](#quick)
-   [Direccionamiento del un certificado para un servicio en la nube](#uploadcertificate)
-   [Direccionamiento del un servicio en la nube](#deploy)

## <span id="concepts"></span></a>Conceptos

Se necesitan tres componentes para implementar una aplicación como servicio en la nube en Azure:

> -   **archivo de definición de servicio**: El archivo de definición de servicio en la nube (.csdef) define el modelo de servicio, incluyendo el número de roles.

> -   **archivo de configuración de servicio**: El archivo de configuración de servicio en la nube (.cscfg) proporciona opciones de configuración para los roles de servicio en la nube e individuales, incluyendo el número de instancias de rol.

> -   **paquete de servicio**: El paquete de servicio (.cspkg) contiene el código de la aplicación y el archivo de definición de servicio.

## <span id="prepare"></span></a>Preparación de la aplicación

Antes de implementar un servicio en la nube, debe crear el paquete de servicio en la nube (.cspkg) desde su código de aplicación y un archivo de configuración de servicio en la nube (.cscfg). Cada paquete de servicio en la nube contiene archivos de aplicación y configuraciones. El archivo de configuración de servicio proporciona las opciones de configuración.

El SDK de Azure proporciona herramientas para preparar estos archivos de implementación necesarios. Puede instalar el SDK desde la página [Descargas de Azure](http://www.windowsazure.com/es-es/develop/downloads/), en el idioma en que prefiera implementar su código de aplicación.

Si no está familiarizado con los servicios en la nube, puede descargar un paquete de servicio en la nube (.cspkg) y un archivo de configuración de servicio (.cscfg) de muestra con el que trabajar desde [Ejemplos de código de Azure](http://code.msdn.microsoft.com/windowsazure/).

Hay tres características del servicio en la nube que requieren configuraciones especiales antes de exportar un paquete de servicio:

-   Si desea implementar un servicio en la nube que utilice Capa de sockets seguros (SSL) para el cifrado de datos, configure su aplicación para SSL. Para obtener más información, consulte [Configurar un certificado SSL en un extremo HTTPS](http://msdn.microsoft.com/es-es/library/windowsazure/ff795779.aspx).

-   Si desea configurar una conexión a Escritorio remoto en instancias de rol, configure los roles para Escritorio remoto. Para obtener más información acerca de cómo preparar el archivo de definición de servicio para el acceso remoto, consulte [Overview of Setting Up a Remote Desktop Connection for a Role](http://msdn.microsoft.com/es-es/library/windowsazure/gg433010.aspx).

-   Si desea configurar una supervisión exhaustiva para su servicio en la nube, habilite Diagnósticos de Azure para el servicio en la nube. *Supervisión mínima* (nivel predeterminado de supervisión) usa contadores de rendimiento recopilados de los sistemas operativos host para las instancias de rol (máquinas virtuales). La *supervisión exhaustiva* recopila métricas adicionales basadas en los datos del rendimiento dentro de las instancias de rol para permitir un análisis más profundo de los problemas que se producen durante el procesamiento de la aplicación. Para obtener información acerca de cómo habilitar Diagnósticos de Azure, consulte [Habilitación de los diagnósticos en Azure](http://www.windowsazure.com/es-es/develop/net/common-tasks/diagnostics/).

## <span id="begin"></span></a>Antes de empezar

-   Si no ha instalado el SDK de Azure, haga clic en **Install Azure SDK** para abrir la [Pagina de descargas de Azure](http://www.windowsazure.com/es-es/develop/downloads/) y, a continuación, descargue el SDK en el idioma en que prefiera desarrollar su código. (Tendrá la oportunidad de hacer esto más tarde).

-   Si hay instancias de rol que necesitan certificados, créelos. Los servicios en la nube requieren un archivo .pfx con una clave privada. Puede cargar los certificados en Azure mientras crea e implementa el servicio en la nube. Para obtener información acerca de cómo crear certificados, consulte [Configurar un certificado SSL en un extremo HTTPS](http://msdn.microsoft.com/es-es/library/windowsazure/ff795779.aspx).

-   Si tiene pensado implementar el servicio en la nube en un grupo de afinidad, créelo. Puede usar un grupo de afinidad para implementar su servicio en la nube y otros servicios de Azure en la misma ubicación de una región. Puede crear el grupo de afinidad en el área **Networks** del Portal de administración, en la página **Grupos de afinidad**. Para obtener más información, consulte la ayuda de la página **Grupos de afinidad**.

## <span id="quick"></span></a>Direccionamiento del un servicio en la nube usando Quick Create

1.  En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Nuevo**, **Proceso**, **Servicio en la nube** y, después, en **Creación rápida**.

    ![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2.  En **URL**, escriba el nombre del subdominio que vaya a usar en la URL pública para obtener acceso a su servicio en la nube en las implementaciones de producción. El formato de la URL para las implementaciones de producción es el siguiente: http://*myURL*.cloudapp.net.

3.  En **Región o grupo de afinidad**, seleccione la zona geográfica o el grupo de afinidad en el que desea implementar el servicio en la nube. Seleccione un grupo de afinidad si desea implementar un servicio en la nube en la misma ubicación que los otros servicios de Azure de una región.

    > [WACOM.NOTE]
    > Para crear un grupo de afinidad, abra el área **Redes** del Portal de administración, haga clic en **Grupos de afinidad** y, a continuación, en **Crear un nuevo grupo de afinidad** o **Crear**. Puede usar grupos de afinidad que haya creado en el anterior Portal de administración de Azure. Y además, puede crear y administrar los grupos de afinidad usando la API de administración de servicios de Azure. Para obtener más información, consulte [Operaciones en grupos de afinidad](http://msdn.microsoft.com/es-es/library/windowsazure/ee460798.aspx).

4.  Haga clic en **Crear servicio en la nube**.

    Puede supervisar el estado del proceso en el área de mensajes situada en parte inferior de la ventana.

    El área **Servicios en la nube** se abre y en ella se muestran los servicios. Si el estado cambia a "Created", es porque la creación de servicios en la nube se ha completado correctamente.

    ![CloudServices\_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

    Si hay roles en el servicio en la nube que requieren un certificado para el cifrado de datos en la Capa de sockets seguros (SSL) y el certificado no se ha cargado en Azure, deberá cargar el certificado antes de implementar el servicio en la nube. Después de cargar el certificado, las aplicaciones de Windows que se estén ejecutando en las instancias de rol podrán obtener acceso al certificado.

## <span id="uploadcertificate"></span></a>Direccionamiento del un certificado para un servicio en la nube

1.  En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

    ![CloudServices\_EmptyDashboard](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)

2.  Haga clic en **Certificates** para abrir la página **Certificates** que se muestra a continuación.

    ![CloudServices\_CertificatesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png)

3.  Haga clic en **Cargar un certificado**.
     Se abrirá **Cargar certificado**.

    ![CloudServices\_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

4.  En **Archivo de certificado**, elija **Examinar** para seleccionar el certificado (archivo .pfx o .cer) que quiera usar.

5.  En **Password**, introduzca la clave privada del certificado.

6.  Haga clic en OK (marca de verificación).

    Puede ver el progreso de carga en el área de mensajes que se muestra a continuación. Cuando la carga termine, el certificado se agregará a la tabla. En el área de mensajes, haga clic en la flecha abajo para cerrar el mensaje, o haga clic en X para eliminar el mensaje.

    ![CloudServices\_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

    Puede implementar el servicio en la nube desde el panel o desde **Quick Start**.

## <span id="deploy"></span></a>Direccionamiento del un servicio en la nube

1.  Si no ha instalado el SDK de Azure, haga clic en **Install Azure SDK** para abrir la [Pagina de descargas de Azure](http://www.windowsazure.com/es-es/develop/downloads/) y, a continuación, descargue el SDK en el idioma en que prefiera desarrollar su código.

    En la página de descargas, también puede instalar las bibliotecas de cliente y el código fuente para las aplicaciones web en desarrollo de Node.js, Java, PHP y otros lenguajes, que puede implementar como servicios en la nube de Azure escalables.

    > [WACOM.NOTE]
    > Para los servicios en la nube creados previamente (anteriormente conocidos como *servicios hospedados*), tendrá que asegurarse de que los sistemas operativos invitados de las máquinas virtuales (instancias de rol) sean compatibles con la versión del SDK de Azure que instale. Para obtener más información, consulte [Notas de la versión de Windows Azure SDK para .NET](http://msdn.microsoft.com/es-es/library/windowsazure/hh552718.aspx).

2.  En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio en la nube para abrir el panel y haga clic en **Panel**.

3.  Haga clic en **Producción** o **Ensayo**.

    Si desea probar su servicio en la nube en Azure antes de implementarlo en producción, puede implementarlo en ensayo. En el entorno de ensayo, el identificador único global (GUID) del servicio en la nube identifica el servicio en la nube en las URL (*GUID*.cloudapp.net). En el entorno de producción, se usará el prefijo DNS más sencillo que asigne (por ejemplo, *myservice*.cloudapp.net). Cuando esté listo para pasar su servicio en la nube de ensayo a producción, utilice **Swap** para redirigir las solicitudes de cliente a dicha implementación.

    ![CloudServices\_QuickStartPage](./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png)

4.  Haga clic en **Cargar** en el panel para implementar su servicio en la nube.

    ![CloudServices\_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

5.  En **Etiqueta de implementación**, escriba un nombre para la nueva implementación; por ejemplo, MyCloudServicev1.

6.  En **Package**, utilice **Browse** para seleccionar el archivo de paquete de servicio (.cspkg) que quiera usar.

7.  En **Configuration**, utilice **Browse** para seleccionar el archivo de configuración de servicio (.cscfg) que quiera usar.

8.  Si el servicio en la nube incluye roles con una sola instancia, active la casilla **Deploy even if one or more roles contain a single instance** para habilitar la implementación y continuar.

    Azure solo puede garantizar el 99,95 % de acceso al servicio en la nube durante el mantenimiento y las actualizaciones de servicio si cada rol tiene dos instancias como mínimo. Si procede, puede agregar instancias de rol adicionales en la página **Escala** después de implementar el servicio en la nube. Para obtener más información, consulte [Contratos de nivel de servicio](http://www.windowsazure.com/es-es/support/legal/sla/).

1.  Haga clic en OK (marca de verificación) para iniciar la implementación del servicio en la nube.

    Puede supervisar el estado de la implementación en el área de mensajes. Haga clic en la flecha abajo para ocultar el mensaje.

    ![CloudServices\_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

### Para comprobar que la implementación se haya completado correctamente

1.  Haga clic en **Panel**.

2.  Debajo de **quick glance**, haga clic en la URL del sitio para abrir su servicio en la nube en un explorador web.

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409



    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)

  [disclaimer]: ../includes/disclaimer.md
  [Entrega continua a Azure usando Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
  [Conceptos]: #concepts
  [Preparación de la aplicación]: #prepare
  [Antes de empezar]: #begin
  [Direccionamiento del un servicio en la nube usando Quick Create]: #quick
  [Direccionamiento del un certificado para un servicio en la nube]: #uploadcertificate
  [Direccionamiento del un servicio en la nube]: #deploy
  [Descargas de Azure]: http://www.windowsazure.com/es-es/develop/downloads/
  [Ejemplos de código de Azure]: http://code.msdn.microsoft.com/windowsazure/
  [Configurar un certificado SSL en un extremo HTTPS]: http://msdn.microsoft.com/es-es/library/windowsazure/ff795779.aspx
  [Overview of Setting Up a Remote Desktop Connection for a Role]: http://msdn.microsoft.com/es-es/library/windowsazure/gg433010.aspx
  [Habilitación de los diagnósticos en Azure]: http://www.windowsazure.com/es-es/develop/net/common-tasks/diagnostics/
  [Portal de administración]: http://manage.windowsazure.com/
  [Operaciones en grupos de afinidad]: http://msdn.microsoft.com/es-es/library/windowsazure/ee460798.aspx
  [Notas de la versión de Windows Azure SDK para .NET]: http://msdn.microsoft.com/es-es/library/windowsazure/hh552718.aspx
  [Contratos de nivel de servicio]: http://www.windowsazure.com/es-es/support/legal/sla/
