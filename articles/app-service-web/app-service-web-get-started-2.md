<properties 
	pageTitle="Incorporación de funcionalidad a su primera aplicación web" 
	description="Agregue características interesantes a su primera aplicación web en unos minutos." 
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

# Incorporación de funcionalidad a su primera aplicación web

En [Implementación de su primera aplicación web en Azure en 5 minutos](app-service-web-get-started.md), implementó una aplicación web de ejemplo en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md). En este artículo, agregará rápidamente funcionalidad muy interesante a su aplicación web implementada. En unos minutos, aprenderá a:

- aplicar autenticación para los usuarios
- escalar la aplicación automáticamente
- recibir alertas sobre el rendimiento de la aplicación

Independientemente de qué aplicación de ejemplo implementó en el artículo anterior, puede seguir el tutorial.

Las tres actividades de este tutorial son solo algunos ejemplos de las muchas funciones útiles que obtendrá al colocar la aplicación web en el Servicio de aplicaciones. Muchas de las funciones están disponibles en el nivel **Gratis** (donde se ejecuta su primera aplicación web), y puede usar sus créditos de prueba para probar las que requieren planes de tarifa superiores. Puede estar seguro de que la aplicación web permanecerá en el nivel **Gratis** a menos que la cambie explícitamente a un plan de tarifa diferente.

