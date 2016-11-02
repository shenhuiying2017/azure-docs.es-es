<properties
    pageTitle="Incorporación de soluciones de Log Analytics desde la galería de soluciones | Microsoft Azure"
    description="Las soluciones de Log Analytics son una colección de lógica, visualización y reglas de adquisición de datos que brindan métricas que giran en torno a una determinada área de problemas."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="add-log-analytics-solutions-from-the-solutions-gallery"></a>Incorporación de soluciones de Log Analytics desde la galería de soluciones

Las soluciones de Log Analytics son una colección de **lógica**, **visualización** y **reglas de adquisición de datos** que brindan métricas que giran en torno a una determinada área de problemas. En este artículo se enumeran las soluciones que admite Log Analytics y se indica cómo agregarlas y quitarlas con la galería de soluciones.

Las soluciones permiten obtener información más en profundidad para:

- ayudar a investigar y resolver los problemas operativos con más rapidez.
- recopilar y correlacionar diversos tipos de datos de la máquina.
- ayudarle a ser proactivo con actividades, como la planeación de la capacidad, la generación de informes de estado de las revisiones y la auditoría de seguridad.


>[AZURE.NOTE] Log Analytics incluye la función de búsqueda de registros, por lo que no es necesario instalar una solución para habilitarla. Pero si agrega soluciones de la Galería de soluciones, obtendrá visualizaciones de datos, sugerencias de búsqueda y más información.

Una vez que agrega una solución, se recopilan datos desde los servidores de la infraestructura y se envían al servicio de OMS. El servicio OMS suele tardar en procesar esto entre unos minutos y una hora. Una vez que el servicio procesa los datos, puede verlos en OMS.

Es posible quitar fácilmente una solución cuando ya no es necesaria. Cuando quita una solución, sus datos no se enviarán a OMS, lo que reduce la cantidad de datos usados por la cuota diaria, si tiene una.


## <a name="solutions-supported-by-the-microsoft-monitoring-agent"></a>Soluciones admitidas por Microsoft Monitoring Agent

En este momento, los servidores conectados directamente a OMS mediante Microsoft Monitoring Agent pueden usar la mayoría de las soluciones disponibles, incluidas las siguientes:

- Evaluación de Active Directory
- Administración de alertas (sin alertas de SCOM)
- Antimalware
- Seguimiento de cambios
- Seguridad
- Evaluación de SQL
- Actualizaciones del sistema

Pero las siguientes soluciones *no* son compatibles con Microsoft Monitoring Agent y necesitan el agente de System Center Operations Manager (SCOM).

- Administración de alertas (incluidas las alertas de SCOM)
- Administración de la capacidad
- Evaluación de la configuración

Consulte [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md) para obtener más información sobre cómo conectar el agente de SCOM con Log Analytics.

### <a name="to-add-a-solution-using-the-solutions-gallery"></a>Para agregar una solución mediante la Galería de soluciones

1. En la página de información general de OMS, haga clic en el icono **Galería de soluciones** .    
    ![galería de soluciones](./media/log-analytics-add-solutions/sol-gallery.png)
2. En la página Galería de soluciones de OMS, obtenga información sobre cada solución disponible. Haga clic en el nombre de la solución que desea agregar a OMS.
3. En la página de la solución que eligió, aparecerá información detallada sobre la solución. Haga clic en **Agregar**.
4. En la página de información general de OMS aparece un icono nuevo para la solución que agregó y puede empezar a usarlo una vez que el servicio de OMS procese los datos.

## <a name="to-configure-solutions"></a>Para configurar soluciones
1. Deberá configurar algunas soluciones. Por ejemplo, deberá configurar Automatización, Azure Site Recovery y Copia de seguridad antes de poder usarlas.
2. Para cualquiera de esas soluciones, haga clic en su icono en la página de información general.  
    ![configurar solución](./media/log-analytics-add-solutions/configure-additional.png)
3. Luego, configure la solución con la información necesaria y haga clic en **Guardar**.  
    ![configurar solución](./media/log-analytics-add-solutions/configure.png)

### <a name="to-remove-a-solution-using-the-solutions-gallery"></a>Para quitar una solución mediante la Galería de soluciones

1. En la página de información general de OMS, haga clic en el icono **Configuración** .
2. En la pestaña Soluciones de la página Configuración, haga clic en **Quitar** en la solución que quiera quitar.
3. En el cuadro de diálogo de configuración, haga clic en **Sí** para quitar la solución.

