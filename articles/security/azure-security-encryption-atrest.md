---
title: Cifrado en reposo de datos de Microsoft Azure | Microsoft Docs
description: "Este artículo proporciona información general sobre el cifrado en reposo de datos de Microsoft Azure, funcionalidades y consideraciones."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: b02afa77ce99f576fed76b398642ba3f3ce2ba98
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="azure-data-encryption-at-rest"></a>Cifrado en reposo de datos de Azure
Hay varias herramientas en Microsoft Azure para proteger los datos según las necesidades de seguridad y cumplimiento de su empresa. Este documento se centra en:
- Cómo se protegen los datos en reposo en Microsoft Azure.
- Describir los distintos componentes que forman parte de la implementación de protección de datos.
- Revisar las ventajas y desventajas de los distintos enfoques clave de protección de la administración. 

El cifrado en reposo es un requisito de seguridad habitual. Una ventaja de Microsoft Azure es que las organizaciones pueden lograr el cifrado en reposo sin tener el costo de implementación y administración y el riesgo de una solución de administración de claves personalizadas. Las organizaciones tienen la opción de permitir a Azure administrar completamente el cifrado en reposo. Además, las organizaciones tienen varias opciones para administrar con detenimiento el cifrado y las claves de cifrado.

## <a name="what-is-encryption-at-rest"></a>¿Qué es el cifrado en reposo?
El cifrado en reposo hace referencia al encoding criptográfico (cifrado) de datos cuando se conserva. Los diseños del cifrado en reposo de Azure utilizan cifrado asimétrico para cifrar o descifrar rápidamente grandes cantidades de datos según un modelo conceptual sencillo:

- Se usa una clave de cifrado simétrico para cifrar datos mientras se conservan 
- La misma clave de cifrado se utiliza para descifrar los datos tal y como se prepararon para su uso en la memoria
- Se pueden particionar datos y se pueden usar claves diferentes para cada partición
- Las claves deben almacenarse en una ubicación segura con directivas de control de acceso para limitar el acceso a determinadas identidades y registrar el uso de la clave. Las claves de cifrado de datos se cifran a menudo con el cifrado asimétrico para limitar aún más el acceso (se describe en *Jerarquía de la clave*, más adelante en este artículo)

Los pasos anteriores describen los elementos comunes de alto nivel de cifrado en reposo. En la práctica, los escenarios de control y administración de la clave, así como las convicciones de escala y disponibilidad, requieren construcciones adicionales. A continuación se describen los componentes y conceptos del cifrado en reposo de Microsoft Azure.

## <a name="the-purpose-of-encryption-at-rest"></a>Propósito del cifrado en reposo
El cifrado en reposo está diseñado para proporcionar protección de datos para datos en reposo (como se describe anteriormente). Los ataques contra los datos en reposo incluyen intentos de obtener acceso físico al hardware en el que se almacenan los datos y, a continuación, poner en peligro los datos contenidos. En este tipo de ataque, la unidad del disco duro de un servidor puede utilizarse de forma incorrecta durante el mantenimiento permitiendo a un atacante eliminar la unidad de disco duro. Más adelante el atacante tendría que poner el disco duro en un equipo bajo su control para intentar obtener acceso a los datos. 

El cifrado en reposo está diseñado para evitar que el atacante obtenga acceso a los datos sin cifrar asegurándose de que los datos se cifran en el disco. Si un atacante lograra obtener una unidad de disco duro con este tipo de datos cifrados y no tiene acceso a las claves del cifrado, el atacante no podría comprometer los datos fácilmente. En este escenario, un atacante tendría que intentar atacar a los datos cifrados que son mucho más complejos y consumen recursos que si tuviera acceso a los datos sin cifrar en un disco duro. Por este motivo, el cifrado en reposo es muy recomendable y es un requisito de alta prioridad para muchas organizaciones. 

