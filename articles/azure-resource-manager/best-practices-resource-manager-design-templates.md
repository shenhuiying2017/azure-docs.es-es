---
title: "Diseño de plantillas de Azure para soluciones complejas | Microsoft Docs"
description: "Muestra los procedimientos recomendados para diseñar plantillas de Azure Resource Manager para escenarios complejos"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ce1141d6-ece7-4976-acea-1db1f775409e
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
ms.openlocfilehash: dcc31f7a8c85a8f7fbd554371a66fb1e348bca17
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="design-patterns-for-azure-resource-manager-templates-when-deploying-complex-solutions"></a>Diseño de patrones para plantillas de Azure Resource Manager cuando implementa soluciones complejas
Con un enfoque flexible basado en plantillas de Azure Resource Manager, puede implementar topologías complejas de forma rápida y coherente. Puede adaptar estas implementaciones fácilmente a medida que evolucionan las ofertas centrales o para dar cabida a variantes para escenarios o clientes atípicos.

Este tema forma parte de un artículo más extenso. Para leer el documento completo, descargue [World Class Azure Resource Manager Templates Considerations and Proven Practices](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf) (Consideraciones sobre las plantillas de Resource Manager de talla mundial y prácticas comprobadas).

Las plantillas combinan las ventajas del Administrador de recursos de Azure subyacente con la capacidad de adaptación y legibilidad de la Notación de objetos JavaScript (JSON). El uso de plantillas le permite:

* Implementar topologías y sus cargas de trabajo de forma coherente.
* Administrar juntos todos los recursos de una aplicación mediante grupos de recursos.
* Aplicar control de acceso basado en rol (RBAC) para conceder el acceso adecuado a usuarios, grupos y servicios.
* Usar asociaciones de etiquetas para simplificar tareas como resúmenes de facturación.

Este artículo proporciona información detallada sobre escenarios de consumo, arquitectura y patrones de implementación identificados durante nuestras sesiones de diseño e implementaciones de plantillas reales con los clientes del equipo de asesoría de clientes de Azure (AzureCAT). Lejos de ser académicos, estos enfoques son prácticas probadas obtenidas mediante el desarrollo de plantillas para 12 de las principales tecnologías de software de código abierto basadas en Linux, como por ejemplo: Apache Kafka, Apache Spark, Cloudera, Couchbase, Hortonworks HDP, DataStax Enterprise powered by Apache Cassandra, Elasticsearch, Jenkins, MongoDB, PostgreSQL, Redis y Nagios. 

Este artículo comparte estas prácticas de demostrada eficacia con el fin de ayudarle a diseñar plantillas del Administrador de recursos de Azure de talla mundial.  

En nuestro trabajo con los clientes, hemos identificado varias experiencias de consumo de plantillas de Resource Manager en empresas, integradores de sistemas y proveedores de soluciones en la nube. Las siguientes secciones proporcionan información general de alto nivel de escenarios y patrones comunes para distintos tipos de clientes.

## <a name="enterprises-and-system-integrators"></a>Empresas e integradores de sistemas
En las grandes organizaciones, observamos normalmente dos consumidores de plantillas de Resource Manager: los equipos de desarrollo de software internos y los grupos de TI corporativos. Hemos visto que los escenarios para los integradores de sistemas se corresponden con los escenarios para empresas, por lo que se aplican las mismas consideraciones.

### <a name="internal-software-development-teams"></a>Equipos de desarrollo de software internos
Si su equipo desarrolla software para respaldar su negocio, las plantillas proporcionan una manera fácil de implementar rápidamente tecnologías para su uso en soluciones específicas de la empresa. También puede usar plantillas para crear rápidamente entornos de aprendizaje que permitan a los miembros del equipo obtener los conocimientos necesarios.

Puede usar las plantillas tal y como están o ampliarlas o componerlas para adaptarlas a sus necesidades. El uso de etiquetado dentro de las plantillas permite proporcionar un resumen de facturación con diversas vistas como equipo, proyecto, individuo y educación.

Las empresas a menudo quieren que los equipos de desarrollo de software creen una plantilla para la implementación coherente de una solución. La plantilla facilita las restricciones para que determinados elementos dentro de ese entorno permanezcan fijos y no se puedan invalidar. Por ejemplo, un banco puede requerir que una plantilla incluya RBAC, de modo que un programador no pueda revisar una solución bancaria para enviar datos a una cuenta de almacenamiento personal.

### <a name="corporate-it"></a>Grupos de TI corporativos
Las organizaciones de TI corporativa suelen usar plantillas para ofrecer capacidad en la nube y funcionalidades hospedadas en la nube.