>[AZURE.NOTE] La aplicación web que creó con la CLI de Azure se ejecuta en el nivel **Gratis**, donde solo se permite una instancia de máquina virtual compartida con cuotas de recursos. Para más información sobre lo que se obtiene con el nivel **Gratis**, consulte [Límites de Servicio de aplicaciones](../azure-subscription-service-limits.md#app-service-limits).

## Autenticación de los usuarios

Ahora, veamos lo fácil que es agregar autenticación a su aplicación (lea más en [Expanding App Service Authentication/Authorization](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/) [Expansión de autorización y autenticación en el Servicio de aplicaciones]).

1. En la hoja del portal de su aplicación que acaba de abrir, haga clic en **Configuración** > **Autenticación/autorización**. ![Autenticar, hoja de configuración](./media/app-service-web-get-started/aad-login-settings.png)
    
2. Haga clic en **Activado** para activar la autenticación.
    
4. En **Proveedores de autenticación**, haga clic en **Azure Active Directory**. ![Autenticar, seleccionar Azure AD](./media/app-service-web-get-started/aad-login-config.png)

5. En la hoja **Configuración de Azure Active Directory**, haga clic en **Rápido** y haga clic en **Aceptar**. La configuración predeterminada creará una aplicación de Azure AD en el directorio predeterminado. ![Autenticar, configuración rápida](./media/app-service-web-get-started/aad-login-express.png)

6. Haga clic en **Guardar**. ![Autenticar, guardar configuración](./media/app-service-web-get-started/aad-login-save.png)

    Una vez realizado el cambio correctamente, verá que el indicador de notificación cambia a verde y aparece un mensaje descriptivo.

7. De nuevo en la hoja del portal de su aplicación, haga clic en el vínculo **URL** (o **Examinar** en la barra de menús). El vínculo es una dirección HTTP. ![Autenticar, ir a dirección URL](./media/app-service-web-get-started/aad-login-browse-click.png) Pero, una vez que la aplicación se abre en una nueva pestaña, el cuadro Dirección URL redirecciona varias veces y termina en la aplicación con una dirección HTTPS. Lo que está viendo es que ya ha iniciado sesión en la suscripción de Azure y se le autentica de forma automática en la aplicación. ![Autenticar, sesión iniciada](./media/app-service-web-get-started/aad-login-browse-http-postclick.png) Si ahora abre una sesión sin autenticar en un explorador diferente, verá una pantalla de inicio de sesión cuando vaya a la misma dirección URL.
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
    Si nunca ha hecho nada con Azure Active Directory, el directorio predeterminado podría no tener ningún usuario de Azure AD. En ese caso, probablemente la única cuenta aquí es la cuenta de Microsoft con su suscripción de Azure. Por eso se inició sesión automáticamente en la aplicación en el mismo explorador antes. Puede usar la misma cuenta Microsoft para iniciar sesión también en esta página de inicio de sesión.

Felicidades, está autenticando todo el tráfico a su sitio.

Quizás haya observado en la hoja **Autenticación/autorización** que se puede hacer mucho más, por ejemplo:

- Habilitar el inicio de sesión social
- Habilitar varias opciones de inicio de sesión
- Cambiar el comportamiento predeterminado cuando los usuarios navegan a su aplicación por primera vez

El Servicio de aplicaciones proporciona una solución completa para algunas de las necesidades de autenticación más comunes por lo que no es necesario proporcionar la lógica de autenticación personalmente. Para más información, consulte [Expanding App Service Authentication/Authorization](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/) (Expansión de autorización y autenticación en el Servicio de aplicaciones).

## Escalado automático de la aplicación según la demanda

Ahora vamos a escalar automáticamente su aplicación para que ajuste su capacidad de forma automática en respuesta a la demanda de los usuarios (lea más en [Escalado de una aplicación web en el Servicio de aplicaciones de Azure](web-sites-scale.md) y [Escalado manual o automático del número de instancias](../azure-portal/insights-how-to-scale.md)).

Brevemente, la aplicación web se escala de dos maneras:

- [Escalado vertical](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenga más CPU, memoria, espacio en disco y características adicionales como máquinas virtuales dedicadas, dominios y certificados personalizados, ranuras de almacenamiento provisional, escalado automático y mucho más. Para escalar horizontalmente, se cambia el plan de tarifa del plan del Servicio de aplicaciones al que pertenece la aplicación.
- [Escalado horizontal](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): se aumenta el número de instancias de máquina virtual que ejecutan la aplicación. Se puede escalar horizontalmente a un máximo de 50 instancias, según el plan de tarifa.

Sin más rodeos, vamos a configurar el escalado automático.

1. En primer lugar, vamos realizar el escalado vertical para habilitar el escalado automático. En la hoja del portal de su aplicación, haga clic en **Configuración** > **Escalar verticalmente (plan de servicio de la aplicación)**. ![Escalado vertical, hoja de configuración](./media/app-service-web-get-started/scale-up-settings.png)

2. Desplácese y seleccione el plan **S1 estándar**, el más bajo que admite el escalado automático (rodeado con un círculo en la captura de pantalla), y haga clic en **Seleccionar**. ![Escalado vertical, elegir nivel](./media/app-service-web-get-started/scale-up-select.png)

    Ya ha terminado con el escalado vertical.
    
    >[AZURE.IMPORTANT] Este nivel gastará los créditos de la evaluación gratuita. Si tiene una cuenta de pago por uso, incurrirá en cargos.
    
3. A continuación, vamos a configurar el escalado automático. En la hoja del portal de su aplicación, haga clic en **Configuración** > **Escalar horizontalmente (plan de servicio de la aplicación)**. ![Escalado horizontal, hoja de configuración](./media/app-service-web-get-started/scale-out-settings.png)

4. Cambie **Escalar por** a **Porcentaje de CPU**. Los controles deslizantes debajo de la lista desplegable se actualizarán como corresponde. A continuación, defina un intervalo de **Instancias** entre **1** y **2**, y un **Rango objetivo** entre **40** y **80**. Para hacerlo, escriba en los cuadros o mueva los controles deslizantes. ![Escalado horizontal, configurar el escalado automático.](./media/app-service-web-get-started/scale-out-configure.png)
    
    Según esta configuración, la aplicación se escalará horizontalmente de forma automática cuando el uso de CPU sea superior al 80 % y se reducirá horizontalmente cuando el uso de CPU sea inferior al 40 %.
    
5. Haga clic en **Guardar** en la barra de menús.

Enhorabuena, su aplicación ya escala automáticamente.

Quizás haya observado en la hoja **Configuración de escala** que puede hacer mucho más, por ejemplo:

- Escalar manualmente a un número específico de instancias
- Escalar por otras métricas de rendimiento, como la cola de disco o el porcentaje de memoria
- Personalizar el comportamiento de escalado cuando se desencadena una regla de rendimiento
- Escalado automático según una programación
- Establecer el comportamiento del escalado automático para un evento futuro

Para más información sobre el escalado vertical de su aplicación, consulte [Escalado de una aplicación web en el Servicio de aplicaciones de Azure](../app-service-web/web-sites-scale.md). Para más información sobre el escalado horizontal, consulte [Escalado manual o automático del número de instancias](../azure-portal/insights-how-to-scale.md).

## Recepción de alertas para su aplicación

Ahora que la aplicación tiene el escalado automático, ¿qué sucede cuando alcanza el número máximo de instancias (2) y la CPU está por encima de uso deseado (80 %)? Puede configurar una alerta (lea más en [Recibir notificaciones de alerta](../azure-portal/insights-receive-alert-notifications.md)) que informe de esta situación para poder escalar vertical u horizontalmente la aplicación, por ejemplo. Vamos a configurar rápidamente una alerta para este escenario.

1. En la hoja del portal de la aplicación, haga clic en **Herramientas** > **Alertas**. ![Alertas, hoja de configuración](./media/app-service-web-get-started/alert-settings.png)

2. Haga clic en **Agregar alerta**. Después, en el cuadro **Recurso**, seleccione el recurso que termina con **(serverfarms)**. Ese es el plan del Servicio de aplicaciones. ![Alertas, agregar alerta para el plan del Servicio de aplicaciones](./media/app-service-web-get-started/alert-add.png)

3. Especifique **Nombre** como `CPU Maxed`, **Métrica** como **Porcentaje de CPU** y **Umbral** como `90`; después, seleccione **Lectores, colaboradores y propietarios de correo electrónico** y haga clic en **Aceptar**. ![Alertas, configurar alertas](./media/app-service-web-get-started/alert-configure.png)
    
    Cuando Azure termine de crear la alerta, la verá en la hoja **Alertas**. ![Alertas, vista terminada](./media/app-service-web-get-started/alert-done.png)

Enhorabuena, ya recibe alertas.

Esta configuración de alertas comprobará el uso de la CPU cada cinco minutos. Si esa cifra supera el 90 %, se le enviará una alerta por correo electrónico, además a otras personas que estén autorizadas. Para ver todos los usuarios que están autorizados para recibir las alertas, vuelva a la hoja del portal y haga clic en el botón **Acceso**. ![Ver quién recibe las alertas](./media/app-service-web-get-started/alert-rbac.png)

Verá que en **Administradores de suscripciones** ya aparece el **Propietario** de la aplicación. Este grupo le incluiría si fuera el administrador de cuentas de su suscripción de Azure (por ejemplo, su suscripción de evaluación). Para más información sobre el control de acceso basado en rol de Azure, consulte [Uso de asignaciones de roles para administrar el acceso a los recursos de Azure Active Directory](../active-directory/role-based-access-control-configure.md).

> [AZURE.NOTE] Las reglas de alerta son una característica de Azure. Para más información, consulte [Recibir notificaciones de alerta](../azure-portal/insights-receive-alert-notifications.md).

## Pasos siguientes

En el proceso de configuración de la alerta, puede que haya observado un amplio conjunto de herramientas en la hoja **Herramientas**. Aquí, puede solucionar los problemas, supervisar el rendimiento, probar los puntos vulnerables, administrar los recursos, interactuar con la consola de la máquina virtual y agregar extensiones útiles. Le invitamos a que haga clic en cada una de ellas para descubrir las herramientas sencillas pero eficaces que tiene a su alcance.

Averigüe también qué más puede hacer con la aplicación que ha implementado. Esta es una lista parcial:

- [Compre y configure un nombre de dominio personalizado](custom-dns-web-site-buydomains-web-app.md): compre un dominio atractivo para la aplicación web en lugar del dominio *.azurewebsites.net. También puede usar un dominio que ya tenga.
- [Configure entornos de ensayo](web-sites-staged-publishing.md): implemente la aplicación en una dirección URL de ensayo antes de ponerla en producción. Actualice la aplicación web activa con confianza. Configure una solución de DevOps elaborada con varias ranuras de implementación.
- [Configure la implementación continua](app-service-continuous-deployment.md): integre la implementación de la aplicación en el sistema de control de código fuente. Impleméntela en Azure con cada confirmación.
- [Acceda a recursos locales](web-sites-hybrid-connection-get-started.md): acceda a una base de datos local existente o un sistema CRM.
- [Haga una copia de seguridad de la aplicación](web-sites-backup.md): configure la copia de seguridad y la restauración para la aplicación web. Prepárese para errores inesperados y recupérese de ellos.
- [Habilite los registros de diagnóstico](web-sites-enable-diagnostic-log.md): lea los registros de IIS desde seguimientos de la aplicación o desde Azure. Léalos en una transmisión, descárguelos o pórtelos a [Application Insights](../application-insights/app-insights-overview.md) para realizar un análisis "llave en mano".
- [Detecte vulnerabilidades en la aplicación](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/): examine la aplicación web en busca de amenazas modernas con el servicio proporcionado por [Tinfoil Security](https://www.tinfoilsecurity.com/).
- [Ejecute trabajos en segundo plano](../azure-functions/functions-overview.md): ejecute trabajos de procesamiento de datos, informes, etc.
- [Obtener información acerca de cómo funciona el Servicio de aplicaciones](../app-service/app-service-how-works-readme.md)

<!---HONumber=AcomDC_0803_2016-->