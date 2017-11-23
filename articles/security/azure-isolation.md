---
title: "Aislamiento en la nube pública de Azure | Microsoft Docs"
description: "Obtenga información acerca de los servicios de computación en la nube que incluyen una amplia selección de instancias y servicios de proceso, que se pueden escalar tanto vertical como horizontalmente para satisfacer las necesidades de su aplicación o de su empresa."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: a153d70e077ad63a042e76d0c4ae40e3cc067a2a
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="isolation-in-the-azure-public-cloud"></a>Aislamiento en la nube pública de Azure
##  <a name="introduction"></a>Introducción
### <a name="overview"></a>Información general
Para ayudar a clientes actuales y potenciales de Azure a comprender y utilizar las diversas funcionalidades relacionadas con la seguridad disponibles en torno a la plataforma de Azure, Microsoft ha desarrollado una serie de notas del producto, información general de seguridad, procedimientos recomendados y listas de comprobación.
Los temas varían en extensión y detalle, y se actualizan periódicamente. Este documento forma parte de esa serie, como se resume en la breve descripción de la siguiente sección.

### <a name="azure-platform"></a>Plataforma Azure
Azure es una plataforma de servicios en la nube abierta y flexible que admite la más amplia selección de sistemas operativos, lenguajes de programación, marcos, herramientas, bases de datos y dispositivos. Por ejemplo, puede:
- Ejecutar contenedores de Linux con integración con Docker.
- Compilar aplicaciones con JavaScript, Python, .NET, PHP, Java y Node.js.
- Crear back-ends para dispositivos iOS, Android y Windows.

Microsoft Azure admite las mismas tecnologías en las que ya confían millones de desarrolladores y profesionales de TI.

Al crear en un proveedor de servicios en la nube pública, o migrar recurso de TI al mismo, está confiando en la capacidad de la organización para proteger sus aplicaciones y datos con los servicios y controles que proporcionan para administrar la seguridad de sus recursos en la nube.

La infraestructura de Azure está diseñada desde la instalación hasta las aplicaciones para hospedar millones de clientes simultáneamente, y proporciona una base de confianza en la que las empresas pueden satisfacer sus necesidades de seguridad. Además, Azure ofrece una amplia gama de opciones de seguridad configurables, así como la posibilidad de controlarlas, por lo que puede personalizar la seguridad para satisfacer los requisitos únicos de sus implementaciones. Este documento le ayuda a cumplir estos requisitos.

### <a name="abstract"></a>Descripción breve

Microsoft Azure permite ejecutar aplicaciones y máquinas virtuales (VM) en una infraestructura física compartida. Una de las motivaciones económicas principales para ejecutar aplicaciones en un entorno de nube es la capacidad de distribuir el costo de los recursos compartidos entre varios clientes. Esta práctica de multiinquilino mejora la eficiencia al multiplexar los recursos entre los distintos clientes a un bajo costo. Lamentablemente, también presenta el riesgo del uso compartido de servidores físicos y otros recursos de infraestructura al ejecutar aplicaciones confidenciales y máquinas virtuales que pueden pertenecer a un usuario arbitrario y potencialmente malintencionado.

En este artículo se describe cómo Microsoft Azure proporciona aislamiento contra usuarios malintencionados y no malintencionados y cómo sirve como guía para diseñar soluciones en la nube, ya que ofrece diversas opciones de aislamiento a los arquitectos. Estas notas del producto se centran en la tecnología de la plataforma de Azure y los controles de seguridad orientados al cliente y no pretende indicar ningún SLA, modelo de precios o consideraciones prácticas sobre DevOps.

## <a name="tenant-level-isolation"></a>Aislamiento en el nivel de inquilino
Una de las principales ventajas de la informática en la nube es el concepto de una infraestructura común compartida por varios clientes simultáneamente, dando lugar a economías de escala. Este concepto se denomina multiinquilinato. Microsoft trabaja continuamente para asegurarse de que la arquitectura multiinquilino de Microsoft Cloud Azure admita las normas de seguridad, confidencialidad, privacidad, integridad y disponibilidad.

En un área de trabajo habilitada en la nube, un inquilino puede definirse como un cliente o una organización que posee y administra una instancia específica de ese servicio en la nube. Con la plataforma de identidad proporcionada por Microsoft Azure, un inquilino es simplemente una instancia dedicada de Azure Active Directory (Azure AD) que su organización recibe y posee cuando se suscribe a un servicio en la nube de Microsoft.

