---
title: "Introducción a Azure Policy | Microsoft Docs"
description: Azure Policy es un servicio de Azure que se usa para crear, asignar y administrar las definiciones de directivas en el entorno de Azure.
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 10/06/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: 01b0915cdf37ddc00a4e6a38c99ce7f6f8c4f9c9
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-policy"></a>¿Qué es Azure Policy?

El gobierno de TI genera claridad entre los objetivos comerciales y los proyectos de TI. Un buen gobierno de TI implica planear las iniciativas y establecer prioridades en un nivel estratégico. Si la empresa experimenta un número considerable de problemas de TI que no parecen resolverse, la implementación de directivas le permite realizar una mejor administración y evitarlos. Es aquí donde Azure Policy entra en juego.

Azure Policy es un servicio de Azure que se usa para crear, asignar y administrar las definiciones de directivas. Las definiciones de directivas aplican distintas reglas y acciones sobre los recursos, para que estos sigan siendo compatibles con los estándares corporativos y los contratos de nivel de servicio. Para ello, ejecuta una evaluación de los recursos para examinar cuáles no son compatibles con las definiciones de directivas que tiene aplicadas.

## <a name="policy-definition"></a>Definición de directiva

Cada definición de directiva tiene condiciones bajo las cuales se aplica y una acción de acompañamiento que se lleva a cabo si se cumplen esas condiciones.

En Azure Portal, se ofrecen algunas directivas integradas que están disponibles de manera predeterminada. Por ejemplo:

- **Requerir SQL Server 12.0**: esta definición de directiva tiene condiciones o reglas para garantizar que todos los servidores SQL usen la versión 12.0. Su acción es denegar a todos los servidores que no cumplen con estos criterios.
- **SUK de cuenta de almacenamiento permitida**: esta definición de directiva tiene un conjunto de condiciones o reglas que determinan si una cuenta de almacenamiento que se está implementado se encuentra dentro de un conjunto de tamaños de SKU. Su acción es denegar a todos los servidores que no cumplen con el conjunto de tamaños de SKU definidos.
- **Tipo de recurso permitido**: esta definición de directiva tiene un conjunto de condiciones o reglas para especificar los tipos de recursos que puede implementar la organización. Su acción es denegar a todos los recursos que no forman parte de esta lista definida.

Para más información sobre las estructuras de las definiciones de directiva, consulte este artículo: [Estructura de definición de directiva](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure).

## <a name="policy-assignment"></a>Asignación de directiva
Una asignación de directiva es una definición de directiva que se asignó para que ocurra dentro de un ámbito específico. Este ámbito puede ir desde un grupo de administración a un grupo de recursos.

Para más información sobre cómo establecer definiciones y asignaciones de directiva, consulte [Información general sobre las directivas de recursos](../azure-resource-manager/resource-manager-policy.md).

## <a name="policy-parameters"></a>Parámetros de directiva
Los parámetros de directiva permiten simplificar la administración de directivas reduciendo el número de definiciones de directiva que debe crear. Cuando crea una definición de directiva, puede definir parámetros para que sea más genérica. Luego puede reutilizar esa definición de directiva en distintos escenarios mediante el establecimiento de valores diferentes (por ejemplo, mediante la especificación de un conjunto de ubicaciones para una suscripción) al asignar la directiva.

Los parámetros se definen o crean cuando se crea una definición de directiva. Cuando se define un parámetro, recibe un nombre y, de manera opcional, un valor. Por ejemplo, podría definir un parámetro para una directiva como ubicación y darle distintos valores, como *EastUS* o *WestUS* al asignar una directiva.

