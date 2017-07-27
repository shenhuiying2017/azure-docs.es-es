# <a name="securing-docker-containers-in-azure-container-service"></a>Protección de contenedores de Docker en Azure Container Service

Este artículo presenta consideraciones y recomendaciones para proteger los contenedores de Docker implementados en Azure Container Service. Muchas de estas consideraciones se aplican generalmente a los contenedores de Docker implementados en Azure o en otros entornos. 

## <a name="image-security"></a>Seguridad de imágenes

Los contenedores se crean a partir de imágenes que están almacenadas en uno o varios repositorios. Estos repositorios pueden pertenecer a registros de contenedores públicos o privados. Un ejemplo de un registro público es [Docker Hub](https://hub.docker.com/). Un ejemplo de un registro privado es el [Registro de confianza de Docker](https://docs.docker.com/datacenter/dtr/2.0/), que puede instalarse de forma local o en una nube privada virtual. También hay servicios de registro privado de contenedores basados en la nube, incluido [Azure Container Registry](../articles/container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Imágenes públicas y privadas
En general, al igual que con cualquier paquete de software publicado de forma pública, una imagen de contenedor disponible públicamente no garantiza la seguridad. Las imágenes de contenedor constan de varias capas de software, y cada capa de software podría tener vulnerabilidades. Es esencial entender el origen de la imagen de contenedor, incluido el propietario de la imagen (para determinar si se trata de una fuente de confianza o no), las capas de software de las que consta y las versiones del software. 

Por ejemplo, si va al [repositorio nginx](https://hub.docker.com/_/nginx/) oficial en el Centro de Docker y navega hasta la pestaña **Etiquetas**, verá las vulnerabilidades codificadas por colores en cada imagen. Cada color representa la vulnerabilidad de una capa de software de la imagen. Para más información sobre el examen de vulnerabilidades en el Centro de Docker, consulte [Descripción de los repositorios oficiales en el Centro de Docker](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/).

![Imágenes nginx en el Centro de Docker](./media/container-service-security/docker-hub-nginx.png)

Las empresas ponen especial cuidado a la seguridad y para protegerse contra los ataques de seguridad deben almacenar y recuperar las imágenes de un registro privado, como Azure Container Registry o el Registro de confianza de Docker. Además de proporcionar un registro privado administrado, Azure Container Registry admite [autenticación basada en la entidad de servicio](../articles/container-registry/container-registry-authentication.md) a través de Azure Active Directory para los flujos de autenticación básica, incluido el acceso basado en roles para permisos de solo lectura, escritura y propietario.

### <a name="image-security-scanning"></a>Examen de seguridad de imágenes

Incluso cuando se usa un registro privado, es una buena idea usar soluciones de examen de imágenes para la validación de seguridad adicional. Cada capa de software en una imagen de contenedor es potencialmente susceptible a vulnerabilidades independiente del resto de niveles en la imagen de contenedor. A medida que cada vez más empresas empiezan a implementar sus cargas de trabajo de producción que se basan en tecnologías de contenedor, el examen de las imágenes adquiere importancia para asegurarse de prevenir amenazas a la seguridad en sus organizaciones. 

Las soluciones de supervisión y análisis de seguridad, como [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) y [Aqua Security](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry), entre otros, se pueden utilizar para examinar imágenes de contenedor en un registro privado e identificar posibles vulnerabilidades. Es importante comprender la profundidad del análisis que proporcionan las distintas soluciones. Por ejemplo, algunas soluciones podrían verificar las capas de imagen en comparación con vulnerabilidades conocidas. Es posible que estas soluciones no logren verificar software con capas de imágenes creado mediante cierto software de administración de paquetes. Otras soluciones tienen una integración del análisis más profunda y pueden encontrar vulnerabilidades en cualquier software empaquetado.

### <a name="production-deployment-rules-and-audit"></a>Auditoría y reglas de implementación de producción
Una vez que una aplicación se implementa en producción, es esencial establecer algunas reglas para asegurarse de que las imágenes que se utilizan en entornos de producción sean seguras y no contengan ninguna vulnerabilidad.

* Como regla, las imágenes con vulnerabilidades, incluso menores, no debe permitirse que se ejecuten en un entorno de producción. Además, lo ideal es que todas las imágenes implementadas en producción se guarden en un registro privado donde solo algunas personas específicas tengan acceso. También es importante mantener un número pequeño de imágenes de producción para asegurarse de que se puedan administrar eficazmente.

* Puesto que es difícil identificar el origen del software desde una imagen de contenedor disponible públicamente, es una buena práctica crear imágenes desde la fuente para garantizar el conocimiento del origen de la capa. Cuando surge una vulnerabilidad en una imagen de contenedor generada automáticamente, los clientes pueden encontrar una ruta más rápida a una resolución. Con una imagen pública, los clientes tendrían que encontrar la raíz de una imagen pública para corregirla u obtener otra imagen segura del publicador.

* No se garantiza que una imagen digitalizada exhaustivamente implementada en producción esté actualizada para la duración de la aplicación. Se puede informar de las vulnerabilidades de seguridad para las capas de la imagen que no se conocían anteriormente o que se introdujeron después de la implementación de producción. Son necesarias auditorías periódicas de imágenes implementadas en producción para identificar las imágenes que no están al día o que no se han actualizado en un tiempo. Se podrían usar metodologías de implementación de verde azulado y activar mecanismos de actualización para actualizar las imágenes de contenedor sin tiempo de inactividad. El examen de imágenes puede realizarse con herramientas que se describen en la sección anterior. 

* Una canalización de integración continua (CI) para crear imágenes y el examen de seguridad integrada puede ayudar a mantener seguros los registros privados con imágenes de contenedor seguras. El análisis de vulnerabilidad creado en la solución de CI garantiza que las imágenes que pasan todas las pruebas se incluyan en el registro privado desde el que se implementan las cargas de trabajo de producción. Un error de canalización de CI garantiza que las imágenes vulnerables no se incluyan en el registro privado usado para las implementaciones de carga de trabajo de producción. También automatiza el examen de seguridad de imágenes si hay un número significativo de imágenes. De lo contrario, auditar las imágenes manualmente para buscar vulnerabilidades de seguridad puede ser bastante lento y estar propenso a errores.

## <a name="host-level-container-isolation"></a>Aislamiento de contenedor a nivel de host
Cuando un cliente implementa aplicaciones de contenedor en recursos de Azure, se implementan en un nivel de suscripción en grupos de recursos y no son tienen varios inquilinos. Esto significa que si un cliente comparte una suscripción con otros usuarios, no hay ningún límite que se pueda crear entre dos implementaciones en la misma suscripción. Por lo tanto, no se garantiza la seguridad a nivel de contenedor. 

También es muy importante comprender que los contenedores comparten el kernel y los recursos del host (que en Azure Container Service es una VM de Azure en un clúster). Por lo tanto, los contenedores que se ejecutan en producción se deben ejecutar en modo de usuario sin privilegios. Ejecutar un contenedor con privilegios de raíz puede poner en peligro a todo el entorno. Con acceso de nivel de raíz en un contenedor, un hacker puede tener acceso a todos los privilegios de raíz en el host. Además, es importante que ejecute contenedores con sistemas de archivos de solo lectura. Esto evita que alguien que tenga acceso al contenedor en peligro escriba scripts malintencionados en el sistema de archivos y obtenga acceso a otros archivos. De igual forma, es importante limitar los recursos (por ejemplo, memoria, CPU y ancho de banda de red) asignados a un contenedor. Esto ayuda a impedir que los hackers acaparen recursos y realicen actividades ilegales, como fraudes con tarjetas de crédito o minería bitcoin, que podría impedir que otros contenedores se ejecuten en el host o clúster.

## <a name="orchestrator-considerations"></a>Consideraciones sobre el orquestador

Cada orquestador disponible en Azure Container Service tiene sus propias consideraciones de seguridad. Por ejemplo, debe limitar el acceso directo de SSH a nodos del orquestador en Container Service. En su lugar, debe usar la IU o las herramientas de línea de comandos de cada orquestador (como `kubectl` para Kubernetes) para administrar el entorno del contenedor sin acceso a los hosts. Para más información, consulte [Establecimiento de una conexión remota a un clúster de Kubernetes, DC/OS o Docker Swarm](../articles/container-service/kubernetes/container-service-connect.md).

Para información adicional sobre la seguridad específica del orquestador, consulte estos los siguientes recursos:

* **Kubernetes**: [prácticas recomendadas de seguridad para la implementación de Kubernetes](http://blog.kubernetes.io/2016/08/security-best-practices-kubernetes-deployment.html)

* **DC/OS**: [cómo asegurar su clúster](https://dcos.io/docs/1.8/administration/securing-your-cluster/)

* **Docker Swarm**: [seguridad de Docker](https://www.docker.com/docker-security)

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de la seguridad de la arquitectura y el contenedor de Docker, consulte [Introducción a la seguridad del contenedor](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf).

* Para más información sobre la seguridad de la plataforma de Azure, vaya al [Azure Security Center](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure).