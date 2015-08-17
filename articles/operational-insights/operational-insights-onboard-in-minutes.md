<properties
    pageTitle="Incorporación a Visión operativa en minutos"
    description="Obtenga información acerca de empezar a trabajar con Visión operativa en minutos"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/09/2015"
    ms.author="banders"/>

# Incorporación a Visión operativa en minutos


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Puede estar listo y trabajando con Visión operativa en minutos. Tiene dos opciones a la hora de elegir cómo crear un área de trabajo de Visión operativa, que es similar a una cuenta:

- Microsoft Operations Management Suite
- Suscripción de Microsoft Azure

Puede crear un área de trabajo de Microsoft Operations Management Suite mediante el sitio web de Microsoft Operations Management Suite. O bien, puede usar una suscripción de Microsoft Azure para crear un área de trabajo de Visión operativa. Actualmente, ambas áreas de trabajo son funcionalmente equivalentes. La única diferencia entre ellas es el nombre. Si usa una suscripción de Azure, también puede usar esa suscripción para acceder a otros servicios de Azure. Independientemente del método que use para crear el área de trabajo, podrá crearla con una cuenta de Microsoft o una cuenta profesional.

## Registro en 3 pasos con Microsoft Operations Management Suite

1. Vaya al sitio web de [Microsoft Operations Management Suite](http://microsoft.com/oms) y haga clic en **Try it for free** (Probar gratis). Inicie sesión con su cuenta Microsoft, como Outlook.com, o con una cuenta profesional proporcionada por su compañía o institución educativa para usar con otros servicios de Microsoft o de Office 365.
2. Proporcione un nombre único al área de trabajo. Un área de trabajo es un contenedor lógico donde se almacenan los datos de administración. Permite crear particiones de datos entre los diferentes equipos de su organización, porque los datos son exclusivos de su área de trabajo. Especifique una dirección de correo electrónico y la región donde desea que se almacenen los datos.![crear área de trabajo y vincular la suscripción](./media/operational-insights-onboard-in-minutes/create-workspace-link-sub.png)
3. Después, puede crear una nueva suscripción de Azure o un vínculo a una suscripción de Azure existente. Si desea continuar usando la versión de evaluación gratuita, haga clic en **Not now** (Ahora no).

Ya está listo para empezar a usar el portal de Operations Management Suite.

Encontrará más información sobre cómo configurar el área de trabajo y vincular cuentas de Azure existentes a áreas de trabajo creadas con Operations Management Suite en [Configurar el área de trabajo y administrar la configuración](operational-insights-setup-workspace.md).

## Registro rápido con Microsoft Azure

1. Vaya al [Portal de administración de Azure](https://manage.windowsazure.com) e inicie sesión; a continuación, en la lista de servicios, seleccione **Visión operativa**.![Portal de Azure](./media/operational-insights-onboard-in-minutes/azure-portal-op-insights.png)
2. Haga clic en**Crear un área de trabajo** y en **Creación rápida**; a continuación, en Cuenta, escriba el nombre del área de trabajo, elija un nivel y elija la ubicación donde se almacenarán los datos del área de trabajo. Si tiene varias suscripciones, elija cuál desea usar y, después, haga clic en **Crear área de trabajo**.![Portal de Azure](./media/operational-insights-onboard-in-minutes/quick-create.png)
3. Seleccione el área de trabajo que creó y haga clic en **Visite su cuenta de Visión operativa** para abrir el sitio web de Operations Management Suite.![visitar cuenta](./media/operational-insights-onboard-in-minutes/visit-account.png)
4. En el sitio web de Operations Management Suite, escriba su dirección de correo electrónico y haga clic en **Confirmar y continuar**. Se le enviará un correo electrónico de confirmación. Abra el correo electrónico y, en él, haga clic en **Confirmar ahora**.
5. El sitio web de Operations Management Suite muestra la página Información general. Haga clic en **Comenzar** para continuar.

Ya está listo para empezar a usar el portal de Operations Management Suite.

Encontrará más información sobre cómo configurar el área de trabajo y vincular suscripciones de Azure con las áreas de trabajo existentes creadas con Operations Management Suite en [Configurar el área de trabajo y administrar la configuración](operational-insights-setup-workspace.md).

## Introducción al portal de Operations Management Suite
Para elegir las soluciones y conectar los servidores que desea administrar, haga clic en el icono **Get started** (Comenzar) y siga estos pasos:

![crear área de trabajo y vincular la suscripción](./media/operational-insights-onboard-in-minutes/get-started.png)

- Seleccione las soluciones que le gustaría usar y, después, haga clic en **Add selected Solutions** (Agregar soluciones seleccionadas). ![soluciones](./media/operational-insights-onboard-in-minutes/solutions.png)
- Elija cómo desea conectarse a su entorno de servidor para recopilar datos:
    - Instale un agente para conectar un cliente o un servidor de Windows Server directamente.
    - Use System Center Operations Manager para conectar sus grupos de administración o toda la implementación de Operations Manager.
    - Use una cuenta de almacenamiento de Azure configurada con la extensión de VM de diagnóstico de Azure para Windows o Linux.
- Configure al menos un registro para rellenar los datos. Puede seleccionar los registros de IIS y agregar registros de eventos, y seleccione **Save** (Guardar) en la parte inferior de la página. Para los registros de eventos, puede especificar el tipo de mensajes que desea supervisar, por ejemplo, error, advertencia e información. ![soluciones](./media/operational-insights-onboard-in-minutes/logs.png)

## Opcionalmente, instalar un agente para conectar servidores directamente a Operations Management Suite
1. En la vista Get started (Comenzar), haga clic en el nodo **Connect a data source** (Conectar un origen de datos) y, después, haga clic en **Download Windows Agent** (Descargar el agente de Windows). El agente solo se puede instalar en Windows Server 2008 SP 1 o posterior, o en Windows 7 SP1 o posterior. Los servidores necesitan una arquitectura x64.
2. Instale el agente en uno o más servidores. Puede instalar los agentes uno por uno, o usar un método más automatizado con un [script personalizado](operational-insights-direct-agent.md#configure-the-microsoft-monitoring-agent-optional); también puede usar una solución de distribución de software existente que pueda tener.
3. Una vez aceptado el contrato de licencia y elegida la carpeta de instalación, seleccione **Connect the agent to Microsoft Azure Operational Insights** (Conectar el agente a Visión operativa de Microsoft Azure). ![instalación del agente](./media/operational-insights-onboard-in-minutes/agent.png)
4. En la página siguiente, se le pedirá el identificador de área de trabajo y la clave de área de trabajo. El identificador y la clave de área de trabajo se muestran en la pantalla donde descargó el archivo del agente. ![conectar servidores](./media/operational-insights-onboard-in-minutes/key.png)
5. Durante la instalación, tiene la opción de hacer clic en **Advanced** (Avanzadas) para configurar el servidor proxy y proporcionar la información de autenticación. Haga clic en el botón **Next** (Siguiente) para volver a la pantalla de información del área de trabajo.
6. Haga clic en **Next** (Siguiente) para validar el identificador y la clave de área de trabajo. Si se encuentran errores, puede hacer clic en **Atrás** para corregirlos. Una vez validados el identificador y la clave de área de trabajo, haga clic en **Install** (Instalar) para completar la instalación del agente.
7. Vuelva a iniciar sesión en el portal de Operations Management Suite y haga clic en el icono **Settings** (Configuración), en la página Overview (Información general). Cuando los agentes se comunican con el servicio de Operations Management Suite, aparecerá un icono de marca de verificación verde. Inicialmente, esto tarda unos 5-10 minutos.

> [AZURE.NOTE]Actualmente, los servidores conectados directamente a Operations Management Suite no admiten soluciones de evaluación de configuración y administración de la capacidad.

También puede conectar al agente a System Center Operations Manager 2012 SP1 y versiones posteriores. Para ello, puede seleccionar **Connect the agent to System Center Operations Manager** (Conectar el agente a System Center Operations Manager). Cuando elige esa opción, se envían datos al servicio sin necesidad de hardware adicional ni de cargas en los grupos de administración.

Para obtener más información sobre cómo conectar agentes directamente a Operations Management Suite, lea [Conexión de equipos directamente en Vista operativa](operational-insights-direct-agent.md).

## Opcionalmente, conectar servidores con System Center Operations Manager

1. En la consola de Operations Manager, seleccione **Administración**.
2. Expanda el nodo **Visión operativa** y seleccione **Conexión a Visión operativa**.
3. Haga clic en el vínculo **Registrarse en Visión operativa** en la parte superior derecha y siga las instrucciones que se muestran.
4. Después de completar el asistente de registro, haga clic en el vínculo **Agregar un equipo/grupo**.
5. En el cuadro de diálogo **Búsqueda de equipos** puede buscar los equipos o grupos supervisados por Operations Manager. Seleccione los equipos o grupos para incorporarlos a Visión operativa, haga clic en **Agregar** y, después, haga clic en **Aceptar**. Para comprobar que el servicio Visión operativa recibe datos, vaya al icono **Uso** en el portal de Operations Management Suite. Los datos aparecerán en unos 5-10 minutos.

Para obtener más información sobre cómo conectar Operations Manager con Operations Manager Suite, lea [Conexión a Visión operativa desde System Center Operations Manager](operational-insights-connect-scom.md).

## Opcionalmente, analizar datos desde los servicios en la nube de Microsoft Azure

Con Operations Management Suite puede buscar rápidamente registros de IIS y eventos para servicios en la nube y máquinas virtuales, habilitando los diagnósticos en Servicios en la nube de Azure. También puede instalar Microsoft Monitoring Agent para recibir información adicional sobre sus máquinas virtuales de Azure. Para obtener más información sobre cómo configurar el entorno de Azure para usar Operations Management Suite, lea [Análisis de datos de servidores en Microsoft Azure](operational-insights-analyze-data-azure.md).


## Pasos siguientes
- Empezar a usar [soluciones](operational-insights-solutions.md)
- Familiarizarse con la [búsqueda](operational-insights-search.md)
- Usar [paneles](operational-insights-use-dashboards.md) para guardar y mostrar búsquedas personalizadas

<!---HONumber=August15_HO6-->