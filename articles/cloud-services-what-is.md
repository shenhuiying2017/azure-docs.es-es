<properties 
	pageTitle="¿Qué es un servicio en la nube? - Administración de servicios de Azure" 
	description="Introducción a Servicios en la nube de Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="adegeo"/>




# ¿Qué es un servicio en la nube?
Cuando se crea una aplicación y se ejecuta en Azure, el código y configuración en conjunto se denominan un servicio en la nube de Azure (conocido como un *hosted service* en versiones anteriores de Azure).

Al crear un servicio en la nube, puede implementar una aplicación web de niveles múltiples en Azure, definir múltiples roles para distribuir el procesamiento y permitir un escalado flexible de su aplicación. Un servicio en la nube consta de uno o varios roles web y/o roles de trabajo, cada uno de los cuales con sus propios archivos de aplicación y configuración. Los Sitios web y las Máquinas virtuales de Azure también habilitan aplicaciones web en Azure.  La principal ventaja de los servicios en la nube es la capacidad de admitir más arquitecturas de niveles múltiples complejas. Para obtener una comparación detallada, consulte [Comparación entre Sitios web Azure, Servicios en la nube y Máquinas virtuales][Comparison].

Para un servicio en la nube, Azure mantiene la infraestructura por usted, de modo que realiza un mantenimiento rutinario, revisa los sistemas operativos e intenta recuperarse de los errores de hardware y del servicio. Si define al menos dos instancias de cada rol, la mayor parte del mantenimiento, así como sus propias actualizaciones del servicio, se pueden realizar sin interrupciones del servicio. Un servicio en la nube debe tener al menos dos instancias de cada rol para optar al contrato de nivel de servicio de Azure, que garantiza la conectividad externa a sus roles accesibles desde Internet con una disponibilidad mínima del 99,95%. 

Cada servicio en la nube tiene dos entornos en los que puede implementar el paquete y la configuración del servicio. Puede implementar un servicio en la nube en el entorno de ensayo para probarlo antes de pasar a producción. Para pasar un servicio en la nube de ensayo a producción, tan solo es necesario intercambiar las direcciones IP virtuales (VIP) que están asociadas a los dos entornos. 


## Conceptos ##


- **rol de servicio en la nube:** un rol de servicio en la nube consta de archivos de aplicación y una configuración. Un servicio en la nube puede tener dos tipos de rol:
 
>- **rol web:**un rol web proporciona un servidor web de Internet Information Services (IIS) dedicado, que se utiliza para hospedar aplicaciones web front-end.

>- **rol de trabajo:** las aplicaciones hospedadas en roles de trabajo pueden ejecutar tareas asincrónicas, de ejecución prolongada o perpetuas, independientes de la entrada o de la interacción del usuario.

- **instancia de rol:** una instancia de rol es una máquina virtual en la que se ejecutan el código de la aplicación y la configuración de rol. Un rol puede tener varias instancias, las que se encuentran definidas en el archivo de configuración de servicio.

- **sistema operativo invitado:** el sistema operativo invitado para un servicio en la nube es el sistema operativo que está instalado en las instancias de los roles (máquinas virtuales) en las que se ejecuta el código de la aplicación.

- **componentes del servicio en la nube:** se necesitan tres componentes para implementar una aplicación como servicio en la nube en Azure:

>- **archivo de definición de servicio:** el archivo de definición de servicio en la nube (.csdef) define el modelo de servicio, incluyendo el número de roles.

>- **archivo de configuración de servicio:** el archivo de configuración de servicio en la nube (.cscfg) proporciona opciones de configuración para los roles de servicio en la nube e individuales, incluyendo el número de instancias de rol.

>- **paquete de servicio:** el paquete de servicio (.cspkg) contiene el código de la aplicación y el archivo de definición de servicio.

- **implementación del servicio en la nube:** una implementación del servicio en la nube es una instancia de un servicio en la nube implementada en el entorno de ensayo o de producción de Azure. Puede mantener implementaciones tanto en producción como en ensayo.