Cada directorio de Azure AD es distinto e independiente de otros directorios de Azure AD. Del mismo modo que un edificio de oficinas para empresas es un activo seguro dedicado específicamente a su organización, un directorio de Azure AD se ha diseñado también para ser un activo seguro para el uso exclusivo de su organización. La arquitectura de Azure AD aísla los datos del cliente y la información de identidad para evitar contactos cruzados. Esto significa que los usuarios y los administradores de un directorio de Azure AD no tendrán acceso a los datos de otro directorio, ya sea de manera involuntaria o malintencionada.

### <a name="azure-tenancy"></a>Inquilinato de Azure
El inquilinato de Azure (suscripción de Azure) hace referencia a una relación "cliente/facturación" y a un único [inquilino](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) en [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). El aislamiento en el nivel de inquilino en Microsoft Azure se logra con Azure Active Directory y los [controles basados en roles](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) que ofrece. Cada suscripción de Azure está asociada a un directorio de Azure Active Directory (AD).

Los usuarios, grupos y aplicaciones de ese directorio pueden administrar los recursos en la suscripción de Azure. Puede asignar estos derechos de acceso con Azure Portal, las herramientas de la línea de comandos de Azure o las API de administración de Azure. Un inquilino de Azure está aislado lógicamente mediante límites de seguridad de forma que ningún cliente puede acceder o poner en riesgo los coinquilinos, ya sea de forma malintencionada o por accidente. Azure AD se ejecuta en servidores sin sistema operativo, aislados en un segmento de red separado donde el filtrado de paquetes de nivel de host y Firewall de Windows bloquean el tráfico y las conexiones no deseadas.

- El acceso a los datos de Azure AD requiere autenticación del usuario mediante un servicio de token de seguridad (STS). El sistema de autorización usa la información sobre la existencia, estado habilitado y rol del usuario se usa para determinar si el acceso solicitado al inquilino de destino está autorizado para este usuario en esta sesión.

![Inquilinato de Azure](./media/azure-isolation/azure-isolation-fig1.png)


- Los inquilinos son contenedores separados y no hay ninguna relación entre ellos.

- No hay acceso entre inquilinos a menos que un administrador lo conceda mediante federación o el aprovisionamiento de cuentas de usuario de otros inquilinos.

- El acceso físico a los servidores que componen el servicio Azure AD y el acceso directo a los sistemas back-end de Azure AD están restringidos.

- Los usuarios de Azure AD no tienen acceso a los recursos ni las ubicaciones físicas y, por lo tanto, no pueden omitir las comprobaciones lógicas de directiva de RBAC que se indican a continuación.

Por razones de diagnóstico y mantenimiento, se necesita y utiliza un modelo de operaciones que emplee un sistema de elevación de privilegios Just-In-Time. Azure AD Privileged Identity Management (PIM) presenta el concepto de administrador apto. Los [administradores aptos](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) deben ser usuarios que necesiten acceso con privilegios de vez en cuando, pero no todos los días. El rol está inactivo hasta que el usuario necesita acceso, luego realiza un proceso de activación y se convierte en un administrador activo durante una cantidad de tiempo predeterminada.

![Administración de identidades con privilegios de Azure AD](./media/azure-isolation/azure-isolation-fig2.png)

Azure Active Directory hospeda a cada inquilino en su propio contenedor protegido, con directivas y permisos solo para el contenedor, que el inquilino administra y del que es propietario.

El concepto de contenedores de inquilino está profundamente relacionado con el servicio de directorio en todas las capas, desde los portales hasta el almacenamiento persistente.

Aunque se almacenan metadatos de varios inquilinos de Azure Active Directory en el mismo disco físico, no hay relación entre los contenedores salvo la que se define en el servicio de directorio, que a su vez viene determinada por el administrador del inquilino.

### <a name="azure-role-based-access-control-rbac"></a>Control de acceso basado en rol (RBAC) de Azure
El [control de acceso basado en rol (RBAC) de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) ayuda a compartir varios componentes disponibles dentro de una suscripción de Azure, y proporciona una administración de acceso detallada en Azure. RBAC de Azure le permite separar las tareas dentro de su organización y conceder acceso en función de lo que los usuarios necesitan para realizar sus tareas. En lugar de proporcionar a todos los empleados permisos no restringidos en los recursos o la suscripción de Azure, puede permitir solo determinadas acciones.

RBAC de Azure cuenta con tres roles básicos que se aplican a todos los tipos de recurso:

- **propietario** tiene acceso completo a todos los recursos y cuenta con el derecho a delegar este acceso a otros.

- **colaborador** puede crear y administrar todos los tipos de recursos de Azure pero no puede conceder acceso a otros.

- **lector** solo puede ver los recursos existentes de Azure.

![Control de acceso basado en rol de Azure](./media/azure-isolation/azure-isolation-fig3.png)