En algunos casos, también se requiere el cifrado en reposo como necesidad de la organización de los esfuerzos de cumplimiento y gobernanza de datos. Las normas gubernamentales y del sector, como HIPAA, PCI y FedRAMP, diseñan las medidas de seguridad específicas a través de los requisitos de cifrado y la protección de datos. Para muchos de esos reglamentos, el cifrado en reposo es una medida obligatoria necesaria para la protección y administración de datos compatibles. 

Además de los requisitos de cumplimiento y regulatorios, el cifrado en reposo se debería percibir como una funcionalidad de la plataforma para defenderse en fondo. Mientras que Microsoft proporciona una plataforma compatible para los servicios, aplicaciones y datos, instalación completa y seguridad física, auditoría y control de acceso a los datos, es importante proporcionar medidas de seguridad "superpuesta" adicionales en caso de que se produzca un error en una de las otras medidas de seguridad. El cifrado en reposo proporciona un mecanismo de grupo defensivo adicional.

Microsoft se compromete a proporcionar opciones de cifrado de reposo para servicios en la nube y para proporcionar a los clientes una capacidad de administración adecuada de las claves de cifrado y acceso a los registros que muestran cuándo se usan las claves de cifrado. Además, Microsoft está trabajando para conseguir el objetivo de cifrar en reposo todos los datos de clientes de forma predeterminada.

## <a name="azure-encryption-at-rest-components"></a>Componentes del cifrado en reposo de Azure

Como se describe anteriormente, el objetivo del cifrado en reposo es que los datos que se guardan en el disco se cifren con una clave de cifrado secreta. Para lograr la creación de una clave segura para el objetivo, se debe proporcionar almacenamiento, control del acceso y administración de las claves del cifrado. Aunque los detalles pueden variar, las implementaciones del cifrado en reposo de los servicio de Azure se pueden describir en cuanto a los siguientes conceptos que, a continuación, se ilustran en el diagrama.

