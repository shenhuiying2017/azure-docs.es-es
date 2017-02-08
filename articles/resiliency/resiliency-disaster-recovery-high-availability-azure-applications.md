---
title: "Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure | Microsoft Docs"
description: "Información general técnica y detallada sobre cómo diseñar aplicaciones para alta disponibilidad y recuperación ante desastres en Microsoft Azure."
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: e13d5f49-2b91-44ba-829a-1e0f1fceaae8
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: ee3e7103bd647c2d8651ec912fbb764a342781e2


---
# <a name="disaster-recovery-and-high-availability-for-applications-built-on-microsoft-azure"></a>Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure
## <a name="introduction"></a>Introducción
Este artículo se centra en la alta disponibilidad para aplicaciones que se ejecutan en Azure. Una estrategia global para la alta disponibilidad abarca también el aspecto de la recuperación ante desastres. Para elaborar planes ante errores y desastres en la nube, es necesario reconocer los errores con rapidez. A continuación, se implementa una estrategia que se adapte al tiempo de inactividad de la aplicación que se pueda tolerar. Además, se debe considerar el grado de pérdida de datos que la aplicación puede admitir sin causar repercusiones negativas para la empresa cuando se restauren.

La mayoría de las empresas afirman estar preparadas para errores temporales y a gran escala. Sin embargo, antes de contestar esa pregunta, piense en las siguientes: ¿ensayan en su empresa estos errores? ¿Han probado la recuperación de bases de datos para asegurarse de que estén instaurados los procesos correctos? Probablemente no. Eso es porque una recuperación ante desastres satisfactoria comienza con gran cantidad de planeamiento y diseño para implementar estos procesos. Al igual que muchos otros requisitos no funcionales, como la seguridad, la recuperación ante desastres no consigue casi nunca el análisis inicial y la asignación de tiempo que realmente requiere. Además, la mayoría de las empresas carecen de presupuesto para regiones distribuidas geográficamente con capacidad redundante. Por lo tanto, es frecuente que incluso las aplicaciones críticas queden excluidas de un planeamiento satisfactorio de la recuperación ante desastres.

Las plataformas en la nube, como Azure, proporcionan regiones dispersas geográficamente por el mundo. Estas plataformas también ofrecen funcionalidades que admiten disponibilidad y diversos escenarios de recuperación ante desastres. Ahora se puede dedicar la atención necesaria a todas las aplicaciones críticas en la nube para preparar al sistema ante posibles desastres. Azure integra resistencia y recuperación ante desastres en muchos de sus servicios. Debe estudiar detenidamente estas características de la plataforma y complementarlas con estrategias para aplicaciones.