#### <a name="cloud-capacity"></a>Capacidad en la nube
Un método común que emplean los grupos de TI corporativos para proporcionar capacidad en la nube a los equipos es con "tamaños de camiseta", que son tamaños de oferta estándar, por ejemplo, pequeña, mediana y grande. Las ofertas basadas en el tamaño de camiseta pueden combinar diferentes tipos de recursos y cantidades y proporcionar al mismo tiempo un nivel de estandarización que hace posible el uso de plantillas. Las plantillas entregan capacidad de una manera coherente gracias a la aplicación de directivas corporativas y al uso de etiquetas para proporcionar contracargo o asignación de costos a las organizaciones que las consumen.

Por ejemplo, puede que necesite proporcionar entornos de desarrollo, prueba o producción en los que los equipos de desarrollo de software puedan implementar sus soluciones. El entorno tiene una topología de red predefinida y elementos que los equipos de desarrollo de software no pueden cambiar, como las reglas que controlan el acceso a la inspección de paquetes e Internet pública. Puede que también haya roles específicos de la organización en estos entornos con derechos de acceso distintos en cada uno.

#### <a name="cloud-hosted-capabilities"></a>Funcionalidades hospedadas en la nube
Puede usar plantillas para respaldar las funcionalidades hospedadas en la nube, como paquetes de software individuales u ofertas compuestas que se ofrecen a las líneas de negocio internas. Un ejemplo de una oferta compuesta sería el análisis como servicio: análisis, visualización y otras tecnologías, proporcionado en una configuración optimizada y conectada en una topología de red predefinida.

Las funcionalidades hospedadas en la nube se ven afectadas por las consideraciones sobre seguridad y roles establecidas por la oferta de capacidad en la nube en la que se basan. Estas funcionalidades se ofrecen tal y como están o como un servicio administrado. En este último caso, se requieren roles con acceso limitado para permitir el acceso en el entorno con fines de administración.

## <a name="cloud-service-vendors"></a>Proveedores de servicios en la nube
Después de hablar con muchos CSV, hemos identificado varios enfoques que puede adoptar para implementar servicios para los clientes, junto con los requisitos asociados.

### <a name="csv-hosted-offering"></a>Oferta hospeda por el CSV
Si hospeda su oferta en su propia suscripción de Azure, dos son los enfoques de hospedaje más comunes: realizar una implementación distinta para cada cliente o implementar unidades de escalado que respalden una infraestructura compartida usada para todos los clientes.

* **Implementaciones distintas para cada cliente.** Las implementaciones distintas por cliente requieren topologías fijas de diferentes configuraciones conocidas. Dichas implementaciones pueden tener diferentes tamaños de máquinas virtuales, un número variable de nodos y distintas cantidades de almacenamiento asociado. El etiquetado de las implementaciones se usa en el resumen de facturación de cada cliente. RBAC puede habilitarse para permitir a los clientes acceso a aspectos de su entorno de nube.
* **Unidades de escalado en entornos multiempresa compartidos.** Una plantilla puede representar una unidad de escalado en entornos multiempresa. En este caso, se usa la misma infraestructura para respaldar a todos los clientes. Las implementaciones representan un grupo de recursos que proporcionan un nivel de capacidad para la oferta hospedada, como el número de usuarios y el número de transacciones. Estas unidades de escalado se aumentan o disminuyen según exija la demanda.

### <a name="csv-offering-injected-into-customer-subscription"></a>Oferta de CSV insertada en la suscripción del cliente
Puede que quiera implementar su software en suscripciones propiedad de los clientes finales. Puede usar plantillas para realizar distintas implementaciones en la cuenta de Azure de un cliente.

Estas implementaciones usan RBAC de modo que puede actualizar y administrar la implementación en la cuenta del cliente.

### <a name="azure-marketplace"></a>Azure Marketplace
Para anunciar y vender sus ofertas mediante un mercado, como Azure Marketplace, puede desarrollar plantillas para proporcionar distintos tipos de implementaciones que se ejecutan en la cuenta de Azure de un cliente. Estas distintas implementaciones pueden describirse normalmente como una talla de camiseta (pequeña, mediana, grande), el tipo de producto/público (comunidad, desarrollador, empresa) o el tipo de característica (básica, alta disponibilidad).  En algunos casos, estos tipos permiten especificar ciertos atributos de la implementación, como el tipo de máquina virtual o el número de discos.

## <a name="oss-projects"></a>Proyectos de software de código abierto
Dentro de los proyectos de código abierto, las plantillas del Administrador de recursos permiten a una comunidad implementar una solución rápidamente mediante prácticas de demostrada eficacia. Luego, puede almacenar las plantillas en un repositorio de GitHub para que la comunidad pueda revisarlas con el tiempo. Los usuarios implementan estas plantillas en sus propias suscripciones de Azure.

