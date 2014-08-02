<properties linkid="manage-services-what-is-a-cloud-service" urlDisplayName="What is a Cloud Service" pageTitle="What is a cloud service - Azure service management" metaKeywords="Azure cloud services intro, cloud services overview, cloud services basics" description="An introduction to the cloud service in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="What is a cloud service?" authors="ryanwi" solutions="" manager="" editor="" />

¿Qué es un servicio en la nube?
===============================

Al crear una aplicación y ejecutarla en Azure, al código y a la configuración se les denomina de forma conjunta un servicio en la nube de Azure (conocido como un *servicio hospedado* en las versiones anteriores de Azure).

Al crear un servicio en la nube, puede implementar una aplicación de niveles múltiples en Azure, definir múltiples roles para distribuir el procesamiento y permitir un escalado flexible de su aplicación. Un servicio en la nube consta de uno o varios roles web y/o roles de trabajo, cada uno de los cuales con sus propios archivos de aplicación y configuración.

Para un servicio en la nube, Azure mantiene la infraestructura por usted, de modo que realiza un mantenimiento rutinario, revisa los sistemas operativos e intenta recuperarse de los errores de hardware y del servicio. Si define al menos dos instancias de cada rol, la mayor parte del mantenimiento, así como sus propias actualizaciones del servicio, se pueden realizar sin interrupciones del servicio. Un servicio en la nube debe tener al menos dos instancias de cada rol para optar al contrato de nivel de servicio de Azure, que garantiza la conectividad externa a sus roles accesibles desde Internet con una disponibilidad mínima del 99,95%.

Cada servicio en la nube tiene dos entornos en los que puede implementar el paquete y la configuración del servicio. Puede implementar un servicio en la nube en el entorno de ensayo para probarlo antes de pasar a producción. Para pasar un servicio en la nube de ensayo a producción, tan solo es necesario intercambiar las direcciones IP virtuales (VIP) que están asociadas a los dos entornos.

Conceptos
---------

-   **Rol de servicio en la nube:** un rol de servicio en la nube está compuesto de archivos de aplicación y una configuración. Un servicio en la nube puede tener dos tipos de rol:

> -   **Rol web:** un rol web proporciona un servidor web de Internet Information Services (IIS) dedicado, que se utiliza para hospedar aplicaciones web front-end.

> -   **Rol de trabajo:** las aplicaciones hospedadas en roles de trabajo pueden ejecutar tareas asincrónicas, de ejecución prolongada o perpetuas, independientes de la entrada o la interacción del usuario.

-   **Instancia de rol:** una instancia de rol es una máquina virtual en la que se ejecutan el código de aplicación y la configuración del rol. Un rol puede tener múltiples instancias, definidas en el archivo de configuración del servicio.

-   **Sistema operativo invitado:** el sistema operativo invitado para un servicio en la nube es el sistema operativo que están instalado en las instancias de los roles (máquinas virtuales) en las que se ejecuta el código de la aplicación.

-   **Componentes del servicio en la nube:** se necesitan tres componentes para implementar una aplicación como servicio en la nube en Azure:

> -   **Archivo de definición de servicio:** el archivo de definición de servicio en la nube (.csdef) define el modelo de servicio, incluyendo el número de roles.

> -   **Archivo de configuración de servicio:** el archivo de configuración de servicio en la nube (.cscfg) proporciona opciones de configuración para los roles de servicio en la nube e individuales, incluyendo el número de instancias de rol.

> -   **Paquete de servicio:** el paquete de servicio (.cspkg) contiene el código de la aplicación y el archivo de definición de servicio.

-   **Implementación de servicio en la nube:** una implementación de servicio en la nube es una instancia de un servicio en la nube implementado en el entorno de ensayo o producción de Azure. Puede mantener las implementaciones en ensayo y en producción.

-   **Entornos de implementación:** Azure ofrece dos entornos de implementación para los servicios en la nube: un *entorno de ensayo* en el que puede probar la implementación antes de pasarla al *entorno de producción*. Los dos entornos se distinguen entre sí solo por las direcciones IP virtuales (VIP) por las que se tiene acceso al servicio en la nube. En el entorno de ensayo, el identificador único global (GUID) del servicio en la nube identifica este servicio en la nube en las URL (*GUID*.cloudapp.net). En el entorno de producción, la URL se basa en un prefijo DNS más intuitivo que se le asigna al servicio en la nube (por ejemplo, *myservice*.cloudapp.net).

-   **Cambiar implementaciones:** para pasar una implementación del entorno de ensayo de Azure al entorno de producción, puede "cambiar" las implementaciones mediante un cambio de VIP, a través del cual se tiene acceso a las dos implementaciones. Después de la implementación, el nombre DNS del servicio en la nube apunta a la implementación que estaba en el entorno de ensayo.

-   **Supervisión mínima frente a detallada:** la *supervisión mínima*, configurada de forma predeterminada para un servicio en la nube, usa contadores de rendimiento recopilados de los sistemas operativos host para las instancias de rol (máquinas virtuales). La *supervisión detallada* recopila métricas adicionales basadas en los datos del rendimiento dentro de las instancias de rol para permitir un análisis más profundo de los problemas que se producen durante el procesamiento de la aplicación. Para obtener más información, consulte [Supervisión de servicios en la nube](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

-   **Diagnóstico de Azure:** Diagnósticos de Azure es la API que le permite recopilar datos de diagnóstico de las aplicaciones que se ejecutan en Azure. Los Diagnósticos de Azure deben estar habilitados para los roles del servicio en la nube para que la supervisión detallada esté activada.

-   **Vincular un recurso:** para ver las dependencias de su servicio en la nube en otros recursos, como una instancia de Base de datos SQL de Azure, puede "vincular" el recurso con el servicio en la nube. En el Portal de administración de vista previa, puede ver los recursos vinculados en la página **Linked Resources**, ver sus estados en el panel y escalar una instancia de Base de datos SQL vinculada junto con los roles de servicio en la página **Scale**. En este sentido, vincular un recurso no conecta el recurso con la aplicación; debe configurar las conexiones en el código de la aplicación.

-   **Escalar un servicio en la nube:** un servicio en la nube amplía su escalado mediante el aumento del número de instancias de rol (máquinas virtuales) implementadas para un rol. Un servicio en la nube disminuye su escalado mediante la reducción de instancias de rol. En el Portal de administración de vista previa, también puede escalar una instancia de Base de datos SQL vinculada mediante el cambio de edición de la Base de datos SQL y de tamaño máximo de la base de datos, al escalar los roles del servicio.

-   **Contrato de nivel de servicio de Azure (SLA):** el SLA de proceso de Azure garantiza que, al implementar dos o más instancias de rol para cada rol, el acceso al servicio en la nube se mantendrá en una disponibilidad mínima del 99,95%. Además, la detacción y las acciones correctivas se iniciarán en un 99,9% de las veces si un proceso de instancia de rol no se está ejecutando. Para obtener más información, consulte [Contratos de nivel de servicio](https://www.windowsazure.com/en-us/support/legal/sla/).


