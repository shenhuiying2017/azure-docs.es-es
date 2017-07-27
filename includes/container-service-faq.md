# <a name="container-service-frequently-asked-questions"></a>Preguntas más frecuentes sobre Azure Container Service

## <a name="orchestrators"></a>Orquestadores

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>¿Qué orquestadores de contenedor son compatibles con Azure Container Service? 

Hay compatibilidad con Kubernetes, Docker Swarm y DC/OS de código abierto. Para más información, consulte la [introducción](../articles/container-service/kubernetes/container-service-intro-kubernetes.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>¿Se admite el modo de Docker Swarm? 

Actualmente no se admite el modo Swarm, pero está en el plan de servicio. 

### <a name="does-azure-container-service-support-windows-containers"></a>¿Admite Azure Container Service contenedores de Windows?  

Los contenedores de Linux se admiten con todos los orquestadores. La compatibilidad de contenedores de Windows con Kubernetes está en versión preliminar.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>¿Se recomienda un organizador específico en Azure Container Service? 
Por lo general, no se recomienda un organizador específico. Si tiene experiencia con uno de los orquestadores compatibles, puede aplicarla a Azure Container Service. Sin embargo, las tendencias de datos sugieren que DC/OS es productivo para cargas de trabajo de IoT y macrodatos, Kubernetes es adecuado para las cargas de trabajo nativo de la nube y Docker Swarm se conoce por su integración con herramientas de Docker y su fácil curva de aprendizaje.

Según el escenario, también puede crear y administrar soluciones de contenedor personalizadas con otros servicios de Azure. Estos servicios incluyen [Virtual Machines](../articles/virtual-machines/linux/overview.md), [Service Fabric](../articles/service-fabric/service-fabric-overview.md), [Web Apps](../articles/app-service-web/app-service-web-overview.md) y [Batch](../articles/batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>¿Cuál es la diferencia entre Azure Container Service y ACS Engine? 
Azure Container Service es un servicio de Azure basado en SLA con características en Azure Portal, las herramientas de línea de comandos de Azure y las API de Azure. El servicio le permite implementar y administrar rápidamente clústeres que ejecutan las herramientas de organización de contenedores estándar con un número relativamente pequeño de opciones de configuración. 

[ACS Engine](http://github.com/Azure/acs-engine) es un proyecto de código abierto que permite a los usuarios avanzados personalizar la configuración de los clústeres en todos los niveles. Esta capacidad de modificar la configuración de la infraestructura y el software significa que no ofrecemos ningún SLA para ACS Engine. El soporte técnico se controla a través del proyecto de código abierto en GitHub, en lugar de a través de los canales oficiales de Microsoft. 

## <a name="cluster-management"></a>Administración de clústeres

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>¿Cómo se crean claves de SSH para el clúster?

Puede utilizar las herramientas estándar del sistema operativo para crear un par de claves SSH RSA públicas y privadas para la autenticación en las máquinas virtuales Linux del clúster. Para conocer los pasos, consulte las instrucciones para [OS X y Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) o [Windows](../articles/virtual-machines/linux/ssh-from-windows.md). 

Si usa los [comandos de la CLI de Azure 2.0](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) para implementar un clúster de servicio de contenedor, se pueden generar claves SSH para el clúster automáticamente.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>¿Cómo creo una entidad de servicio para el clúster de Kubernetes?

Para crear un clúster de Kubernetes en Azure Container Service también se necesitan ID y contraseña de la entidad de servicio de Azure Active Directory. Para más información, consulte [About the Azure Active Directory service principal for a Kubernetes cluster in Azure Container Service](../articles/container-service/kubernetes/container-service-kubernetes-service-principal.md) (Acerca de la entidad de servicio de Azure Active Directory para un clúster de Kubernetes de Azure Container Service).

Si usa los [comandos de la CLI de Azure 2.0](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) para implementar un clúster de Kubernetes, se pueden generar credenciales de entidad de servicio para el clúster automáticamente.

### <a name="how-large-a-cluster-can-i-create"></a>¿Cómo de grande puedo crear un clúster?
Puede crear un clúster con 1, 3 o 5 nodos maestros. Puede elegir hasta 100 nodos de agente.

> [!IMPORTANT]
> Para clústeres más grandes y en función del tamaño de la máquina virtual que elija para los nodos, deberá aumentar la cuota de núcleos de su suscripción. Para solicitar un aumento de cuota, abra una [solicitud de soporte técnico al cliente en línea](../articles/azure-supportability/how-to-create-azure-support-request.md) sin cargo alguno. Si usa una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), solo puede usar un número limitado de núcleos de proceso de Azure.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>¿Cómo se aumenta el número de patrones una vez creado un clúster? 
Una vez creado el clúster, el número de patrones es fijo y no se puede cambiar. Durante la creación del clúster, lo ideal es seleccionar varios maestros para lograr una alta disponibilidad.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>¿Cómo se aumenta el número de agentes una vez creado un clúster? 
Puede aumentar el número de agentes del clúster mediante Azure Portal o las herramientas de la línea de comandos. Consulte [Escalado de un clúster de Azure Container Service](../articles/container-service/kubernetes/container-service-scale.md).

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>¿Cuáles son las direcciones URL de mis patrones y agentes? 
Las direcciones URL de los recursos de clúster en Azure Container Service se basan en el prefijo del nombre DNS y el nombre de la región de Azure que haya elegido para la implementación. Por ejemplo, el nombre de dominio completo (FQDN) del nodo principal es de esta forma:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Encontrará direcciones URL de clúster comunes en Azure Portal, el Explorador de recursos de Azure u otras herramientas de Azure.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>¿Cómo se puede saber qué versión de orquestador se está ejecutando en mi clúster?

* DC/OS: Consulte la [documentación de Mesosphere](https://support.mesosphere.com/hc/en-us/articles/207719793-How-to-get-the-DCOS-version-from-the-command-line-)
* Docker Swarm: ejecute `docker version`
* Kubernetes: ejecute `kubectl version`

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>¿Cómo puedo actualizar el orquestador después de la implementación?

Actualmente, Azure Container Service no proporciona herramientas para actualizar la versión del orquestador que implementó en el clúster. Si Container Service es compatible con una versión posterior, puede implementar un nuevo clúster. Otra opción es usar herramientas específicas de orquestador si están disponibles para actualizar un clúster local. Por ejemplo, consulte [DC/OS Upgrading](https://dcos.io/docs/1.8/administration/upgrading/) (Actualización de DC/OS).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>¿Dónde se encuentra la cadena de conexión de SSH para el clúster?

Puede encontrar la cadena de conexión en Azure Portal o mediante las herramientas de la línea de comandos de Azure. 

1. En el portal, desplácese hasta el grupo de recursos para la implementación del clúster.  

2. Haga clic en **Información general** y en el vínculo **Implementaciones** de **Información esencial**. 

3. En la hoja **Historial de implementación**, haga clic en la implementación cuyo nombre comience por **microsoft-acs**, seguido de una fecha de implementación. Ejemplo: microsoft-acs-201701310000.  

4. En la página **Resumen**, en **Salidas**, se proporcionan varios vínculos de clúster. **SSHMaster0** proporciona una cadena de conexión de SSH para el primer patrón del clúster de servicio de contenedor. 

Como se indicó anteriormente, también puede utilizar las herramientas de Azure para buscar el FQDN del patrón. Realice una conexión de SSH con el patrón mediante el FQDN de este y el nombre de usuario que especificó al crear el clúster. Por ejemplo:

```bash
ssh userName@masterFQDN –A –p 22 
```

Para más información, consulte [Conexión a un clúster de Azure Container Service](../articles/container-service/kubernetes/container-service-connect.md).

## <a name="next-steps"></a>Pasos siguientes

* [Más información](../articles/container-service/kubernetes/container-service-intro-kubernetes.md) sobre Azure Container Service.
* Implementar un clúster del servicio de contenedores mediante el [portal](../articles/container-service/dcos-swarm/container-service-deployment.md) o la [CLI de Azure 2.0](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md).
