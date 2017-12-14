---
title: Sistema de gobierno en Azure | Microsoft Docs
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
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 875b78f6af330aea3333b045ee72e9632e9aa8a6
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="governance-in-azure"></a>Sistema de gobierno en Azure

Sabemos que la seguridad tiene la máxima prioridad en la nube y conocemos la importancia que tiene que buscar información exacta y a tiempo sobre la seguridad de Azure. Una de las mejores razones para usar Azure en sus aplicaciones y servicios es poder aprovechar su amplia gama de funcionalidades y herramientas de seguridad. Estas herramientas y funcionalidades permiten crear soluciones seguras en la plataforma Azure segura.

Para ayudarlo a comprender mejor la matriz de controles de gobierno implementados en Microsoft Azure desde la perspectiva del cliente y de las operaciones de Microsoft, este artículo, "Sistema de gobierno en Azure", se ha redactado para proporcionar información completa sobre las características de gobierno disponibles con Microsoft Azure.

## <a name="azure-platform"></a>Plataforma Azure

Azure es una plataforma de servicios en la nube pública que admite una amplia variedad de sistemas operativos, lenguajes de programación, marcos, herramientas, bases de datos y dispositivos. Se pueden ejecutar contenedores de Linux con integración de Docker, compilar aplicaciones con JavaScript, Python, .NET, PHP, Java y Node.js, y crear back-ends para dispositivos iOS, Android y Windows. Los servicios en la nube pública de Azure admiten las mismas tecnologías en las que ya confían millones de desarrolladores y profesionales de TI.

Cuando elige un proveedor de servicios en la nube pública para crear o migrar recursos de TI, confía en las capacidades de la organización para proteger sus aplicaciones y datos con los servicios y los controles que facilitan para administrar la seguridad de los recursos basados en la nube.

El diseño de la infraestructura de Azure comprende desde la instalación hasta las aplicaciones para así poder hospedar millones de clientes simultáneamente. Además, proporciona una base de confianza en la que las empresas pueden satisfacer sus requisitos de seguridad. Además, Azure ofrece muchas opciones de seguridad y la capacidad de controlarlas, por lo que puede personalizar la seguridad para satisfacer los requisitos exclusivos de las implementaciones de su organización.

En este documento se explica cómo las funcionalidades de gobierno de Azure pueden ayudarlo a cumplir estos requisitos.

## <a name="abstract"></a>Descripción breve

El sistema de gobierno en la nube de Microsoft Azure proporciona un enfoque de auditoría y consultoría integradas que tiene como objetivo revisar el uso que las organizaciones hacen de la plataforma de Azure y asesorarlas. Con las funcionalidades de gobierno en la nube de Microsoft Azure nos referimos a los procesos de toma de decisiones, los criterios y las directivas implicadas en el planeamiento, la arquitectura, la adquisición, la implementación, el uso y la administración de un entorno de informática en la nube.

Para crear un plan de sistema de gobierno en la nube de Microsoft Azure, debe analizar en profundidad las personas, los procesos y las tecnologías que hay actualmente. Después, tiene que crear una serie de marcos de trabajo para que el equipo de TI pueda responder con facilidad y coherencia a las necesidades empresariales dotando al mismo tiempo a los usuarios finales de la flexibilidad necesaria para utilizar las eficaces características de Microsoft Azure.

En este artículo se describe cómo puede lograr un nivel elevado de gobierno de los recursos de TI en Microsoft Azure. Asimismo, puede ayudarlo a entender las características de seguridad y gobierno integradas en Microsoft Azure.

Estos son los principales problemas de gobierno que trataremos en este documento:

- Implementación de directivas, procesos y procedimientos según los objetivos de la organización

- Seguridad y cumplimiento continuo con las normas de las organizaciones

- Supervisión y alertas

## <a name="implementation-of-policies-processes-and-procedures"></a>Implementación de directivas, procesos y procedimientos 

El departamento de administración de Azure ha establecido roles y responsabilidades para supervisar la implementación de la directiva de seguridad de la información y la continuidad de las operaciones mediante Azure. Asimismo, es responsable de supervisar la seguridad y los procedimientos de continuidad en sus respectivos equipos (incluidos los terceros), así como de facilitar el cumplimiento con las directivas de seguridad, los procesos y las normas.

Estos son los factores que se han mejorado:

- Account Provisioning (Aprovisionamiento de cuentas)

- Controles de suscripción

- Control de acceso basado en rol

- Administración de recursos

- Seguimiento de recursos

- Control de recursos críticos

- Acceso de las API a la información de facturación

- Controles de red

## <a name="account-provisioning"></a>Aprovisionamiento de cuentas

Definir la jerarquía de las cuentas, además de ser la estructura de gobierno básica, constituye un paso importante para usar y estructurar los servicios de Azure en una compañía. Los clientes con el contrato Enterprise pueden subdividir el entorno en departamentos, cuentas y, finalmente, suscripciones.

