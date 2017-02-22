---
title: Cumplimiento de Azure Government | Microsoft Docs
description: "Proporciona información general de los servicios de cumplimiento disponibles en Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: jomolesk
manager: zakramer
ms.assetid: 1d2e0938-482f-4f43-bdf6-0a5da2e9a185
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/07/2016
ms.author: jomolesk
translationtype: Human Translation
ms.sourcegitcommit: 474ee83dfc8532f8410b79c6426af3cad6f3d320
ms.openlocfilehash: cd54456c8f9e0f37f53973fca80b8bd3514b62d1


---
# <a name="azure-government-compliance"></a>Cumplimiento de Azure Government

## <a name="azure-blueprint"></a>Azure Blueprint

El programa Azure Blueprint está diseñado para facilitar el uso seguro y compatible de Azure en organismos gubernamentales y proveedores de terceros que trabajan en nombre del gobierno. Los clientes de Azure Government pueden sacar provecho de la Autorización provisional para operar (P-ATO) de JAB de FedRAMP o de la Autorización provisional de DoD (PA), lo que reduce el ámbito de los controles de seguridad de responsabilidad del cliente en sistemas basados en Azure. La herencia de las implementaciones de control de la seguridad de Azure Government permite a los clientes centrarse en implementaciones de control específicas de sus entornos de IaaS, PaaS o SaaS integrados en Azure.

> [!NOTE]
> En el contexto de Azure Blueprint, el "cliente" hace referencia a la organización que compila directamente en Azure. Los clientes de Azure pueden incluir la compilación de ISV de terceros en nombre del gobierno o de los organismos gubernamentales que compilan directamente en Azure.

## <a name="blueprint-customer-responsibilities-matrix"></a>Matriz de responsabilidades del cliente de Blueprint

La matriz de responsabilidades del cliente (CRM) de Azure Blueprint está diseñada para ayudar a los clientes de Azure Government a implementar y documentar los controles de seguridad específicos del sistema implementados en Azure. El CRM enumera explícitamente todos los requisitos de control de seguridad de [NIST SP 800-53](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r4.pdf) para las líneas de base de FedRAMP y DISA que incluyen un requisito de implementación de clientes. Aquí se incluyen los controles con una responsabilidad compartida entre Azure y los clientes de Azure, así como los controles que deben implementar completamente los clientes de Azure. En su caso, los controles se definen en una granularidad de requisito secundario de control para proporcionar instrucciones más específicas.

El formato de CRM está diseñado para favorecer su utilidad y contribuye a la documentación de solo las partes del cliente de los controles de seguridad implementados.

Por ejemplo, el control AC-1 requiere directivas y procedimientos de control de acceso documentados para el sistema que busca una ATO. Para este control, Microsoft cuenta con directivas y procedimientos específicos de Azure relacionados con los mecanismos de control de acceso usados para administrar la infraestructura y la plataforma de Azure. Los clientes también deben crear sus propios procedimientos y directivas de control de acceso usados en su sistema específico creado en Azure. Los documentos de la CRM controlan las partes AC-1a, que requieren que los procedimientos y directivas incluyan contenido específico, así como AC-1b, que requiere que los clientes revisen y actualicen dichos documentos anualmente.

El CRM de Blueprint está disponible en forma de libro de Microsoft Excel para las bases de referencia altas y moderadas de FedRAMP y las bases de referencia L4 y L5 SRG de informática en la nube de DISA.