En las secciones siguientes se identifican los aspectos que debe tener en cuenta antes de diseñar la solución.

## <a name="identifying-what-is-outside-and-inside-a-vm"></a>Identificación de lo que está dentro y fuera de una máquina virtual
Al diseñar la plantilla, resulta útil examinar los requisitos en términos de lo que está fuera y dentro de las máquinas virtuales:

* Fuera significa las máquinas virtuales y otros recursos de la implementación, como la topología de red, el etiquetado, las referencias a los certificados o secretos y el control de acceso basado en rol. Todos estos recursos forman parte de la plantilla.
* Dentro significa el software instalado y la configuración del estado global deseado. Otros mecanismos, como extensiones de máquina virtual o scripts, se usan en su totalidad o en parte. La plantilla puede identificar y ejecutar estos mecanismos pero no están en ella.

Algunos ejemplos comunes de actividades que haría "dentro de la caja" serían:  

* Instalar o quitar características y roles de servidor
* Instalar y configurar software en el nivel de nodo o clúster
* Implementar sitios web en un servidor web
* Implementar esquemas de base de datos
* Administrar el Registro u otros tipos de valores de configuración
* Administrar archivos y directorios
* Iniciar, detener y administrar procesos y servicios
* Administrar cuentas de usuario y grupos locales
* Instalar y administrar paquetes (.msi, .exe, yum, etc.).
* Administrar variables de entorno
* Ejecutar scripts nativos (Windows PowerShell, bash, etc.)

### <a name="desired-state-configuration-dsc"></a>Configuración de estado deseado (DSC)
Considerando el estado interno de las máquinas virtuales más allá de la implementación, querrá asegurarse de que esta implementación no se "desvía" de la configuración que ha definido y comprobado en el control de código fuente. Este enfoque garantiza que los desarrolladores o el personal de operaciones no realizan cambios ad hoc en un entorno que no estén investigado, probando o registrando en el control de código fuente. Este control es importante porque los cambios manuales no están en el control de código fuente. Tampoco forman parte de la implementación estándar y afectarán a futuras implementaciones automatizadas del software.

Dejando a un lado los empleados internos, la configuración de estado deseado también es importante desde el punto de vista de la seguridad. Los piratas informáticos intentan periódicamente poner en peligro y aprovechar las vulnerabilidades de los sistemas de software. Cuando lo consiguen, suelen instalar archivos y cambiar de algún modo el estado de un sistema en peligro. Con la configuración de estado deseado, puede identificar diferencias entre el estado deseado y real y restaurar una configuración conocida.

Existen extensiones de recursos para los mecanismos más conocidos de DSC: DSC de PowerShell, Chef y Puppet. Cada una de estas extensiones puede implementar el estado inicial de la máquina virtual y también se puede usar para asegurarse de que se mantiene el estado deseado.

## <a name="common-template-scopes"></a>Ámbitos comunes de plantillas
En nuestra experiencia, hemos asistido al surgimiento de tres ámbitos de plantillas de soluciones principales. Estos tres ámbitos, capacidad, funcionalidad y solución completa, se describen en las siguientes secciones.

### <a name="capacity-scope"></a>Ámbito de capacidad
Un ámbito de capacidad proporciona un conjunto de recursos en una topología estándar que está preconfigurada para cumplir con las reglamentaciones y directivas. El ejemplo más común es la implementación de un entorno de desarrollo estándar en un escenario de TI empresarial o de integradores de sistemas.

### <a name="capability-scope"></a>Ámbito de funcionalidad
Un ámbito de funcionalidad se centra en la implementación y configuración de una topología para una tecnología determinada. Los escenarios comunes incluyen tecnologías como SQL Server, Cassandra y Hadoop.

### <a name="end-to-end-solution-scope"></a>Ámbito de solución completa
Un ámbito de solución completa va más allá de una funcionalidad única; se centra en la entrega de una solución completa compuesta por varias funcionalidades.  

Una plantilla con ámbito de solución se manifiesta como un conjunto de una o varias plantillas con ámbito de funcionalidad con recursos, lógica y estado deseado específicos de una solución. Un ejemplo de una plantilla con ámbito de solución es una plantilla de solución de la canalización de datos completa. La plantilla puede mezclar la topología y el estado específicos de la solución con varias plantillas de solución con ámbito de funcionalidad, como Kafka, Storm y Hadoop.

## <a name="choosing-free-form-vs-known-configurations"></a>Elección de configuraciones conocidas frente a forma libre
Es posible que inicialmente piense que una plantilla debe ofrecer a los clientes la máxima flexibilidad, pero son muchos los aspectos a tener en cuenta a la hora de decidir si usar configuraciones de forma libre o conocidas. En esta sección se identifican los requisitos clave de los clientes y las consideraciones técnicas que conforman el enfoque compartido en este documento.

