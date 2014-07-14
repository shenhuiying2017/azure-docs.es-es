
# Soporte de AMQP 1.0 en el Bus de servicio

> Tanto el Bus de servicio de Azure como el Bus de Servicio para Windows Server (Bus de servicio 1.1) admiten el protocolo AMQP (Advanced Message Queueing Protocol) 1.0. AMQP le permite construir aplicaciones híbridas, entre plataformas, utilizando un protocolo estándar abierto. Puede construir aplicaciones mediante componentes creados con distintos lenguajes y marcos, y que se ejecutan en diferentes sistemas operativos. Todos estos componentes se pueden conectar al Bus de servicio e intercambiar directamente mensajes empresariales estructurados de manera eficaz y con total fidelidad.

## Introducción: &iquest;Qué es AMQP 1.0 y por qué es tan importante?

Tradicionalmente, los productos de middleware orientados a mensajes utilizaban protocolos propietarios para la comunicación entre las aplicaciones cliente y los agentes. Esto significa que una vez seleccionado el agente de mensajes de un proveedor particular, usted debía utilizar las bibliotecas de dicho proveedor para conectar las aplicaciones cliente con ese agente. Esto ocasionaba un cierto grado de dependencia con ese proveedor, ya que la portabilidad de una aplicación a otro producto requería que se volvieran a codificar todas las aplicaciones relacionadas.

Es más, la conexión de agentes de mensajes desde proveedores diferentes es compleja y por lo general requiere un puente a nivel de la aplicación para pasar los mensajes de un sistema a otro y para convertirlos a sus formatos de mensajes propietarios. Es un requisito habitual, por ejemplo, cuando se proporciona una nueva interfaz unificada a diversos sistemas más antiguos o al integrar sistemas de TI después de una fusión.

La industria del software es un negocio muy dinámico; nuevos lenguajes de programación y marcos de aplicaciones se inventan a veces a un ritmo enloquecido. De igual forma, los requisitos de los sistemas de TI evolucionan con el tiempo y los desarrolladores desean sacar partido de los lenguajes y los marcos. Sin embargo, a veces el proveedor de mensajes seleccionado no admite estas plataformas. Como los protocolos son propietarios, no es posible que otros proporcionen bibliotecas para estas nuevas plataformas. Por tanto, está limitado a construir puertas de enlace, puentes y otros métodos.

Estos problema motivaron el desarrollo de AMQP (Advanced Message Queuing Protocol) 1.0. Tuvo su origen en JP Morgan Chase, que, al igual que la mayoría de empresas de servicios financieros, consume una gran cantidad de middleware orientado a mensajes. El objetivo era sencillo: crear un protocolo de mensajes de estándar abierto que hiciera posible crear aplicaciones basadas en mensajes utilizando componentes construidos con otros lenguajes, marcos y sistemas operativos, usando todos ellos los mejores componentes de una variedad de proveedores.

## Características técnicas de AMQP 1.0

AMQP 1.0 es un protocolo de mensajes a nivel de red, confiable y eficaz que se puede utilizar para crear aplicaciones de mensajes robustas y compatibles entre plataformas. El objetivo del protocolo es muy sencillo: definir la mecánica de la transferencia segura, confiable y eficaz de mensajes entre dos partes. Los mismos mensajes se codifican usando una representación de datos portátiles que permite que remitentes y receptores heterogéneos intercambien mensajes empresariales estructurados con la máxima fidelidad. A continuación se incluye un resumen de las características más importantes:

* **Eficaz**: AMQP 1.0 es un protocolo orientado a la conexión que
  utiliza una codificación binaria para las instrucciones de protocolo y
  los mensajes empresariales transferidos por él. Incorpora avanzados
  esquemas de control de flujo para potenciar al máximo la utilización
  de la red y los componentes conectados. Es decir, el protocolo se ha
  diseñado para lograr un equilibrio entre eficacia, flexibilidad e
  interoperabilidad.
* **Confiable**: El protocolo AMQP 1.0 permite que se intercambien los
  mensajes con un rango de garantías de confiabilidad, desde el "enviar
  y olvidar" a la entrega confiable y confirmada.
* **Flexible**: AMQP 1.0 es un protocolo flexible que se puede usar
  para admitir distintas topologías. El mismo protocolo se puede
  utilizar para las comunicaciones cliente-a-cliente, cliente-a-agente y
  agente-a-agente.
* **Modelo independiente de agente**: La especificación AMQP 1.0 no
  impone ningún requisito en el modelo de mensajes utilizado por un
  agente. Esto significa que es posible que se pueda agregar fácilmente
  compatibilidad con AMPQ 1.0 a agentes de mensajes existentes.

