<properties linkid="" urlDisplayName="" pageTitle="Architecture" metaKeywords="" description="Architecture overview that covers common design patterns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Architecture Overview" authors="waltpo" solutions="" manager="bjsmith" editor="mattshel" />

Arquitectura
============

Aprenda a implementar patrones de diseño común en Azure.

### Conjunto de símbolos/iconos de Azure

[Descargue el conjunto de símbolos/iconos de Azure (en inglés)](http://www.microsoft.com/en-us/download/details.aspx?id=41937) para crear materiales técnicos que describen (o usan) Azure: elementos como diagramas de arquitectura, materiales de formación, presentaciones, hojas de datos, infografía y documentos. Puede descargar los símbolos en los formatos PPT, Visio o PNG. Deseamos saber lo que piensa; para ello, la descarga incluye instrucciones para que entregue sus comentarios.

![Conjunto de símbolos/iconos de Azure](./media/architecture-overview/AzureSymbols.png)

Patrones de diseño
------------------

### [Competing Consumers](http://msdn.microsoft.com/es-es/library/dn568101.aspx)

![Consumidores de la competencia](./media/architecture-overview/CompetingConsumers.png)

Habilite varios consumidores simultáneos para procesar los mensajes que se reciben en el mismo canal de mensajería. Este patrón permite que un sistema procese varios mensajes simultáneamente a fin de optimizar el rendimiento, mejorar la escalabilidad y disponibilidad y equilibrar la carga de trabajo.

### [Command and Query Responsibility Segregation](http://msdn.microsoft.com/es-es/library/dn568103.aspx)

![Segregación de responsabilidad de comandos y consultas](./media/architecture-overview/CQRS.png)

Operaciones de segregación que leen datos desde operaciones que actualizan datos usando interfaces aparte. Este patrón puede maximizar el rendimiento, la escalabilidad y la seguridad; dar apoyo a la evolución del sistema en el tiempo a través de una mayor flexibilidad y evitar que los comandos de actualización provoquen conflictos de fusión en el nivel de dominio.

### [Leader Election](http://msdn.microsoft.com/es-es/library/dn568104.aspx)

![Elección de líder](./media/architecture-overview/LeaderElection.png)

Coordine las acciones realizadas por una colección de instancias de tareas de colaboración en una aplicación distribuida al elegir una instancia como el líder que asume la responsabilidad de administrar las otras instancias. Este patrón puede ayudar a que las instancias de tareas no entren en conflicto entre sí, causar disputa por los recursos compartidos o interferir sin darse cuenta con el trabajo que realizan las otras instancias de tareas.

### [Pipes and Filters](http://msdn.microsoft.com/es-es/library/dn568100.aspx)

![Canalizaciones y filtros](./media/architecture-overview/PipesAndFilters.png)

Descomponga una tarea que realiza procesamiento complejo en una serie de elementos discretos que se pueden volver a usar. Este patrón puede mejorar el rendimiento, la escalabilidad y la reutilización al permitir que los elementos de la tarea que realizan el procesamiento se implementen y escalen de manera independiente.

### [Valet Key](http://msdn.microsoft.com/es-es/library/dn568102.aspx)

![Clave de valet](./media/architecture-overview/ValetKey.png)

Use un token o una clave que le otorgue a los clientes acceso directo restringido a un recurso o servicio específico para descargar las operaciones de transferencia de datos desde el código de la aplicación. Este patrón es especialmente útil en las aplicaciones que usan sistemas de almacenamiento o colas hospedados en la nube, y puede minimizar los costos y maximizar la escalabilidad y el rendimiento.

### Orientación adicional

Para obtener información sobre más patrones de diseño comunes en Azure, consulte [Cloud Design Patterns](http://msdn.microsoft.com/es-es/library/dn568099.aspx).