### <a name="free-form-configurations"></a>Configuraciones de forma libre
A primera vista, las configuraciones de forma libre parecen idóneas. Permiten seleccionar un tipo de máquina virtual y proporcionan un número arbitrario de nodos y discos conectados para esos nodos, y lo hacen como parámetros para una plantilla. Sin embargo, este enfoque no es el ideal para algunos escenarios.

En [Sizes for Windows virtual machines in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Tamaños de máquinas virtuales Windows en Azure), se identifican los distintos tipos y tamaños de máquina virtual disponibles, y cada número de discos durables (2, 4, 8, 16 o 32) que se pueden conectar. Cada disco conectado proporciona 500 IOPS, y se pueden agrupar múltiplos de estos discos por un multiplicador de ese número de IOPS. Por ejemplo, se pueden agrupar 16 discos para proporcionar 8000 IOPS. La agrupación se realiza con la configuración del sistema operativo, usando espacios de almacenamiento de Microsoft Windows o una matriz redundante de discos independientes (RAID) en Linux.

Una configuración de forma libre permite la selección de varias instancias de máquina virtual, diferentes tipos y tamaños de VM para esas instancias, varios discos para el tipo de máquina virtual y uno o más scripts para configurar el contenido de la máquina virtual.

Es frecuente que una implementación pueda tener varios tipos de nodos, como nodos maestros y de datos, por lo que esta flexibilidad se proporciona a menudo para cada tipo de nodo.

Conforme empiece a implementar clústeres de cierta importancia, comenzará a trabajar con estos escenarios complejos. Si fuera a implementara un clúster de Hadoop, por ejemplo, con 8 nodos maestros y 200 nodos de datos y agrupara 4 discos conectados en cada nodo principal y 16 discos conectados por nodo de datos, habría 208 máquinas virtuales y 3232 discos para administrar.

Una cuenta de almacenamiento limita las solicitudes anteriores por encima de su límite identificado de 20 000 transacciones por segundo, por lo que debe examinar el particionamiento de la cuenta de almacenamiento y usar cálculos para determinar el número adecuado de cuentas de almacenamiento que hacen posible esta topología. Dada la gran variedad de combinaciones que admite el enfoque de forma libre, se requieren cálculos dinámicos para determinar el particionamiento adecuado. El lenguaje de plantillas del Administrador de recursos de Azure no proporciona actualmente funciones matemáticas, por lo que debe realizar estos cálculos en el código y generar una plantilla única codificada de forma rígida con los detalles correspondientes.

En escenarios de integradores de sistemas y tecnología de la información, alguien debe mantener las plantillas y proporcionar soporte para las topologías implementadas en una o varias organizaciones. Esta sobrecarga adicional, configuraciones y plantillas diferentes para cada cliente, está lejos de ser deseable.

Puede usar estas plantillas para implementar los entornos en la suscripción de Azure de sus clientes, pero tanto los equipos de TI corporativos como los CSV suelen implementarlos en sus propias suscripciones y usan una función de contracargo para facturar a sus clientes. En estos casos, el objetivo es implementar capacidad para varios clientes en un grupo de suscripciones y mantener las implementaciones densamente pobladas en las suscripciones a fin de minimizar la proliferación de estas; es decir, más suscripciones que administrar. Con tamaños de implementación verdaderamente dinámicos, conseguir este tipo de densidad requiere una cuidadosa planeación y el desarrollo adicional del trabajo de scaffolding en nombre de la organización.

Además, no puede crear suscripciones mediante una llamada API, sino que deberá hacerlo manualmente mediante el portal. A medida que aumenta el número de suscripciones, cualquier expansión resultante precisará de intervención humana, no se puede automatizar. Con tanta variabilidad en el tamaño de las implementaciones, tendrá que aprovisionar previamente una serie de suscripciones manualmente para asegurarse de que estén disponibles.

Teniendo en cuenta todos estos factores, una configuración verdaderamente de forma libre es menos atractiva que a primera vista.

### <a name="known-configurations--the-t-shirt-sizing-approach"></a>Configuraciones conocidas: el enfoque del tamaño de camiseta
En lugar de ofrecer una plantilla que proporcione flexibilidad total e incontables variaciones, un patrón común según nuestra experiencia es proporcionar la posibilidad de seleccionar configuraciones conocidas, de hecho, tamaños de camiseta estándar como pequeña, mediana y grande. Otros ejemplos de tamaños de camiseta son ofertas de productos, como edición para la comunidad o edición para impresa.  En otros casos, pueden ser configuraciones de una tecnología específicas de la carga de trabajo, como MapReduce o NoSQL.

