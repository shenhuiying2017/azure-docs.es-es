<properties
   pageTitle="Información general sobre Testability"
   description="En este artículo se describe la característica de Testability en Microsoft Azure Fabric de servicio."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/13/2015"
   ms.author="rsinha"/>

# Información general sobre Testability

Testability es un conjunto de herramientas diseñadas de manera específica para probar los servicios basados en Microsoft Azure Service Fabric. Las herramientas permiten al desarrollador provocar fácilmente errores significativos y ejecutar escenarios de prueba para ejercer y validar los distintos estados y transiciones que experimentará un servicio a lo largo de su duración, y todo ello de forma segura y controlada.

Testability proporciona acciones y escenarios que permiten realizar esta función. Las acciones son los errores individuales que se dirigen a un servicio para probarlo. Un programador del servicio puede utilizar como bloques de creación para escribir escenarios complicados. Por ejemplo:

  + Reiniciar un nodo para simular cualquier número de situaciones en que un equipo o máquina virtual se reinicia.
  + Mover una réplica de un servicio con estado para simular el equilibrio de carga, la conmutación por error o la actualización de la aplicación.
  + Invocar la pérdida de quórum en un servicio con estado para crear una situación en la que las operaciones de escritura no pueden continuar porque no hay suficientes réplicas "secundarias" o "de copia de seguridad" para aceptar los datos nuevos.
  + Invocar la pérdida de datos en un servicio con estado para crear una situación en la que se borra completamente todo el estado en memoria.

Los escenarios son pruebas completas que se componen de una o varias acciones y, puesto que las acciones no son más que comandos de PowerShell y funciones de C#, pueden adoptar cualquier forma: servicios de ejecución prolongada, comandos de PowerShell, aplicaciones de línea de comandos, etc. En Testability se incluyen dos escenarios de serie:

  + Prueba de caos
  + Prueba de conmutación por error

Testability expone API tanto de PowerShell como de C#. Esto permite el desarrollador del servicio tener más agilidad con el scripting de PowerShell y un mayor control con las API de C# como y cuando sea necesario.

## Importancia de Testability

Service Fabric facilita considerablemente el trabajo de escribir y administrar aplicaciones escalables distribuidas. El objetivo del componente de Testability en Service Fabric es facilitar la realización de pruebas de una aplicación distribuida. Durante las pruebas deben resolverse tres problemas principales:

1. Simular o generar errores que pueden producirse en los escenarios del mundo real: uno de los aspectos importantes de Service Fabric es que permite a las aplicaciones distribuidas recuperarse de varios errores. Sin embargo, para probar que la aplicación puede recuperarse de estos errores se necesita un mecanismo que simule o genere estos errores reales en un entorno de prueba controlado.
2. La capacidad de generar los errores correlacionados: mientras los errores básicos del sistema como un error de red o los errores del equipo son fáciles de generar de forma individual. Generar un número significativo de escenarios que pueden ocurrir en el mundo real como resultado de las interacciones de estos errores individuales no es trivial.
3. Experiencia unificada a través de varios niveles de desarrollo e implementación: existen muchos sistemas de inserción de errores que proporcionan la capacidad para realizar varios tipos de errores. Sin embargo, la experiencia en todos ellos es irregular al pasar de los escenarios de desarrollo one box a ejecutar las mismas pruebas en entornos de prueba grandes y a utilizarlas para pruebas en producción.

Aunque existen varios mecanismos para resolver los problemas mencionados, falta un sistema que haga lo mismo con las garantías requeridas, desde un entorno del desarrollador one box hasta la prueba en clústeres de producción. El componente Testability permite a los desarrolladores de aplicaciones concentrarse en probar su lógica de negocios. La característica de comprobación proporciona todas las capacidades necesarias para probar la interacción del servicio con el sistema distribuido subyacente.

### Simulación/generación de escenarios de error reales
Para probar la solidez de un sistema distribuido frente a errores, se necesita un mecanismo para generar errores. Aunque, en teoría, parezca fácil generar un error, como un nodo inactivo, empieza a plantear el mismo conjunto de problemas de coherencia que Service Fabric intenta resolver. Por ejemplo, si se desea apagar un nodo, este es el flujo de trabajo requerido:

1. Desde el cliente, generar una solicitud de apagado del nodo.
2. Enviar la solicitud al nodo correcto.
	1. Si no se encuentra el nodo, debería aparecer un error.
	2. Si se encuentra el nodo, debería devolverla sólo si el nodo está apagado.

