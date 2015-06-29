<properties
 pageTitle="¿Qué es Programador?"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article" 
 ms.date="05/12/2015"
 ms.author="krisragh"/>

# ¿Qué es Programador?

Programador de Azure le permite describir mediante declaración las acciones para ejecutar en la nube. A continuación, programa y ejecuta esas acciones de forma automática. Para hacerlo, Programador de Azure usa el código del [Portal de Azure](scheduler-get-started-portal.md), la [API de REST](https://msdn.microsoft.com/library/dn528946) o PowerShell.

Programador de Azure mantiene, administra, programa e invoca un trabajo programado. Programador de Azure no hospeda ninguna carga de trabajo ni ejecuta código. Solo _invoca_ código hospedado en cualquier otro lugar; este código puede estar hospedado en Azure, localmente o con otro proveedor. Invoca a través de HTTP, HTTPS o una cola de almacenamiento.

Programador de Azure programa trabajos, mantiene un historial de los resultados de la ejecución de trabajos que se puede consultar y programa, de manera determinante y confiable, las cargas de trabajo que se ejecutarán. Scripts programados de Servicios móviles de Azure, WebJobs de Aplicaciones web de Azure y otras funcionalidades de programación de Azure usan Programador de Azure en segundo plano. La [API de REST de Programador](https://msdn.microsoft.com/library/dn528946) ayuda a administrar la comunicación para estas acciones. De ese modo, Programador admite [programaciones complejas y periodicidad avanzada con facilidad](scheduler-advanced-complexity.md).

Existen varios escenarios que se prestan para el uso de Programador de Azure. Por ejemplo:

+ _Acciones de aplicaciones recurrentes_: recopilación periódica de datos desde Twitter y recopilación de los datos en una fuente.
+ _Mantenimiento diario_: eliminación diaria de registros, realización de copias de seguridad y otras tareas de mantenimiento. Por ejemplo, una administradora podría elegir crear copias de seguridad de su base de datos a la 01:00 a.m. cada día durante los próximos nueve meses.

Programador permite crear, actualizar, eliminar, ver y administrar ["colecciones de trabajos" y "trabajos"](scheduler-concepts-terms.md) de manera programática, mediante scripts y en el portal.

## Otras referencias

 [Conceptos, terminología y jerarquía de entidades de Programador](scheduler-concepts-terms.md)

 [Introducción al uso de Programador en el Portal de administración](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Creación de programaciones complejas y periodicidad avanzada con Programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de API de REST de Programador](https://msdn.microsoft.com/library/dn528946)

 [Referencia de cmdlets de PowerShell de Programador](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Programador](scheduler-outbound-authentication.md)
 

<!---HONumber=58_postMigration-->