Muchas organizaciones de TI corporativa, proveedores de software de código abierto e integradores de sistemas permiten que sus ofertas estén disponibles de esta manera en entornos virtualizados locales (empresas) o como ofertas de software como servicio (SaaS) (CSV y OSV).

Este enfoque proporciona configuraciones bien conocidas de tamaños variables que están preconfiguradas para los clientes. Sin configuraciones conocidas, los clientes finales deben determinar el tamaño del clúster por su cuenta, tener en cuenta las restricciones de recursos de plataforma y realizar cálculos matemáticos para identificar el particionamiento resultante de las cuentas de almacenamiento y otros recursos (debido a restricciones en el tamaño del clúster y los recursos). Las configuraciones conocidas permiten a los clientes seleccionar fácilmente el tamaño correcto de camiseta, es decir, una implementación determinada. Además de crear una experiencia mejor para el cliente, un número pequeño de configuraciones conocidas es más fácil de mantener y puede ayudarle a ofrecer un nivel mayor de densidad.

Un enfoque de configuración conocida centrado en los tamaños de camiseta también puede tener un número variable de nodos dentro de un mismo tamaño. Por ejemplo, un tamaño de camiseta pequeña puede estar entre 3 y 10 nodos.  El tamaño de camiseta estaría diseñado para alojar hasta 10 nodos y proporcionaría al cliente la posibilidad de realizar selecciones de forma libre hasta el tamaño máximo identificado.  

Un tamaño de camiseta basado en el tipo de carga de trabajo puede tener más una naturaleza de forma libre en lo relativo al número de nodos que se pueden implementar, pero tendrá un tamaño de nodo distinto de carga de trabajo y la configuración del software en el nodo.

Las ofertas de productos basadas en los tamaños de camiseta, como Comunidad o Empresa, pueden tener tipos de recursos distintos y un número máximo de nodos que se pueden implementar, lo que va asociado normalmente a las consideraciones de licencia o la disponibilidad de características en las diferentes ofertas.

También puede adaptarse a los clientes con variantes únicas usando plantillas basadas en JSON. Cuando se trabaja con clientes atípicos, puede incorporar la planeación adecuada y las consideraciones sobre desarrollo, soporte técnico y costos.

En función de los escenarios de consumo de plantillas del cliente y de los requisitos identificados al principio de este documento, identificamos un patrón para la descomposición de plantillas.

## <a name="capacity-and-capability-scoped-solution-templates"></a>Plantillas de soluciones con ámbito de capacidad y de funcionalidad
La descomposición proporciona un enfoque modular hacia el desarrollo de plantillas que admite la reutilización, la extensibilidad, la prueba y el uso de herramientas. En esta sección se proporcionan detalles sobre cómo se puede aplicar un enfoque de descomposición a plantillas con un ámbito de capacidad o de funcionalidad.

En este enfoque, una plantilla principal recibe los valores de parámetro de un consumidor de plantillas y luego se vincula a varios tipos de plantillas y scripts de nivel inferior tal y como se muestra a continuación. Se usan parámetros, variables estáticas y variables generadas para proporcionar valores dentro y fuera de las plantillas vinculadas.

![Parámetros de plantilla](./media/best-practices-resource-manager-design-templates/template-parameters.png)

**Los parámetros se pasan a una plantilla principal y luego a plantillas vinculadas**

Las secciones siguientes se centran en los tipos de plantillas y scripts que se descomponen en una sola plantilla. Las secciones presentan enfoques para pasar información de estado entre las plantillas. Cada plantilla y los tipos de scripts de la imagen se describen junto con ejemplos. Para obtener un ejemplo contextual, vea "Integración: una implementación de ejemplo" más adelante en este documento.

### <a name="template-metadata"></a>Metadatos de plantilla
Los metadatos de plantilla (archivo metadata.json) contienen los pares de clave y valor que describen una plantilla en JSON, que los seres humanos y los sistemas de software pueden leer.

![Metadatos de plantilla](./media/best-practices-resource-manager-design-templates/template-metadata.png)

**Los metadatos de plantilla se describen en el archivo metadata.json**

Los agentes de software pueden recuperar el archivo metadata.json y publicar la información y un vínculo a la plantilla en una página o un directorio web. Los elementos incluyen *itemDisplayName*, *description*, *summary*, *githubUsername* y *dateUpdated*.

A continuación, se muestra un archivo de ejemplo en su totalidad.

    {
        "itemDisplayName": "PostgreSQL 9.3 on Ubuntu VMs",
        "description": "This template creates a PostgreSQL streaming-replication between a master and one or more slave servers each with 2 striped data disks. The database servers are deployed into a private-only subnet with one publicly accessible jumpbox VM in a DMZ subnet with public IP.",
        "summary": "PostgreSQL stream-replication with multiple slave servers and a publicly accessible jumpbox VM",
        "githubUsername": "arsenvlad",
        "dateUpdated": "2015-04-24"
    }