## <a name="data-collection-details-for-oms-features-and-solutions"></a>Detalles de recopilación de datos para soluciones y características de OMS

En la tabla siguiente se muestran los métodos de recolección de datos y otros detalles sobre cómo se recopilan los datos para las soluciones y características de OMS. Los agentes directos y SCOM son esencialmente lo mismo, sin embargo, el agente directo incluye funcionalidad adicional para permitir la conexión al área de trabajo OMS y enrutar a través de un servidor proxy. Si usa un agente SCOM, debe ser un agente OMS para comunicarse con OMS. Los agentes SCOM de esta tabla son agentes OMS conectados a SCOM. Para información sobre cómo conectar el entorno de SCOM existente con OMS, consulte [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).

>[AZURE.NOTE] El tipo de agente que use determina cómo se envían los datos a OMS, con las condiciones siguientes:

- Se usa el agente directo o un agente OMS conectado a SCOM.
- Cuando se requiere SCOM, los datos del agente SCOM de la solución siempre se envían a OMS a través del grupo de administración de SCOM. Además, cuando se necesita SCOM, la solución utiliza solo el agente SCOM.
- Cuando SCOM no es necesario y en la tabla se muestra que se envían los datos del agente SCOM a OMS a través del grupo de administración, los datos del agente SCOM siempre se envían a OMS a través de grupos de administración. Los agentes directos omiten el grupo de administración y envían los datos directamente a OMS.
- Cuando los datos del agente SCOM no se envían a través de un grupo de administración, los datos se envían directamente a OMS, omitiendo el grupo de administración.


|tipo de datos| plataforma | Agente directo | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
|---|---|---|---|---|---|---|---|
|Evaluación de AD|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|  7 días|
|Estado de replicación de AD|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 días|
|Alertas (Nagios)|Linux|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|a la llegada|
|Alertas (Zabbix)|Linux|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 minuto|
|Alertas (Operations Manager)|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 minutos|
|Antimalware|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| cada hora|
|Administración de la capacidad|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| cada hora|
|Seguimiento de cambios|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| cada hora|
|Seguimiento de cambios|Linux|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|cada hora|
|Evaluación de la configuración (asesor heredado)|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![yes](./media/log-analytics-add-solutions/oms-bullet-green.png)| dos veces al día|
|ETW|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Registros IIS|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Almacenes de claves|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Puertas de enlace de aplicaciones|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Grupos de seguridad de red|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Office 365|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|según notificación|
|Contadores de rendimiento|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|mínimo de 10 segundos, según lo programado|
|Contadores de rendimiento|Linux|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|mínimo de 10 segundos, según lo programado|
|Service Fabric|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Evaluación de SQL|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| 7 días|
|SurfaceHub|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|a la llegada|
|Syslog|Linux|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|de Almacenamiento de Azure: 10 minutos; del agente: a la llegada|
|Actualizaciones del sistema|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| al menos 2 veces al día y 15 minutos después de instalar una actualización|
|Registros de eventos de seguridad de Windows|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)| para Almacenamiento de Azure: 10 minutos; para el agente: a la llegada|
|Registros de Firewall de Windows|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)| a la llegada|
|Registros de eventos de Windows|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| para Almacenamiento de Azure: 1 minuto; para el agente: a la llegada|
|Wire Data|Windows (2012 R2 / 8.1 o posterior)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)| cada 1 minuto|

## <a name="log-analytics-preview-solutions-and-features"></a>Características y soluciones de vista previa de Log Analytics

Cuando ejecutamos un servicio y seguimos los procedimientos de las operaciones de desarrollo, podemos asociarnos con clientes para desarrollar soluciones y características.

Durante una vista previa privada, damos acceso a un pequeño grupo de clientes a una primera implementación de la característica o solución con objeto de recibir comentarios y efectuar mejoras. Esta implementación temprana posee las características y funciones operativas mínimas.

Nuestro objetivo es probarlas rápidamente para saber qué funciona y qué no. Pasamos varias veces por este mismo proceso hasta que los comentarios de los clientes de la vista previa privada indican que estamos listos para lanzar una vista previa pública.

Durante la vista previa pública, ofrecemos la característica o solución a todos los usuarios para obtener más comentarios y validar nuestra capacidad de escalado y eficacia. Durante esta fase:

- Las características de vista previa aparecerán en la pestaña Configuración y cualquier usuario podrá habilitarlas.
- Se pueden agregar soluciones de vista previa a través de la galería o mediante un script publicado.