Para solicitar una copia del CRM de Azure Blueprint o para aportar comentarios, envíe un correo electrónico a [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

## <a name="blueprint-system-security-plan"></a>Plan de seguridad de sistema BluePrint

La plantilla del plan de seguridad del sistema (SSP) Azure Blueprint se centra en el cliente y se ha diseñado para que se use en el desarrollo de un SSP que documente tanto las implementaciones del control de seguridad de los clientes así como los controles heredados de Azure. Los controles que incluyen una responsabilidad del cliente contienen instrucciones para la documentación de la implementación de controles con una respuestas exhaustiva y compatible. Las secciones de la herencia de Azure documentan la forma en que Azure implementa los controles de seguridad en nombre del cliente.

El SSP de Azure Blueprint está disponible para las bases de referencia altas y moderadas de FedRAMP y las bases de referencia L4 y L5 SRG de informática en la nube de DISA. 

Para solicitar una copia del SSP de Azure Blueprint o para aportar comentarios, envíe un correo electrónico a [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

## <a name="azure-blueprint-implementation-guidance"></a>Instrucciones para la implementación de Azure Blueprint

Las instrucciones para la implementación de Azure Blueprint están diseñadas para ayudar a los arquitectos de soluciones en la nube y al personal de seguridad a conocer cómo se pueden implementar las características y servicios de Azure Government para implementar un subconjunto de los controles de seguridad de FedRAMP y DoD de responsabilidad del cliente. Para guiar la implementación segura de características y servicios de Azure, está disponible una matriz de documentación, herramientas, plantillas y otros recursos. Los recursos de Azure se pueden implementar mediante los [bloques de creación](https://github.com/mspnp/template-building-blocks) de la plantilla de Azure Resource Manager, las [plantillas de inicio rápido](https://azure.microsoft.com/resources/templates/) de Azure a las que ha contribuido la comunidad o mediante el uso de plantillas de Resource Manager basadas en JSON [creadas por el cliente](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). La arquitectura de una implementación básica de Azure incluye recursos de proceso, de red y de almacenamiento. En esta guía de implementación se aborda cómo se pueden implementar estos recursos de distintas formas que ayudan a cumplir los requisitos de implementación de controles de seguridad.

### <a name="virtual-machines"></a>Virtual Machines

Las plantillas de Azure Resource Manager se pueden usar para implementar máquinas virtuales preconfiguradas que cumplen las guías de implementación técnica de seguridad (STIG) del Gobierno de Estados Unidos y del sector y superan los bancos de pruebas de seguridad. Las máquinas virtuales de Azure personalizadas se pueden crear con una máquina preconfigurada existente, o bien se puede implementar una nueva máquina virtual y aplicar una directiva de seguridad con Active Directory, en el caso de los equipos unidos a un dominio, o la utilidad Local Group Policy Object en el caso de las máquinas independientes. La extensión de script personalizado de la máquina virtual de Azure se puede usar para administrar estas tareas de configuración posteriores a la implementación.

La configuración de las máquinas virtual Windows puede incluir implementaciones de controles de seguridad para:

- Notificación y confirmación de uso del sistema [Control AC-8 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AC-8: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se configura de acuerdo con el banco de pruebas CIS para Windows Server 2012 R2 y se personaliza de forma que cumpla los requisitos de la organización, incluido el uso de una notificación de uso del sistema adecuada, que los usuarios deben confirmar que los usuarios antes de inicio de sesión (consulte las secciones 2.3.7.4 y 2.3.7.5 del banco de pruebas CIS).*

- Restricciones de la cuenta de la máquina local incluido el bloqueo de cuentas [control AC-7 de NIST SP 800-53], el control de sesiones concurrentes [control AC-10 de NIST SP 800-53], el bloqueo de sesión [control AC-11 de NIST SP 800-53], la administración del autenticador [control IA-5 de NIST SP 800-53], etc.

  > Instrucción de implementación del control de ejemplo AC-7: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se configura de acuerdo con el banco de pruebas CIS para Windows Server 2012 R2 y se personaliza de forma que cumpla los requisitos de la organización, incluido el inicio de un bloqueo de cuenta después de varios intentos consecutivos de inicio de sesión infructuosos (consulte las secciones 1.2.1, 1.2.2 y 1.2.3 del banco de pruebas CIS; nota: los valores predeterminados del banco de pruebas CIS deben cambiarse para cumplir los requisitos de parámetros de FedRAMP y DoD).*

  > Instrucción de implementación del control de ejemplo AC-10: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se configura de acuerdo con el banco de pruebas CIS para Windows Server 2012 R2 y se personaliza de forma que cumpla los requisitos de la organización, incluida la restricción de los usuarios a una sola sesión de servicios de escritorio remoto, que cumple los requisitos de FedRAMP y DoD (consulte la sección 18.9.48.3.2 del banco de pruebas CIS).*

  > Instrucción de implementación del control de ejemplo AC-11: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se configura de acuerdo con el banco de pruebas CIS para Windows Server 2012 R2 y se personaliza de forma que cumpla los requisitos de la organización, incluido el inicio de un bloqueo de sesión después de 900 segundos (15 minutos) de inactividad que se conserva hasta que el usuario se vuelve a autenticar (consulte las secciones 2.3.7.3, 19.1.3.1 y 19.1.3.3 del banco de pruebas CIS).*

  > Instrucción de implementación del control de ejemplo IA-5: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se configura de acuerdo con el banco de pruebas CIS para Windows Server 2012 R2 y se personaliza para que cumpla los requisitos de la organización, incluidas las restricciones sobre las contraseñas de cuentas locales que exigen restricciones de una vigencia mínima (1 día) y máxima (60 días), condiciones de reutilización (24 contraseñas), longitud (14 caracteres) y requisitos de complejidad, que cumplen los requisitos de FedRAMP (consulte las secciones 1.1.1, 1.1.2, 1.1.3, 1.1.4 y 1.1.5 del banco de pruebas CIS; nota: los valores predeterminados del banco de pruebas CIS deben cambiarse para cumplir los requisitos de parámetros de DoD).*

- Opciones de configuración, incluida la funcionalidad mínima [controles CM-6 y 7 CM de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo CM-6: *un objeto de directiva de grupo (GPO) controlado por la configuración se mantiene en todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se configura de acuerdo con el banco de pruebas CIS para Windows Server 2012 R2 y se personaliza de forma que cumpla los requisitos de la organización específicos para las necesidades operativas del sistema. Este objeto de directiva de grupo se establece, además de otros recursos de configuración, para documentar y asegurar una implementación coherente de las opciones de configuración.*

  > Instrucción de implementación del control de ejemplo CM-7: *un objeto de directiva de grupo (GPO) controlado por la configuración se mantiene en todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se configura de acuerdo con el banco de pruebas CIS para Windows Server 2012 R2 y se personaliza de forma que cumpla los requisitos de la organización específicos para las necesidades operativas del sistema. El objeto de directiva de grupo se utiliza para establecer una imagen de máquina virtual de base de referencia, que se mantiene en todas las máquinas Windows de Azure y refleja la configuración de las máquinas Windows para proporcionar solo las funcionalidades fundamentales para el funcionamiento del sistema.*

  Herramientas de corrección de base de referencia de seguridad, que incluyen objetos de directiva de grupo (GPO) preconfigurados para máquinas Windows y scripts de shell, para máquinas Linux, están disponibles en [Microsoft](https://technet.microsoft.com/itpro/windows/keep-secure/windows-security-baselines), para máquinas de Windows y [Center for Internet Security](https://benchmarks.cisecurity.org/) y [Defense Information Systems Agency (DISA)](http://iase.disa.mil/stigs), para máquinas Windows y Linux.  

Máquinas virtuales de Azure se pueden cifrar para cumplir los requisitos de control de seguridad para proteger la información en reposo [control SC-28 de NIST SP 800-53]. Las plantillas de inicio rápido de Azure se pueden usar para implementar el cifrado tanto en máquinas virtuales [nuevas](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) y [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) como en las existentes. 

> Instrucción de implementación de control SC-28: * las máquinas virtuales controladas por el cliente de Azure implementan el cifrado de disco para proteger la confidencialidad e integridad de la información en reposo. Azure Disk Encryption para Windows se implementa mediante la característica BitLocker de Windows; el cifrado del disco para máquinas virtuales Linux se implementa mediante la característica de DM Crypt de Linux.

Se pueden implementar varias extensiones de máquina virtual de Azure para cumplir los requisitos de control de seguridad. La [extensión de máquina virtual Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) se puede implementar tanto en las máquinas virtuales nuevas como en las existentes. La extensión Antimalware puede habilitar la protección en tiempo real, así como exámenes programados periódicos [control SI-3 de NIST SP 800-53].

> Instrucción de implementación de control SI-3: *las protecciones antimalware basadas en host para máquinas virtuales Windows controladas por el cliente en Azure se implementan mediante la extensión de máquina virtual Microsoft Antimalware. Esta extensión se configura para realizar exámenes periódicos (semanalmente) y en tiempo real, actualizar automáticamente tanto el motor antimalware como las firmas de protección, realizar acciones de corrección automática y proporcionar notificaciones a través de Azure Diagnostics.*

*Recursos y herramientas adicionales*

[Bloques de creación](https://github.com/mspnp/template-building-blocks) de la plantilla de Azure Resource Manager que se pueden personalizar y usar para implementar recursos de Azure, incluida la implementación de [máquinas virtuales](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/multi-vm-n-nic-m-storage) y de [extensiones de máquinas virtuales](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/virtualMachine-extensions). 

### <a name="virtual-network"></a>Virtual Network

Azure Virtual Network (VNet) permite tanto el control total de las directivas de seguridad como el enrutamiento en las arquitecturas de red virtual a través de la implementación y configuración de subredes, grupos de seguridad de red y rutas definidas por el usuario. Se pueden aplicar grupos de seguridad de red a subredes o equipos individuales, separando lógicamente los recursos por carga de trabajo, con una arquitectura de niveles múltiples o con cualquier otro fin. En la arquitectura de referencia siguiente, los recursos se agrupan en subredes independientes para las capas web, empresarial y de datos, y en subredes para los recursos y la administración de Active Directory. Los grupos de seguridad de red se aplican a cada subred para restringir el tráfico de red en la red virtual. 
 
![Arquitectura de n niveles con Microsoft Azure](./media/documentation-government-plan-compliance/compute-n-tier.png)

Los grupos de seguridad de red permiten tener un control total de las rutas de comunicación entre los recursos [control AC-4 de NIST SP 800-53].

> Instrucción de implementación del control de ejemplo AC-4: *las máquinas virtuales controladas por cliente de Azure que implementan funciones de las capas web, empresarial y de datos se segregan por subred. En casa subred se definen y aplican grupos de seguridad de red que restringen el flujo de información en el nivel de red al mínimo necesario para la funcionalidad del sistema de información.*

Los grupos de seguridad de red se pueden aplicar a las comunicaciones salientes de las subredes y máquinas virtuales. Esto permite tener un control total sobre la comunicación que existe entre los componentes del sistema de información en sistemas de información externos y de Azure [Control CA-3 (5) de NIST SP 800-53]. El procesamiento de reglas de los grupos de seguridad de red se implementa como una función de permisos por excepción y denegar todo. Además, las rutas definidas por el usuario se pueden configurar para que enruten tanto las comunicaciones entrantes como las salientes de subredes y máquinas virtuales específicas a través de una aplicación virtual, como un firewall o IDS/IPS, lo que mejora la administración de las comunicaciones del sistema.

> Instrucción de implementación del control de ejemplo CA-3 (5): *todas las comunicaciones de salida cuyo origen sean recursos controlados por el cliente de Azure se restringen mediante la implementación de grupos de seguridad de red, que se configuran con un conjunto de reglas de salida que deniega todo el tráfico, excepto el que se permita explícitamente para dar soporte a la funcionalidad del sistema de información, que cumple los requisitos de FedRAMP y DoD L4. Además, las rutas definidas por el usuario se configuran para enrutar todo el tráfico de salida a través de una aplicación de firewall virtual, que está configurado con un conjunto de reglas que permite la comunicación exclusivamente con sistemas de información externos de aprobados.*

La arquitectura de referencia anterior muestra la forma en que los recursos de Azure pueden agruparse lógicamente en subredes independientes a las que se aplican conjuntos de reglas de grupos de seguridad de red para garantizar que se aíslan tanto las funciones de seguridad como las funciones que no son de seguridad. En este caso, las tres capas de las aplicaciones web se aíslan tanto de la subred de Active Directory como de la de administración, que pueden hospedar herramientas y recursos del sistema de información y de la administración de seguridad [control SC-3 de NIST SP 800-53].

> Instrucción de implementación del control de ejemplo SC-3: *las funciones de seguridad se aíslan de las funciones que no son de seguridad en el entorno de Azure controlado por el cliente a través de la implementación de las subredes y los grupos de seguridad que se aplican a estas subredes. Los recursos del sistema de información dedicados a las capas de web, empresarial y de datos de la aplicación web están separadas lógicamente de la subred de administración, donde se realizan las tareas relacionadas con la seguridad del sistema de información.*

La arquitectura de referencia también implementa los puntos de control de acceso administrados para el acceso remoto al sistema de información [control AC-17 (3) NIST SP 800-53]. Se implementa un equilibrador de carga con conexión a Internet para distribuir el tráfico entrante de Internet a la aplicación web y la subred de administración incluye un jumpbox, o bastion host, a través del cual se controla todo el acceso remoto relacionado con la administración al sistema. Los grupos de seguridad de red restringen el tráfico dentro de la red virtual, lo que garantiza que el tráfico externo solo se enruta a los recursos de orientación pública designados.

> Instrucción de implementación de control de ejemplo AC-17 (3): *el acceso remoto a los componentes controlados por el cliente del sistema de información de Azure se restringe a dos puntos de control de acceso de red administrados. 1) El tráfico de Internet designado para la aplicación web se administra a través de un equilibrador de carga con conexión a Internet, que distribuye el tráfico a los recursos de la capa de web. 2) El acceso remoto a la administración se administra a través de un bastion host en una subred segregada del entorno. El grupo de seguridad de red que se aplica a la subred de administración, en el que reside el jumpbox, solo permite conexiones solo desde las direcciones IP públicas en la lista de permitidos y se restringe exclusivamente al tráfico del escritorio remoto.*

Los grupos de seguridad de red permiten un control total de las comunicaciones entre los recursos de Azure y los sistemas y host externos, así como entre las subredes y los hosts internos, separando los componentes del sistema de información que se designan como accesibles públicamente y los que no. Además de las soluciones de la arquitectura de referencia anterior, Azure permite el desarrollo de aplicaciones virtuales, como las soluciones de firewall e IDS/IPS que, cuando se usan en conjunción con rutas definidas por el usuario, protegen más y administran mejor las conexiones entre recursos de Azure y redes externas y sistemas de información [control SC-7 de NIST SP 800-53].

> Instrucción de implementación de control de ejemplo SC-7: *los recursos controlados por el cliente de Azure están protegidos mediante la implementación de varios mecanismos de protección de límites. Se definen grupos de seguridad de red que se aplican subredes de red que restringen el tráfico tanto en el límite del sistema de información como dentro de la red virtual. Los grupos de seguridad de red incluyen reglas de lista de control de acceso (ACL) que controlan el tanto tráfico que llega a las subredes y redes virtuales como el que parte de ellas. Los equilibradores de carga se implementan para distribuir el tráfico entrante de Internet a determinados recursos. Las subredes garantizan que los componentes de los sistemas de información accesibles públicamente están separados lógicamente de los recursos que no son públicos.*

Los conjuntos de reglas de los grupos de seguridad de red permiten restricciones en puertos de red y protocolos concretos, algo que es un componente clave para asegurarse de que los sistemas de asegurarse de esa información se implementan de tal forma que proporcionan solo funcionalidades capacidades [control CM-7 de NIST SP 800-53].

> Instrucción de implementación del control de ejemplo CM-7: *los grupos de seguridad de red que se aplican a las subredes y máquinas virtuales con el entorno de Azure controlado por el cliente se implementan mediante un enfoque de permiso por excepción y denegar todo. Esto garantiza solo se permite el tráfico de red a través de los protocolos y puertos aprobados explícitamente, lo que refuerza el concepto de funcionalidad menor.*

*Recursos y herramientas adicionales*

La documentación de la arquitectura de referencia anterior está disponible en el [sitio de documentación](https://docs.microsoft.com/azure/guidance/guidance-compute-n-tier-vm) de Azure. Las plantillas de Azure Resource Manager para implementar la arquitectura de referencia se incluyen en la misma página. El sitio de documentación de Azure también contiene información detallada sobre los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) y las [rutas definidas por el usuario](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

### <a name="storage"></a>Storage

Los datos almacenados en Azure Storage se replican para garantizar una alta disponibilidad. Aunque hay varias opciones de replicación disponibles, tanto el almacenamiento con redundancia geográfica (GRS) como el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) garantizan que los datos se replican en una región secundaria. Las regiones principal y secundaria se emparejan para garantizar la distancia necesaria entre los centros de datos, con el fin de garantizar la disponibilidad si se produce una interrupción o un desastre en un área extensa [control CP-9 de NIST SP 800-53]. Para el almacenamiento con redundancia geográfica de alta disponibilidad, seleccione un almacenamiento con redundancia geográfica (GRS) o un almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) al crear una nueva cuenta de almacenamiento. 

> Instrucción de implementación del control de ejemplo CP-9: *en Azure, todas las cuentas de almacenamiento controladas por el cliente implementan el almacenamiento con redundancia geográfica, lo que garantiza que se mantienen seis copias de todos los datos en nodos independientes de dos centros de datos.*

El Cifrado del servicio Azure Storage (SSE) protege los datos en reposo en las cuentas de Azure Storage [controles SC-28 y SC-28 (1) de NIST SP 800-53]. Cuando se habilita, Azure cifra automáticamente los datos antes de almacenarlos. Para ello, se usa el cifrado AES de 256 bits. SSE admite el cifrado de blobs en bloques, blobs en anexos y blobs en páginas.

> Instrucción de implementación de los controles de ejemplo SC-28, SC-28 (1): la *confidencialidad e integridad de todos los blobs de almacenamiento con el entorno de Azure controlado por el cliente se protegen mediante el uso de Azure SSE, que usa el cifrado AES de 256 bits para todos los datos en reposo.*

*Recursos y herramientas adicionales*

El sitio de documentación de Azure contiene información detallada sobre el [cifrado del servicio de almacenamiento](https://docs.microsoft.com/azure/storage/storage-service-encryption) y la [replicación del almacenamiento](https://docs.microsoft.com/azure/storage/storage-redundancy). 

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory ofrece funcionalidades de identidad y acceso para los sistemas de información que se ejecutan en Microsoft Azure. Mediante el uso de servicios de directorio, grupos de seguridad y una directiva de grupo, los clientes pueden ayudar a controlar las directivas de acceso y seguridad de las máquinas que hacen uso de Azure Active Directory. Las cuentas y los grupos de seguridad pueden utilizarse como ayuda para administrar el acceso al sistema de información. La directiva de grupo puede ayudar a garantizar que se cumplen los requisitos de configuración.

Los grupos de seguridad y los servicios de directorio de Azure Active Directory pueden ayudar a implementar esquemas de control de acceso basado en rol (RBAC) y a controlar el acceso al sistema de información. Aquí se pueden incluir las implementaciones de control de seguridad de:

- Administración de cuentas [control AC-2 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AC-2.a: *Azure Active Directory se utiliza para administrar el acceso mediante la implementación del control de acceso basado en rol con el uso de grupos de Active Directory. Hay requisitos establecidos para la administración y el acceso a las cuentas en los dominios compatibles con AAD. El acceso a los servidores miembro del dominio solo es compatible con los grupos de seguridad. Cada grupo tiene un propietario principal y otro secundario. Estos propietarios son responsables de mantener la precisión de la pertenencia, el permiso y la descripción del grupo.*

  > Instrucción de implementación del control de ejemplo AC-2.c: *cuando un usuario solicita acceso a cualquier grupo de seguridad, la solicitud debe aprobarla el propietario de dicho grupo en función de los criterios definidos para la pertenencia a él. Las condiciones de la cuenta las determinan los propietarios del grupo de seguridad.*

  > Instrucción de implementación del control de ejemplo de AC-2.f: *Azure Active Directory se utiliza para administrar el control de acceso a los sistemas de información. Los administradores de cuentas crean, habilitan, modificar, deshabilitan o quitar cuentas del sistema de información según sea necesario, para lo que se rigen por la directiva de administración de cuentas.*

  > Instrucción de implementación del control de ejemplo AC-2 (1): *Azure Active Directory se utiliza para administrar el acceso mediante la implementación del control de acceso basado en rol con el uso de grupos de Active Directory. Hay requisitos establecidos para la administración y el acceso a las cuentas en los dominios compatibles con AAD. El acceso a los servidores miembro del dominio solo es compatible con los grupos de seguridad. Cada grupo tiene un propietario principal y otro secundario. Estos propietarios son responsables de mantener la precisión de la pertenencia, el permiso y la descripción del grupo.*

  > Instrucción de implementación del control de ejemplo de AC-2 (2): *Azure Active Directory se utiliza para administrar el control de acceso a los sistemas de información. Los administradores de cuentas crean cuentas temporales que se rigen por la directiva de administración de cuentas. Debe establecerse que dichas cuentas expiren en consonancia con los requisitos de la directiva.*

  > Instrucción de implementación del control de ejemplo AC-2 (3): *Azure Active Directory administra el control de acceso a los sistemas de información. Los administradores de cuentas crean cuentas temporales que se rigen por la directiva de administración de cuentas. Debe establecerse que dichas cuentas expiren en consonancia con los requisitos de la directiva.*

  >Instrucción de implementación del control de ejemplo AC-2 (7): *Azure Active Directory administra el acceso de control a los sistemas de información de acuerdo con un esquema de acceso basado en rol, que organiza los privilegios del sistema de información en roles que están asignados a grupos de seguridad. Los administradores del grupo de seguridad son los responsables de conceder acceso a los usuarios para asignar usuarios al grupo de seguridad correcto. A cada grupo de seguridad se le asignan permisos con el acceso mínimo apropiado para poder realizar correctamente sus tareas.*

  > Instrucción de implementación del control de ejemplo AC-2 (11): *Azure Active Directory administra el control de acceso a los sistemas de información en Azure. Las restricciones de uso que requieren las directivas de seguridad las definen y exigen en Azure Active Directory los administradores de cuentas.*

- Cumplimiento de acceso [control AC-3 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AC-3: *Azure Active Directory exige las autorizaciones aprobadas en el entorno del cliente mediante el uso del control de acceso basado en rol. De la administración del acceso a los grupos de seguridad de Azure Active Directory se encargan los administradores del grupo de seguridad. Los usuarios se colocan en grupos de seguridad adecuados en función de sus roles, para lo que se usan los principios de privilegio mínimo.*

- Privilegio mínimo [control AC-6 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AC-6 (10): *Azure Active Directory exige las autorizaciones aprobadas en el entorno del cliente mediante el uso del control de acceso basado en rol. De la administración del acceso a los grupos de seguridad de Azure Active Directory se encargan los administradores del grupo de seguridad. A los usuarios sin privilegios no se les concede acceso a los grupos de seguridad que les permitiría acceder a funciones con privilegios, incluidos los permisos que les permitiría deshabilitar, sortear o modificar las medidas de seguridad.*

- Acceso remoto [control AC-17 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo de AC-17 (1): *Azure Active Directory se utiliza para supervisar y controlar todo el acceso remoto. Azure Active Directory incluye informes de seguridad, actividad y auditoría del directorio.*

- Protección de la información de auditoría [control AU-9 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AU-9: *los controles estrechos de acceso administrado se utilizan para proteger la información de auditoría y las herramientas contra el acceso, la modificación y la eliminación no autorizados. Azure Active Directory exige un acceso lógico aprobado a los usuarios mediante un enfoque de capa de las autorizaciones de acceso y controles lógicos mediante directivas de Active Directory y las pertenencias a grupos basadas en rol. La capacidad para ver información de auditoría y utilizar herramientas de auditoría queda limitada a los usuarios que requieran dichos permisos.*

  > Instrucción de implementación del control de ejemplo AU-9 (4): *Azure Active Directory restringe la administración de la funcionalidad de auditoría a los miembros de los grupos de seguridad apropiados. Solo se conceden estos permisos al personal con una necesidad concreta de acceso a la administración de la funcionalidad de auditoría.*

- Restricciones de acceso para cambios [control CM-5 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo CM-5: *el RBAC que exige por Azure Active Directory se utiliza para definir, documentar, aprobar y exigir las restricciones de acceso lógico asociadas a los cambios. Todas las cuentas que se crean en el sistema se basan en roles. Acceso a las solicitudes del personal por parte de los administradores de cuentas, y si se aprueba, se colocan en los grupos de seguridad adecuados según sus roles. El acceso al entorno de producción solo se permite a los miembros de grupos de seguridad específicos tras la aprobación.*

  > Instrucción de implementación del control de ejemplo CM-5 (1): *Azure Active Directory exige restricciones de acceso lógico a través de la pertenencia a grupos de seguridad. Esto requiere que los propietarios de grupos de seguridad concedan acceso a un grupo de seguridad dado.*

- Identificación y autenticación de usuarios [control IA-2 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo IA-2: *el personal que accede al entorno del sistema de información se identifican de manera exclusiva mediante su nombre de usuario y se autentica con Azure Active Directory. Para acceder al sistema de información, es preciso autenticarse en Azure Active Directory.*

  > Instrucción de implementación del control de ejemplo IA-2 (8): *la funcionalidad Kerberos integrada en Azure Active Directory protege el acceso al entorno de producción de ataques de reproducción. En la autenticación de Kerberos, el autenticador que envía el cliente contiene datos adicionales, como una lista de IP cifrada, marcas de tiempo de clientes y la vigencia del vale. Si se reproduce un paquete, se comprueba la marca de tiempo. Si la marca de tiempo es anterior que un autenticador anterior, o la misma, el paquete se rechaza.*

  > Instrucción de implementación del control de ejemplo IA-2 (9): *la funcionalidad Kerberos integrada en Azure Active Directory protege todas las cuentas contra ataques de reproducción. En la autenticación de Kerberos, el autenticador que ha enviado el cliente contiene datos adicionales, como una lista de IP cifrada y las marcas de tiempo de clientes, la vigencia del vale. Si se reproduce un paquete, se comprueba la marca de tiempo. Si la marca de tiempo es anterior que un autenticador anterior, o la misma, el paquete se rechaza.*

- Administración de identificadores [control IA-4 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo IA-4.b: *los identificadores de cuentas de Azure Active Directory se usan para identificar a los usuarios. Estos identificadores únicos no se reutilizan.*

  > Instrucción de implementación del control de ejemplo IA-4.b: *Azure Active Directory es el repositorio de cuentas central que se usa para proporcionar acceso al entorno del servicio. Cuando se crean cuentas en Azure Active Directory, el identificador único del usuario se asigna al individuo.*

  > Instrucción de implementación del control de ejemplo IA-4.d: *los identificadores de usuario únicos nunca se reutilizan, algo que exige Azure Active Directory.*

  > Instrucción de implementación del control de ejemplo IA-4.e: *todas las cuentas de Azure Active Directory están configuradas para deshabilitarse automáticamente después de 35 días de inactividad.*

  > Instrucción de implementación del control de ejemplo IA-4 (4): *Azure Active Directory denota a los contratistas y proveedores mediante el uso de convenciones de nomenclatura que se aplican a sus credenciales de Azure Active Directory únicas.*

- Administración de autenticadores [control IA-5 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo IA-5.b: *en el momento en que se crea una cuenta Azure Active Directory asigna una identificación única y una contraseña temporal aleatoria que cumple los requisitos de la directiva. Azure Active Directory mantiene la identificación única asociada a la cuenta durante toda la vida de la misma. La identificación de la cuenta nunca se repite en Azure Active Directory*

  > Instrucción de implementación del control de ejemplo de IA-5.c: *Azure Active Directory garantiza que las contraseñas emitidas cumplen los requisitos de la directiva en cuanto a complejidad de contraseñas.*

  > Instrucción de implementación del control de ejemplo IA-5.d: *los procedimientos de distribución de autenticadores iniciales los controlan los administradores de las cuentas. Las modificaciones del acceso a las cuentas de usuario pasan por el proceso de administración de las cuentas, como se define en AC-2. Si un autenticador se pierde o está en peligro, los administradores de cuentas siguen los procesos definidos para restablecer, volver a emitir o revocar el autenticador, según sea necesario.*

  > Instrucción de implementación del control de ejemplo IA-5 (1).c: *Azure Active Directory se utiliza para garantizar que todas las contraseñas se protegidas mediante criptografía cuando se almacenan y se transmiten. A las contraseñas que almacena Azure Active Directory Automáticamente se les ha aplicado el algoritmo hash como parte de la funcionalidad de Azure Active Directory.*

  > Instrucción de implementación del control de ejemplo IA-5 (1).f: *Azure Active Directory se utiliza para administrar el control de acceso al sistema de información. Cada vez que se crea una cuenta o se genera una contraseña temporal, Azure Active Directory se emplea para requerir que el usuario cambie la contraseña en el siguiente inicio de sesión.*

  > Instrucción de implementación del control de ejemplo IA-5 (4): *Azure Active Directory es la herramienta automatizada que se emplea para determinar si los autenticadores de contraseña son suficientemente seguros como para satisfacer las restricciones de longitud, complejidad, rotación y duración de contraseña establecidas en IA-5 (1). Azure Active Directory garantiza que la seguridad del autenticador de contraseñas durante la creación cumple estos estándares.*

La configuración de la directiva de grupos de Azure Active Directory se puede utilizar para implementar las directivas de seguridad de cliente que cumplan requisitos de seguridad concretos. Azure Active Directory puede aplicar estas directivas a las máquinas virtuales de Azure del cliente.
Las configuraciones de la directiva de grupos de Azure Active Directory pueden incluir las implementaciones de control de seguridad:

- Privilegio mínimo [control AC-6 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AC-6 (9): *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza para cumplir los requisitos de la organización, entre los que se incluye la auditoría de la ejecución de las funciones con privilegios.*

- Intentos de inicio de sesión fallidos [control AC-7 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AC-7: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza de forma que cumpla los requisitos de la organización, incluido el inicio de un bloqueo de cuenta después de varios intentos consecutivos de inicio de sesión infructuosos.*

- Control de sesiones simultáneas [control AC-10 de NIST SP 800-53], bloqueo de sesión [control AC-11 de NIST SP 800-53], terminación de la sesión [control AC-12 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AC-10: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza de forma que cumpla los requisitos de la organización, incluida la restricción de los usuarios a una sola sesión de servicios de escritorio remoto, que cumple los requisitos de FedRAMP y DoD.*

  > Instrucción de implementación del control de ejemplo AC-11.a: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza de forma que cumpla los requisitos de la organización, incluido el inicio de un bloqueo de sesión después de 900 segundos (15 minutos) de inactividad que se conserva hasta que el usuario se vuelve a autenticar.*

  > Instrucción de implementación del control de ejemplo AC-12: *Windows termina automáticamente las sesiones de escritorio remoto al recibir una solicitud de cierre de sesión del usuario del cliente. Adicionalmente, se aplica un objeto de directiva de grupo (GPO) controlado por la configuración a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza para cumplir los requisitos de la organización, lo que incluye la terminación de una sesión de escritorio remoto después de 900 segundos (15 minutos) de inactividad.*

- Administración de autenticadores [control IA-5 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo IA-5.f: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza para que cumpla los requisitos de la organización, incluidas las restricciones sobre las contraseñas de cuentas locales que exigen restricciones de una vigencia mínima (1 día) y máxima (60 días), condiciones de reutilización (24 contraseñas), longitud (14 caracteres) y requisitos de complejidad, que cumplen los requisitos de FedRAMP.*

  > Instrucción de implementación del control de ejemplo IA-5.g: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza para que cumpla los requisitos de la organización, incluidas las restricciones sobre las contraseñas de cuentas locales que exigen restricciones de una vigencia máxima (60 día), que cumplen los requisitos de FedRAMP.*

  > Instrucción de implementación del control de ejemplo IA-5 (1).a: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza para cumplir los requisitos de la organización, incluidos los requisitos de complejidad, que cumplen los requisitos de FedRAMP.*

  > Instrucción de implementación del control de ejemplo IA-5 (1).b: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza para cumplir los requisitos de la organización, incluidos los requisitos de complejidad, que cumplen los requisitos de FedRAMP.*

  > Instrucción de implementación del control de ejemplo IA-5 (1).d: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza para que cumpla los requisitos de la organización, incluidas las restricciones sobre las contraseñas de cuentas locales que exigen restricciones de una vigencia mínima (1 día) y máxima (60 días), que cumplen los requisitos de FedRAMP."

  > Instrucción de implementación del control de ejemplo IA-5 (1).e: *un objeto de directiva de grupo (GPO) controlado por la configuración se aplica a todos los equipos de Windows controlados por el cliente en Azure. El objeto de directiva de grupo se personaliza para cumplir los requisitos de la organización, incluidas las restricciones sobre las condiciones de reutilización (24 contraseñas), que cumplen los requisitos de FedRAMP.*

*Recursos y herramientas adicionales*

Para guiar la implementación segura de características y servicios de Azure, están disponibles la documentación, las herramientas, las plantillas y otros recursos. Para empezar a trabajar con Azure Active Directory, visite [Microsoft Azure Docs](https://docs.microsoft.com/azure/active-directory/).

### <a name="key-vault"></a>Key Vault

Azure Key Vault ofrece protección a las claves criptográficas y los secretos que usan los servicios y aplicaciones en la nube. Mediante el uso de Azure Key Vault, los clientes pueden crear, administrar y proteger claves y secretos. Se pueden usar contenedores seguros (almacenes) para almacenar y administrar claves criptográficas y secretos de forma segura. Azure Key Vault se puede utilizar para generar claves criptográficas mediante los HSM que tienen validación FIPS 140-2 de nivel 2.

Los contenedores de Azure Key Vault pueden servir de ayuda para almacenar claves criptográficas de forma segura con alta disponibilidad. Aquí se pueden incluir las implementaciones de control de seguridad de:

-   Protección de los autenticadores [control IA-5 (7) de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo IA-5 (7): *se prohíbe explícitamente el uso de autenticadores estáticos sin cifrar insertados en aplicaciones, scripts de acceso o las teclas de función. Cualquier script o aplicación que utilice un autenticador realiza una llamada a un contenedor de Azure Key Vault antes de cada uso. El acceso a los contenedores de almacén Azure Key Vault se audita, lo que permite la detección de infracciones de esta prohibición si se usa una cuenta de servicio para acceder a un sistema sin una llamada correspondiente al contenedor de Azure Key Vault.*

- Establecimiento y administración de una clave criptográfica [control SC-12 (1) de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo SC-12 (1): *Azure Key Vault se usa para almacenar claves criptográficas y secretos. Este servicio realiza el seguimiento y la supervisión del acceso a los secretos. Este servicio se utiliza para garantizar que no se pierden secretos.*

Azure Key Vault se puede utilizar para crear claves criptográficas mediante los HSM que cumplen la validación FIPS 140-2 de nivel 2. Las configuraciones de la directiva de grupos de Azure Active Directory pueden incluir las implementaciones de control de seguridad:

- Establecimiento y administración de una clave criptográfica [control SC-12 (2) de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo SC-12 (2): *Azure Key Vault se usa para producir, controlar y distribuir claves criptográficas. Estas claves se generan mediante los HSM que tienen la validación FIPS 140-2 de nivel 2. Estas claves se almacenan y administran en contenedores de cifrados de forma segura de Azure Key Vault.*

*Recursos y herramientas adicionales*

Para guiar la implementación segura de características y servicios de Azure, están disponibles la documentación, las herramientas, las plantillas y otros recursos. Para empezar a trabajar con Azure Key Vault, visite [Microsoft Azure Docs](https://docs.microsoft.com/azure/key-vault/).

### <a name="operations-management-suite"></a>Operations Management Suite

Microsoft Operations Management Suite (OMS) es la solución de administración de TI basada en la nube de Microsoft que ayuda a administrar y proteger una infraestructura en la nube y local. La seguridad y el cumplimiento ayudan a identificar, evaluar y mitigar los riesgos de seguridad de una infraestructura. Estas características de OMS se implementan a través de varias soluciones, entre las que se incluye Log Analytics, que analizan los datos de registro y supervisan las configuraciones de seguridad.

Los servicios de seguridad y auditoría con OMS habilitado pueden ayudarle a implementar la supervisión y el registro de cuentas. Aquí se pueden incluir las implementaciones de control de seguridad de:

- Administración de cuentas [control AC-2 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AC-2.g: *Microsoft Operation Management Suite (OMS) se usa para supervisar el uso de cuentas del sistema. OMS crea registros de auditoría para las cuentas del sistema que, posteriormente, se pueden analizar con OMS y generar alertas basándose en un conjunto definido de criterios.*

  > Instrucción de implementación del control de ejemplo AC-2 (4): *Microsoft Operations Management Suite (OMS) se usa para generar los registros de auditoría de las funciones de administración de cuentas, como por ejemplo; acciones de creación, modificación, habilitación, deshabilitación y eliminación de cuentas. OMS se usa para alertar al propietario del sistema si se ha ejecutado cualquiera de las condiciones anteriores.*

  > Instrucción de implementación del control de ejemplo AC-2 (12): *Microsoft Operation Management Suite (OMS) se usa para supervisar si el uso de las cuentas del sistema es atípico. OMS crea registros de auditoría para las cuentas del sistema que, posteriormente, se pueden analizar con OMS y alertar al personal apropiado basándose en un conjunto definido de criterios.*

- Privilegio mínimo [control AC-6 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AC-6 (9): *Microsoft Operation Management Suite (OMS) se usa para supervisar la ejecución de funciones con privilegios. OMS crea registros de auditoría para una lista de funciones especificadas que, posteriormente, se pueden analizar con OMS y generar alertas basándose en un conjunto definido de criterios.*

 - Acceso remoto [control AC-17 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AC-17 (1): *Microsoft Operation Management Suite (OMS) se usa para supervisar y controlar el acceso remoto. OMS incluye informes de seguridad, actividad y auditoría para el directorio.*

- Auditar eventos [control AU-2 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AU-2: *Microsoft Operations Management Suite (OMS) se utiliza para auditar: eventos de inicio de sesión de cuenta correctos e incorrectos, eventos de administración de cuentas, acceso a objetos, cambio de directiva, funciones de privilegios, seguimiento de procesos y eventos del sistema. Para aplicaciones web: toda la actividad del administrador, las comprobaciones de autenticación, las comprobaciones de autorización, las eliminaciones de datos, el acceso a los datos, los cambios de datos y los cambios de permiso. También cualquier otro conjunto de eventos definido por el cliente.*

- Contenido de registros de auditoría [control AU-3 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AU-3 (2): *Microsoft Operations Management Suite (OMS) se usa como administración y configuración centralizadas para todos los registros de auditoría generados por la red, el almacenamiento y los equipos informáticos.*

- Auditoría de la capacidad de almacenamiento [control AU-4 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AU-4: *Microsoft Operations Management Suite (OMS) se usa para la administración y configuración centralizadas de todos los registros de auditoría generados por la red, el almacenamiento y los equipos informáticos. La herramienta de administración centralizada se utiliza para configurar la capacidad de almacenamiento de los registros de auditoría.*

- Respuesta a errores de procesamiento de auditoría [control AU-5 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AU-5: *Microsoft Operations Management Suite (OMS) se usa con la solución de seguridad y auditoría para alertar al personal definido de la organización de errores de procesamiento de auditoría.*

  > Instrucción de implementación del control de ejemplo AU-5 (1): *Microsoft Operations Management Suite (OMS) se usa con la solución de seguridad y auditoría para alertar al personal definido de la organización dentro del periodo de organización que el almacenamiento de registros de auditoría ha alcanzado un porcentaje de la organización definido del volumen máximo del repositorio.*

  > Instrucción de implementación del control de ejemplo AU-5 (2): *Microsoft Operations Management Suite (OMS) se usa con la solución de seguridad y auditoría para alertar al personal definido de la organización en tiempo real cuando se producen errores en eventos auditables definidos en la directiva de la organización.*

- Auditoría de revisión, análisis e informes [control AU-6 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AU-6 (3): *Microsoft Operations Management Suite (OMS) se usa con la solución Security and Audit, así como con la solución Log Analytics, para analizar diferentes repositorios del registro de auditoría para proporcionar a toda la organización conocimiento sobre la situación. La herramienta de análisis se usa para informar de los registros de auditoría para proporcionar el conocimiento de la situación.*

  > Instrucción de implementación del control de ejemplo AU-6 (4): *Microsoft Operations Management Suite (OMS) se usa para la administración y configuración centralizadas de todos los registros de auditoría. La herramienta de administración centralizada permite revisar y analizar registros de auditoría de todos los orígenes.*

  > Instrucción de implementación del control de ejemplo AU-6 (5): *Microsoft Operations Management Suite (OMS) se usa con la solución de seguridad y auditoría, así como la solución Log Analytics, para analizar diferentes datos relacionados con las seguridad generados a partir del análisis de vulnerabilidades, la supervisión del sistema de información y los datos relacionados con el rendimiento. El análisis de todos estos recursos diferentes proporciona una capacidad mejorada de identificar actividad sospechosa.*

- Auditoría de reducción y generación de informes [control AU-7 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AU-7: *Microsoft Operations Management Suite (OMS) se usa con la solución Security and Audit, así como con la solución Log Analytics, para generar informes en lenguaje natural a petición que permiten realizar investigaciones a posteriori de incidentes de seguridad. El uso de la herramienta Microsoft Log Analytics no modifica de forma permanente o irreversible el contenido original del registro de auditoría ni la ordenación de hora.*

  > Instrucción de implementación del control de ejemplo AU-7 (1): *Microsoft Operations Management Suite (OMS) se usa con la solución Security and Audit para consultar eventos auditables definidos de la organización.*

- Protección de la información de auditoría [control AU-9 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AU-9: *Microsoft Operations Management Suite (OMS) se utiliza con grupos de seguridad para administrar el acceso a auditar la información y herramientas para evitar el acceso no autorizado a registros de auditoría, su modificación y eliminación. La capacidad para ver información de auditoría y utilizar herramientas de auditoría queda limitada a los usuarios que requieran dichos permisos.*

  > Instrucción de implementación del control de ejemplo AU-9 (2): *Microsoft Operations Management Suite (OMS) se utiliza con la solución Security and Audit, así como con Azure Backup para realizar copias de seguridad de los registros de auditoría en Azure que, después, replica los datos para proporcionar disponibilidad y confiabilidad de los datos. Azure Backup proporciona un lugar seguro para que los registros de auditoría se almacenen en un sistema que no sea el que se ha auditado.* 

  > Instrucción de implementación del control de ejemplo AU-9 (4): *Microsoft Operations Management Suite (OMS) se utiliza con grupos de seguridad para administrar el acceso a auditar la información y herramientas. Solo se conceden estos permisos al personal con una necesidad concreta de acceso a la administración de la funcionalidad de auditoría.*

- Retención de registros de auditoría [control AU-11 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AU-11: *Microsoft Operations Management Suite (OMS) se usa con la solución Security and Audit para configurar la retención de registros de auditoría. La retención de registro de auditoría está configurado para conservar los datos de auditoría durante un mínimo de 90 días.*

- Generación de auditoría [control AU-12 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo AU-12: *Microsoft Operations Management Suite (OMS) se usa con la solución Security and Audit para recopilar los eventos auditables definidos en AU-02 de los componentes del sistema de información. La solución Security and Audit de OMS la utiliza el personal definido de la organización para definir qué eventos auditables se recopilan de dispositivos específicos.*

  > Instrucción de implementación del control de ejemplo AU-12 (1): *Microsoft Operations Management Suite (OMS) se usa con la solución Security and Audit, así como con la solución Log Analytics para compilar registros de auditoría de los componentes del sistema que se pueden ordenar por marcas de tiempo para crear una traza de auditoría de todo el sistema.*

- Restricciones de acceso para cambios [control CM-5 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo CM-5 (1): *Microsoft Operations Management Suite (OMS) se usa con la solución Security and Audit y Active Directory para exigir restricciones de acceso lógico a través de la pertenencia a un grupo de seguridad. OMS audita las acciones que realizan estos grupos de seguridad.*

- Inventario de componentes del sistema de información [control CM-8 de NIST SP 800-53]

  > Instrucción de implementación del control de ejemplo CM-8 (3): *Microsoft Operations Management Suite (OMS) se usa con la solución Security and Audit, así como con la solución Antimalware para detectar la presencia de software no autorizado. Tras la detección OMS deshabilita el componente infectado y envía una alerta al personal definido de la organización.*

Para guiar la implementación segura de características y servicios de Azure, están disponibles la documentación, las herramientas, las plantillas y otros recursos. Para empezar a utilizar Operation Management Suite, visite [Microsoft Azure Docs](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview/).

### <a name="additional-implementation-guidance"></a>Instrucciones adicionales para la implementación

Además de los principales servicios de Azure descritos anteriormente, varias características integradas pueden ayudar a las organizaciones a cumplir los requisitos de cumplimiento del control de seguridad. Todos los recursos de Azure se organizan en grupos de recursos. Los recursos se pueden organizar aún más mediante la aplicación de etiquetas, que son los pares de clave-valor que puede seleccionar el cliente para identificar los recursos por categoría o cualquier otra propiedad. Los grupos de recursos de Azure garantizan la identificación completa y el seguimiento de todos los recursos de cliente implementados en Azure [control CM-8 de NIST SP 800-53]. 

> Instrucción de implementación del control de ejemplo CM-8: *todos los recursos controlados por el cliente implementados dentro pertenecen al grupo de recursos "Recursos del cliente". En Azure Portal, todos los recursos implementados se identifican en la hoja del grupo de recursos, lo que garantiza un inventario preciso y actualizado. Cuando es necesario, se aplican etiquetas a los recursos para cumplir los requisitos de seguimiento e informes.*

Plantillas de Azure Resource Manager, permiten a los clientes definir la configuración de los recursos en su implementación de Azure. Las plantillas de Resource Manager se basan en JSON y utilizan una sintaxis declarativa para los recursos del documento, los parámetros de configuración y otros aspectos de un entorno de Azure. Las plantillas de Resource Manager se generan automáticamente cuando las soluciones de Azure se implementan desde Azure Portal y también se pueden crear y editar manualmente para que cumplan las necesidades específicas del cliente. Estas plantillas de Resource Manager pueden servir como representación de la configuración de la base de referencia para un sistema de información [control CM-2 de NIST SP 800-53]. Las plantillas existentes de Resource Manager, como los bloques de creación mencionados anteriormente, se pueden personalizar para satisfacer las necesidades de cualquier implementación.

> Instrucción de implementación del control de ejemplo CM-2: *como componente de la configuración de la base de referencia del sistema de información, las plantillas de Azure Resource Manager se mantienen, bajo el control de la configuración, lo que representa los recursos implementados controlados por el cliente y la configuración de los componentes del sistema de información dentro de Azure. Estas plantillas capturan los recursos implementados, que incluyen las máquinas virtuales, almacenamiento y recursos de red (incluidas las configuraciones de los recursos de red que controlan el tráfico de red que entra, sale y que está dentro del entorno de Azure del cliente.*

## <a name="next-steps"></a>Pasos siguientes

Para consultas relacionadas con los procesos de Azure Blueprint, FedRAMP, DoD o Agency ATO emitido, si se necesita cualquier otro tipo de información sobre el cumplimiento o para proporcionar comentarios sobre Blueprint, envíe un correo electrónico a [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

[Microsoft Trust Center](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

[Blog de Microsoft Azure Government ](https://blogs.msdn.microsoft.com/azuregov/)



<!--HONumber=Jan17_HO4-->