El resto de los roles RBAC de Azure permiten la administración de recursos específicos de Azure. Por ejemplo, el rol de colaborador de máquina virtual permite al usuario crear y administrar máquinas virtuales. No otorga acceso a la instancia de Azure Virtual Network ni a la subred a la que se conecta la máquina virtual.

[Roles integrados para el control de acceso basado en rol](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) enumera los roles disponibles en Azure. Especifica las operaciones y el ámbito de cada rol integrado que se concede a los usuarios. Si quiere definir sus propios roles para tener un mayor control, consulte [Custom Roles in Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)(Roles personalizados en RBAC de Azure).

Otras funcionalidades de Azure Active Directory incluyen:
- Azure AD habilita SSO para las aplicaciones SaaS, independientemente de donde estén hospedadas. Algunas aplicaciones están federadas con Azure AD y otras usan SSO con contraseña. Las aplicaciones federadas también pueden admitir aprovisionamiento de usuarios y [almacén de contraseñas](https://www.techopedia.com/definition/31415/password-vault).

- El acceso a datos en [Azure Storage](https://azure.microsoft.com/services/storage/) se controla mediante la autenticación. Cada cuenta de almacenamiento tiene una clave principal ([clave de la cuenta de almacenamiento](https://docs.microsoft.com/azure/storage/storage-create-storage-account), o SAK) y una clave secreta secundaria (la firma de acceso compartido, o SAS).

- Azure AD proporciona identidad como servicio a través de la federación (mediante los [Servicios de federación de Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs)), sincronización y replicación de directorios locales.

- [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) es el servicio de autenticación multifactor que requiere que los usuarios también comprueben los inicios de sesión mediante una aplicación móvil, una llamada de teléfono o un mensaje de texto. Se puede usar con Azure AD como ayuda para proteger los recursos locales con el servidor de Azure Multi-Factor Authentication, y también con aplicaciones y directorios personalizados mediante el SDK.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) permite unir máquinas virtuales de Azure a un dominio de Active Directory sin implementar controladores de dominio. Puede conectarse a estas máquinas virtuales con sus credenciales corporativas de Active Directory y administrar las máquinas virtuales unidas a un dominio mediante una directiva de grupo para aplicar una base de referencia de seguridad en todas sus máquinas virtuales de Azure.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) proporciona un servicio de administración de identidades global y de alta disponibilidad para aplicaciones de consumo que se escalan a cientos de millones de identidades. Se puede integrar en plataformas móviles y web. Los consumidores pueden iniciar sesión en todas las aplicaciones con experiencias personalizables usando sus cuentas de redes sociales existentes o mediante la creación de credenciales.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Aislamiento de los administradores de Microsoft y eliminación de datos
Microsoft aplica medidas seguras para proteger los datos contra el acceso inadecuado o el uso por parte de personas no autorizadas. Estos controles y procesos operativos están detallados en los [términos de los servicios en línea](http://aka.ms/Online-Services-Terms), que recoge los compromisos contractuales que controlan el acceso a los datos.

-   Los ingenieros de Microsoft no tienen acceso de forma predeterminada a los datos en la nube. En su lugar, se les concede acceso, bajo la supervisión de la administración, solo cuando sea necesario. El acceso se controla y registra cuidadosamente y se revoca tan pronto como deje de ser necesario.

-   Microsoft puede contratar a otras empresas para que proporcionen servicios limitados en su nombre. Los subcontratistas pueden tener acceso a los datos de los clientes solo para ofrecer el servicio para el cual han sido contratados y se les prohíbe utilizarlos para cualquier otro propósito. Además, están contractualmente obligados a mantener la confidencialidad de la información de nuestros clientes.

Los servicios de negocios con certificaciones auditadas como la ISO/IEC 27001 se verifican con regularidad por Microsoft y otras empresas de auditoría acreditadas, que llevan a cabo auditorías de muestra para certificar que el acceso se produce solo con fines legítimos de la empresa. Siempre puede tener acceso a sus propios datos de cliente en cualquier momento y por cualquier motivo.

Si se elimina algún dato, Microsoft Azure eliminará los datos, incluidas las copias en memoria caché y las copias de seguridad. Para los servicios incluidos en el ámbito, la eliminación se realizará 90 días después del final del período de retención. (Los servicios incluidos en el ámbito se definen en los términos del procesamiento de datos, en los [términos de los servicios en línea](http://aka.ms/Online-Services-Terms).)

Si una unidad de disco utilizada para el almacenamiento sufre un error de hardware, se [borra o destruye](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) de un modo seguro antes de que Microsoft la devuelva al fabricante para su reemplazo o reparación. Los datos de la unidad se sobrescriben para asegurarse de que no se puedan recuperar de ninguna forma.

## <a name="compute-isolation"></a>Aislamiento de proceso
Microsoft Azure proporciona diversos servicios de computación en la nube que incluyen una amplia selección de instancias y servicios de proceso que se pueden escalar vertical y horizontalmente para satisfacer las necesidades de su aplicación o de su empresa. Estos servicios e instancias de proceso ofrecen aislamiento en varios niveles para proteger los datos, sin sacrificar la flexibilidad en la configuración que los clientes demandan.

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Aislamiento de Hyper-V y sistema operativo raíz entre la máquina virtual raíz y las máquinas virtuales invitadas
La plataforma de proceso de Azure se basa en la virtualización, lo que significa que todo el código del cliente se ejecuta en una máquina virtual de Hyper-V. En cada nodo de Azure (o punto de conexión de red), hay un hipervisor que se ejecuta directamente sobre el hardware y divide un nodo en un número variable de máquinas virtuales invitadas.


![Aislamiento de Hyper-V y sistema operativo raíz entre la máquina virtual raíz y las máquinas virtuales invitadas](./media/azure-isolation/azure-isolation-fig4.jpg)


Cada nodo tiene también una máquina virtual raíz especial, que ejecuta el sistema operativo host. Un límite crítico es el aislamiento de la máquina virtual raíz de las máquinas virtuales invitadas y de las máquinas virtuales invitadas entre sí, administrado por el hipervisor y el sistema operativo raíz. El emparejamiento del hipervisor y el sistema operativo raíz aprovecha las décadas de experiencia de Microsoft en seguridad del sistema operativo y su aprendizaje más reciente en Hyper-V para proporcionar un aislamiento sólido de las máquinas virtuales invitadas.

La plataforma de Azure usa un entorno virtualizado. Las instancias de usuario funcionan como máquinas virtuales independientes que no tienen acceso a un servidor host físico y este aislamiento se aplica mediante niveles de privilegios de procesador (anillo 0/anillo 3) físicos.

El anillo 0 tiene los mayores privilegios y el anillo 3 tiene los menores. El sistema operativo invitado se ejecuta en el anillo 1, con menos privilegios, y las aplicaciones en el anillo 3, el que menos privilegios tiene. Esta virtualización de recursos físicos conduce a una separación clara entre el sistema operativo invitado e hipervisor, lo que resulta en la separación de seguridad adicional entre los dos.

El hipervisor de Azure actúa como un micronúcleo y pasa todas las solicitudes de acceso al hardware desde las máquinas virtuales invitadas hasta el host para procesarlas mediante una interfaz de memoria compartida denominada VMBus. Esto impide que los usuarios obtengan acceso de lectura/escritura/ejecución sin procesar en el sistema y reduce el riesgo de compartir recursos del sistema.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Algoritmo avanzado de selección de ubicación de la máquina virtual y protección frente a ataques de canal lateral
Cualquier ataque entre máquinas virtuales conlleva dos pasos: situar una máquina virtual controlada por el adversario en el mismo host que una de las máquinas virtuales víctimas y, a continuación, romper el límite de aislamiento para robar información confidencial de la víctima o afectar a su rendimiento por codicia o vandalismo. Microsoft Azure proporciona protección frente a ambos pasos mediante el uso de un algoritmo avanzado de selección de ubicación de máquinas virtuales y protección frente a todos los ataques de canal lateral conocidos, incluidos los ataques de máquinas virtuales vecinas que puedan generar ruido.

### <a name="the-azure-fabric-controller"></a>Controlador de tejido de Azure
El controlador de tejido de Azure es responsable de asignar recursos de infraestructura a cargas de trabajo de inquilinos y administra las comunicaciones unidireccionales desde el host hasta las máquinas virtuales. El algoritmo de selección de ubicación de máquinas virtuales del controlador de tejido de Azure es muy sofisticado y casi imposible de predecir en el nivel de host físico.

![Controlador de tejido de Azure](./media/azure-isolation/azure-isolation-fig5.png)

El hipervisor de Azure fuerza la separación de la memoria y el proceso entre las máquinas virtuales y enruta de forma segura el tráfico de red a los inquilinos del sistema operativo invitado. Esto elimina la posibilidad de cualquier ataque de canal lateral en el nivel de máquina virtual.

En Azure, la máquina virtual raíz es especial: ejecuta un sistema operativo reforzado llamado sistema operativo raíz que hospeda un agente de tejido. Los agentes de tejido sirven a su vez para administrar agentes de invitado en los sistemas operativos de invitado en las máquinas virtuales de los clientes. Los agentes de tejido también administran los nodos de almacenamiento.

El conjunto del hipervisor de Azure, el sistema operativo raíz y los agentes de tejido, y las máquinas virtuales y los agentes de invitado componen un nodo de proceso. Los agentes de tejido son administrados por un controlador de tejido que se encuentra fuera de los nodos de proceso y almacenamiento (los clústeres de proceso y almacenamiento se administran mediante controladores de tejido diferentes). Si un cliente actualiza el archivo de configuración de la aplicación mientras se está ejecutando, el controlador de tejido se comunica con el agente de tejido que, a continuación, se pone en contacto con los agentes de invitado, los cuales notifican a la aplicación del cambio de configuración. Si se produce un error de hardware, el controlador de tejido encontrará automáticamente hardware disponible y reiniciará la máquina virtual en este.

![Controlador de tejido de Azure](./media/azure-isolation/azure-isolation-fig6.jpg)

La comunicación desde un controlador de tejido con un agente es unidireccional. El agente implementa un servicio protegido por SSL que solo responde a las solicitudes realizadas desde el controlador. No puede iniciar conexiones con el controlador ni con otros nodos internos con privilegios. El controlador de tejido trata todas las respuestas como si no fueran de confianza.


![Controlador de tejido](./media/azure-isolation/azure-isolation-fig7.png)

El aislamiento se extiende desde la máquina virtual raíz a las máquinas virtuales invitadas y a las máquinas virtuales invitadas entre sí. Los nodos de proceso también están aislados de los nodos de almacenamiento para mejorar la protección.


El hipervisor y el sistema operativo host proporcionan filtros de paquetes de red para ayudar a garantizar que las máquinas virtuales que no son de confianza no puedan generar tráfico simulado o recibir tráfico no dirigido a ellas, para dirigir tráfico a puntos de conexión protegidos de la infraestructura, y para enviar y recibir tráfico de difusión inadecuado.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Reglas adicionales configuradas por el agente del controlador de tejido para aislar la máquina virtual
De forma predeterminada, cuando se crea una máquina virtual, se bloquea todo el tráfico y luego el agente del controlador de tejido configura el filtro de paquetes para agregar reglas y excepciones a fin de permitir el tráfico autorizado.

Se programan dos categorías de reglas:

-   **Reglas de configuración de la máquina o la infraestructura**: de forma predeterminada, se bloquea toda la comunicación. Existen excepciones para permitir que una máquina virtual envíe y reciba tráfico DHCP y DNS. Las máquinas virtuales también pueden enviar tráfico a la Internet "pública" y a otras máquinas virtuales de la instancia de Azure Virtual Network y al servidor de activación del sistema operativo. La lista de destinos de salida permitidos de las máquinas virtuales no incluye subredes de enrutador de Azure, administración de Azure y otras propiedades de Microsoft.

-   **Archivo de configuración de roles**: define las listas de control de acceso (ACL) de entrada según el modelo de servicio del inquilino.

### <a name="vlan-isolation"></a>Aislamiento de VLAN
Hay tres redes VLAN en cada clúster:

![Aislamiento de VLAN](./media/azure-isolation/azure-isolation-fig8.jpg)


-   La red VLAN principal: interconecta nodos de cliente que no son de confianza.

-   La red VLAN FC: contiene controladores de tejido (FC) de confianza y sistemas auxiliares

-   La red VLAN de dispositivo: contiene dispositivos de red y otra infraestructura de confianza

Se permite la comunicación desde la VLAN FC a la VLAN principal, pero no se puede iniciar desde la VLAN principal hacia la VLAN FC. La comunicación también está bloqueada desde la VLAN principal hacia la VLAN de dispositivo. Esto asegura que incluso si un nodo que ejecuta código del cliente se ve comprometido, no puede atacar nodos ni de la VLAN FC ni de las VLAN de dispositivo.

## <a name="storage-isolation"></a>Aislamiento de almacenamiento
### <a name="logical-isolation-between-compute-and-storage"></a>Aislamiento lógico entre Compute y Storage
Como parte fundamental de su diseño, Microsoft Azure separa las máquinas virtuales de proceso y las de almacenamiento. Esta separación permite escalar la computación y el almacenamiento de forma independiente, facilitando así el multiinquilinato y el aislamiento.

Por tanto, Azure Storage se ejecuta en hardware independiente sin conectividad de red con Azure Compute, excepto en el nivel lógico. [Esto](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) significa que, cuando se crea un disco virtual, no se asigna espacio en disco para toda su capacidad. En su lugar, se crea una tabla que asigna direcciones en el disco virtual a las áreas en el disco físico y la tabla está inicialmente vacía. **La primera vez que un cliente escribe datos en el disco virtual, se asigna espacio en el disco físico y se coloca un puntero a este en la tabla.**
### <a name="isolation-using-storage-access-control"></a>Aislamiento con Storage Access Control
**Azure Storage Access Control** tiene un modelo de control de acceso simple. Cada suscripción de Azure puede crear una o más cuentas de almacenamiento. Cada cuenta de almacenamiento tiene una única clave secreta que se utiliza para controlar el acceso a todos los datos de esa cuenta de almacenamiento.

![Aislamiento con Storage Access Control](./media/azure-isolation/azure-isolation-fig9.png)

**El acceso a los datos de Azure Storage (incluidas las tablas)** se puede controlar con un token [SAS (Firma de acceso compartido)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1), que concede acceso de ámbito. El token SAS se crea mediante una plantilla de consulta (URL) firmada con la [SAK (Clave de la cuenta de almacenamiento)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Esta [URL firmada](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) se puede entregar a otro proceso (delegado), que puede completar los detalles de la consulta y hacer la petición al servicio de almacenamiento. Un token SAS le permite conceder acceso temporal a los clientes sin revelar la clave secreta de la cuenta de almacenamiento.

Esto significa que puede conceder permisos limitados a los clientes a objetos en su cuenta de almacenamiento durante un período específico y con un conjunto determinado de permisos. Se pueden conceder estos permisos limitados sin tener que compartir las claves de acceso de su cuenta.

### <a name="ip-level-storage-isolation"></a>Aislamiento del almacenamiento en el nivel de dirección IP
Puede establecer firewalls y definir un intervalo de direcciones IP para los clientes de confianza. Con un intervalo de direcciones IP, solo los clientes que tengan una dirección IP dentro del intervalo definido podrán conectarse a [Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide).

Los datos de almacenamiento de IP se pueden proteger contra usuarios no autorizados mediante un mecanismo de red que se utiliza para asignar un túnel de tráfico dedicado al almacenamiento de IP.

### <a name="encryption"></a>Cifrado
Azure ofrece los siguientes tipos de cifrado para proteger los datos:
-   Cifrado en tránsito

-   Cifrado en reposo

#### <a name="encryption-in-transit"></a>Cifrado en tránsito
Cifrado en tránsito es un mecanismo para proteger datos cuando se transmiten a través de redes. Con Azure Storage, puede proteger los datos mediante:

-   [Cifrado de nivel de transporte](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), como HTTPS para transferir datos a Azure Storage o desde este servicio.

-   [Cifrado en el cable](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), como el cifrado SMB 3.0 para recursos compartidos de Azure File.

-   [Cifrado de cliente](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), para cifrar los datos antes de transferirlos al almacenamiento y descifrarlos una vez transferidos desde este servicio.

#### <a name="encryption-at-rest"></a>Cifrado en reposo
Para muchas organizaciones, el [cifrado de los datos en reposo](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) es un paso obligatorio en lo que respecta a la privacidad de los datos, el cumplimiento y la soberanía de los datos. Hay tres características de Azure que proporcionan cifrado de datos "en reposo":

-   [Cifrado del servicio Storage](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) permite solicitar que el servicio de almacenamiento cifre automáticamente los datos al escribirlos en Azure Storage.

-   [Cifrado de cliente](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) también proporciona la característica de cifrado en reposo.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) permite cifrar los discos de datos y del sistema operativo usados por una máquina virtual de IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) para máquinas virtuales le ayuda solucionar los aspectos de seguridad y cumplimiento normativo de su organización, ya que cifra los discos de las máquinas virtuales (incluidos los discos de inicio y de datos) con claves y directivas que se controlan en [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

La solución Cifrado de discos para Windows se basa en la tecnología de [Cifrado de unidad BitLocker de Microsoft](https://technet.microsoft.com/library/cc732774.aspx), y la solución de Linux se basa en [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

La solución admite los siguientes escenarios para las máquinas virtuales IaaS cuando se habilitan en Microsoft Azure:
-   Integración con Azure Key Vault

-   Máquinas virtuales de nivel estándar: máquinas virtuales IaaS de las series A, D, DS, G, GS, etc.

-   Habilitación del cifrado en máquinas virtuales IaaS Linux y Windows

-   Deshabilitación del cifrado en las unidades de datos y del sistema operativo en máquinas virtuales IaaS Windows

-   Deshabilitación del cifrado en unidades de datos en máquinas virtuales IaaS Linux

-   Habilitación del cifrado en máquinas virtuales IaaS que ejecutan el sistema operativo cliente de Windows

-   Habilitación del cifrado en volúmenes con rutas de montaje

-   Habilitación del cifrado en máquinas virtuales Linux configuradas con seccionamiento de disco (RAID) mediante [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Habilitación del cifrado en máquinas virtuales Linux mediante el uso de [LVM (administrador de discos lógicos)](https://msdn.microsoft.com/library/windows/desktop/bb540532) en los discos de datos

-   Habilitación del cifrado en máquinas virtuales con Windows configuradas mediante Espacios de almacenamiento

-   Se admiten todas las regiones públicas de Azure.

La solución no admite los siguientes escenarios, características y tecnologías en la versión:

-   Máquinas virtuales IaaS de nivel básico

-   Deshabilitación del cifrado en una unidad del sistema operativo para máquinas virtuales IaaS Linux

-   Máquinas virtuales IaaS creadas con el método clásico de generación de máquinas virtuales

-   Integración con el Servicio de administración de claves local

-   Azure Files (sistema de archivos compartido), Network File System (NFS), volúmenes dinámicos y máquinas virtuales Windows configuradas con sistemas RAID basadas en software

## <a name="sql-azure-database-isolation"></a>Aislamiento de base de datos de SQL Azure
SQL Database es un servicio de bases de datos relacionales de Microsoft Cloud que usa el motor de Microsoft SQL Server líder del mercado, lo que le permite controlar cargas de trabajo críticas. SQL Database ofrece aislamiento de datos predecible en el nivel de cuenta, basado en región o área geográfica, o basado en red, todo ello con una administración prácticamente inexistente.

### <a name="sql-azure-application-model"></a>Modelo de aplicación de SQL Azure

[Microsoft SQL Azure Database](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) es un servicio de base de datos relacional en la nube que se basa en la tecnología de SQL Server. Proporciona un servicio de base de datos de alta disponibilidad, escalable y multiinquilino, hospedado por Microsoft en la nube.

Desde la perspectiva de una aplicación, SQL Azure proporciona la siguiente jerarquía: cada nivel puede contener uno o varios de los niveles inferiores.

![Modelo de aplicación de SQL Azure](./media/azure-isolation/azure-isolation-fig10.png)

Cuenta y suscripción son conceptos de la plataforma de Microsoft Azure para asociar la facturación y la administración.

Servidores y bases de datos lógicas son conceptos específicos de SQL Azure y se administran mediante SQL Azure, que proporciona interfaces OData y TSQL, o bien con el portal de SQL Azure integrado en Azure Portal.

Los servidores de SQL Azure no son físicos ni instancias de máquinas virtuales, son colecciones de bases de datos que comparten administración y directivas de seguridad almacenadas en una base de datos llamada “maestra lógica”.

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Las bases de datos “maestras lógicas” incluyen:

-   Inicios de sesión SQL usados para conectarse al servidor

-   Reglas de firewall

No se garantiza que la información de facturación y aquella relacionada con el uso de las bases de datos de SQL Azure en el mismo servidor lógico estén en la misma instancia física en un clúster de SQL Azure; en su lugar, las aplicaciones deben proporcionar el nombre de la base de datos de destino al conectarse.

Desde la perspectiva del cliente, un servidor lógico se crea en un región geográfica, en tanto que la creación real del servidor se produce en uno de los clústeres de la región.

### <a name="isolation-through-network-topology"></a>Aislamiento mediante topología de red

Cuando se crea un servidor lógico y se registra su nombre DNS, el nombre DNS apunta a la dirección “VIP de puerta de enlace” del centro de datos específico en el que se sitúa el servidor.

Detrás de la dirección VIP (dirección IP virtual), tenemos una colección de servicios de puerta de enlace sin estado. En general, las puertas de enlace se ven involucradas cuando hay necesidades de coordinación entre varios orígenes de datos (base de datos maestra, base de datos de usuario, etc). Los servicios de puerta de enlace implementan lo siguiente:
-   **Proxy de conexión TDS.** Esto incluye ubicar la base de datos de usuario en el clúster back-end, implementar la secuencia de inicio de sesión y, a continuación, reenviar los paquetes TDS al back-end y de vuelta.

-   **Administración de bases de datos.** Esto incluye la implementación de una colección de flujos de trabajo para realizar operaciones de base de datos CREATE, ALTER y DROP. Las operaciones de base de datos se pueden invocar mediante el rastreo de paquetes TDS o mediante API de OData explícitas.

-   Operaciones de usuario, de inicio de sesión, CREATE, ALTER y DROP

-   Operaciones de administración del servidor lógico a través de API de OData

![Aislamiento mediante topología de red](./media/azure-isolation/azure-isolation-fig12.png)

La capa situada detrás de las puertas de enlace se denomina "back-end". Ahí es donde se almacenan todos los datos en modo de alta disponibilidad. Cada parte de los datos se dice que pertenece a una "partición" o "unidad de conmutación por error", cada una de las cuales tiene al menos tres réplicas. El motor de SQL Server almacena y replica las réplicas, que se administran mediante un sistema de conmutación por error, habitualmente conocido como "tejido".

Por lo general, el sistema back-end no tiene comunicaciones salientes a otros sistemas como precaución de seguridad. Eso se reserva para los sistemas de la capa front-end (puerta de enlace). Las máquinas de la capa de puerta de enlace tienen privilegios limitados en las máquinas back-end para minimizar la superficie de ataque como un mecanismo de defensa en profundidad.

### <a name="isolation-by-machine-function-and-access"></a>Aislamiento por función y acceso de la máquina
SQL Azure se compone de servicios que se ejecutan en máquinas de funciones diferentes. SQL Azure se divide en la base de datos en la nube “back-end” y entornos “front-end” (puerta de enlace y administración), con el principio general de que el tráfico va hacia el sistema back-end pero no sale de él. El entorno front-end puede comunicarse con cualquier otro servicio exterior y, en general, solo tiene permisos limitados en el entorno back-end (los suficientes para llamar a los puntos de entrada que necesita invocar).

## <a name="networking-isolation"></a>Aislamiento de red
La implementación de Azure tiene varios niveles de aislamiento de red. El siguiente diagrama muestra los diferentes niveles de aislamiento de red que Azure proporciona a los clientes. Estos niveles son nativos en la plataforma de Azure y también son características definidas por el cliente. En la entrada desde Internet, DDoS de Azure proporciona aislamiento frente a ataques a gran escala contra Azure. En el siguiente nivel de aislamiento están las direcciones IP públicas (puntos de conexión) definidas por el cliente que se usan para determinar el tráfico que puede pasar desde el servicio en la nube a la red virtual. El aislamiento de la red virtual de Azure nativa garantiza un aislamiento completo de todas las demás redes y que el tráfico solo fluya a través de los métodos y las rutas de acceso configurados por el usuario. Estas rutas de acceso y métodos son el siguiente nivel, en el que se pueden usar NSG, UDR y dispositivos de red virtual para crear límites de aislamiento y así proteger las implementaciones de aplicaciones en la red protegida.

![Aislamiento de red](./media/azure-isolation/azure-isolation-fig13.png)

**Aislamiento del tráfico:** una [red virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) es el límite para aislamiento del tráfico en la plataforma Azure. Las máquinas virtuales de una red virtual no se pueden comunicar directamente con las máquinas virtuales de otra red virtual, incluso si las dos redes virtuales las creó el mismo cliente. El aislamiento consiste en una propiedad fundamental que garantiza que las máquinas virtuales del cliente y la comunicación sigan siendo privadas en una red virtual.

[Subred](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#subnets) ofrece un nivel de aislamiento adicional de la red virtual basado en un intervalo de direcciones IP. Direcciones IP en la red virtual, puede dividir una red virtual en varias subredes por organización y seguridad. Las instancias de rol de PaaS y máquinas virtuales implementadas en subredes (iguales o distintas) dentro de una red virtual pueden comunicarse entre sí sin ninguna configuración adicional. También puede configurar [grupos de seguridad de red (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#network-security-groups-nsg) para permitir o denegar el tráfico de red a una instancia de máquina virtual en función de las reglas configuradas en la lista de control de acceso (ACL) del NSG. Los NSG se pueden asociar con las subredes o las instancias individuales de máquina virtual dentro de esa subred. Cuando un NSG está asociado a una subred, las reglas de la ACL se aplican a todas las instancias de la máquina virtual de esa subred.

## <a name="next-steps"></a>Pasos siguientes

- [Opciones de aislamiento de red para máquinas Windows en redes virtuales de Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Esto incluye el escenario de front-end y back-end clásico en el que las máquinas de una determinada red o subred back-end pueden permitir conectarse solo a determinados clientes o equipos a un punto de conexión en particular, en función de una lista de direcciones IP autorizadas.

- [Aislamiento de proceso](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure proporciona diversos servicios de computación en la nube que incluyen una amplia selección de instancias y servicios de proceso que se pueden escalar vertical y horizontalmente de forma automática para satisfacer las necesidades de su aplicación o de su empresa.

- [Aislamiento del almacenamiento](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure separa la computación basada en máquinas virtuales de cliente del almacenamiento. Esta separación permite escalar la computación y el almacenamiento de forma independiente, facilitando así el multiinquilinato y el aislamiento. Por tanto, Azure Storage se ejecuta en hardware independiente sin conectividad de red con Azure Compute, excepto en el nivel lógico. Todas las peticiones se ejecutan sobre HTTP o HTTPS, a elección del cliente.