### <a name="main-template"></a>Plantilla principal
La plantilla principal recibe parámetros de un usuario, usa esa información para rellenar las variables de objetos complejos y ejecuta las plantillas vinculadas.

![Plantilla principal](./media/best-practices-resource-manager-design-templates/main-template.png)

**La plantilla principal recibe parámetros de un usuario**

Un parámetro proporcionado es un tipo de configuración conocida, también conocido como parámetro de tamaño de camiseta debido a sus valores estándar: pequeño, mediano o grande. En la práctica, puede usar este parámetro de varias formas. Para obtener más información, vea "Plantilla de recursos de configuración conocida" más adelante en este documento.

Algunos recursos se implementan sin tener en cuenta la configuración conocida especificada por un parámetro de usuario. Estos recursos se aprovisionan con una plantilla de recursos compartidos únicos y otras plantillas los comparten, por lo que la plantilla de recursos compartidos se ejecuta primero.

Algunos recursos se implementan opcionalmente, sin tener en cuenta la configuración conocida especificada.

### <a name="shared-resources-template"></a>Plantilla de recursos compartidos
Esta plantilla proporciona los recursos que son comunes a todas las configuraciones conocidas. Contiene la red virtual, los conjuntos de disponibilidad y otros recursos que son necesarios, independientemente de la plantilla de configuración conocida que se implemente.

![Recursos de plantilla](./media/best-practices-resource-manager-design-templates/template-resources.png)

**Plantilla de recursos compartidos**

Los nombres de los recursos, como el nombre de red virtual, se basan en la plantilla principal. Puede especificarlos como una variable dentro de esa plantilla o recibirlos como un parámetro del usuario, según requiera su organización.

### <a name="optional-resources-template"></a>Plantilla de recursos opcionales
La plantilla de recursos opcionales contiene los recursos que se implementan mediante programación en función del valor de un parámetro o de una variable.

![Recursos opcionales](./media/best-practices-resource-manager-design-templates/optional-resources.png)

**Plantilla de recursos opcionales**

Por ejemplo, puede usar una plantilla de recursos opcionales para configurar un Jumpbox que permita el acceso indirecto a un entorno implementado desde la Internet pública. Usaría un parámetro o una variable para determinar si debe habilitarse el Jumpbox y la función *concat* para crear el nombre de destino de la plantilla, como *jumpbox_enabled.json*. La vinculación de plantillas usaría la variable resultante para instalar el Jumpbox.

Puede vincular la plantilla de recursos opcionales desde varios lugares:

* Cuando sea aplicable para cada implementación, cree un vínculo controlado por parámetro de la plantilla de recursos compartidos.
* Cuando sea aplicable para seleccionar configuraciones conocidas, por ejemplo, para instalar en implementaciones de gran envergadura, cree un vínculo controlado por variable o controlado por parámetro a partir de la plantilla de configuración conocida.

Si un recurso determinado es opcional, podría no ser controlado por el consumidor de la plantilla, sino por el proveedor de la misma. Por ejemplo, puede que necesite satisfacer un determinado requisito de producto o complemento de producto (común para los CSV) o aplicar directivas (común para los SI y grupos de TI empresariales). En estos casos, puede usar una variable para identificar si el recurso que debe implementarse.

### <a name="known-configuration-resources-template"></a>Plantilla de recursos de configuración conocida
En la plantilla principal, se puede exponer un parámetro para permitir que el consumidor de la plantilla especifique una configuración conocida que desee implementar. A menudo, esta configuración conocida usa un enfoque de tamaño de camiseta, con un conjunto de tamaños de configuración fijos, como espacio aislado, pequeña, mediana y grande.

![Recursos de configuración conocida](./media/best-practices-resource-manager-design-templates/known-config.png)

**Plantilla de recursos de configuración conocida**

Normalmente se usa el enfoque de tamaño de camiseta, pero los parámetros pueden representar cualquier conjunto de configuraciones conocidas. Por ejemplo, puede especificar un conjunto de entornos para una aplicación empresarial, como Desarrollo, Prueba y Producto. O bien, puede usarlo en un servicio en la nube para representar unidades de escalado, versiones o configuraciones de producto diferentes como Comunidad, Desarrollador o Empresa.

Al igual que con la plantilla de recursos compartidos, las variables se pasan a la plantilla de configuraciones conocidas desde:

* Un usuario final, es decir, los parámetros enviados a la plantilla principal.
* Una organización, es decir, las variables de la plantilla principal que representan los requisitos o directivas internos.

