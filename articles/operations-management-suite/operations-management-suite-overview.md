---
title: "Información general de Operations Management Suite (OMS) | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura local y en la nube.  Este artículo describe el valor de OMS, identifica los distintos servicios y ofertas que se incluyen en OMS y proporciona vínculos a contenido detallado."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.openlocfilehash: 94dedebe48060441cd3167fea87f6b721eb14517
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="what-is-operations-management-suite-oms"></a>¿Qué es Operations Management Suite (OMS)?
Este artículo proporciona una introducción a Operations Management Suite (OMS) incluyendo una breve información general sobre el valor empresarial que proporciona, los servicios y las soluciones de administración que incluye y las ofertas que reúnen en un solo paquete diferentes servicios y soluciones.  Se incluyen vínculos a la documentación detallada sobre la implementación y uso de cada servicio y solución.

## <a name="from-on-premises-to-the-cloud"></a>Del entorno local a la nube
Microsoft lleva ya mucho tiempo proporcionado productos para la administración de entornos empresariales.  En 2007 varios productos se consolidaron para constituir el conjunto de productos de administración System Center.  Este conjunto incluía Configuration Manager que proporciona características como el inventario y la distribución de software, Operations Manager que proporciona supervisión proactiva de sistemas y aplicaciones, Orchestrator que incluye runbooks para automatizar los procesos manuales y Data Protection Manager para la realización de copias de seguridad y recuperación de datos críticos.

Con cada vez más recursos de computación situados en la nube, los productos de System Center han ido aumentando las características en la nube, como Operations Manager y la administración de recursos de Orchestrator en Azure.  No obstante estas soluciones estaban diseñadas fundamentalmente como soluciones de entorno local y requieren una inversión importante para la implementación y el mantenimiento de un entorno de administración local.  Para aprovechar completamente las posibilidades que ofrece la nube y permitir la compatibilidad con las aplicaciones futuras, se requería un nuevo enfoque de la administración.

## <a name="introducing-operations-management-suite"></a>Introducción a Microsoft Operations Management Suite
Operations Management Suite (también conocido como OMS) es una colección de servicios de administración que desde el principio se diseñaron en la nube.  En lugar de implementar y administrar recursos locales, los componentes de OMS se hospedan en su totalidad en Azure.  La configuración es mínima, y puede estar listo y rindiendo literalmente en cuestión de minutos.  

- **Mínimo costo y complejidad de implementación.**  Dado que todos los componentes y datos de OMS están almacenados en Azure, puede estar en funcionamiento en muy poco tiempo, sin la complejidad ni la inversión que necesitan los componentes locales.
- **Escalado a los niveles de la nube.**  No tiene que preocuparse por el pago de los recursos de proceso que no necesite, ni por quedarse sin espacio de almacenamiento, ya que la nube le permite pagar solo lo que realmente utiliza y se escalará fácilmente para adaptarse a las cargas que requiera.  Puede empezar administrando unos pocos recursos para empezar a trabajar, e ir escalando hasta abarcar todo el entorno.
- **Aprovechamiento de las características más recientes.**  Las características de los servicios de OMS se están agregando y actualizando constantemente.  Tendrá acceso constante a las características más recientes sin ningún requisito para implementar las actualizaciones.
- **Servicios integrados.**  Si bien cada uno de los servicios de OMS proporciona un valor significativo de forma individual, también pueden funcionar juntos para resolver escenarios administrativos complejos.  Por ejemplo, un runbook en Azure Automation puede dirigir un proceso de conmutación por error con Azure Site Recovery y, a continuación, registrar información en Log Analytics para generar una alerta.
- **Información global.**  Las soluciones de administración de OMS tienen acceso continuo a la información más reciente.  La solución Seguridad y auditoría, por ejemplo, puede realizar un análisis de amenazas usando las amenazas más recientes que se hayan detectado por todo el mundo.
- **Acceso desde cualquier lugar.**  Obtenga acceso a su entorno de administración desde cualquier lugar en donde tenga un explorador.  Instale la aplicación OMS en su smartphone para tener acceso a los datos de supervisión.

### <a name="is-it-just-for-the-cloud"></a>¿Es solo para la nube?
El hecho de que los servicios de OMS se ejecuten en la nube no significa que no puedan administrar eficazmente cualquier entorno local.  Coloque un agente en cualquier equipo Windows o Linux del centro de datos, y este enviará datos a Log Analytics, donde se pueden analizar, junto con los restantes datos recopilados de los servicios en la nube o locales.  Use Azure Backup y Azure Site Recovery para sacar provecho de la nube para realizar copias de seguridad de los recursos locales y para lograr una alta disponibilidad de los mismos.  
Los runbooks en la nube normalmente no pueden acceder a los recursos locales, pero puede instalar un agente en uno o varios equipos que hospeden los runbooks en su centro de datos.  Cuando se inicia un runbook, simplemente especifique si desea que se ejecute en la nube o en un trabajo local.

## <a name="hybrid-management-with-system-center"></a>Administración híbrida con System Center
En una instalación existente de System Center, estos componentes se pueden integrar con los servicios de OMS para proporcionar una solución híbrida para los entornos locales y en la nube que saque provecho de las especialidades relativas de cada producto.  Conecte el grupo de administración de Operations Manager existente a Log Analytics para analizar los agentes administrados en la nube.  Utilice el proceso de copia de seguridad existente con Data Protection Manager para realizar una copia de seguridad de los datos en la nube.  


## <a name="oms-services"></a>Servicios de OMS
Un conjunto de servicios que se ejecutan en Azure proporciona la funcionalidad principal de OMS.  Cada servicio ofrece una función de administración específica, y se pueden combinar los servicios para lograr escenarios de administración diferentes.

|| Servicio | Descripción |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | Supervisión y análisis de la disponibilidad y el rendimiento de los distintos recursos, incluidas las máquinas físicas y virtuales. |
| ![Azure Automation](media/operations-management-suite-overview/icon-automation.png) | Automation | Automatización de procesos manuales y aplicación de configuraciones a máquinas físicas y virtuales. |
| ![Azure Backup](media/operations-management-suite-overview/icon-backup.png) | Backup | Realización de copias de seguridad y restauración de datos críticos. |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Site Recovery | Provisión de una alta disponibilidad para las aplicaciones críticas. |

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) proporciona servicios de supervisión para OMS recopilando datos de los recursos administrados en un repositorio central.  Estos datos podrían incluir eventos, datos de rendimiento o datos personalizados proporcionados a través de la API. Una vez recopilados, los datos están disponibles para las alertas, el análisis y la exportación.  Este método permite consolidar datos de varios orígenes, por lo que puede combinar datos de los servicios de Azure con el entorno local existente.  También separa claramente la recopilación de los datos de la acción realizada en los datos, para que todas las acciones estén disponibles para todos los tipos de datos.  