En este artículo se describen los pasos necesarios en cuanto a arquitectura que debe realizar para preparar una implementación de Azure para enfrentarse a desastres. Después, puede implementar el proceso de continuidad empresarial de mayor envergadura. Un plan de continuidad empresarial es una guía para mantener las operaciones en funcionamiento en condiciones adversas. Podría tratarse de un error tecnológico, como un servicio inactivo, o de un desastre natural, como una tormenta o una interrupción del suministro eléctrico. La resistencia de una aplicación ante los desastres es solo un subconjunto del proceso de recuperación ante desastres más amplio, tal y como se describe en este documento de NIST: [Contingency Planning Guide for Information Technology Systems](https://www.fismacenter.com/sp800-34.pdf)(Guía de planeamiento ante contingencias para sistemas de tecnologías de la información).

En las secciones siguientes, se definen los distintos niveles de errores, las técnicas para tratarlos y las arquitecturas que admiten estas técnicas. Esta información aporta datos para sus procesos y procedimientos de recuperación ante desastres para asegurar que su estrategia para tal fin funcione de forma correcta y eficaz.

## <a name="characteristics-of-resilient-cloud-applications"></a>Características de las aplicaciones en la nube resistentes
Una aplicación bien diseñada puede resistir errores de capacidad en un nivel táctico y también tolerar errores estratégicos en todo el sistema a nivel de región. En las secciones siguientes, se define la terminología que se utiliza en todo el documento para describir los diversos aspectos de los servicios en la nube resistentes.

### <a name="high-availability"></a>Alta disponibilidad
Una aplicación en la nube de alta disponibilidad implementa estrategias para asumir la interrupción de dependencias tales como los servicios administrados ofrecidos por la plataforma en la nube. A pesar de posibles errores en las funcionalidades de la plataforma en la nube, este enfoque permite que la aplicación siga exhibiendo las características sistémicas funcionales y no funcionales que se esperan. Esto se trata en profundidad en la serie de vídeos de Channel 9 [Failsafe: Guidance for Resilient Cloud Architectures](https://channel9.msdn.com/Series/FailSafe)(Failsafe: Guía para las arquitecturas resistentes en la nube).

Cuando implemente la aplicación, debe tener en cuenta la probabilidad de que se produzca una interrupción de la capacidad. Además, considere las repercusiones que podría acarrear una interrupción para la aplicación desde la perspectiva empresarial antes de dedicarse en profundidad a las estrategias de implementación. Sin la debida consideración de las repercusiones empresariales y la probabilidad de que se produzca la condición de riesgo, la implementación puede resultar costosa y potencialmente innecesaria.

Usemos un automóvil como analogía para considerar la alta disponibilidad. Incluso con piezas de calidad y una ingeniería excelente no se evitan averías ocasionales. Por ejemplo, cuando se pincha una rueda, el automóvil sigue funcionando, pero con menos prestaciones. Si ha planeado para esta eventualidad, puede usar uno de los neumáticos de repuesto hasta que llegue a un taller de reparación. Aunque no puede ir a gran velocidad con el neumático de repuesto, puede seguir llevando el vehículo hasta que lo cambie. De forma similar, un servicio en la nube que cuente con planes para la posible pérdida de funcionalidades puede evitar que un problema relativamente poco importante deje toda la aplicación sin funcionar. Esto es así incluso si el servicio en la nube se debe ejecutar con funcionalidad degradada.

Existen unas pocas características clave para los servicios en la nube de alta disponibilidad: disponibilidad, escalabilidad y tolerancia a errores. Aunque están interrelacionadas, es importante entender cada una individualmente y cómo contribuyen a la disponibilidad general de la solución.

### <a name="availability"></a>Disponibilidad
Una aplicación disponible tiene en cuenta la disponibilidad de su infraestructura subyacente y los servicios dependientes. Las aplicaciones disponibles eliminan los únicos puntos de error mediante la redundancia y un diseño resistente. Al ampliar la perspectiva para abordar la disponibilidad en Azure, es importante comprender el concepto de la disponibilidad efectiva de la plataforma. Para la disponibilidad efectiva, se tienen en cuenta los Acuerdos de Nivel de Servicio (SLA) de cada servicio dependiente y su efecto acumulado sobre la disponibilidad total del sistema.

La disponibilidad del sistema mide el porcentaje de un período de tiempo que el sistema podrá funcionar. Por ejemplo, el SLA de disponibilidad de al menos dos instancias de un rol web o de trabajo de Azure es del 99,95 % (sobre 100 %). No se mide el rendimiento ni la funcionalidad de los servicios que se ejecutan en esos roles. Sin embargo, la disponibilidad efectiva del servicio en la nube también se ve afectada por los diversos SLA de los demás servicios dependientes. Cuantos más elementos compongan el sistema, más cuidado se debe tener para garantizar que la aplicación pueda cumplir de forma resistente los requisitos de disponibilidad de sus usuarios finales.

Tenga en cuenta los siguientes SLA para un servicio de Azure que utiliza estos servicios de Azure: Proceso, Base de datos SQL de Azure y Almacenamiento de Azure.

| Servicio de Azure | Contrato de nivel de servicio | Inactividad posible en minutos al mes (30 días) |
|:--- |:--- |:---:|
| Proceso |99,95 % |21,6 minutos |
| Base de datos SQL |99,99% |4,3 minutos |
| Almacenamiento |99,90 % |43,2 minutos |

Debe planear para la posibilidad de que todos los servicios dejen de funcionar en momentos diferentes. En este ejemplo simplificado, el número total de minutos al mes que la aplicación puede estar inactiva es 108 minutos. En un mes de 30 días hay un total de 43 200 minutos. 108 minutos corresponde al 0,25 % del número total de minutos en un mes de 30 días (43 200 minutos). Esto proporciona una disponibilidad efectiva del 99,75 % para el servicio en la nube.

Sin embargo, si se usan las técnicas de disponibilidad descritas en este artículo, es posible mejorarla aún más. Por ejemplo, si diseña la aplicación para que continúe ejecutándose cuando Base de datos SQL no esté disponible, puede eliminar ese elemento de la ecuación. Esto puede suponer que la aplicación se ejecute con funcionalidades reducidas, por lo que también hay que considerar los requisitos empresariales. Para ver una lista completa de los SLA de Azure, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

### <a name="scalability"></a>Escalabilidad
La escalabilidad afecta directamente a la disponibilidad. Una aplicación que genere un error bajo una mayor carga deja de estar disponible. Las aplicaciones escalables son capaces de satisfacer la creciente demanda con resultados constantes en períodos aceptables. Cuando un sistema es escalable, realiza el escalado de forma horizontal o vertical para administrar los aumentos de carga sin por ello dejar de ofrecer un rendimiento constante. En términos básicos, el escalado horizontal agrega más máquinas del mismo tamaño (procesador, memoria y ancho de banda) mientras que el vertical aumenta el tamaño de las máquinas existentes. En Azure, dispone de opciones de escalado vertical para seleccionar diversos tamaños de máquina para los procesos. No obstante, si se cambia el tamaño de la máquina, es necesaria una nueva implementación. Por lo tanto, las soluciones más flexibles son las diseñadas para el escalado horizontal. Esto es especialmente cierto para los procesos, ya que puede aumentar fácilmente el número de instancias en ejecución de cualquier rol web o de trabajo. Estas instancias adicionales controlan el mayor volumen de tráfico mediante el portal web de Azure, scripts de PowerShell o código. Fundamente esta decisión en los incrementos de determinadas métricas supervisadas. En este escenario, el rendimiento de usuario o las métricas no sufren un descenso notable bajo carga. Por lo general, los roles web y de trabajo almacenan cualquier estado externamente. Esto permite flexibilidad en el equilibrio de la carga y el control estable de cualquier cambio en los números de instancias. El escalado horizontal también funciona bien con los servicios, como Almacenamiento de Azure, que no proporcionan opciones por niveles para el escalado vertical.

Las implementaciones en la nube deben considerarse como una colección de unidades de escalado. Esto permite que la aplicación sea flexible al atender las necesidades de rendimiento de los usuarios finales. Las unidades de escalado son más fáciles de visualizar en el nivel web y de servidor de aplicaciones. Esto se debe a que Azure ya proporciona nodos de proceso sin estado mediante los roles web y de trabajo. Si se agregan más unidades de escalado de procesos a la implementación, no se causará ningún efecto secundario sobre la administración del estado de las aplicaciones. Esto se debe a que las unidades de escalado de los procesos carecen de estado. Una unidad de escalado de almacenamiento se encarga de administrar una partición de datos (estructurados o no estructurados). Algunos ejemplos de unidades de escalado de almacenamiento son partición de Tabla de Azure, contenedor de blobs de Azure y Base de datos SQL de Azure. Incluso el uso de varias cuentas de Almacenamiento de Azure ejerce un impacto directo sobre la escalabilidad de la aplicación. Debe diseñar un servicio en la nube muy escalable para incorporar varias unidades de escalado de almacenamiento. Por ejemplo, si una aplicación usa datos relacionales, reparta los datos entre varias bases de datos SQL. Esto permite que el almacenamiento se mantenga al día con el modelo flexible de unidad de escalado de procesos. De forma similar, Almacenamiento de Azure permite esquemas de partición de datos que requieren diseños deliberados para satisfacer las necesidades de rendimiento de la capa de proceso. Para ver una lista de procedimientos recomendados para diseñar servicios en la nube escalables, consulte [Best Practices for the Design of Large-Scale Services on Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) (Procedimientos recomendados para el diseño de servicios a gran escala en Servicios en la nube de Azure).

### <a name="fault-tolerance"></a>Tolerancia a errores
Las aplicaciones deben dar por sentado que toda la funcionalidad dependiente en la nube puede quedar inactiva y lo estará en algún momento. Una aplicación con tolerancia a errores detecta los elementos con errores y los sortea para continuar devolviendo los resultados correctos en un período concreto. En el caso de las condiciones de error transitorias, un sistema con tolerancia a errores empleará una directiva de reintentos. Para los errores más graves, la aplicación puede detectar problemas y conmutar por error a hardware o planes de contingencia alternativos hasta que se corrija el error. Una aplicación de confianza puede administrar correctamente el error de uno o más elementos y seguir funcionando de forma satisfactoria. Las aplicaciones con tolerancia a errores pueden aplicar una o varias estrategias de diseño, como redundancia, replicación o funcionalidad degradada.

## <a name="disaster-recovery"></a>Recuperación ante desastres
Una implementación en la nube podría dejar de funcionar debido a la inactividad sistémica de los servicios dependientes o de la infraestructura subyacente. En tales condiciones, un plan de continuidad empresarial desencadena el proceso de recuperación ante desastres. Por lo general, este proceso implica tanto al personal de operaciones como procedimientos automatizados con el objetivo de reactivar la aplicación en una región disponible. Esto requiere la transferencia de los usuarios, los datos y los servicios de la aplicación a la nueva región. También supone utilizar medios de copia de seguridad o replicación continuada.

Volvamos a la anterior analogía que comparaba la alta disponibilidad con la capacidad de recuperarse del pinchazo de una rueda gracias al neumático de repuesto. En contraste, la recuperación ante desastres engloba los pasos que se llevan a cabo después de un accidente, cuando el automóvil ya no funciona. En ese caso, la mejor solución es encontrar una manera eficaz de cambiar de vehículo llamando a un servicio de viajes o a un amigo. En este escenario, es probable que el retraso antes de volver a la carretera sea mayor. Además, la reparación y la vuelta al vehículo original también revisten mayor complicación. Del mismo modo, la recuperación ante desastres en otra región es una tarea compleja que suele suponer algún tiempo de inactividad y la posible pérdida de datos. Para comprender y evaluar mejor las estrategias de recuperación ante desastres, es importante definir dos términos: objetivo de tiempo de recuperación (RTO) y objetivo de punto de recuperación (RPO).

### <a name="recovery-time-objective"></a>Objetivo de tiempo de recuperación
El objetivo de tiempo de recuperación (RTO) es la cantidad máxima de tiempo asignado para restaurar la funcionalidad de la aplicación. Se basa en los requisitos empresariales y guarda relación con la importancia de la aplicación. Las aplicaciones empresariales críticas requieren un RTO bajo.

### <a name="recovery-point-objective"></a>Objetivo de punto de recuperación
El objetivo de punto de recuperación (RPO) es el período aceptable de pérdida de datos debido al proceso de recuperación. Por ejemplo, si el RPO es de una hora, debe hacer una copia de seguridad completa de los datos o replicarlos al menos una vez cada hora. Una vez que se recupera la aplicación en una región alternativa, puede que la copia de seguridad carezca de los datos correspondientes a un máximo de una hora. Al igual que el RTO, las aplicaciones críticas necesitan un RPO mucho menor.

## <a name="checklist"></a>Lista de comprobación
Resumamos los puntos clave que se han tratado en este artículo (y los artículos relacionados sobre la [alta disponibilidad](resiliency-high-availability-azure-applications.md) y la [recuperación ante desastres](resiliency-disaster-recovery-azure-applications.md) para aplicaciones de Azure). Este resumen servirá de lista de comprobación de aquellos elementos que debe considerar para su planeamiento para la disponibilidad y la recuperación ante desastres. Se trata de los procedimientos recomendados que han resultados útiles a los clientes que buscan en serio la implementación de una solución satisfactoria.

1. Lleve a cabo una evaluación de los riesgos para cada aplicación, ya que cada una puede tener requisitos diferentes. Algunas aplicaciones son más críticas que otras y justificarían el costo adicional de diseñarlas para la recuperación ante desastres.
2. Utilice esta información para definir el RTO y RPO para cada aplicación.
3. Diseñe teniendo en cuenta un error potencial, empezando por la arquitectura de la aplicación.
4. Implemente procedimientos recomendados para la alta disponibilidad, al mismo tiempo que equilibra el costo, la complejidad y el riesgo.
5. Implemente planes y procesos de recuperación ante desastres.
   * Considere la posibilidad de que se produzcan errores que abarquen el nivel de módulo e incluso lleguen a una interrupción completa del funcionamiento de la nube.
   * Establezca estrategias de copia de seguridad para todos los datos transaccionales y de referencia.
   * Elija una arquitectura de recuperación ante desastres multisitio.
6. Defina un propietario específico para los procesos, la automatización y las pruebas de la recuperación ante desastres. El propietario debe administrar todo el proceso y responsabilizarse de él.
7. Documente los procesos de modo que se puedan repetir con facilidad. Aunque existe un propietario, debería haber varias personas que entiendan y puedan seguir los procesos en caso de emergencia.
8. Entrene al personal para implementar el proceso.
9. Realice con regularidad simulaciones de desastres, tanto de entrenamiento como para validación del proceso.

## <a name="summary"></a>Resumen
Cuando se produce un error en el hardware o las aplicaciones dentro de Azure, las técnicas y estrategias para su administración son diferentes a las empleadas cuando el error se produce en sistemas locales. La razón principal es que las soluciones en la nube suelen poseer más dependencias en la infraestructura distribuida por una región de Azure y se administran como servicios independientes. Debe tratar los errores parciales por medio de técnicas de alta disponibilidad. Para administrar los errores más graves, posiblemente causados por un desastre, utilice estrategias de recuperación ante desastres.

Azure detecta y controla muchos errores, pero hay muchos tipos que requieren estrategias específicas de la aplicación. Debe prepararse para los errores de aplicaciones, servicios y datos, así como administrarlos, de forma activa.

Al crear el plan de recuperación ante desastres y disponibilidad de la aplicación, considere las consecuencias empresariales del error de la aplicación. La definición de los procesos, las directivas y los procedimientos para restaurar sistemas críticos después de un evento catastrófico conlleva tiempo, planeamiento y dedicación. Y una vez establecidos los planes, no se puede detener ahí. Debe analizar con regularidad, probar y mejorar continuamente los planes según su cartera de aplicaciones, las necesidades del negocio y las tecnologías disponibles. Azure proporciona nuevas funcionalidades y nuevos desafíos para crear aplicaciones sólidas que resistan los errores.

## <a name="additional-resources"></a>Recursos adicionales
[Alta disponibilidad para aplicaciones creadas en Microsoft Azure](resiliency-high-availability-azure-applications.md)

[Recuperación ante desastres para aplicaciones generadas en Microsoft Azure](resiliency-disaster-recovery-azure-applications.md)

[Guía técnica sobre resistencia en Azure](resiliency-technical-guidance.md)

[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](../sql-database/sql-database-business-continuity.md)

[Alta disponibilidad y recuperación ante desastres para SQL Server en Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md)

[Failsafe: Guidance for resilient cloud architectures (Failsafe: Guía para las arquitecturas resistentes en la nube)](https://channel9.msdn.com/Series/FailSafe)

[Best Practices for the design of large-scale services on Azure Cloud Services (Procedimientos recomendados para el diseño de servicios a gran escala en Servicios en la nube de Azure)](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

## <a name="next-steps"></a>Pasos siguientes
Este artículo forma parte de una serie enfocada a la recuperación ante desastres y la alta disponibilidad para las aplicaciones de Azure. El siguiente artículo de esta serie es [Alta disponibilidad para aplicaciones creadas en Microsoft Azure](resiliency-high-availability-azure-applications.md).




<!--HONumber=Jan17_HO2-->