![Account Provisioning (Aprovisionamiento de cuentas)](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Si no tiene un contrato Enterprise, considere la posibilidad de usar las [etiquetas Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) en el nivel de suscripción para definir la jerarquía. Una suscripción de Azure es la unidad básica donde se encuentran todos los recursos. También define varios límites en Azure, como el número de núcleos, recursos, etc. Las suscripciones pueden contener [grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), que pueden incluir recursos. En estos tres niveles se aplican los principios de [RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control).

Cada empresa es diferente. Además, la jerarquía que utiliza las etiquetas de Azure para los clientes sin un contrato Enterprise permite una gran flexibilidad en lo que respecta a cómo se organiza Azure en la compañía. Antes de implementar recursos en Microsoft Azure, debe modelar la jerarquía y comprender el acceso a los recursos, la complejidad y el impacto que tiene en la facturación.

## <a name="subscription-controls"></a>Controles de suscripción

La suscripción controla cómo se notifica y factura el uso de los recursos. Las suscripciones pueden estar configuradas para que la facturación y los pagos sean independientes. Como mencionamos anteriormente, en una cuenta de Azure podemos tenemos varias suscripciones. Las suscripciones se pueden utilizar para determinar el uso de los recursos de Azure de varios departamentos de una compañía.

Por ejemplo, una compañía tiene departamentos de TI, de recursos humanos y de marketing con diferentes proyectos en marcha. Según el uso que se haga de los recursos de Azure, como las máquinas virtuales de cada departamento, podremos facturarlos en consecuencia. De este modo, se pueden controlar las finanzas de cada departamento.

Las suscripciones de Azure establecen tres parámetros:

- Un identificador de suscriptor único

- Una ubicación de facturación

- Conjunto de recursos disponibles

Para una persona, incluiría el identificador de una cuenta Microsoft, un número de tarjeta de crédito y el conjunto completo de servicios de Azure, aunque Microsoft aplica límites de consumo en función del tipo de suscripción.

Las jerarquías de inscripción Azure definen cómo se estructuran los servicios en un contrato Enterprise. Enterprise Portal permite a los clientes dividir el acceso a los recursos de Azure asociados con un contrato Enterprise según jerarquías flexibles personalizables para responder a las necesidades únicas de una organización. El patrón de la jerarquía debe coincidir con la estructura geográfica y de administración de una organización para que se pueda tener en cuenta de forma precisa el acceso a los recursos y las facturas asociadas.

Los tres patrones de alto nivel son el funcional, el de unidad de negocio y el geográfico, y los departamentos se emplean con fines administrativos para realizar las agrupaciones de cuentas. Dentro de cada departamento, las cuentas pueden tener suscripciones asignadas, con lo que se generan silos de facturación y varios límites importantes en Azure (por ejemplo, número de máquinas virtuales, cuentas de almacenamiento, etc.).

![Controles de suscripción](./media/governance-in-azure/security-governance-in-azure-fig2.png)


En lo que respecta a las organizaciones con un contrato Enterprise, las suscripciones de Azure siguen una jerarquía de cuatro niveles:

- Administrador de inscripciones de la empresa

- Administrador de departamentos

- Propietario de cuenta

- Administrador de servicios

Esta jerarquía controla lo siguiente:

- Relación de facturación

- Administración de cuentas

- Control de acceso basado en rol (RBAC) a los artefactos

- Límites

- Límites

  - Uso y facturación (lista de tarifas basada en números de oferta)

  - límites

  - Virtual Network

- Recursos conectados a una cuenta de Azure Active Directory (debe estar asociada a varias suscripciones)

- Recursos asociados a una cuenta de inscripción empresarial

## <a name="role-based-access-controls"></a>Controles de acceso basado en rol

Cuando se publicó inicialmente Azure, los controles de acceso a una suscripción eran básicos: Administrador o Coadministrador. Acceder una suscripción del modelo clásico implicaba hacerlo a todos los recursos del portal. Esta falta de un control específico provocó una proliferación de suscripciones que proporcionaban un nivel de control de acceso razonable a una inscripción de Azure.

![Controles de acceso basado en rol](./media/governance-in-azure/security-governance-in-azure-fig3.png)

Esta proliferación de suscripciones ya no es necesaria. Gracias al control de acceso basado en rol, puede asignar usuarios a roles estándares (por ejemplo, los tipos de roles comunes de lectura y escritura). También se pueden definir reglas personalizadas.

El [control de acceso basado en rol (RBAC) de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) permite realizar una administración detallada del acceso en Azure. Con RBAC, puede conceder únicamente el grado de acceso que los usuarios necesiten para realizar sus trabajos. Las empresas de seguridad deben centrarse en conceder a los empleados los permisos exactos que necesiten. Un número elevado de permisos provocará que la cuenta esté expuesta a los atacantes. Si se conceden muy pocos, los empleados no podrán realizar su trabajo de manera eficaz. Gracias al control de acceso basado en roles (RBAC) de Azure, podrá abordar este problema, ya que es posible realizar una administración avanzada del acceso para Azure. RBAC ayuda a repartir las tareas entre el equipo y a conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo. En lugar de proporcionar a todos los empleados permisos no restringidos en los recursos o la suscripción de Azure, puede permitir solo determinadas acciones.

Por ejemplo, utilice RBAC para dejar que un empleado administre máquinas virtuales en una suscripción, y que otro pueda administrar bases de datos SQL en la misma suscripción.

RBAC de Azure cuenta con tres roles básicos que se aplican a todos los tipos de recurso:

- **propietario** tiene acceso completo a todos los recursos y cuenta con el derecho a delegar este acceso a otros.

- **Colaborador**: puede crear y administrar todos los tipos de recursos de Azure, pero no puede conceder acceso a otros.

- **lector** solo puede ver los recursos existentes de Azure.

El resto de los roles RBAC de Azure permiten la administración de recursos específicos de Azure. Por ejemplo, el rol de colaborador de máquina virtual permite al usuario crear y administrar máquinas virtuales. No otorga acceso a la red virtual ni a la subred a las que se conecta la máquina virtual.

[Roles RBAC integrados](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) muestra los roles disponibles en Azure. Especifica las operaciones y el ámbito de cada rol integrado que se concede a los usuarios.

Conceda acceso asignando el rol RBAC adecuado a usuarios, grupos y aplicaciones de un determinado ámbito. El ámbito de una asignación de roles puede ser una suscripción, un grupo de recursos o un único recurso. Un rol asignado en un ámbito principal también concede acceso a los elementos secundarios dentro del mismo.

Por ejemplo, un usuario con acceso a un grupo de recursos puede administrar todos los recursos que contiene, como sitios web, máquinas virtuales y subredes.

RBAC de Azure solo es compatible con operaciones de administración de los recursos de Azure del Portal de Azure y de las API de Azure Resource Manager. No todas las operaciones de nivel de datos para recursos de Azure pueden autorizarse. Por ejemplo, puede autorizar a un empleado que administre las cuentas de almacenamiento, pero no los blobs o las tablas de una cuenta de almacenamiento. De igual forma, una base de datos SQL se puede administrar, pero no las tablas que contiene.

Si desea más detalles sobre cómo RBAC ayuda a administrar el acceso, consulte [¿Qué es el control de acceso basado en rol?](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)

También puede [crear un rol personalizado](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) en RBAC de Azure si ninguno de los roles integrados responde a sus necesidades de acceso específicas. Se pueden crear roles personalizados con [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), la [interfaz de la línea de comandos (CLI) de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli) y la [API de REST](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Igual que los roles integrados, los roles personalizados pueden asignarse a usuarios, grupos y aplicaciones en ámbitos de suscripciones, grupos de recursos y recursos.

Dentro de cada suscripción, puede conceder hasta 2000 asignaciones de roles.

## <a name="resource-management"></a>Administración de recursos

Originalmente, Azure solo proporcionaba el modelo de implementación clásica. En este modelo, cada recurso existía de forma independiente; no había manera de agrupar los recursos relacionados. En su lugar, debía realizarse un seguimiento manual de los recursos que componían la solución o aplicación, y acordarse de administrarlos de manera coordinada.

Para implementar una solución, había que crear cada recurso individualmente mediante Azure Portal o crear un script que implementara todos los recursos en el orden correcto. Para eliminar una solución, tenía que eliminar cada recurso individualmente. No se podía aplicar ni actualizar fácilmente las directivas de control de acceso para los recursos relacionados. Por último, no podía aplicar etiquetas a los recursos para etiquetarlos con términos que le ayudaran a supervisar los recursos y administrar la facturación.

En 2014, Azure presentó Resource Manager, que incorpora el concepto de un grupo de recursos. Un grupo de recursos es un contenedor para los recursos que comparten un ciclo de vida común. El modelo de implementación de Resource Manager ofrece varias ventajas:

- Puede implementar, administrar y supervisar todos los servicios para su solución como grupo, en lugar de controlar estos servicios individualmente.

- Puede implementar la solución repetidamente a lo largo del ciclo de vida y tener la seguridad de que los recursos se implementan de forma coherente.

- Puede aplicar control de acceso a todos los recursos del grupo de recursos y las directivas se aplican automáticamente cuando se agregan nuevos recursos al grupo de recursos.

- Puede aplicar etiquetas a los recursos para organizar de manera lógica todos los recursos de la suscripción.

- Puede usar la notación de objetos JavaScript (JSON) para definir la infraestructura de la solución. El archivo JSON se conoce como una plantilla de Resource Manager.

- Puede definir las dependencias entre recursos de modo que se implementen en el orden correcto.

![Administración de recursos](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Resource Manager permite colocar recursos en grupos significativos de administración, facturación y afinidad natural. Como se mencionó anteriormente, Azure tiene dos modelos de implementación. En el [modelo clásico](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) anterior, la unidad básica de administración era la suscripción. Era difícil desglosar los recursos dentro de una suscripción, así que había que crear un gran número de suscripciones. Con el modelo de Resource Manager, se han introducido los grupos de recursos.

Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. El [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) puede incluir todos los recursos de la solución o solo aquellos que se desean administrar como grupo. Para decidir cómo asignar los recursos a los grupos de recursos, tenga en cuenta lo que más conviene a su organización.

Para más recomendaciones sobre platillas, consulte [Procedimientos recomendados para crear plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analiza las dependencias para asegurarse de que los recursos se crean en el orden correcto. Si un recurso se basa en un valor de otro recurso (por ejemplo, una máquina virtual que necesita una cuenta de almacenamiento para los discos), establezca una dependencia.

>[!Note]
>Para obtener más información, consulte [Definición de dependencias en plantillas del Administrador de recursos de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies).

También puede utilizar la plantilla para las actualizaciones de la infraestructura. Por ejemplo, puede agregar un recurso a la solución y agregar reglas de configuración para los recursos que ya están implementados. Si la plantilla especifica la creación de un recurso pero ese recurso ya existe, Azure Resource Manager realiza una actualización, en lugar de crear un nuevo recurso. Administrador de recursos de Azure actualiza el activo existente al mismo estado que tendría si fuese nuevo.

Resource Manager proporciona extensiones para escenarios en los que se necesitan operaciones adicionales, como instalar un software que no está incluido en el programa de instalación.

## <a name="resource-tracking"></a>Seguimiento de recursos

A medida que los usuarios de su organización agregan recursos a la suscripción, cada vez adquiere más importancia asociar los recursos con el departamento, el cliente y el entorno adecuados. Puede asociar metadatos a recursos mediante las [etiquetas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags), que sirven para proporcionar información sobre el recurso o el propietario. Además, no solo permiten agregar y agrupar los recursos de varias maneras, sino también emplear esos datos para fines de contracargo.

Use etiquetas cuando tenga una colección compleja de grupos de recursos y recursos, y necesite visualizar dichos activos de la manera que le resulte más conveniente. Por ejemplo, puede etiquetar recursos que cumplen una función similar en la organización o que pertenecen al mismo departamento.

Sin etiquetas, los usuarios de su organización pueden crear varios recursos que son difíciles de identificar y administrar posteriormente. Por ejemplo, puede querer eliminar todos los recursos de un proyecto. Si dichos recursos no están etiquetados en el proyecto, tendrá que buscarlos manualmente. El etiquetado puede ser un aspecto importante para reducir costos innecesarios en la suscripción.

Los recursos no tienen que residir en el mismo grupo de recursos para compartir una etiqueta. Puede crear su propia taxonomía de etiquetas para asegurarse de que todos los usuarios de la organización utilizan etiquetas comunes y no aplican accidentalmente etiquetas ligeramente diferentes (por ejemplo, "dept" en lugar de "departamento").

Las directivas de recursos le permiten crear reglas estándar para su organización. Puede crear directivas que se aseguren de que los recursos están etiquetados con los valores adecuados.

> [!Note]
> Para más información, consulte [Iniciativa de directiva de etiquetas de facturación](../azure-policy/scripts/billing-tags-policy-init.md).

También puede ver recursos etiquetados mediante Azure Portal.

El [informe de uso](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) de la suscripción incluye los nombres y los valores de las etiquetas, lo que permite desglosar los costos por etiquetas.

> [!Note]
> Para más información sobre las etiquetas, consulte [Iniciativa de directiva de etiquetas de facturación](../azure-policy/scripts/billing-tags-policy-init.md).

Se aplican las siguientes limitaciones a las etiquetas:

- Cada recurso o grupo de recursos puede tener un máximo de 15 pares de clave/valor de etiquetas. Esta limitación solo se aplica a las etiquetas que se aplican directamente al recurso o grupo de recursos. Un grupo de recursos puede contener muchos recursos con 15 pares de clave/valor de etiquetas cada uno.

- El nombre de la etiqueta está limitado a 512 caracteres.

- El valor de la etiqueta está limitado a 256 caracteres.

- Los recursos de un grupo de recursos no heredan las etiquetas aplicadas a este.

Si necesita asociar más de 15 valores a un recurso, utilice una cadena JSON como valor de la etiqueta. La cadena JSON puede contener muchos valores que se aplican a una sola clave de etiqueta.

### <a name="tags-and-billing"></a>Etiquetas y facturación

Las etiquetas le permiten agrupar los datos de facturación. Por ejemplo, si va a ejecutar varias máquinas virtuales para organizaciones diferentes, use etiquetas para agrupar el uso por centro de costo. También puede usar etiquetas para clasificar los costos por entorno de tiempo de ejecución; por ejemplo, el uso de facturación en máquinas virtuales que se ejecutan en el entorno de producción.

Puede recuperar información sobre las etiquetas a través de las [API de RateCard y de uso de recursos de Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) o mediante el archivo de valores separados por coma (CSV). Puede descargar el archivo de uso en el [Portal de cuentas de Azure](https://account.windowsazure.com/) o el [portal EA](https://ea.azure.com/).

>[!Note]
> Para obtener más información sobre el acceso a información de facturación mediante programación, vea [Obtención de información sobre el consumo de recursos de Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Para las operaciones de API de REST, vea [Referencia de API de REST de facturación de Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Al descargar el archivo .csv de uso correspondiente a los servicios que admiten etiquetas con facturación, estas aparecerán en la columna Etiquetas.

## <a name="critical-resource-controls"></a>Controles de recursos críticos

A medida que su organización agrega servicios básicos a la suscripción, cada vez reviste más importancia asegurarse de que estos servicios estén disponibles para evitar la interrupción de la actividad empresarial. Gracias a los [bloqueos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources), se pueden restringir las operaciones en recursos de gran valor donde modificarlas o eliminarlas tendría un gran impacto en las aplicaciones o la infraestructura en la nube. Puede aplicar bloqueos a una suscripción, un recurso o un grupo de recursos. Normalmente, los bloqueos se aplican a recursos fundamentales como redes virtuales, puertas de enlace y cuentas de almacenamiento.

Además, en estos momentos, admiten dos valores: CanNotDelete y ReadOnly. CanNotDelete significa que los usuarios (con los derechos adecuados) todavía pueden leer o modificar un recurso, pero no eliminarlo. ReadOnly significa que los usuarios autorizados no pueden eliminar o modificar un recurso.

Los bloqueos de Resource Manager solo se aplican a las operaciones que se producen en el plano de la administración, que consta de las operaciones enviadas a <https://management.azure.com>. Los bloqueos no restringen cómo los recursos realizan sus propias funciones. Los cambios de recursos están restringidos, pero no así las operaciones de recursos. Por ejemplo, un bloqueo de solo lectura de SQL Database evita tener que eliminar o modificar dicha base de datos, pero no podrá crear, actualizar o eliminar los datos de ella.

Al aplicar **ReadOnly**, pueden producirse resultados inesperados, ya que algunas operaciones que parecen ser similares a operaciones de lectura requieren acciones adicionales. Por ejemplo, al colocar un bloqueo **ReadOnly** en una cuenta de almacenamiento, evita que se muestren las claves a todos los usuarios. La operación de visualización claves se administra mediante solicitudes POST debido a que las claves devueltas están disponibles para las operaciones de escritura.

![Controles de recursos críticos](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Otro ejemplo: al colocar un bloqueo ReadOnly en un recurso de App Service, se evita que el Explorador de servidores de Visual Studio muestre los archivos del recurso, ya que esa interacción requiere acceso de escritura.

Al diferencia del control de acceso basado en rol, los bloqueos de administración se usan para aplicar una restricción a todos los usuarios y roles. Para obtener información sobre cómo establecer permisos para usuarios y roles, vea [Control de acceso basado en roles de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Cuando se aplica un bloqueo en un ámbito primario, todos los recursos heredan el mismo bloqueo. Incluso los recursos que agregue posteriormente heredan el bloqueo del elemento primario. El bloqueo más restrictivo de toda la herencia tiene prioridad.

Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones Microsoft.Authorization/ _o Microsoft.Authorization/locks/_. Entre los roles integrados, solamente se conceden esas acciones al **propietario** y al **administrador de acceso de usuarios**.

## <a name="api-access-to-billing-information"></a>Acceso de las API a la información de facturación

Use las API de facturación de Azure para extraer datos de uso y de recursos e incorporarlos en las herramientas de análisis de datos de su preferencia. Las API de RateCard y de uso de recursos de Azure pueden ayudarlo a predecir y administrar los costos de forma precisa. Las API se implementan como proveedor de recursos, como parte de la familia de API expuesta por Azure Resource Manager.

### <a name="azure-resource-usage-api-preview"></a>API de uso de recursos de Azure (versión preliminar)

Use la [API de uso de recursos](https://msdn.microsoft.com/library/azure/mt219003) de Azure para obtener los datos estimados de consumo de Azure. La API incluye:

- **Control de acceso basado en rol de Azure**: configure las directivas de acceso en [Azure Portal](https://portal.azure.com/) o mediante [cmdlets de Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar qué usuarios o aplicaciones pueden obtener acceso a los datos de uso de la suscripción. Los autores de llamadas deben utilizar tokens de Azure Active Directory estándar para la autenticación. Agregue el autor de la llamada al rol Lector de facturación, Lector, Propietario o Colaborador para que obtenga acceso a los datos de uso de una suscripción de Azure específica.

- **Agregaciones cada hora o diarias** : los autores de llamadas pueden especificar si desean sus datos de uso de Azure en depósitos cada hora o diarios. El valor predeterminado es diario.

- **Metadatos de instancia (incluye etiquetas de recursos)**: obtenga detalles de nivel de instancia, como el URI de recurso completo (/subscriptions/{id-suscripción}/..), la información del grupo de recursos y las etiquetas de recursos. Estos metadatos lo ayudarán de forma determinista y mediante programación a asignar el uso mediante las etiquetas, para casos de uso como cargos cruzados.

- **Metadatos de recursos**: detalles de recursos, como el nombre del medidor, la categoría del medidor, la subcategoría del medidor, la unidad y la región permiten que el autor de la llamada comprenda mejor lo que se ha consumido. También estamos trabajando para alinear la terminología de metadatos de recursos en todo Azure Portal, CSV de uso de Azure, CSV de facturación de EA y otras experiencias orientadas al público, para permitirle que pueda poner en correlación los datos en todas las experiencias.

- **Uso para todos los tipos de ofertas**: los datos de uso están disponibles para todos los tipos de ofertas, incluidos el pago por uso, MSDN, compromiso monetario, crédito monetario y EA.

**API de RateCard de recursos de Azure (versión preliminar)**

Use Azure Resource RateCard API para obtener la lista de recursos de Azure disponibles y los precios estimados de cada uno. La API incluye:

- **Control de acceso basado en rol de Azure**: configure las directivas de acceso en Azure Portal o mediante cmdlets de Azure PowerShell para especificar qué usuarios o aplicaciones pueden obtener acceso a los datos de RateCard. Los autores de llamadas deben utilizar tokens de Azure Active Directory estándar para la autenticación. Agregue el autor de la llamada al rol Lector, Propietario o Colaborador para que obtenga acceso a los datos de uso de una suscripción de Azure determinada.

- **Compatibilidad con ofertas de pago por uso, MSDN, compromiso monetario y crédito monetario (no compatible con EA)**: esta API proporciona información de tarifas de nivel de oferta de Azure. El autor de llamada de esta API debe pasar la información de oferta para obtener detalles y tarifas de recursos Actualmente no podemos proporcionar tarifas de EA porque las ofertas de EA tienen tarifas personalizadas por inscripción. Éstos son algunos de los escenarios posibles con la combinación de las API de uso y de RateCard:

- **Gasto en Azure durante el mes**: use la combinación de las API de uso y de RateCard para obtener información más detallada sobre el gasto por usar la nube durante el mes. Puede analizar los depósitos por hora y diarios de las estimaciones de usos y cargos.

- **Configurar alertas**: use las API de uso y de RateCard para obtener una estimación del consumo de la nube y los cargos, y configure alertas basadas en recursos o basadas en importes monetarios.

- **Predicción de facturas**: obtenga el gasto en la nube y el consumo estimados, y aplique algoritmos de aprendizaje automático para poder predecir cuál sería la factura al final del ciclo de facturación.

- **Análisis de costos previos al consumo**: use la API de RateCard para predecir cuál sería el importe de su factura para el uso esperado cuando mueva las cargas de trabajo a Azure. Si tiene cargas de trabajo existentes en otras nubes o nubes privadas, también puede asignar su uso con las tarifas de Azure para obtener una mejor estimación del gasto de Azure. Esta estimación permite tener la capacidad de dinamizar una oferta y de comparar y contrastar los distintos tipos de ofertas que van más allá del pago por uso, como son el compromiso monetario y el crédito monetario. La API también le ofrece la posibilidad de ver las diferencias de costos por región y le permite realizar un análisis de hipótesis para ayudarlo a tomar decisiones en cuanto a la implementación.

- **Análisis de hipótesis**: puede determinar si es más rentable ejecutar las cargas de trabajo en otra región o en otra configuración del recurso de Azure. Los costos de los recursos de Azure pueden variar en función de la región de Azure que usa.

- También puede determinar si otro tipo de oferta de Azure ofrece una mejor tarifa en un recurso de Azure.

## <a name="networking-controls"></a>Controles de red

El acceso a los recursos puede ser interno (dentro de la red corporativa) o externo (a través de Internet). Es muy fácil que los usuarios de una organización puedan colocar accidentalmente los recursos en la zona incorrecta y, posiblemente, abrirlos para acceder con intenciones malintencionado. Al igual que con los dispositivos locales, las empresas deben agregar los controles adecuados para asegurarse de que los usuarios de Azure tomen las decisiones correctas.

![Controles de red](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Para el gobierno de suscripciones, identificamos los recursos principales que proporcionan un control de acceso básico. Se trata de los siguientes:

### <a name="network-connectivity"></a>Conectividad de red

Las [redes virtuales](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) son objetos de contenedor de las subredes. Aunque no es estrictamente necesario, se suelen utilizar al conectar las aplicaciones a los recursos corporativos internos. El servicio de Azure Virtual Network le permite conectar de forma segura los recursos de Azure con redes virtuales.

Una red virtual es una representación de su propia red en la nube. Una red virtual es un aislamiento lógico de la nube de Azure dedicada a su suscripción. También puede conectar redes virtuales a la red local.

A continuación, se muestran las funcionalidades de Azure Virtual Network:

- **Aislamiento**: las redes virtuales están completamente aisladas entre sí. Puede crear redes virtuales independientes para el desarrollo, la prueba y la producción que usan los mismos bloques de direcciones de CIDR. Por el contrario, puede crear varias redes virtuales que usan diferentes bloques de direcciones CIDR y que conectan redes entre sí. También puede segmentar una red virtual en varias subredes. Azure proporciona resolución de nombres interna para máquinas virtuales e instancias de rol de Cloud Services conectadas a una red virtual. Si lo desea, puede configurar una red virtual para usar sus propios servidores DNS, en lugar de utilizar la resolución de nombres interna Azure.

- **Conectividad a Internet**: todas las instancias de Azure Virtual Machines y de Cloud Services conectadas a una red virtual tienen acceso a Internet, de forma predeterminada. También puede habilitar el acceso entrante a recursos específicos, según sea necesario.

- **Conectividad de los recursos de Azure**: los recursos de Azure, como Cloud Services y las máquinas virtuales, se pueden conectar a la misma red virtual. Los recursos pueden conectarse entre sí mediante direcciones IP privadas, aunque estén en subredes diferentes. Azure proporciona el enrutamiento predeterminado entre subredes, redes virtuales y redes locales, por lo que no tendrá que configurar ni administrar rutas.

- **Conectividad de red virtual**: las redes virtuales se pueden conectar entre sí, con lo que se habilitan los recursos conectados a cualquier red virtual para que se comuniquen con cualquier recurso de cualquier red virtual.

- **Conectividad local**: las redes virtuales se pueden conectar a redes locales a través de conexiones de redes privadas entre la red y Azure, o bien a través de una conexión VPN de sitio a sitio a través de Internet.

- **Filtrado de tráfico**: el tráfico de red de entrada y salida de las instancias de rol de Cloud Services y las máquinas virtuales se puede filtrar por dirección IP y puerto de origen, dirección IP y puerto de destino, y por protocolo.

- **Enrutamiento**: de manera opcional, puede invalidar el enrutamiento predeterminado de Azure mediante la configuración de sus propias rutas, o bien utilizando rutas BGP a través de una puerta de enlace de red.

## <a name="network-access-controls"></a>Controles de acceso a la red

Los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) son similares a los firewalls y proporcionan reglas de cómo un recurso puede comunicarse a través de la red. Proporcionan un control granular sobre cómo o bajo qué condición una subred (o máquina virtual) puede conectarse a Internet o a otras subredes de la misma red virtual.

Un grupo de seguridad de red (NSG) contiene una lista de reglas de seguridad que permiten o deniegan el tráfico de red a recursos conectados a Azure Virtual Network (VNet). Los grupos de seguridad de red se pueden asociar a subredes, máquinas virtuales individuales (clásicas) o interfaces de red (NIC) individuales conectadas a máquinas virtuales (Resource Manager).

Cuando un grupo de seguridad de red está asociado a una subred, las reglas se aplican a todos los recursos conectados a la subred. El tráfico se puede restringir aún más si se asocia también un grupo de seguridad de red a una máquina virtual o interfaz de red.

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>Seguridad y cumplimiento continuo con las normas de las organizaciones

Cada empresa tiene necesidades distintas y todas obtendrán diferentes ventajas de las soluciones en la nube. No obstante, clientes de todo tipo tienen las mismas preocupaciones básicas en lo que respecta a cómo realizar la migración a la nube. Quieren seguir controlando sus datos, y que esos datos sigan siendo privados y seguros al mismo tiempo que se conserven la transparencia y el cumplimiento.

Esto es lo que buscan los clientes de un proveedor de nube:

- **Proteger los datos**: pese a la confirmación de que la nube puede ofrecer más seguridad para los datos y un mejor control administrativo, a los líderes de TI les sigue preocupando que, por realizar la migración a la nube, la organización sea más vulnerable a los piratas informáticos que utilizando sus soluciones internas actuales.

- **Mantener la privacidad de los datos**: los servicios en la nube privada conllevan desafíos específicos para las empresas en materia de privacidad. Del mismo modo que las compañías recurren a la nube para ahorrar en los costos de infraestructura y mejorar su flexibilidad, también les preocupa perder el control de dónde se almacenan sus datos, quién tiene acceso a ellos y cómo se utilizan.

- **Tener el control**: aunque puedan sacar partido de la nube para implementar soluciones más innovadoras, a las compañías les preocupa perder el control de sus datos. Debido a las noticias recientes de agencias gubernamentales que acceden a datos de clientes a través de medios tanto legales como ilegales, algunos CIO no se atreven a almacenar sus datos en la nube.

- **Promover la transparencia**: aunque el control, la privacidad y la seguridad son importantes para los encargados de tomar decisiones empresariales, también quieren tener la capacidad de comprobar de manera independiente cómo se almacenan y protegen sus datos, además de cómo se accede a ellos.

- **Mantener el cumplimiento**: a medida que las compañías adopten más las tecnologías de nube, la complejidad y el ámbito de las normas y los reglamentos seguirán evolucionando. Las compañías necesitan saber que se cumplirán las normas de cumplimiento, y que ese cumplimiento evolucionará al ritmo que cambian los reglamentos.

## <a name="security-configuration-monitoring-and-alerting"></a>Configuración, supervisión y alertas de seguridad

Los suscriptores de Azure pueden administrar sus entornos de nube desde diversos dispositivos, incluidas estaciones de trabajo de administración, equipos de desarrollador e incluso dispositivos de usuario final con privilegios que tengan permisos específicos para la tarea. En algunos casos, las funciones administrativas se realizan mediante consolas web como Azure Portal. En otros casos, puede haber conexiones directas a Azure desde sistemas locales a través de redes privadas virtuales (VPN), Terminal Services, protocolos de aplicación de cliente o Azure Service Management API (SMAPI). Además, los puntos de conexión de cliente pueden estar unidos a un dominio o aislados y no administrados, como tabletas o smartphones.

Aunque las diversas funcionalidades de administración y acceso proporcionan un amplio conjunto de opciones, esta variabilidad puede suponer un riesgo importante para una implementación en la nube y dificultar la administración, el seguimiento y la auditoría de las acciones administrativas. Esta variabilidad también puede presentar amenazas para la seguridad debidas al acceso no regulado a los puntos de conexión de cliente que se usan para administrar los servicios en la nube. El uso de estaciones de trabajo personales o generales para desarrollar y administrar infraestructuras abre la puerta a amenazas impredecibles que se producen tanto en la exploración web (por ejemplo, ataques a los sitios web más utilizados) como en el correo electrónico (tales como ingeniería social y suplantación de identidad [phishing]).

La supervisión, el registro y la auditoría proporcionan una base para el seguimiento y la descripción de las actividades administrativas, pero puede que no siempre sea factible auditar todas las acciones con detalle debido a la cantidad de datos generados. Sin embargo, auditar la efectividad de las directivas de administración es un procedimiento recomendado.

El sistema de gobierno de seguridad de Azure utiliza GPO de AD DS para controlar todas las interfaces de Windows de los administradores, como el uso compartido de archivos. Incluya las estaciones de trabajo de administración de procesos de auditoría, supervisión y registro. Realice un seguimiento del acceso y uso de los administradores y programadores.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) proporciona una perspectiva central del estado de seguridad de los recursos de las suscripciones y ofrece recomendaciones que ayudan a evitar que se realicen ataques a los recursos. Asimismo, puede habilitar directivas más granulares (por ejemplo, aplicar directivas a grupos de recursos concretos que permiten a la empresa adaptar su postura con respecto al riesgo que están abordando).

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center proporciona funcionalidades de administración de directivas y supervisión de la seguridad integradas en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad. Después de habilitar las [directivas de seguridad](https://docs.microsoft.com/azure/security-center/security-center-policies) para los recursos de una suscripción, Security Center analizará la seguridad de los recursos con el fin de identificar posibles vulnerabilidades. La información acerca de la configuración de la red está disponible de inmediato.

Azure Security Center representa una combinación de procedimientos recomendados, análisis y administración de directivas de seguridad para todos los recursos de una suscripción de Azure. Esta herramienta eficaz y fácil de usar permite a los equipos de seguridad y a los responsables de riesgos evitar y detectar amenazas de seguridad (además de responder a ellas), ya que recopila y analiza automáticamente los datos de seguridad de los recursos de Azure, la red y las soluciones de asociados, como programas antimalware y firewalls.

Además, Azure Security Center aplica análisis avanzados (entre ellos, el aprendizaje automático y el análisis del comportamiento) y utiliza al mismo tiempo la información global sobre amenazas de los productos y servicios de Microsoft, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) y otras fuentes externas. Las funcionalidades de [gobierno de seguridad](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) pueden aplicarse ampliamente en el nivel de suscripción o restringirse a requisitos concretos y detallados aplicados a recursos específicos mediante la definición de directivas.

Finalmente, Azure Security Center analiza el estado de seguridad de los recursos en función de esas directivas, y aprovecha esta información para proporcionar paneles detallados y alertas de eventos como la detección de malware o la conexión de IP malintencionadas.

>[!Note]
> Para más información sobre cómo aplicar las recomendaciones, lea [Implementación de recomendaciones de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Security Center recopila datos de las máquinas virtuales para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y avisarle de las amenazas. La primera vez que se accede al Centro de seguridad la recopilación de datos se habilita en todas las máquinas virtuales de la suscripción. Se recomienda utilizar la recopilación de datos, pero se puede cancelar [desactivando la recopilación de datos](https://docs.microsoft.com/azure/security-center/security-center-faq) en la directiva de Security Center.

Finalmente, Azure Security Center es una plataforma abierta que permite a los asociados de Microsoft y fabricantes independientes de software crear software que se integre en Azure Security Center para mejorar sus funcionalidades.

Azure Security Center supervisa los siguientes recursos de Azure:

- Máquinas virtuales (se incluye Cloud Services)

- Instancias de Azure Virtual Network

- Servicio de SQL Azure

- Soluciones de asociados integradas en la suscripción de Azure, como un firewall de aplicaciones web en las máquinas virtuales y en [App Service Environment](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

### <a name="operations-management-suite"></a>Operations Management Suite

El [programa de gobierno](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) y seguridad de la información del equipo de servicio y desarrollo de software de OMS respalda sus requisitos empresariales y cumple las leyes y los reglamentos, tal y como se describe en el [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/) y en [Microsoft Trust Center Compliance](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). En estas páginas también se describe cómo OMS establece los requisitos de seguridad, identifica los controles de seguridad, y administra y supervisa los riesgos. Cada año, revisamos las directivas, los estándares, los procedimientos y las instrucciones.

Cada miembro del equipo de desarrollo de OMS recibe cursos formales sobre seguridad de aplicaciones. Internamente, utilizamos un sistema de control de versiones para el desarrollo de software. Cada proyecto de software se protege mediante el sistema de control de versiones.

Microsoft cuenta con un equipo de seguridad y cumplimiento normativo que supervisa y evalúa todos los servicios de Microsoft. Los responsables de seguridad de la información conforman el equipo y no están vinculados con los departamentos de ingeniería que desarrollan OMS. Los responsables de seguridad tienen su propia cadena de administración y llevan a cabo evaluaciones independientes de los productos y servicios para garantizar la seguridad y el cumplimiento normativo.

Operations Management Suite (también conocido como OMS) es una colección de servicios de administración que desde el principio se diseñaron en la nube. En lugar de implementar y administrar recursos locales, los componentes de OMS se hospedan en su totalidad en Azure. La configuración es mínima, y puede estar listo y rindiendo literalmente en cuestión de minutos.

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig8.png)

El hecho de que los servicios de OMS se ejecuten en la nube no significa que no puedan administrar eficazmente cualquier entorno local.

Coloque un agente en cualquier equipo Windows o Linux del centro de datos, y este enviará datos a Log Analytics, donde se pueden analizar, junto con los restantes datos recopilados de los servicios en la nube o locales. Use Azure Backup y Azure Site Recovery con el fin de utilizar la nube para realizar copias de seguridad de los recursos locales y de lograr una alta disponibilidad de estos.

Los runbooks en la nube normalmente no pueden acceder a los recursos locales, pero puede instalar un agente en uno o varios equipos que hospeden los runbooks en su centro de datos. Cuando se inicia un runbook, simplemente especifique si desea que se ejecute en la nube o en un trabajo local.

Un conjunto de servicios que se ejecutan en Azure proporciona la funcionalidad principal de OMS. Cada servicio ofrece una función de administración específica, y se pueden combinar los servicios para lograr escenarios de administración diferentes.

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Operations Manager de Azure amplía sus funcionalidades proporcionando soluciones de administración. Las [soluciones de administración](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) son conjuntos de lógica preempaquetados que implementan un escenario de administración concreto aprovechando uno o varios servicios de OMS.

![Operations Manager de Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Hay disponibles diferentes soluciones de Microsoft y de asociados que se pueden agregar fácilmente a la suscripción de Azure para aumentar el valor de su inversión en OMS.

Como asociado, puede crear sus propias soluciones para que sean compatibles con los servicios y las aplicaciones, y proporcionarlas a los usuarios a través de las plantillas de inicio rápido o Azure Marketplace.

## <a name="performance-alerting-and-monitoring"></a>Supervisión y alertas de rendimiento

### <a name="alerting"></a>Alertas

Las alertas son un método de supervisión de los registros, los eventos o las métricas de recursos de Azure, con la intención de recibir una notificación cuando una condición especificada se cumple.

**Alertas en los distintos servicios de Azure**

Las alertas están disponibles en los diferentes servicios, incluidos:

- Application Insights: admite las alertas de métricas y pruebas web.

>[!Note]
> Vea [Definición de alertas en Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) y [Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sitio web](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- Log Analytics (Operations Management Suite): habilita el enrutamiento de los registros de actividad y diagnóstico en Log Analytics. Operations Management Suite permite métrica, registro y otros tipos de alerta.

>[!Note]
> Para obtener más información, consulte la sección Alertas de [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- Azure Monitor: admite alertas basadas en valores de métricas y en eventos de registro de actividades. Puede usar la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx) para administrar las alertas.

>[!Note]
> Para más información, vea la información sobre cómo [utilizar Azure Portal, PowerShell o la interfaz de la línea de comandos para crear alertas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Supervisión

Los problemas de rendimiento de la aplicación en la nube pueden afectar a su negocio. Con varios componentes interconectados y versiones frecuentes, las degradaciones pueden ocurrir en cualquier momento. Y si va a desarrollar una aplicación, los usuarios normalmente se encontrarán con problemas que no se han detectado durante las pruebas. Debe tener conocimiento de estos problemas de inmediato y disponer de las herramientas de diagnóstico y solución de problemas. Microsoft Azure tiene una gama de herramientas para identificar estos problemas.

**¿Cómo puedo supervisar mis aplicaciones en la nube de Azure?**

Hay una gama de herramientas para la supervisión de servicios y aplicaciones de Azure. Algunas de sus características se superponen. Se debe en parte a motivos históricos y en parte al desenfoque entre el desarrollo y el funcionamiento de una aplicación.

Estas son las herramientas principales:

- **Azure Monitor** es una herramienta básica para la supervisión de servicios que se ejecutan en Azure. Proporciona datos a nivel de infraestructura sobre el rendimiento de un servicio y el entorno circundante. Si va a administrar todas las aplicaciones en Azure, decida si desea escalar o reducir verticalmente los recursos y luego Azure Monitor proporciona lo que debe usar para empezar.

- **Application Insights** puede utilizarse para el desarrollo y como una solución de supervisión de producción. Funciona mediante la instalación de un paquete en la aplicación y, por tanto, ofrece una perspectiva más interna de lo que está sucediendo. Los datos incluyen tiempos de respuesta de dependencias, seguimientos de excepciones, instantáneas de depuración y perfiles de ejecución. Proporciona herramientas inteligentes y sólidas para analizar toda esta telemetría, a fin de ayudar a depurar una aplicación y a saber qué hacen los usuarios con ella. Puede indicar si un pico en los tiempos de respuesta se debe a algo de una aplicación o a algún problema de recursos externo. Si utiliza Visual Studio y la aplicación presenta errores, puede recurrir directamente a las líneas de código del problema para poder corregirlo.

- **Log Analytics** está destinado a quienes necesitan ajustar el rendimiento y planificar el mantenimiento de aplicaciones que se ejecutan en producción. Se basa en Azure. Recopila y agrega los datos de muchos orígenes, aunque con un retraso de 10 a 15 minutos. Proporciona una solución integral de administración de TI para Azure, entornos locales e infraestructuras de terceros basadas en la nube (por ejemplo, Amazon Web Services). Proporciona herramientas más enriquecidas para analizar datos en más orígenes, permite realizar consultas complejas en todos los registros y puede alertar de forma proactiva sobre condiciones específicas. Incluso puede recopilar datos personalizados en su repositorio central para que pueda consultarlos y visualizarlos.

- **System Center Operations Manager (SCOM)** está diseñado para administrar y supervisar grandes instalaciones de la nube. Es posible que ya esté familiarizado con esta solución como una herramienta de administración para nubes locales basadas en Windows Sever e Hyper-V, pero también se puede integrar con aplicaciones de Azure y administrarlas. Entre otras cosas, puede instalar Application Insights en las aplicaciones dinámicas existentes. Si una aplicación deja de funcionar, se lo notifica en cuestión de segundos.


## <a name="next-steps"></a>Pasos siguientes

- [Procedimientos recomendados para crear plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Ejemplos de implementación de un sistema de gobierno de suscripciones de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/)