Desde la perspectiva de la prueba, necesita saber que cuando este error se provoca, el error sucede para comprobar el error. La garantía que proporciona Windows Fabric es que el nodo se quedará fuera de servicio o ya estará fuera de servicio cuando el comando alcance el nodo. En cualquier caso, la prueba debe ser capaz de deducir correctamente el estado y realizar, o no, su validación correctamente. Un sistema implementado fuera de Service Fabric para realizar el mismo conjunto de errores podría detectar un gran número de problemas de red, hardware y software, lo que impediría que proporcione las garantías indicadas anteriormente. Si surgen los problemas indicados anteriormente, Service Fabric reconfigurará el estado del clúster para solucionar los problemas y, por tanto, Testability podría ofrecer el conjunto correcto de garantías.

### Generación de los eventos y el escenario requeridos
Aunque no es fácil simular un error real de forma coherente, la capacidad para generar errores correlacionados es aún más difícil. Por ejemplo, una pérdida de datos se produce en un servicio con estado guardado cuando ocurre lo siguiente:

1. En la replicación solo se detecta un quórum de escritura de las réplicas. Todas las réplicas secundarias se retrasan, con respecto a la principal.
2. El quórum de escritura pasa a estar fuera de servicio porque las réplicas se quedan fuera de servicio (debido a que el paquete de código o nodo están fuera de servicio).
3. El quórum de escritura no puede realizar la copia de seguridad porque se han perdido los datos de las réplicas pierde (debido a que el disco está dañado o al restablecimiento de la imagen inicial de la máquina).

Estos errores correlacionados realmente suceden en el mundo real (aunque no con tanta frecuencia como los errores individuales). La capacidad de probar estos escenarios antes de que se produzcan en producción es fundamental. Además, la capacidad de simularlos en la carga de trabajo de producción de forma controlada (durante el día, con todos los ingenieros en su lugar de trabajo) es preferible a se produzcan por primera vez en entornos de producción a las 2:00 de la mañana.

### Experiencia unificada en distintos entornos
Tradicionalmente se han creado tres conjuntos diferentes de experiencias: una para el entorno de desarrollo, otra para las pruebas y otra para producción. Este era el modelo:

1. En el entorno de desarrollo, producir transiciones de estado que permitan realizar pruebas unitarias de los métodos individuales.
2. En el entorno de prueba, producir errores que permitan pruebas completas que provoquen distintos escenarios de error.
3. Mantener el entorno de producción intacto en el que no se permitan errores no naturales y asegurarse de que se produce una respuesta humana extremadamente rápida ante cualquier error.

En Service Fabric, a través del módulo y servicio de Testability, proponemos cambiarlo para que se use la misma metodología desde el entorno de desarrollo hasta la producción. Hay dos formas de lograrlo: 1. Para inducir errores controlados, use las API de Testability desde un entorno one box hasta los clústeres de producción. 2. Para dar al clúster una fiebre que provoque la inducción automática de errores, use el servicio de Testability para generar errores automáticos. El control de la tasa de errores a través de la configuración permite que el mismo servicio se pruebe de manera diferente en distintos entornos.

Con Service Fabric, aunque la escala de errores sería diferente en los distintos entornos el mecanismo real sería idéntico. Esto permite un código mucho más rápido para el proceso de la implementación y la capacidad para probar los servicios con carga real.

## Uso de Testability
### Uso de Testability en C## 
En System.Fabric.Testability.dll están presentes las distintas características de Testability. Este archivo dll puede encontrarse en el paquete de NuGet Microsoft.ServiceFabric.Testability.nupack. Para utilizar las características de Testability, incluya el paquete de NuGet como referencia en el proyecto.

## Uso de Testability en PowerShell
Para usar Testability PowerShell, se requiere una instalación del MSI en tiempo de ejecución. Una vez instalado el MSI, el módulo ServiceFabricTestability PowerShell se carga automáticamente para que los desarrolladores lo usen.

## Conclusión
Para crear los verdaderos servicios de escala en la nube, es muy crítica la capacidad para garantizar que los servicios pueden resistir errores reales antes de que se implementa (y también mientras en la implementación de producción). En el actual mundo de servicios, también es muy importante la capacidad para innovar y mover código a producción. La característica de Testability de Service Fabric permite a los desarrolladores de servicios realizar las operaciones anteriores con precisión.

## Pasos siguientes

- [Acciones de Testability](service-fabric-testability-actions.md)
- [Escenarios de Testability](service-fabric-testability-actions.md)
- Procedimientos para probar un servicio
	- [Simulación de errores durante las cargas de trabajo del servicio](service-fabric-testability-workload-tests.md)
   - [Errores de comunicación entre servicios](service-fabric-testability-scenarios-service-communication.md)

 

<!---HONumber=Oct15_HO3-->