## AMQP 1.0 es un Estándar (con mayúscula)

AMQP 1.0 lo ha estado desarrollando desde 2008 un grupo central de 20 compañías, tanto proveedores de tecnología como empresas usuarias. Durante este tiempo, las empresas usuarias han contribuido con sus requisitos empresariales del mundo real y los proveedores de tecnología han hecho evolucionar el protocolo para cumplir con estos requisitos. Durante el proceso, los proveedores han participado en talleres, en los que han colaborado para validar la interoperabilidad entre sus implementaciones.

En octubre de 2011, el trabajo de desarrollo pasó a un comité técnico dentro de la Organization for the Advancement of Structured Information Standards (OASIS) y el estándar OASIS AMQP 1.0 se publicó en octubre de 2012. Las firmas siguientes participaron en el comité técnico durante el desarrollo del estándar:

* **Proveedores de tecnología**: Axway Software, Huawei Technologies,
  IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation,
  Primeton Technologies, Progress Software, Red Hat, SITA, Software AG,
  Solace Systems, VMware, WSO2, Zenika.
* **Empresas usuarias**: Bank of America, Credit Suisse, Deutsche
  Boerse, Goldman Sachs, JPMorgan Chase.

Algunos de los beneficios que se mencionan con más frecuencia de los estándares abiertos son los siguientes:

* Menos probabilidad de dependencia con el proveedor
* Interoperabilidad
* Amplia disponibilidad de bibliotecas y herramientas
* Protección contra la obsolescencia
* Disponibilidad de personal experto
* Menor riesgo y más controlable

## AMQP 1.0 y Bus de servicio

La incorporación de AMQP 1.0 implica que ahora puede sacar partido de las características de encolamiento y publicación/suscripción de mensajes de agentes del Bus de servicio desde una amplia variedad de plataformas mediante un eficaz protocolo binario. Además, puede crear aplicaciones compuestas de componentes creados con una mezcla de idiomas, marcos y sistemas operativos.

En el diagrama siguiente se muestra un desarrollo de ejemplo en el que clientes de Java que se ejecutan en Linux, escritos usando la API estándar Java Message Service (JMS), y clientes .NET que se ejecutan en Windows intercambian mensajes a través del Bus de servicio mediante AMQP 1.0.

![](./media/service-bus-amqp-overview/Example1.png)

**Ilustración 1: Escenario de implementación de ejemplos que muestran mensajes entre plataformas mediante el Bus de servicio y AMQP 1.0**

En este momento las siguientes bibliotecas de cliente están trabajando con el Bus de servicio:

<table>
  <tr>
    <th>Lenguaje</th>

    <th>Biblioteca</th>

  </tr>

  <tr>
    <td>Java</td>

    <td>Cliente Apache Qpid Java Message Service (JMS) <br  />
 Cliente IIT Software SwiftMQ Java</td>

  </tr>

  <tr>
    <td>C</td>

    <td>Apache Qpid Proton-C</td>

  </tr>

  <tr>
    <td>PHP</td>

    <td>Apache Qpid Proton-PHP</td>

  </tr>

  <tr>
    <td>Python</td>

    <td>Apache Qpid Proton-Python</td>

  </tr>


</table>

**Ilustración 2: Tabla de bibliotecas de cliente de AMQP 1.0**

Para obtener más información sobre cómo obtener y usar estas bibliotecas con el Bus de servicio, consulte la guía del desarrollador de AMQP del Bus de servicio. Consulte a continuación la sección "Referencias" para más información.

## Resumen

* AMQP 1.0 es un protocolo de mensajes confiable y abierto que se puede
  utilizar para crear aplicaciones híbridas, entre plataformas. AMQP 1.0
  es un estándar de OASIS.
* El soporte de AMQP 1.0 ahora está disponible en el Bus de servicio de
  Azure y en el Bus de servicio para Windows Server (Bus de servicio
  1.1). El precio es el mismo que el de los protocolos existentes.

## Referencias

* [Uso de AMQP 1.0 con la API .NET del bus de servicio][1]
* [Uso de la API de Java Message Service (JMS) con el bus de servicio y
  AMQP 1.0][2]
* [Service Bus AMQP 1.0 Developer\'s Guide][3]
* [OASIS Advanced Message Queuing Protocol (AMQP) Version 1.0
  specification][4]



[1]: http://aka.ms/lym3vk
[2]: http://aka.ms/ll1fm3
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/jj841071.aspx
[4]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf