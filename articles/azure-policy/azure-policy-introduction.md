---
title: "Introducción a Azure Policy | Microsoft Docs"
description: Azure Policy es un servicio de Azure que se usa para crear, asignar y administrar las definiciones de directivas en el entorno de Azure.
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders; nini
ms.date: 01/17/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 37fdb8156e4507046a574ba681e2ea3f6909c4a6
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="what-is-azure-policy"></a>¿Qué es Azure Policy?

El gobierno de TI genera claridad entre los objetivos comerciales y los proyectos de TI. Un buen gobierno de TI implica planear las iniciativas y establecer prioridades en un nivel estratégico. ¿Su empresa experimenta muchos problemas de TI que parecen no resolverse nunca? La implementación de directivas ayuda a administrarlos mejor y a evitarlos. Aquí es donde Azure Policy entra en juego.

Azure Policy es un servicio de Azure que se usa para crear, asignar y administrar las definiciones de directivas. Las definiciones de directivas aplican distintas reglas y acciones sobre los recursos, para que estos sigan siendo compatibles con los estándares corporativos y los contratos de nivel de servicio. Azure Policy ejecuta una evaluación de los recursos, para detectar los que no son compatibles con las definiciones de directivas definidas. Por ejemplo, puede disponer de una directiva que permita solo determinados tipos de máquinas virtuales. Otras directivas exigen que todos los recursos tengan una etiqueta concreta. Estas directivas se evalúan al crear y actualizar los recursos.

## <a name="how-is-it-different-from-rbac"></a>¿En qué se diferencia de RBAC?

Hay algunas diferencias importantes entre directiva y control de acceso basado en roles (RBAC). RBAC se centra en las acciones del usuario en ámbitos diferentes. Por ejemplo, puede que se le agregue al rol de colaborador para un grupo de recursos en el ámbito deseado. El rol permite realizar cambios en ese grupo de recursos. La directiva se centra en las propiedades de los recursos durante la implementación y para todos los recursos existentes. Por ejemplo, a través de directivas, puede controlar los tipos de recursos que se pueden aprovisionar. O puede restringir las ubicaciones en las que se pueden aprovisionar los recursos. A diferencia de RBAC, la directiva es un sistema que permite de manera predeterminada y niega explícitamente.

Para usar las directivas, debe autenticarse a través de RBAC. En concreto, la cuenta necesita:

- El permiso `Microsoft.Authorization/policydefinitions/write` para definir una directiva.
- El permiso `Microsoft.Authorization/policyassignments/write` para asignar una directiva.

Estos permisos no se incluyen en el rol **Colaborador**.


## <a name="policy-definition"></a>Definición de directiva

Cada definición de directiva tiene condiciones que regulan su aplicación. Además, tiene una acción complementaria que lleva a cabo cuando se cumplen las condiciones.

En Azure Portal, se ofrecen algunas directivas integradas que están disponibles de manera predeterminada. Por ejemplo: 

- **Requerir SQL Server 12.0**: esta definición de directiva tiene condiciones o reglas para garantizar que todos los servidores SQL usen la versión 12.0. Su acción es denegar a todos los servidores que no cumplen con estos criterios.
- **SUK de cuenta de almacenamiento permitida**: esta definición de directiva tiene un conjunto de condiciones o reglas que determinan si una cuenta de almacenamiento que se está implementado se encuentra dentro de un conjunto de tamaños de SKU. Su acción es denegar a todos los servidores que no cumplen con el conjunto de tamaños de SKU definidos.
- **Tipo de recurso permitido**: esta definición de directiva tiene un conjunto de condiciones o reglas para especificar los tipos de recursos que puede implementar la organización. Su acción es denegar a todos los recursos que no forman parte de esta lista definida.
- **Ubicaciones permitidas**: esta directiva permite restringir las ubicaciones que la organización puede especificar al implementar los recursos. La acción se utiliza para exigir los requisitos de cumplimiento por ubicación geográfica.
- **SKU de máquina virtual permitidas**: esta directiva permite especificar un conjunto de SKU de máquina virtual que la organización puede implementar.
- **Aplicar etiqueta y su valor predeterminado**: esta directiva aplica una etiqueta obligatoria y su valor predeterminado cuando el usuario no la especifica.
- **Forzar etiqueta y su valor**: esta directiva fuerza una etiqueta necesaria y su valor en un recurso.
- **Tipos de recursos no permitidos**: esta directiva permite especificar los tipos de recursos que la organización no puede implementar.

Puede asignar cualquiera de estas directivas a través de Azure Portal, PowerShell o la CLI de Azure.

Para más información sobre las estructuras de las definiciones de directiva, consulte este artículo: [Estructura de definición de directiva](policy-definition.md).

## <a name="policy-assignment"></a>Asignación de directiva

Una asignación de directiva es una definición de directiva que se asignó para que ocurra dentro de un ámbito específico. Este ámbito puede ir desde un grupo de administración a un grupo de recursos. El término *ámbito* hace referencia a todos los grupos de recursos, suscripciones o grupos de administración a los que se asigna la definición de directiva. Todos los recursos secundarios heredan las asignaciones de directivas. De este modo, si una directiva se aplica a un grupo de recursos, también es aplicable a todos los recursos de dicho grupo. Sin embargo, puede excluir un subámbito de la asignación de directiva. Por ejemplo, en el ámbito de la suscripción, puede asignar una directiva que impida la creación de recursos de red. Sin embargo, excluye un grupo de recursos dentro de la suscripción que está diseñado para la infraestructura de red. Concede acceso a este grupo de recursos de red a los usuarios de confianza con la creación de recursos de red.

Para más información sobre la configuración de las definiciones y asignaciones de directiva, vea [Creación de una asignación de directiva para identificar recursos no compatibles en el entorno de Azure](assign-policy-definition.md).

## <a name="policy-parameters"></a>Parámetros de directiva

Los parámetros de directiva permiten simplificar la administración de directivas reduciendo el número de definiciones de directiva que debe crear. Cuando crea una definición de directiva, puede definir parámetros para que sea más genérica. Después, puede volver a usar esa definición de directiva para diferentes escenarios. Para ello, transmita distintos valores al asignar la definición de directiva. Por ejemplo, especifique un conjunto de ubicaciones para una suscripción.

Los parámetros se definen o crean cuando se crea una definición de directiva. Cuando se define un parámetro, recibe un nombre y, de manera opcional, un valor. Por ejemplo, podría definir un parámetro para una directiva denominada *location*. Después, puede asignarle valores diferentes, como *EastUS* o *WestUS*, al asignar una directiva.