### <a name="member-resources-template"></a>Plantilla de recursos de miembros
Dentro de una configuración conocida, a menudo se incluyen uno o más tipos de nodos de miembros. Por ejemplo, con Hadoop tiene nodos maestros y de datos. Si instala MongoDB, tiene nodos de datos y un árbitro. Si implementa DataStax, tiene nodos de datos, así como una máquina virtual con OpsCenter instalado.

![Recursos de miembros](./media/best-practices-resource-manager-design-templates/member-resources.png)

**Plantilla de recursos de miembros**

Cada tipo de nodo puede tener distintos tamaños de máquinas virtuales, números de discos conectados, scripts para instalar y configurar los nodos, configuraciones de puerto para las máquinas virtuales, número de instancias y otros detalles. De modo que cada tipo de nodo obtiene su propia plantilla de recursos de miembros, que contiene los detalles para implementar y configurar una infraestructura, así como para ejecutar scripts para implementar y configurar el software en la máquina virtual.

Para las máquinas virtuales se usan normalmente dos tipos de scripts: scripts ampliamente reutilizables y scripts personalizados.

### <a name="widely-reusable-scripts"></a>Scripts ampliamente reutilizables
Los scripts ampliamente reutilizables se pueden usar en varios tipos de plantillas. En uno de los mejores ejemplos de estos scripts ampliamente reutilizables, se configura RAID en Linux para agrupar los discos y obtener un número mayor de IOPS. Sin tener en cuenta el software que se instala en la máquina virtual, este script proporciona la reutilización de prácticas de demostrada eficacia en escenarios comunes.

![Scripts reutilizables](./media/best-practices-resource-manager-design-templates/reusable-scripts.png)

**Las plantillas de recursos de miembros pueden llamar a los scripts ampliamente reutilizables**

### <a name="custom-scripts"></a>Scripts personalizados
Las plantillas llaman habitualmente a uno o varios scripts que instalan y configuran software en las máquinas virtuales. Se considera un patrón común con topologías grandes donde se implementan varias instancias de uno o varios tipos de miembros. Se inicia un script de instalación para cada máquina virtual que se puede ejecutar en paralelo, seguido de un script de configuración al que se llama después de que se implementan todas las máquinas virtuales (o todas las máquinas virtuales de un tipo de miembro especificado).

![Scripts personalizados](./media/best-practices-resource-manager-design-templates/custom-scripts.png)

**Las plantillas de recursos de miembros pueden llamar a scripts de fin específico, como la configuración de una máquina virtual**

## <a name="capability-scoped-solution-template-example---redis"></a>Ejemplo de plantilla de solución con ámbito de funcionalidad: Redis
Para mostrar cómo podría funcionar una implementación, echemos un vistazo a un ejemplo práctico de creación de una plantilla que facilita la implementación y configuración de Redis en tamaños de camiseta estándar.  

Para la implementación, hay un conjunto de recursos opcionales (red virtual, cuenta de almacenamiento, conjuntos de disponibilidad) y un recurso opcional (Jumpbox). Hay varias configuraciones conocidas representadas como tamaños de camiseta (pequeña, mediana, grande), pero cada una con un único tipo de nodo. También hay dos scripts de fin específico (instalación, configuración).

### <a name="creating-the-template-files"></a>Creación de los archivos de plantilla
Crea una plantilla principal denominada azuredeploy.json.

Crea una plantilla de recursos compartidos denominada resources.json.

Crea una plantilla de recursos opcionales para permitir la implementación de un Jumpbox, denominada jumpbox_enabled.json.

Redis usa un solo tipo de nodo, así que crea una sola plantilla de recursos de miembros denominada node-resources.json.

Con Redis, desea instalar cada nodo individual y, a continuación, configurar el clúster.  Dispone de scripts para la instalación y la configuración: redis-cluster-install.sh y redis-cluster-setup.sh.

### <a name="linking-the-templates"></a>Vinculación de las plantillas
Mediante la vinculación de plantillas, la plantilla principal se vincula con la plantilla de recursos compartidos, que establece la red virtual.

La lógica se agrega en la plantilla principal para permitir que los consumidores de la plantilla especifiquen si se debe implementar JumpBox. Un valor *habilitado* para el parámetro *EnableJumpbox* indica que el cliente desea implementar un Jumpbox. Cuando se proporciona este valor, la plantilla concatena *_enabled* como sufijo de un nombre de plantilla base para la funcionalidad de JumpBox.

La plantilla principal aplica el valor del parámetro *large* como sufijo a un nombre de plantilla base de tamaños de camiseta y luego usa ese valor en un vínculo de plantilla a *technology_on_os_large.json*.