- **entornos de implementación:** Azure ofrece dos entornos de implementación para servicios en la nube: un *staging environment* en el cual puede probar la implementación antes de promoverla al *production environment*. Los dos entornos se distinguen solo por las direcciones IP virtuales (VIP) a través de las cuales se obtiene acceso al servicio en la nube. En el entorno de ensayo, el identificador único global (GUID) del servicio en la nube lo identifica en las direcciones URL (*GUID*.cloudapp.net). En el entorno de producción, la dirección URL se basa en el prefijo de DNS más fácil de usar asignado al servicio en la nube (por ejemplo, *myservice*.cloudapp.net).

- **intercambio de implementaciones:** para promover una implementación en el entorno de ensayo de Azure al entorno de producción, puede "intercambiar" las implementaciones mediante el cambio de las VIP a través de las cuales se obtiene acceso a ambas implementaciones. Después de la implementación, el nombre DNS del servicio en la nube apunta a la implementación que ha estado en el entorno de ensayo. 

- **comparación entre supervisión mínima y supervisión detallada:** *Minimal monitoring*, que está configurada de manera predeterminada para un servicio en la nube, usa contadores de rendimiento recopilados desde los sistemas operativos de host para instancias de rol (máquinas virtuales). *Verbose monitoring* recopila métricas adicionales según los datos de rendimiento dentro de las instancias de rol para habilitar un análisis más cercano de los problemas que se producen durante el procesamiento de la aplicación. Para obtener más información, consulte [Supervisión de servicios en la nube][HTMonitorCloudServices].

- **Diagnósticos de Azure:** Diagnósticos de Azure es la API que le permite recopilar datos de diagnósticos desde aplicaciones que se ejecutan en Azure. Diagnósticos de Azure debe estar habilitada para los roles de servicio en la nube para así poder activar la supervisión detallada. Para obtener más información, consulte [Habilitación de Diagnósticos en Azure][CloudServicesDiagnostics].

- **vincular un recurso:** para mostrar las dependencias que su servicio en la nube tiene de otros recursos, como una instancia de Base de datos SQL de Azure, puede "vincular" el recurso al servicio en la nube. En el Portal de vista previa de administración, puede ver recursos vinculados en la página **Recursos vinculados**, consulte su estado en el panel y escale una instancia de Base de datos SQL vinculada junto con los roles de servicio en la página **Escalar**. Vincular un recurso en este sentido no conecta el recurso con la aplicación: debe configurar las conexiones en el código de la aplicación.

- **escalar un servicio en la nube:** un servicio en la nube se escala horizontalmente al aumentar el número de instancias de rol (máquinas virtuales) implementadas para un rol. Un servicio en la nube se reduce horizontalmente al disminuir las instancias de rol. En el Portal de vista previa de administración, también puede escalar una instancia de Base de datos SQL vinculada si cambia la edición de Base de datos SQL y el tamaño máximo de la base de datos cuando escala los roles de servicio.

- **Contrato de nivel de servicio (SLA) de Azure:** el contrato de nivel de servicio de proceso de Azure garantiza que, cuando implemente dos o más instancias de rol para cada rol, el acceso a su servicio en la nube se mantendrá, al menos, el 99,95 por ciento del tiempo. Además, se iniciará una acción de detección y corrección el 99,9 por ciento de las veces cuando un proceso de instancia de rol no esté en ejecución. Para obtener más información, consulte [Acuerdos de nivel de servicio][SLA].

[HTMonitorCloudServices]:http://azure.microsoft.com/ manage/services/cloud-services/how-to-monitor-a-cloud-service/
[SLA]: http://azure.microsoft.com/ support/legal/sla/
[CloudServicesDiagnostics]: http://azure.microsoft.com/ documentation/articles/cloud-services-dotnet-diagnostics/
[Comparison]: http://azure.microsoft.com/ documentation/articles/choose-web-site-cloud-service-vm/

<!--HONumber=45--> 