![Componentes](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

La ubicación del almacenamiento de las claves de cifrado y el control de acceso a esas claves es fundamental para un modelo de cifrado en reposo. Las claves deben ser muy seguras pero fáciles de administrar por parte de los usuarios especificados y deben estar disponibles para servicios concretos. Para los servicios de Azure, Azure Key Vault es la solución de almacenamiento de claves recomendada y proporciona una experiencia de administración habitual en los servicios. Las claves se almacenan y administran en los almacenes de claves, y se puede proporcionar acceso a Key Vault a los usuarios o servicios. Azure Key Vault admite la creación del cliente de claves o importación de claves de cliente para su uso en escenarios de clave de cifrado administrada por el cliente.

### <a name="azure-active-directory"></a>Azure Active Directory

Los permisos para usar las claves almacenadas en Azure Key Vault, además de para administrar o tener acceso a ellas para el cifrado en reposo y el descifrado, se pueden dar a las cuentas de Azure Active Directory. 

### <a name="key-hierarchy"></a>Jerarquía de las claves

Se usa más de una clave de cifrado en una implementación de cifrado en reposo. El cifrado asimétrico es útil para establecer la confianza y la autenticación necesarias para la administración y acceso a la clave. El cifrado simétrico es más eficaz para el cifrado masivo y descifrado, lo que permite un cifrado más seguro y un mejor rendimiento. Además, si se limita el uso de una clave de cifrado única, se reduce el riesgo de que la clave se encuentre en peligro y el costo de volver a cifrar cuando se debe reemplazar una clave. Para sacar provecho de las ventajas del cifrado simétrico y asimétrico y limitar el uso y la exposición de una clave única, los modelos del cifrados en reposo de Azure usan una jerarquía de claves que se compone de los siguientes tipos de claves:

- **Clave de cifrado de datos (DEK)**: Una clave simétrica AES256 usada para cifrar una partición o un bloque de datos.  Un único recurso puede tener muchas particiones y muchas claves de cifrado de datos. Cifrar cada bloque de datos con una clave diferente dificulta los ataques de análisis criptográficos. Se necesita acceso a las DEK por la instancia de proveedor o aplicación de recursos que cifra y descifra un bloque específico. Cuando se reemplaza una DEK con una nueva clave, solo se deben volver a cifrar los datos de su bloque asociado con una nueva clave.
- **Clave de cifrado de claves (KEK)**: Una clave de cifrado asimétrico utilizada para cifrar las claves de cifrado de datos. El uso de una clave de cifrado de clave permite a las propias claves de cifrado de datos cifrarse y controlarse. La entidad que tiene acceso a la KEK puede ser diferente de la entidad que requiere la DEK. Esto permite que una entidad para intermediar el acceso a la DEK con el fin de garantizar el acceso limitado de cada DEK a una partición específica. Puesto que la KEK es necesaria para descrifrar la DEK, la KEK es de manera eficaz un punto único por el que se pueden eliminar de forma eficaz las DEK mediante la eliminación de la KEK.

Las claves de cifrado de datos cifradas con las claves de cifrado de clave se almacenan por separado y solo una entidad con acceso a la clave de cifrado de clave puede obtener las claves de cifrado de datos cifrados con dicha clave. Se admiten diferentes modelos de almacenamiento de claves. Analizaremos cada modelo con más detalle más adelante en la sección siguiente.

## <a name="data-encryption-models"></a>Modelos de cifrado de datos

La comprensión de los distintos modelos de cifrado y sus ventajas y desventajas es fundamental para entender cómo los distintos proveedores de recursos en Azure implementan el cifrado en reposo. Estas definiciones se comparten entre todos los proveedores de recursos en Azure para asegurar la taxonomía y el idioma común. 

Hay tres escenarios para el cifrado del lado servidor:

- Cifrado del lado servidor mediante claves administradas del servicio
    - Los proveedores de recursos de Azure realizan las operaciones de cifrado y descifrado
    - Microsoft administra las claves
    - Funcionalidad de nube completa

- Cifrado del lado servidor mediante claves administradas por el cliente en Azure Key Vault
    - Los proveedores de recursos de Azure realizan las operaciones de cifrado y descifrado
    - El cliente controla las claves mediante Azure Key Vault
    - Funcionalidad de nube completa

- Cifrado del lado servidor mediante claves administradas por el cliente en el hardware controlado por el cliente
    - Los proveedores de recursos de Azure realizan las operaciones de cifrado y descifrado
    - Claves de controles de cliente en el hardware controlado por el cliente
    - Funcionalidad de nube completa

Para el cifrado del lado cliente, tenga en cuenta lo siguiente:

- Los servicios de Azure no pueden ver los datos descifrados
- Los clientes administran y almacenan las claves en ubicaciones locales (o en otras ubicaciones seguras). Las claves no están disponibles para los servicios de Azure
- Funcionalidad de nube reducida

Los modelos de cifrado admitidos en Azure se dividen en dos grupos principales: "cifrado del cliente" y "cifrado del lado servidor", como se mencionó anteriormente. Tenga en cuenta que, independientemente del modelo de cifrado en reposo utilizado, los servicios de Azure siempre recomiendan el uso de un transporte seguro como TLS o HTTPS. Por lo tanto, el cifrado de transporte debe tratarse con el protocolo de transporte y no debe ser un factor importante para determinar qué modelo de cifrado en reposo se utilizará.

### <a name="client-encryption-model"></a>Modelo de cifrado del cliente

El modelo de cifrado del cliente hace referencia al cifrado que se realiza fuera del proveedor de recursos o Azure mediante el servicio o la aplicación que realiza la llamada. El cifrado puede realizarse mediante la aplicación de servicio de Azure o por una aplicación que se ejecuta en el centro de datos del cliente. En cualquier caso, cuando se saca provecho de este modelo de cifrado, el proveedor de recursos de Azure recibe un blob cifrado de datos sin la capacidad de descifrar los datos de ninguna forma ni tener acceso a las claves de cifrado. En este modelo, la administración de claves se realiza mediante el servicio o aplicación que realiza la llamada y es opaca para el servicio de Azure.

![Cliente](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modelo de cifrado del lado servidor

Los modelos de cifrado del lado servidor hacen referencia al cifrado que se realiza mediante el servicio de Azure. En este modelo, el proveedor de recursos realiza las operaciones de cifrado y descifrado. Por ejemplo, Azure Storage puede recibir datos en las operaciones de texto sin formato y llevará a cabo el cifrado y descifrado internamente. El proveedor de recursos podría utilizar claves de cifrado que están administradas por Microsoft o por el cliente en función de la configuración proporcionada. 

![Server](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modelos de administración de claves de cifrado del lado servidor

Cada uno de los modelos de cifrado en reposo del lado servidor implica características distintivas de administración de claves. Esto incluye dónde y cómo se crean y almacenan las claves de cifrado, así como los modelos de acceso y los procedimientos de rotación de claves. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Cifrado del lado servidor mediante claves administradas del servicio

Para muchos clientes, el requisito esencial es asegurarse de que los datos se cifran siempre que estén en reposo. El cifrado del lado servidor mediante las claves administradas del servicio habilita este modelo al permitir a los clientes marcar el recurso específico (cuenta de almacenamiento, SQL Database, etc.) para el cifrado y dejar todos los aspectos de la administración de claves, como el backup, rotación o emisión de claves a Microsoft. La mayoría de los servicios de Azure que admiten cifrado en reposo normalmente admiten este modelo de descarga de la administración de las claves de cifrado de Azure. El proveedor de recursos de Azure crea las claves, las coloca en un almacenamiento seguro y las recupera cuando es necesario. Esto significa que el servicio tiene acceso completo a las claves y el servicio tiene control total sobre la administración del ciclo de vida de las credenciales.

![administrado](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Por lo tanto, el cifrado del lado servidor mediante las claves administradas del servicio satisface rápidamente la necesidad de que tengan el cifrado en reposo con poca sobrecarga al cliente. Cuando esté disponible, un cliente abrirá con normalidad el Azure portal para la suscripción de destino y el proveedor de recursos y comprobará un cuadro que indica si desearía que los datos se cifraran. El cifrado del lado servidor de algunas instancias de Resource Manager con las claves administradas del servicio se encuentra activado de forma predeterminada. 

El cifrado del lado servidor con las claves de Microsoft administradas implica que el servicio tiene acceso completo para almacenar y administra las claves. Aunque algunos clientes podrían desear administrar las claves porque creen que pueden garantizar una mayor seguridad, se deben tener en cuenta los costos y riesgos asociados a una solución de almacenamiento de claves personalizadas al evaluar este modelo. En muchos casos, una organización podría determinar que los riesgos o restricciones de recursos de una solución local pueden ser mayores que el riesgo de administración en la nube de las claves de cifrado en reposo.  Sin embargo, este modelo podría no ser suficiente para las organizaciones que tienen requisitos para controlar la creación o el ciclo de vida de las claves de cifrado o tener personal diferente para administrar las claves de cifrado de un servicio al que administra el servicio (es decir, la segregación de administración de claves de todo el modelo de administración para el servicio).

##### <a name="key-access"></a>Acceso a la clave

Cuando se usa el cifrado del lado servidor con las claves administradas del servicio, el servicio administra la creación de claves, el almacenamiento y el acceso al servicio. Normalmente, los proveedores fundamentales de recursos de Azure almacenarán las claves de cifrado de datos en un almacén que se encuentra cerca de los datos y está rápidamente disponible y accesible mientras las claves de cifrado de clave se almacenan en un almacén interno seguro.

**Ventajas**

- Instalación simple
- Microsoft administra la redundancia, el backup y la rotación de claves
- El cliente no tiene el costo asociado con la implementación o el riesgo de un esquema personalizado de administración de claves.

**Desventajas**

- No hay control al cliente sobre las claves de cifrado (revocación, ciclo de vida, especificación de clave, etc.)
- La administración de la clave no se puede separar del modelo de administración global para el servicio

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Cifrado del lado servidor mediante claves administradas por el cliente en Azure Key Vault 

Para escenarios donde el requisito es cifrar los datos en reposo y controlar los clientes de las claves de cifrado, los clientes pueden usar el cifrado de lado servidor mediante las claves almacenadas por el cliente en Key Vault. Algunos servicios solo pueden almacenar la clave de cifrado de clave de raíz en Azure Key Vault y almacenar la clave de cifrado de datos cifrada en una ubicación interna cercana a los datos. En este escenario, los clientes pueden aportar sus propias claves a Key Vault (BYOK: aportar su propia clave), o generar nuevas y usarlas para cifrar los recursos deseados. Mientras que el proveedor de recursos realiza las operaciones de cifrado y descifrado, usa la clave configurada como clave raíz para todas las operaciones de cifrado. 

##### <a name="key-access"></a>Acceso a la clave

El modelo de cifrado del lado servidor con claves administradas del cliente en Azure Key Vault implica el servicio de acceso a las claves para cifrar y descifrar según sea necesario. Las claves del cifrado en reposo son accesibles para un servicio a través de una directiva de control de acceso. Esta directiva concede el acceso de identidad de servicio para recibir la clave. Un servicio de Azure que se ejecuta en nombre de una suscripción asociada puede configurarse con una identidad dentro de esa suscripción. El servicio puede realizar la autenticación de Azure Active Directory y recibir un token de autenticación que se identifica como el servicio que actúa en nombre de la suscripción. A continuación, se puede presentar ese token al Key Vault para obtener una clave a la que se le haya dado acceso.

Para las operaciones con claves de cifrado, una identidad de servicio puede tener acceso a cualquiera de las siguientes operaciones: descifrar, cifrar, unwrapKey, wrapKey, comprobar, iniciar sesión, obtener, enumerar, actualizar, crear, importar, eliminar, backup y restaurar.

Para obtener una clave para usar al cifrar o descifrar datos en reposo, la identidad de servicio con la que se ejecutará la instancia de Resource Manager debe tener UnwrapKey (para obtener la clave de descifrado) y WrapKey (para insertar una clave en el almacén de claves al crear una nueva clave).


>[!NOTE] 
>Para obtener más detalles sobre la autorización del Key Vault, vea la protección de la página del almacén de claves en la [documentación de Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Ventajas**

- Control total sobre las claves usadas: Las claves de cifrado se administran en Key Vault del cliente bajo el control del cliente.
- Capacidad de cifrar varios servicios en un patrón
- Puede separar la administración de la clave del modelo de administración global para el servicio
- Puede definir el servicio y la ubicación de la clave en regiones

**Desventajas**

- El cliente tiene responsabilidad total para la administración de acceso a las claves
- El cliente tiene responsabilidad total para la administración del ciclo de vida de las claves
- Sobrecarga de configuración e instalación adicional

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>El cifrado del lado servidor mediante claves administradas del servicio en el hardware controlado por el cliente

Para escenarios donde el requisito es cifrar los datos en reposo y administrar las claves en un repositorio propietario fuera del control de Microsoft, algunos servicios de Azure habilitan el modelo de administración de claves Host Your Own Key (HYOK). En este modelo, el servicio debe recuperar la clave de un sitio externo y, por tanto, se ven afectadas las garantías de rendimiento y disponibilidad y la configuración es más compleja. Además, puesto que el servicio tiene acceso a la DEK durante las operaciones de cifrado y descifrado de las garantías de seguridad general de este modelo son similares a cuando las claves son administradas en Azure Key Vault por el cliente.  Como resultado, este modelo no es adecuado para la mayoría de las organizaciones a menos que tengan requisitos específicos de administración de claves que se necesiten. Debido a estas limitaciones, la mayoría de los servicios de Azure no admiten el cifrado del lado del servidor mediante claves de servidor administradas en el hardware controlado del cliente.

##### <a name="key-access"></a>Acceso a la clave

Cuando se usa el cifrado del lado servidor mediante las claves administradas del servicio en el hardware controlado del cliente, las claves se mantienen en un sistema configurado por el cliente. Los servicios de Azure que admiten este modelo proporcionan un medio para establecer una conexión segura en un almacén de claves proporcionado por el cliente.

**Ventajas**

- Control total sobre la clave raíz usada: una tienda proporcionada por el un cliente administra las claves de cifrado
- Capacidad de cifrar varios servicios en un patrón
- Puede separar la administración de la clave del modelo de administración global para el servicio
- Puede definir el servicio y la ubicación de la clave en regiones

**Desventajas**

- Responsabilidad total para la disponibilidad, rendimiento, seguridad y almacenamiento de claves
- Responsabilidad total para la administración de acceso a las claves
- Responsabilidad total para la administración del ciclo de vida de las claves
- Costos significativos de mantenimiento en curso, configuración y configuración
- Dependencia aumentada sobre la disponibilidad de la red entre el centro de datos del cliente y los centros de datos de Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Cifrado en reposo en servicios en la nube de Microsoft

Los servicios en la nube de Microsoft se utilizan en los tres modelos de la nube: IaaS, PaaS, SaaS. A continuación encontrará ejemplos de cómo encajan en cada modelo:

- Servicios de software, que se conocen como software como servicio o SaaS, que tienen la aplicación proporcionada por la nube, como Office 365.
- Servicios de la plataforma de los que los clientes sacan provecho en la nube en sus aplicaciones, que usan la nube para tareas como almacenamiento, análisis y funcionalidad de bus de servicio.
- Servicios de infraestructura o infraestructura como servicio (IaaS) en la que el cliente implementa sistemas operativos y aplicaciones que se hospedan en la nube y, posiblemente, saca provecho de otros servicios en la nube.

### <a name="encryption-at-rest-for-saas-customers"></a>Cifrado en reposo para clientes de SaaS

Los clientes del software como servicio (SaaS) suelen tener el cifrado en reposo habilitado o disponible en cada servicio. Office 365 dispone de varias opciones para que los clientes comprueben o habiliten el cifrado en reposo. Para obtener información acerca de los servicios de Office 365, vea las tecnologías de cifrado de datos para Office 365.

### <a name="encryption-at-rest-for-paas-customers"></a>Cifrado en reposo para clientes PaaS

Los datos del cliente de la plataforma como servicio (PaaS) normalmente residen en un entorno de ejecución de la aplicación y en cualquier proveedor de recursos de Azure utilizado para almacenar los datos del cliente. Para ver las opciones disponibles del cifrado en reposo, examine la tabla siguiente para las plataformas de aplicación y almacenamiento que utiliza. Si se admite, se proporcionan vínculos a instrucciones sobre cómo habilitar el cifrado en reposo para cada proveedor de recursos. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Cifrado en reposo para clientes de IaaS

Los clientes de la infraestructura como servicio (IaaS) pueden tener una variedad de servicios y aplicaciones en uso. Los servicios de IaaS pueden habilitar el cifrado en reposo en sus discos duros virtuales y máquinas virtuales que se hospedan en Azure mediante Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Almacenamiento cifrado

Al igual que PaaS, las soluciones IaaS pueden sacar provecho de otros servicios de Azure que almacenan los datos que se cifran en reposo. En estos casos, puede habilitar el cifrado en el soporte del cifrado en reposo como proporciona cada servicio consumido de Azure. La siguiente tabla enumera las principales plataformas de aplicación, servicios y almacenamiento y el modelo de cifrado en reposo admitido. Si se admite, se proporcionan vínculos a instrucciones sobre cómo habilitar el cifrado en reposo. 

#### <a name="encrypted-compute"></a>Compute de cifrado

Una solución de cifrado en reposo completo en la requiere que los datos nunca se guarden en formato descifrado. Mientras está en uso en un servidor que carga los datos en la memoria, los datos pueden conservarse localmente de varias maneras, incluido el archivo de paginación de Windows, un volcado de memoria y cualquier registro que la aplicación pueda realizar. Para asegurarse de que estos datos se cifran en reposo, las aplicaciones IaaS pueden usar Azure Disk Encryption en una máquina virtual de IaaS de Azure (Windows o Linux) y un disco virtual. 

#### <a name="custom-encryption-at-rest"></a>Cifrado de datos en reposo personalizado

Se recomienda que siempre que sea posible, las aplicaciones IaaS saquen provecho de las opciones de cifrado en reposo y Azure Disk Encryption proporcionadas por los servicios de Azure consumidos. En algunos casos, como requisitos de cifrado irregulares o almacenamiento que no se basa en Azure, un desarrollador de una aplicación de IaaS podría necesitar implementar el cifrado en reposo. Las soluciones de los desarrolladores de IaaS podrían integrarse mejor con las expectativas de administración y del cliente de Azure mediante el aprovechamiento de ciertos componentes de Azure. En concreto, los desarrolladores deben usar el servicio Azure Key Vault para proporcionar almacenamiento seguro de claves, así como proporcionar a sus clientes opciones de administración de claves coherentes con la mayoría de los servicios de la plataforma de Azure. Además, las soluciones personalizadas deben usar identidades de servicio administradas de Azure para habilitar las cuentas de servicio para tener acceso a las claves de cifrado. Para encontrar información para desarrolladores sobre Azure Key Vault y las identidades de servicio administradas, consulte sus respectivos SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Compatibilidad con modelo de cifrado de proveedores de recursos de Azure

Los servicios de Microsoft Azure admitir uno o más modelos de cifrado en reposo. Para algunos servicios, sin embargo, podrían no ser aplicables uno o varios de los modelos de cifrado. Además, los servicios pueden liberar compatibilidad para estos escenarios en distintas programaciones. Esta sección describe el soporte del cifrado en reposo en el momento de redactar este artículo para cada uno de los servicios de almacenamiento de datos principales de Azure.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Cualquier cliente mediante las características de la infraestructura de Azure como servicio (IaaS) puede lograr el cifrado en reposo para sus discos y máquinas virtuales de IaaS y discos mediante Azure Disk Encryption. Para más información sobre Azure Disk Encryption, vea la [Documentación de Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Almacenamiento de Azure

Azure Blob y Archivo admiten el cifrado en reposo para escenarios cifrados del lado servidor, así como datos cifrados del cliente (cifrado del lado cliente).

- Lado servidor: Los clientes que usan Azure Blob Storage pueden habilitar el cifrado en reposo en cada cuenta de recursos de Azure Storage. Una vez habilitado el lado servidor, el cifrado se realiza de forma transparente en la aplicación. Para más información, consulte [Cifrado del servicio Azure Storage para datos en reposo](https://docs.microsoft.com/azure/storage/storage-service-encryption).
- Lado cliente: Se admite el cifrado del lado cliente de Azure Blobs. Cuando se usa el cifrado del lado cliente, los clientes cifran los datos y los cargan como un blob cifrado. El cliente se encarga de la administración de claves. Consulte [Client-Side Encryption and Azure Key Vault for Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) (Cifrado del lado cliente y Azure Key Vault para Microsoft Azure Storage) para más información.


#### <a name="sql-azure"></a>SQL Azure

SQL Azure admite actualmente el cifrado en reposo para escenarios de cifrado en el lado cliente y en el lado servicio administrados por Microsoft.

Actualmente, la compatibilidad con cifrado del servidor se proporciona a través de la característica SQL denominada cifrado de datos transparente. Una vez que un cliente de SQL Azure habilita la clave TDE se crea y administra automáticamente para él. El cifrado en reposo puede habilitarse en los niveles de base de datos y servidor. A partir de junio de 2017, el [cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) se habilitará de forma predeterminada en las bases de datos recién creadas.

Se admite el cifrado del lado cliente de los datos de SQL Azure a través de la característica [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Always Encrypted utiliza una clave que el cliente crea y almacena. Los clientes pueden almacenar la clave maestra en el almacén de certificados de Windows, Azure Key Vault, o un módulo de seguridad de hardware. Al usar SQL Server Management Studio, los usuarios de SQL eligen qué clave que les gustaría usar para cifrar cada columna.

|                                  |                |                     | **Modelo de cifrado**             |                              |        |
|----------------------------------|----------------|---------------------|------------------------------|------------------------------|--------|
|                                  |                |                     |                              |                              | **Cliente** |
|                                  | **Administración de claves** | **Clave administrada del servicio** | **Cliente administrado en Key Vault** | **Cliente administrado en local** |        |
| **Almacenamiento y bases de datos**            |                |                     |                              |                              |        |
| Disco (IaaS)                      |                | -                   | Sí                          | Sí*                         | -      |
| SQL Server (IaaS)                |                | Sí                 | Sí                          | Sí                          | Sí    |
| SQL Azure (PaaS)                 |                | Sí                 | Vista previa                      | -                            | Sí    |
| Azure Storage (blobs en bloques o en páginas) |                | Sí                 | Vista previa                      | -                            | Sí    |
| Azure Storage (archivos)            |                | Sí                 | -                            | -                            | -      |
| Azure Storage (tablas, colas)   |                | -                   | -                            | -                            | Sí    |
| Cosmos DB (documento DB)          |                | Sí                 | -                            | -                            | -      |
| StorSimple                       |                | Sí                 | -                            | -                            | Sí    |
| Backup                           |                | -                   | -                            | -                            | Sí    |
| **Inteligencia y análisis**       |                |                     |                              |                              |        |
| Azure Data Factory               |                | Sí                 | -                            | -                            | -      |
| Azure Machine Learning           |                | -                   | Vista previa                      | -                            | -      |
| Azure Stream Analytics           |                | Sí                 | -                            | -                            | -      |
| HDInsights (Azure Blob Storage)  |                | Sí                 | -                            | -                            | -      |
| HDInsights (Data Lake Storage)   |                | Sí                 | -                            | -                            | -      |
| Almacén de Azure Data Lake            |                | Sí                 | Sí                          | -                            | -      |
| Azure Data Catalog               |                | Sí                 | -                            | -                            | -      |
| Power BI                         |                | Sí                 | -                            | -                            | -      |
| **Servicios IoT**                     |                |                     |                              |                              |        |
| IoT Hub                          |                | -                   | -                            | -                            | Sí    |
| Azure Service Bus                      |                | Sí (nivel Premium)              | -                            | -                            | Sí    |
| Event Hubs                       |                | Sí             | -                            | -                            | -      |


## <a name="conclusion"></a>Conclusión

La protección de datos del cliente almacenados dentro de los servicios de Azure es de gran importancia para Microsoft. Todos los servicios hospedados en Azure se comprometen a proporcionar opciones de cifrado en reposo. Los servicios fundamentales como el almacenamiento Azure Storage, SQL Azure y análisis e inteligencia de las claves proporcionan ya opciones de cifrado en reposo. Algunos de estos servicios admiten claves de cliente controlada y cifrado en el cliente, así como cifrado y claves administradas del servicio. Los servicios de Microsoft Azure están mejorando ampliamente la disponibilidad del cifrado en reposo y se planean nuevas opciones para la versión preliminar y la versión de disponibilidad general en los próximos meses.