La topología sería similar a esta ilustración.

![Plantilla de Redis](./media/best-practices-resource-manager-design-templates/redis-template.png)

**Estructura de plantilla para una plantilla de Redis**

### <a name="configuring-state"></a>Estado de configuración
Hay dos pasos para configurar el estado en los nodos del clúster, representados ambos por scripts de fin específico.  "redis-cluster-install.sh" instala Redis y "redis-cluster-setup.sh" configura el clúster.

### <a name="supporting-different-size-deployments"></a>Soporte de implementaciones de diferentes tamaños
Dentro de las variables, la plantilla de tamaño de camiseta especifica el número de nodos de cada tipo que se implementa para el tamaño especificado (*grande*). A continuación, implementa ese número de instancias de máquina virtual mediante bucles de recursos, proporcionando nombres únicos a los recursos mediante la anexión de un nombre de nodo con un número de secuencia numérica de *copyIndex()*. Lleva a cabo estos pasos para las máquinas virtuales de las zonas activa y semiactiva, según se define en la plantilla de nombre de camiseta.

## <a name="decomposition-and-end-to-end-solution-scoped-templates"></a>Descomposición y plantillas con ámbito de solución completa
Una plantilla de solución con ámbito de solución completa se centra en proporcionar una solución completa.  Normalmente, este enfoque es una composición de varias plantillas con ámbito de funcionalidad con recursos, lógica y estado adicionales.

Como se resalta en la imagen siguiente, el mismo modelo usado para las plantillas con ámbito de funcionalidad se extiende a las plantillas con un ámbito de solución completa.

Las plantilla de recursos compartidos y las plantillas de recursos opcionales tienen la misma función que en los enfoques de plantillas con ámbito de capacidad y de funcionalidad, pero en el ámbito de la solución completa.

Como las plantillas con ámbito de solución completa también pueden tener normalmente tamaños de camiseta, la plantilla de recursos de configuración conocida refleja lo que se requiere para una configuración conocida dada de la solución.

La plantilla de recursos de configuración conocida se vincula a una o varias plantillas de solución con ámbito de funcionalidad que sean pertinentes para la solución completa y con las plantillas de recursos de miembros que sean necesarias para ella.

Como el tamaño de camiseta de la solución puede ser diferente al de la plantilla individual con ámbito de funcionalidad, se usan variables dentro de la Plantilla de recursos de configuración conocida para proporcionar los valores adecuados para las plantillas de solución con ámbito de funcionalidad de nivel inferior a fin de implementar el tamaño de camiseta adecuado.

![Completa](./media/best-practices-resource-manager-design-templates/end-to-end.png)

**El modelo usado para las plantillas de solución con ámbito de capacidad o de funcionalidad se puede extender fácilmente a los ámbitos de plantilla de solución completa**

## <a name="preparing-templates-for-the-marketplace"></a>Preparación de las plantillas para Marketplace
El enfoque anterior se adapta fácilmente a escenarios donde las empresas, los integradores de sistemas y los proveedores de soluciones en la nube desean implementar las plantillas ellos mismos o permitir que sus clientes las implementen por su cuenta.

Otro escenario deseado es la implementación de una plantilla mediante Marketplace.  Este enfoque de descomposición funciona también para Marketplace, con algunos cambios menores.

Como se mencionó anteriormente, las plantillas se pueden usar para ofrecer distintos tipos de implementación para la venta en Marketplace. Algunos tipos de implementaciones diferentes pueden ser tamaños de camiseta (pequeña, mediana, grande), tipo de producto/público (comunidad, desarrollador, empresa) o tipo de característica (básica, alta disponibilidad).

Como se muestra a continuación, las plantillas con ámbito de funcionalidad o de solución completa se pueden usar fácilmente para mostrar las diferentes configuraciones conocidas en Marketplace.

Primero se modifican los parámetros de la plantilla principal para quitar el parámetro de entrada denominado tshirtSize.

Aunque los distintos tipos de implementaciones se asignan a la plantilla de recursos de configuración conocida, también necesitan los recursos comunes y la configuración encontrada en la plantilla de recursos compartidos y, posiblemente, los de las plantillas de recursos opcionales.

Si desea publicar la plantilla en Marketplace, establezca distintas copias de la plantilla principal que reemplace el parámetro de entrada anteriormente disponible de tshirtSize por una variable incrustada dentro de la plantilla.

![Marketplace](./media/best-practices-resource-manager-design-templates/marketplace.png)

**Adaptación de una plantilla con ámbito de solución para Marketplace**

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre cómo compartir el estado dentro y fuera de las plantillas, consulte [Uso compartido del estado en las plantillas del Administrador de recursos de Azure](best-practices-resource-manager-state.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

