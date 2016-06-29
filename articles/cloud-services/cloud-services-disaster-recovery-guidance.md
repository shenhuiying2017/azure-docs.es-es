<properties
	pageTitle="Qué hacer en caso de una interrupción del servicio de Azure que afecte a los Servicios en la nube de Azure | Microsoft Azure"
	description="Descubra qué hacer en caso de que se produzca una interrupción del servicio de Azure que afecte a los Servicios en la nube de Azure."
	services="cloud-services"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="cloud-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#Qué hacer en caso de una interrupción del servicio de Azure que afecte a los Servicios en la nube de Azure

En Microsoft, hacemos todo lo posible para garantizar que nuestros servicios estén siempre disponibles cuando los necesite. En ocasiones, debido a factores externos que escapan de nuestro control, se producen interrupciones de servicio no planeadas.

Microsoft proporciona Acuerdos de Nivel de Servicio para sus servicios como un compromiso en cuanto al tiempo de actividad y la conectividad. Puede encontrar el Acuerdo de Nivel de Servicio para los diferentes servicios de Azure en [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

Azure ya integra en su plataforma muchas características que admiten aplicaciones de alta disponibilidad. Para obtener más información sobre estos servicios, lea [Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure](https://aka.ms/drtechguide).

En este artículo se expone un escenario real de recuperación ante desastres en el que toda una región experimenta una interrupción debido a un desastre natural importante o a una interrupción del servicio generalizada. Se trata de casos muy infrecuentes, pero debe estar preparado para la posibilidad de que se produzca una interrupción en toda una región. Si una región completa experimenta una interrupción del servicio, las copias con redundancia local de los datos estarían temporalmente no disponibles. Si ha habilitado la replicación geográfica, se almacenan en una región distinta tres copias adicionales de los blobs y las tablas de Almacenamiento de Azure. En caso de una interrupción completa en una región o de un desastre en el que la región primaria no sea recuperable, Azure reasignará todas las entradas DNS a la región de replicación geográfica.

>[AZURE.NOTE]Tenga en cuenta que no tiene ningún control sobre este proceso y que solo se producirá si se da una interrupción del servicio en todo el centro de datos. Por este motivo, también debe confiar en otras estrategias de copia de seguridad específicas de la aplicación para lograr el máximo nivel de disponibilidad. Para obtener más información, consulte la sección sobre [estrategias de datos para la recuperación ante desastres](https://aka.ms/drtechguide#DSDR). Si desea poder influir sobre su propia conmutación por error, plantéese usar un [almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage), el cual crea una copia de solo lectura de sus datos en otra región.

Para ayudarle a administrar estos eventos poco frecuentes, le proporcionamos las siguientes orientaciones para máquinas virtuales (VM) de Azure destinadas a los casos de interrupción del servicio en toda una región donde se ha implementado la aplicación de VM de Azure.

##Opción 1: esperar a que se recupere el servicio
En este caso, no se requieren acciones por su parte. Los equipos de Azure trabajan con rapidez para que el servicio vuelva a estar disponible. El estado actual del servicio se puede ver en el [panel de estado de los servicios de Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Se trata de la mejor opción si el cliente no ha configurado Azure Site Recovery o si cuenta con una implementación secundaria en otra región.

Para los clientes que deseen obtener un acceso inmediato a sus servicios implementados en la nube, se encuentran disponibles las siguientes opciones.

>[AZURE.NOTE]Tenga en cuenta que estas opciones pueden conllevar la pérdida de algunos datos.

##Opción 2: volver a implementar la configuración del servicio en la nube en una nueva región

Si tiene el código original, puede simplemente volver a implementar la aplicación, así como la configuración y los recursos asociados en un nuevo servicio en la nube en una región distinta.

Para obtener más información sobre cómo crear e implementar una aplicación de servicio en la nube, consulte [Creación e implementación de un servicio en la nube](./cloud-services-how-to-create-deploy-portal.md).

En función de los orígenes de datos de la aplicación, es posible que deba comprobar los procedimientos de recuperación para el origen de datos de su aplicación.
  * Para los orígenes de datos de Almacenamiento de Azure, consulte [Replicación de almacenamiento de Azure](../storage/storage-redundancy.md#read-access-geo-redundant-storage) a fin de ver las opciones disponibles según el modelo de replicación elegido para su aplicación.
  * Para los orígenes de Base de datos SQL, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](../sql-database/sql-database-business-continuity.md) a fin de ver las opciones disponibles según el modelo de replicación elegido para su aplicación.

##Opción 3: usar una implementación de copia de seguridad a través del Administrador de tráfico de Azure
Para esta opción, se da por hecho que ya ha diseñado la solución de aplicación teniendo en cuenta la recuperación ante desastres. Puede utilizar esta opción si ya tiene una implementación de aplicación de servicios en la nube secundaria que se ejecuta en una región distinta y está conectada a través de un canal de administrador de tráfico. En este caso, compruebe el estado de la implementación secundaria. Si es bueno, puede redirigir el tráfico a ella mediante el Administrador de tráfico de Azure. Con esta estrategia, es posible aprovechar el método de enrutamiento de tráfico y las configuraciones de orden de conmutación por error del Administrador de tráfico de Azure. Para obtener más información, consulte [Cómo configurar las opciones del Administrador de tráfico](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings).

![Equilibrio de los Servicios en la nube de Azure en distintas regiones con el Administrador de tráfico de Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##Pasos siguientes

Para obtener más información sobre cómo implementar una estrategia de alta disponibilidad y recuperación ante desastres, consulte [Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure](https://aka.ms/drtechguide).

Para obtener unos conocimientos técnicos detallados sobre las funcionalidades de una plataforma de nube, consulte [Guía técnica sobre resistencia en Azure](https://aka.ms/bctechguide).

Si las instrucciones no están claras, o si desea que Microsoft realice las operaciones en su lugar, póngase en contacto con el [servicio de asistencia al cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0615_2016-->