![Introducción a Log Analytics](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>La recopilación de datos
Hay una variedad de formas para obtener datos para el repositorio con el fin de que Log Analytics los analice.

- **Equipos y máquinas virtuales de Windows o Linux.**  Instale Microsoft Monitoring Agent en los equipos o máquinas virtuales de [Windows](../log-analytics/log-analytics-windows-agent.md) y [Linux](../log-analytics/log-analytics-linux-agents.md) de los que desea recopilar datos.  El agente descargará automáticamente la configuración de Log Analytics que define los eventos y datos de rendimiento que se van a recopilar.  Puede instalar fácilmente el agente en máquinas virtuales que se ejecutan en Azure mediante Azure Portal.  Si ya tiene un entorno de Operations Manager, puede conectar el grupo de administración a Log Analytics y empezar a recopilar datos de todos los agentes existentes automáticamente.
- **Servicios de Azure.**  Log Analytics recopila telemetría de [Azure Diagnostics y Azure Monitoring](../log-analytics/log-analytics-azure-storage.md) en el repositorio para que pueda supervisar los recursos de Azure.
- **API del recopilador de datos.**  Log Analytics tiene una [API de REST para rellenar los datos desde cualquier cliente](../log-analytics/log-analytics-data-collector-api.md).  Esto permite recopilar datos de aplicaciones de terceros o implementar escenarios de administración personalizados.  Un método común consiste en usar un runbook en Azure Automation para recopilar datos y, a continuación, usar la API del recopilador de datos para escribirlo en el repositorio.

#### <a name="reporting-and-analyzing-data"></a>Informes y análisis de datos
Log Analytics incluye un lenguaje de consulta eficaz para extraer los datos almacenados en el repositorio.  Puesto que los datos de todos los orígenes se almacenan como registros, puede analizar datos de varios orígenes en una sola consulta.
  
Además de análisis ad hoc, Log Analytics proporciona varias maneras de informar y analizar los datos de una consulta.

- **Vistas y paneles.**  Las [vistas](../log-analytics/log-analytics-view-designer.md) y [paneles](../log-analytics/log-analytics-dashboards.md) ofrecen la visualización de los resultados de una consulta en el portal.  Las soluciones de administración normalmente incluirán vistas que analizan los datos de la solución.  También puede crear sus propias vistas personalizadas para analizar los datos y que estén inmediatamente disponibles en el portal personalizado.
- **Exportación.**  Tiene la opción de exportar los resultados de cualquier consulta, para así poderla analizar fuera de Log Analytics.  Puede incluso programar una exportación regular a [Power BI](../log-analytics/log-analytics-powerbi.md), lo que proporciona funcionalidades de visualización y análisis significativas.
- **API de búsqueda de registros.**  Log Analytics tiene una [API de REST para recopilar datos desde cualquier cliente](../log-analytics/log-analytics-log-search-api.md).  Esto le permite trabajar mediante programación con los datos recopilados en el repositorio o acceder a él desde otra herramienta de supervisión.

#### <a name="alerting"></a>Alertas
Log Analytics puede [alertar de forma proactiva](../log-analytics/log-analytics-alerts.md), o realizar una acción correctora cuando detecta un problema.  Al igual que todos los demás análisis en Log Analytics, esto se realiza mediante una búsqueda de registros.  Esta búsqueda se ejecuta según una programación regular y se crea una alerta si los resultados coinciden con criterios concretos.

![Alertas de Log Analytics](media/operations-management-suite-overview/overview-alerts.png)

Además de crear un registro de alerta en el repositorio de Log Analytics, las alertas pueden realizar las siguientes acciones.

- **Correo electrónico.**  Enviar un correo electrónico para informar de forma activa sobre un problema detectado.
- **Runbook.**  Una alerta de Log Analytics puede iniciar un runbook en Azure Automation.  Esto se hace normalmente para intentar corregir el problema detectado.  El runbook se puede iniciar en la nube, en el caso de un problema en Azure u otra nube, o puede iniciarse en un agente local para un problema que aparezca en una máquina física o virtual.
- **Webhook.**  Una alerta puede iniciar un webhook y pasarle datos de los resultados de la búsqueda de registros.  Esto permite la integración con servicios externos, como un sistema de alertas alternativo, o puede intentar realizar una acción correctora para un sitio web externo.

### <a name="azure-automation"></a>Azure Automation
[Azure Automation](http://azure.microsoft.com/documentation/services/automation) proporciona administración de configuración y automatización de procesos a OMS.  Automatiza procesos manuales y ayuda a aplicar configuraciones a sistemas físicos y virtuales.  

#### <a name="process-automation"></a>Automatización de procesos
Azure Automation automatiza procesos manuales mediante [runbooks](../automation/automation-runbook-types.md) que se basan en un script de PowerShell o un flujo de trabajo de PowerShell.  También incluye recursos auxiliares de los runbooks, tales como variables que se pueden compartir entre varios runbooks, así como credenciales y conexiones que le permiten almacenar información cifrada que un runbook podría necesitar para realizar la autenticación.
Los runbooks ofrecen la automatización de procesos para los demás servicios en el conjunto de aplicaciones.  Puesto que cada uno de los demás servicios es accesible con PowerShell o a través de una API de REST, puede crear runbooks para llevar a cabo funciones como la recopilación de datos de administración en Log Analytics o iniciar una copia de seguridad con Azure Backup.

##### <a name="accessing-resources"></a>Acceso a los recursos
Puesto que los runbooks se basan en PowerShell, pueden administrar cualquier recurso al que se puede acceder con cmdlets de PowerShell.  Cuando [carga un módulo](../automation/automation-integration-modules.md) en su cuenta de Automation, este estará disponible para todos los runbooks de esa cuenta. 
 
Cuando los runbooks se ejecutan en la nube, pueden acceder a cualquier recurso que sea accesible desde la nube.  Podría tratarse de recursos de la suscripción de Azure, de otra nube, como Amazon Web Services (AWS) o de un servicio accesible a través de una API de REST.  Los runbooks de la nube no se ejecutan con credenciales, pero pueden sacar provecho de los recursos de Automation, como las credenciales, conexiones y certificados, para autenticarse en los recursos a los que acceden.

Un runbook que se ejecuta en la nube probablemente no podrá acceder a los recursos en su centro de datos.  De todas formas puede instalar uno o más [Hybrid Runbook Workers](../automation/automation-hybrid-runbook-worker.md) en su centro de datos para ejecutar runbooks que requieren acceso a los recursos locales.  Cuando inicia un runbook, especifique si desea que se ejecute en la nube o en un trabajo específico.

![Runbooks de Azure Automation](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Inicio de un runbook
Los runbooks se pueden [iniciar con una serie de métodos](../automation/automation-starting-a-runbook.md) lo que permite que puedan incluirse en una variedad de escenarios de administración.  

- **Azure Portal.**  Al igual que otros servicios de Azure, Azure Automation puede administrarse desde Azure Portal.  Además de iniciar runbooks, puede importarlos o crear sus propios.
- **Programación.**  Puede programar runbooks para iniciarse a intervalos regulares.  Esto le permite repetir automáticamente un proceso de administración regular o recopilar datos para Log Analytics.
- **PowerShell y API.**  Puede iniciar runbooks y pasarles la información de parámetros necesaria de un cmdlet de PowerShell o la API de REST de Azure Automation.  
- **Webhook.**  Se puede crear un webhook para cualquier runbook que le permita iniciarlo desde otras aplicaciones o sitios web.
- **Alerta de Log Analytics.**  Una alerta en Log Analytics puede iniciar automáticamente un runbook para intentar corregir el problema identificado por la alerta.

#### <a name="configuration-management"></a>Administración de configuración
[Configuración de estado deseado (DSC) de PowerShell](../automation/automation-dsc-overview.md) es una plataforma de administración de Windows PowerShell que permite implementar y aplicar la configuración de máquinas físicas y virtuales.  Azure Automation administra las configuraciones de DSC y proporciona un servidor de extracción en la nube al que pueden tener acceso los agentes para recuperar las configuraciones necesarias.

![DSC de Azure Automation](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Azure Backup y Azure Site Recovery
Azure Backup y Azure Site Recovery contribuyen a la continuidad del negocio y la recuperación ante desastres.  Cada uno de ellos tiene características que le ayudan a garantizar que las aplicaciones permanecen disponibles cuando se producen interrupciones y vuelven a su operatividad normal cuando los sistemas vuelven a estar conectados.  Ambos servicios contribuyen a los objetivos de puntos de recuperación (RPO) y objetivos de tiempo de recuperación (RTO) definidos para su organización. El RPO define el límite aceptable durante el que los datos no están disponibles en una interrupción, mientras que el RTO limita el periodo aceptable durante el que un servicio o aplicación no está disponible en una interrupción.

#### <a name="azure-backup"></a>Azure Backup
[Azure Backup](http://azure.microsoft.com/documentation/services/backup) proporciona copias de seguridad de datos y restauración de servicios de OMS.  Protege los datos de las aplicaciones y los conserva durante años sin necesidad de realizar ninguna inversión y afrontando unos costes operativos mínimos.  Permite realizar la copia de seguridad a partir de los servidores físicos y virtuales de Windows, además de cargas de trabajo de aplicaciones como SQL Server y SharePoint.  System Center Data Protection Manager (DPM) también puede usar esa solución para replicar los datos protegidos en Azure para obtener redundancia y almacenamiento a largo plazo.

Los datos protegidos en Azure Backup se almacenan en un almacén de copia de seguridad ubicado en una región geográfica determinada. Los datos se replican en la misma región y, según el tipo de almacén, también pueden replicarse en otra región para obtener una resistencia adicional.

Azure Backup tiene tres escenarios fundamentales.

- **Máquina de Windows con agente de Azure Backup.** Realice una copia de seguridad de archivos y carpetas desde cualquier servidor o cliente de Windows directamente a su almacén de Azure Backup.<br><br>![Máquina de Windows con agente de Azure Backup](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center Data Protection Manager (DPM) o Microsoft Azure Backup Server.** Aproveche DPM o Microsoft Azure Backup Server para realizar la copia de seguridad de archivos y carpetas, además de las cargas de trabajo de aplicaciones como SQL Server y SharePoint al almacenamiento local y, a continuación, efectuar la replicación en el almacén de Azure Backup. Es compatible con máquinas virtuales Windows y Linux en Hyper-V o VMware.<br><br>![System Center Data Protection Manager (DPM) o Microsoft Azure Backup Server](media/operations-management-suite-overview/overview-backup-02.png)
- **Extensiones de máquina virtual de Azure.** Realice copias de seguridad de las máquinas virtuales Windows o Linux en Azure en su almacén de Azure Backup.<br><br>![Extensiones de la máquina virtual de Azure](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) proporciona continuidad del negocio a través de la organización de la replicación de máquinas virtuales y físicas locales en Azure, o en un sitio secundario. Si su sitio primario no está disponible, se realizará la conmutación por error a la ubicación secundaria para que los usuarios pueden seguir trabajando y se realizará una conmutación por recuperación cuando los sistemas vuelvan a las condiciones de funcionamiento normales. 

Azure Site Recovery proporciona alta disponibilidad para servidores y aplicaciones.  Contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V locales, máquinas virtuales de VMware y servidores Windows/Linux físicos. Puede replicar las máquinas en un centro de datos secundario o ampliar su centro de datos mediante la replicación en Azure. Site Recovery también proporciona conmutación por error simple y recuperación para cargas de trabajo. Se integra con mecanismos de recuperación ante desastres, como SQL Server AlwaysOn y proporciona planes de recuperación para una conmutación por error sencilla de cargas de trabajo que están organizados en niveles entre varias máquinas.

Azure Site Recovery tiene tres escenarios fundamentales de replicación.

- **Replicación de máquinas virtuales de Hyper-V.**  Si las máquinas virtuales de Hyper-V se administran en nubes VMM, puede realizar la replicación en un centro de datos secundario o en Almacenamiento de Azure. La replicación en Azure se realiza a través de una conexión a Internet segura. La replicación en un centro de datos secundario se realiza a través de LAN.  Si VMM no administra las máquinas virtuales de Hyper-V, la replicación solo se pueden realizar a Azure Storage. La replicación en Azure se realiza a través de una conexión a Internet segura.<br><br>![Replicación de máquinas virtuales de Hyper-V](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **Replicación de máquinas virtuales de VMWare.**  Puede realizar la replicación de máquinas virtuales de VMware en un centro de datos secundario con VMware o en Almacenamiento de Azure. La replicación en Azure puede realizarse en una VPN de sitio o sitio o a sitio o Azure ExpressRoute, o a través de una conexión a Internet segura. La replicación a un centro de datos secundario se realiza a través del canal de datos InMage Scout.<br><br>![Replicación de máquinas virtuales de VMWare](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **Replicación de servidores físicos de Windows o Linux.**  Puede realizar la replicación de servidores físicos en un centro de datos secundario o en Almacenamiento de Azure. La replicación en Azure puede realizarse en una VPN de sitio o sitio o a sitio o Azure ExpressRoute, o a través de una conexión a Internet segura. La replicación a un centro de datos secundario se realiza a través del canal de datos InMage Scout. Azure Site Recovery tiene una solución de OMS que muestra algunas estadísticas, pero debe usar Azure Portal para todas las operaciones.<br><br>![Replicación de servidores físicos de Windows o Linux](media/operations-management-suite-overview/overview-siterecovery-physical.png)


Site Recovery almacena metadatos en almacenes ubicados en una región geográfica de Azure determinada. El servicio Site Recovery no almacena datos replicados.

## <a name="management-solutions"></a>Soluciones de administración
[Las soluciones de administración](operations-management-suite-solutions.md) son conjuntos de lógica preempaquetados que implementan un escenario de administración concreto aprovechando uno o más servicios de OMS.  Hay disponibles diferentes soluciones de Microsoft y de asociados que se pueden agregar fácilmente a la suscripción de Azure para aumentar el valor de su inversión en OMS.  Como asociado puede crear sus propias soluciones para que sean compatibles con los servicios y aplicaciones, y proporcionarlas a los usuarios a través de las plantillas de inicio rápido o Azure Marketplace.

Un buen ejemplo de una solución que aprovecha varios servicios para proporcionar funcionalidad adicional es la [solución de administración de actualizaciones](oms-solution-update-management.md).  Esta solución utiliza al agente de Log Analytics para Windows y Linux para recopilar información acerca de las actualizaciones necesarias en cada agente.  Escribe estos datos en el repositorio de Log Analytics en donde puede analizarlo con un panel incluido.  Cuando se crea una implementación, se utilizan runbooks en Azure Automation para instalar las actualizaciones necesarias.  Todo este proceso se puede administrar en el portal y no hace falta preocuparse de los detalles subyacentes.

![Solución](media/operations-management-suite-overview/overview-solution.png)

La mayoría de soluciones puede realizar una o varias de las siguientes funciones.

- Recopilar información adicional.  Log Analytics recopila una variedad de datos de los clientes y servicios, incluidos los eventos y datos de rendimiento.  Una solución de administración puede recopilar información adicional que no esté disponible a partir de otros orígenes de datos, a menudo con runbooks de Azure Automation.
- Proporcionar un análisis adicional de la información recopilada.  Las soluciones de administración incluyen paneles y vistas que proporcionan análisis y visualización de los datos.  Estos vinculan a búsquedas de registros predefinidos que le permiten profundizar en los datos detallados.  También pueden realizar análisis en los datos que ya se han recopilado en el repositorio, por ejemplo, mediante la realización de búsquedas en los eventos de seguridad de patrones que indiquen una amenaza.
- Agregar funcionalidad.  Algunas soluciones proporcionadas por Microsoft pueden basarse en las funcionalidades de los servicios principales para proporcionar funcionalidad adicional.  Por ejemplo, mapa de servicio proporciona su propia consola para detectar y asigna las dependencias de servidor y de proceso en tiempo real.
Microsoft y asociados agregan con regularidad nuevas soluciones a OMS, lo que permite aumentar el valor de su inversión de manera continua.  Puede examinar e instalar soluciones de Microsoft a través del catálogo de soluciones en el portal OMS, o examinar e instalar las soluciones de Microsoft y asociados a través de Azure Marketplace en Azure Portal.  

![Galería de soluciones](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Obtenga información sobre [Azure Automation](../automation/automation-intro.md).
* Obtenga información sobre [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Obtenga información sobre [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).
* Detectar las [soluciones disponibles](../log-analytics/log-analytics-add-solutions.md) en las diferentes ofertas de OMS. 

