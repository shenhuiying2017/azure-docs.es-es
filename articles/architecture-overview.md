<properties linkid="develop-net-architecture sublanding" urlDisplayName="" pageTitle="Architecture" metaKeywords="" description="Architecture overview that covers common design patterns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Architecture Overview" authors="robb" solutions="" manager="johndaw" editor="mattshel" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robb"></tags>

# Arquitectura

Aprenda a implementar patrones de diseño común en Azure.

### Conjunto de símbolos/iconos de Azure

[Descargue el conjunto de símbolos/iconos de Azure][] para crear materiales técnicos que describen (o usan) Azure: elementos como diagramas de arquitectura, materiales de formación, presentaciones, hojas de datos, infografía y documentos. Puede descargar los símbolos en los formatos PPT, Visio o PNG. Deseamos saber lo que piensa; para ello, la descarga incluye instrucciones para que entregue sus comentarios.

![Conjunto de símbolos/iconos de Azure][]

## Patrones de diseño

### [Consumidores de la competencia][]

![Consumidores de la competencia][1]

Habilite varios consumidores simultáneos para procesar los mensajes que se reciben en el mismo canal de mensajería. Este patrón permite que un sistema procese varios mensajes simultáneamente a fin de optimizar el rendimiento, mejorar la escalabilidad y disponibilidad y equilibrar la carga de trabajo.

### [Segregación de responsabilidad de comandos y consultas][]

![Segregación de responsabilidad de comandos y consultas][2]

Operaciones de segregación que leen datos desde operaciones que actualizan datos usando interfaces aparte. Este patrón puede maximizar el rendimiento, la escalabilidad y la seguridad; dar apoyo a la evolución del sistema en el tiempo a través de una mayor flexibilidad y evitar que los comandos de actualización provoquen conflictos de fusión en el nivel de dominio.

### [Elección de líder][]

![Elección de líder][3]

Coordine las acciones realizadas por una colección de instancias de tareas de colaboración en una aplicación distribuida al elegir una instancia como el líder que asume la responsabilidad de administrar las otras instancias. Este patrón puede ayudar a que las instancias de tareas no entren en conflicto entre sí, causar disputa por los recursos compartidos o interferir sin darse cuenta con el trabajo que realizan las otras instancias de tareas.

### [Canalizaciones y filtros][]

![Canalizaciones y filtros][4]

Descomponga una tarea que realiza procesamiento complejo en una serie de elementos discretos que se pueden volver a usar. Este patrón puede mejorar el rendimiento, la escalabilidad y la reutilización al permitir que los elementos de la tarea que realizan el procesamiento se implementen y escalen de manera independiente.

### [Clave de valet][]

![Clave de valet][5]

Use un token o una clave que le otorgue a los clientes acceso directo restringido a un recurso o servicio específico para descargar las operaciones de transferencia de datos desde el código de la aplicación. Este patrón es especialmente útil en las aplicaciones que usan sistemas de almacenamiento o colas hospedados en la nube, y puede minimizar los costos y maximizar la escalabilidad y el rendimiento.

### Orientación adicional

Para obtener información sobre más patrones de diseño comunes en Azure, consulte [Cloud Design Patterns][].

  [Descargue el conjunto de símbolos/iconos de Azure]: http://www.microsoft.com/es-es/download/details.aspx?id=41937
  [Conjunto de símbolos/iconos de Azure]: ./media/architecture-overview/AzureSymbols.png
  [Consumidores de la competencia]: http://msdn.microsoft.com/es-es/library/dn568101.aspx
  [1]: ./media/architecture-overview/CompetingConsumers.png
  [Segregación de responsabilidad de comandos y consultas]: http://msdn.microsoft.com/es-es/library/dn568103.aspx
  [2]: ./media/architecture-overview/CQRS.png
  [Elección de líder]: http://msdn.microsoft.com/es-es/library/dn568104.aspx
  [3]: ./media/architecture-overview/LeaderElection.png
  [Canalizaciones y filtros]: http://msdn.microsoft.com/es-es/library/dn568100.aspx
  [4]: ./media/architecture-overview/PipesAndFilters.png
  [Clave de valet]: http://msdn.microsoft.com/es-es/library/dn568102.aspx
  [5]: ./media/architecture-overview/ValetKey.png
  [Cloud Design Patterns]: http://msdn.microsoft.com/es-es/library/dn568099.aspx