Para más información sobre los parámetros de directiva, consulte [Información general sobre las directivas de recursos: parámetros](../azure-resource-manager/resource-manager-policy.md#parameters).

## <a name="initiative-definition"></a>Definición de iniciativa
Una definición de iniciativa es una colección de definiciones de directiva que están diseñadas para alcanzar un único objetivo general. Por ejemplo, podría crear una iniciativa titulada **Habilitar la supervisión en Azure Security Center**, con el objetivo de supervisar todas las recomendaciones de seguridad disponibles en Azure Security Center.

En esta iniciativa, tendría definiciones de directiva como las siguientes:

1. **Supervisar SQL Database sin cifrar en Security Center**, para supervisar servidores y bases de datos SQL sin cifrar.
2. **Supervisar las vulnerabilidades del SO en Security Center**, para supervisar los servidores que no cumplen con la base de referencia configurada.
3. **Supervisar Endpoint Protection omitido en Security Center**, para supervisar los servidores que no tienen instalado un agente de protección de los puntos de conexión.

## <a name="initiative-assignment"></a>Asignación de iniciativa
Al igual que una asignación de directiva, una asignación de iniciativa es una definición de iniciativa que se asigna a un ámbito específico. Las asignaciones de iniciativa disminuyen la necesidad de crear varias definiciones de iniciativa para cada ámbito. Este ámbito también puede ir desde un grupo de administración a un grupo de recursos.

Del ejemplo anterior, la iniciativa **Habilitar la supervisión en Azure Security Center** se puede asignar a distintos ámbitos. Por ejemplo, una asignación se puede asignar a la suscripción **subscriptionA**, mientras que otra se puede asignar a la suscripción **subscriptionB**.

## <a name="initiative-parameters"></a>Parámetros de iniciativa
Al igual que los parámetros de directiva, los parámetros de iniciativa permiten simplificar la administración de iniciativas mediante la reducción de la redundancia. Los parámetros de iniciativa son esencialmente la lista de los parámetros que las definiciones de directiva usan dentro de la iniciativa.

Por ejemplo, tome un escenario con una definición de iniciativa, **initiativeC**, con dos definiciones de directiva. Cada definición de directiva tiene un parámetro definido:

|Directiva  |nombre del parámetro     |tipo del parámetro  |Nota:                                                                                                |
|--------|----------------------|-------|----------------------------------------------------------------------------------------------------------------|
|policyA |allowedLocations      |array  |Este parámetro espera una lista de cadenas para un valor, porque el tipo de parámetro está definido como una matriz |
|policyB |allowedSingleLocation |cadena |Este parámetro espera una palabra para un valor, porque el tipo de parámetro se definió como una cadena          |

En este escenario, tiene tres opciones en el momento de definir los parámetros de iniciativa para **initiativeC**:

1. Aproveche los parámetros de las definiciones de directiva dentro de esta iniciativa: en este ejemplo, *allowedLocations* y *allowedSingleLocation* se convierten en parámetros de iniciativa para **initiativeC**.
2. Proporcione valores a los parámetros de las definiciones de directiva dentro de esta definición de iniciativa. En este ejemplo, puede proporcionar una lista de ubicaciones al **parámetro de policyA: allowedLocations** y al **parámetro de policyB: allowedSingleLocation**.
También puede proporcionar valores cuando asigne esta iniciativa.
3. Proporcione una lista de las opciones de *value* que se puede usar cuando se asigna esta iniciativa. Esto significa que cuando asigna esta iniciativa, los parámetros inherentes de las definiciones de directiva dentro de la iniciativa solo pueden tener valores provenientes de esta lista.

Por ejemplo, si proporcionó una lista de opciones de valores en el momento de crear la definición de iniciativa, como *EastUS*, *WestUS*, *CentralUS* y *WestEurope*, no podrá ingresar un valor distinto como *Sudeste Asiático* durante la asignación de la iniciativa, porque no forma parte de la lista.

## <a name="recommendations-for-managing-policies"></a>Recomendaciones para la administración de directivas

Cuando cree y administre asignaciones y definiciones de directiva, se recomienda seguir estos punteros:

- Si crea definiciones de directiva en el entorno, se recomienda comenzar con un efecto de auditoría, en contraposición con un efecto de denegación, para llevar un seguimiento del impacto que la definición de directiva tiene en el entorno. Si ya tiene scripts implementados para escalar verticalmente las aplicaciones de manera automática, establecer un efecto de denegación puede impedir las tareas de automatización que ya tiene implementadas.
- Es importante considerar las jerarquías organizativas cuando se crean definiciones y asignaciones. Se recomienda crear definiciones en un nivel más alto, por ejemplo, en el nivel del grupo de administración o de suscripción, y asignarlas en el próximo nivel secundario. Por ejemplo, si crea una definición de directiva en el nivel de grupo de administración, una asignación de directiva de esa definición se puede limitar a un nivel de suscripción.
- Se recomienda usar el plan de tarifa estándar para comprender mejor el estado de cumplimiento del entorno.
- Se recomienda usar siempre las definiciones de iniciativa en lugar de las definiciones de directiva, incluso si solo tiene considerada una directiva. Por ejemplo, si tiene una definición de directiva, *policyDefA* y la crea bajo la definición de iniciativa *initiativeDefC*, si decide crear otra definición de directiva con objetivos como los de *policyDefA*, simplemente puede agregarla en *initiativeDefC* para llevar un mejor seguimiento de ellos.

   Tenga en cuenta que una vez que crea una asignación de iniciativa desde una definición de iniciativa, cualquier definición de directiva nueva que se agregue a la definición de iniciativa se implementará automáticamente en las asignaciones de iniciativa bajo esa definición de iniciativa. Sin embargo, si se introduce un parámetro nuevo en la definición de directiva nueva, debe actualizar la definición de iniciativa y las asignaciones para que reflejen esto mediante la edición de la definición o la asignación.

## <a name="next-steps"></a>Pasos siguientes:
Ahora que tiene información general sobre Azure Policy y algunos de los conceptos clave que estamos presentando, sugerimos seguir estos pasos:

- [Asignación de una definición de directiva](./assign-policy-definition.md)
- [Asignación de una definición de directiva con la CLI de Azure](./assign-policy-definition-cli.md)
- [Asignación de una definición de directiva con PowerShell](./assign-policy-definition-ps.md)