<!--
Next link should point to new Concept page for Parameters
-->
Para más información sobre los parámetros de directiva, consulte [Información general sobre las directivas de recursos: parámetros](policy-definition.md#parameters).

## <a name="initiative-definition"></a>Definición de iniciativa
Una definición de iniciativa es una colección de definiciones de directiva que están diseñadas para alcanzar un único objetivo general. Las definiciones de iniciativa simplifican la administración y asignación de las definiciones de directiva. Tal simplificación se realiza mediante la agrupación de un conjunto de directivas como un solo elemento. Por ejemplo, podría crear una iniciativa titulada **Habilitar la supervisión en Azure Security Center**, con el objetivo de supervisar todas las recomendaciones de seguridad disponibles en Azure Security Center.

En esta iniciativa, tendría definiciones de directiva como las siguientes:

1. **Supervisar SQL Database sin cifrar en Security Center**, para supervisar servidores y bases de datos SQL sin cifrar.
2. **Supervisar las vulnerabilidades del SO en Security Center**, para supervisar los servidores que no cumplen con la base de referencia configurada.
3. **Supervisar Endpoint Protection omitido en Security Center**, para supervisar los servidores que no tienen instalado un agente de protección de los puntos de conexión.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>Asignación de iniciativa
Al igual que una asignación de directiva, una asignación de iniciativa es una definición de iniciativa que se asigna a un ámbito específico. Las asignaciones de iniciativa disminuyen la necesidad de crear varias definiciones de iniciativa para cada ámbito. Este ámbito también puede ir desde un grupo de administración a un grupo de recursos.

Del ejemplo anterior, la iniciativa **Habilitar la supervisión en Azure Security Center** se puede asignar a distintos ámbitos. Por ejemplo, se puede asignar una asignación a **subscriptionA**. Otra se puede asignar a **subscriptionB**.

## <a name="initiative-parameters"></a>Parámetros de iniciativa
Al igual que los parámetros de directiva, los parámetros de iniciativa permiten simplificar la administración de iniciativas mediante la reducción de la redundancia. Los parámetros de iniciativa son esencialmente la lista de los parámetros que las definiciones de directiva usan dentro de la iniciativa.

Por ejemplo, tome un escenario con una definición de iniciativa, **initiativeC**, con dos definiciones de directiva. Cada definición de directiva tiene un parámetro definido:

| Directiva | nombre del parámetro |tipo del parámetro  |Nota: |
|---|---|---|---|
| policyA | allowedLocations | array  |Este parámetro espera una lista de cadenas para un valor, porque el tipo de parámetro está definido como una matriz |
| policyB | allowedSingleLocation |cadena |Este parámetro espera una palabra para un valor, porque el tipo de parámetro se definió como una cadena |

En este escenario, tiene tres opciones en el momento de definir los parámetros de iniciativa para **initiativeC**:

1. Use los parámetros de las definiciones de directiva dentro de esta iniciativa: en este ejemplo, *allowedLocations* y *allowedSingleLocation* se convierten en parámetros de iniciativa para **initiativeC**.
2. Proporcione valores a los parámetros de las definiciones de directiva dentro de esta definición de iniciativa. En este ejemplo, puede proporcionar una lista de ubicaciones al **parámetro de policyA: allowedLocations** y al **parámetro de policyB: allowedSingleLocation**.
También puede proporcionar valores cuando asigne esta iniciativa.
3. Proporcione una lista de las opciones de *value* que se puede usar cuando se asigna esta iniciativa. Cuando asigna esta iniciativa, los parámetros inherentes de las definiciones de directiva dentro de la iniciativa solo pueden tener valores provenientes de esta lista.

Por ejemplo, podría crear una lista de opciones de valores en una definición de iniciativa que contenga *EastUS*, *WestUS*, *CentralUS* y *WestEurope*. En su caso, no se puede proporcionar un valor diferente, como *Southeast Asia*, durante la asignación de la iniciativa, porque no forma parte de la lista.

## <a name="recommendations-for-managing-policies"></a>Recomendaciones para la administración de directivas

Cuando cree y administre asignaciones y definiciones de directiva, se recomienda seguir estos punteros:

- Si crea definiciones de directiva en el entorno, se recomienda comenzar con un efecto de auditoría, en contraposición con un efecto de denegación, para llevar un seguimiento del impacto que la definición de directiva tiene en los recursos del entorno. Si ya tiene scripts implementados para escalar verticalmente las aplicaciones de manera automática, establecer un efecto de denegación puede impedir las tareas de automatización que ya tiene implementadas.
- Es importante considerar las jerarquías organizativas cuando se crean definiciones y asignaciones. Se recomienda crear definiciones en un nivel más alto, por ejemplo, en el nivel del grupo de administración o de suscripción, y asignarlas en el próximo nivel secundario. Por ejemplo, si crea una definición de directiva en el nivel de grupo de administración, una asignación de directiva de esa definición se puede limitar a un nivel de suscripción dentro de dicho grupo de administración.
- Se recomienda usar el plan de tarifa estándar para comprender mejor el estado de cumplimiento del entorno. Para obtener más información sobre los modelos de precios y lo que cada uno de ellos ofrece, eche un vistazo a la página de [precios](https://azure.microsoft.com/pricing/details/azure-policy).
- Se recomienda usar siempre las definiciones de iniciativa en lugar de las definiciones de directiva, incluso si solo tiene considerada una directiva. Por ejemplo, si tiene una definición de directiva, *policyDefA*, y la crea bajo la definición de iniciativa *initiativeDefC*, si decide crear otra definición de directiva más tarde para *policyDefB* con objetivos similares a los de *policyDefA*, agréguela en *initiativeDefC* para llevar un mejor seguimiento de ellos.

   Tenga en cuenta que, una vez que crea una asignación de iniciativa desde una definición de iniciativa, cualquier definición de directiva nueva que se agregue a la definición de iniciativa se implementa automáticamente en las asignaciones de iniciativa bajo esa definición de iniciativa. Sin embargo, si se introduce un parámetro nuevo en la definición de directiva nueva, debe actualizar la definición de iniciativa y las asignaciones mediante la edición de la definición o la asignación de la iniciativa.

## <a name="next-steps"></a>pasos siguientes

Ahora que tiene información general sobre Azure Policy y algunos de los conceptos clave que estamos presentando, sugerimos seguir estos pasos:

- [Asignación de una definición de directiva](./assign-policy-definition.md)
- [Asignación de una definición de directiva con la CLI de Azure](./assign-policy-definition-cli.md)
- [Asignación de una definición de directiva con PowerShell](./assign-policy-definition-ps.md)