### <a name="what-should-i-know-about-preview-features-and-solutions?"></a>¿Qué hay que saber sobre las características y soluciones de vista previa?

Estamos entusiasmados con las nuevas características y soluciones y nos encanta colaborar con los usuarios para desarrollarlas.

Con todo, las características y soluciones de vista previa no son aptas para todos los públicos, por lo que, antes de solicitar unirse a una vista previa privada o habilitar una vista previa pública, confirme que no le importa trabajar con algo que está en fase de desarrollo.

Cuando habilite una característica de vista previa a través del portal, aparecerá una advertencia para recordarle que la característica está en fase de vista previa.

#### <a name="for-both-*private*-and-*public*-preview"></a>Común a las versiones preliminares *públicas* y *privadas*

Las siguientes afirmaciones pueden aplicarse a las vistas previas tanto públicas como privadas:

- Las cosas no siempre funcionan como deben.
  - Las incidencias pueden ir desde una pequeña minucia hasta que algo directamente no funcione.
- Existe la posibilidad de que la vista previa tenga un impacto negativo en sus sistemas o su entorno.
  - Intentamos evitar que se produzcan situaciones adversas en los sistemas que se usan con OMS, pero a veces surgen imprevistos.
- Se pueden producir pérdidas de datos y daños.
- Puede que le pidamos que recopile registros de diagnóstico u otros datos como ayuda para solucionar problemas.
- La característica o solución se puede quitar (temporal o permanentemente).
  - Según la información que recabemos durante la vista previa, podemos decidir no lanzar la característica o solución.
- Puede que las vistas previas no funcionen o no se hayan probado con todas las configuraciones, a raíz de lo cual podremos limitar:
  - Los sistemas operativos que se pueden usar (por ejemplo, puede que una característica solo sea válida en Linux mientras está en fase de vista previa).
  - El tipo de agente (MMA, SCOM) que se puede usar (por ejemplo, puede que una característica no funcione con SCOM en la fase de vista previa).  
- Las características y soluciones de vista previa no están cubiertas por el contrato de nivel de servicio.
- El uso de características de vista previa conllevará cargos por uso.
- Puede que las características o funciones que necesite para que la característica o solución le resulte útil no estén o estén incompletas.
- Puede que las características y soluciones no estén disponibles en todas las regiones.
- Puede que las características y soluciones no estén localizadas.
- Puede que las características y soluciones tengan un límite en cuanto al número de clientes o dispositivos que pueden usarlas.
- Puede que haya que usar scripts para realizar la configuración y habilitar la característica o la solución.
- La interfaz de usuario (UI) estará incompleta y puede variar de un día para otro.
- Puede que las vistas previas públicas no sean adecuadas en sistemas críticos o de producción.

#### <a name="for-*private*-preview"></a>Vistas previas *privadas*

Aparte de los puntos anteriores, los que siguen son específicos de las vistas previas privadas:

- Esperamos que nos envíe comentarios sobre su experiencia para que podamos mejorar la solución o la característica.
- Podemos ponernos en contacto con usted para pedirle comentarios mediante encuestas, llamadas de teléfono o correo electrónico.
- Las cosas no siempre funcionarán como deben.
- Puede que requiramos un acuerdo de confidencialidad para participar o que incluyamos contenido confidencial.
  - Antes de crear entradas en blog, tweets o comunicarse de algún otro modo con terceros, acuda al administrador de programas responsable de la vista previa para conocer todas las restricciones sobre divulgación de contenido.
- No ejecute la vista previa en sistemas críticos o de producción.


### <a name="how-do-i-get-access-to-private-preview-features-and-solutions?"></a>¿Cómo se puede obtener acceso a las características y soluciones de la vista previa privada?

Invitamos a los clientes a las vistas previas privadas de diversas maneras, dependiendo de la vista previa.

- Si responde las encuestas de cliente mensuales y nos permite llevar un seguimiento al respecto, aumentarán las posibilidades de que se le invite a una vista previa privada.
- El equipo de cuentas de Microsoft puede elegirle.
- Puede registrarse basándose en detalles publicados en [msopsmgmt](https://twitter.com/msopsmgmt)
- Puede registrarse basándose en detalles compartidos en eventos comunitarios: búsquenos en encuentros, conferencias y en comunidades en línea.


## <a name="next-steps"></a>Pasos siguientes

- [Busque registros](log-analytics-log-searches.md) para ver información detallada que recopilan las soluciones.



<!--HONumber=Oct16_HO2-->


