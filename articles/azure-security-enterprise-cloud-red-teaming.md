<properties
   pageTitle="Red Teaming de Microsoft Enterprise Cloud"
   description="En este artículo se describe la estrategia y la ejecución de Red Teaming de Microsoft y las pruebas de penetración de sitios web activos en aplicaciones, servicios e infraestructura en la nube administradas por Microsoft. Aprenderá cómo Microsoft simula las infracciones reales, lleva a cabo una supervisión continua de la seguridad y practica la respuesta ante incidentes de seguridad para validar y mejorar la seguridad de Microsoft Azure y Office 365."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="yuridio"/>


#Red Teaming de Microsoft Enterprise Cloud

Red Teaming está más allá de las acreditaciones de cumplimiento u otros requisitos de la industria y proporciona a Microsoft, y a nuestros clientes, la seguridad adicional de que los servicios en la nube de Microsoft realizan continuamente la supervisión, las pruebas y las actualizaciones de seguridad para reflejar las cambiantes amenazas a las que se enfrentan los clientes y Microsoft.

##Introducción

Las organizaciones pueden prepararse mejor para el impacto de las amenazas actuales y futuras al simular ataques reales y al llevar a cabo las tácticas, técnicas y procedimientos (TTP) que usan los adversarios determinados y persistentes durante las infracciones. En lugar de simplemente intentar evitar que se produzcan incidentes de seguridad, es fundamental suponer que un incidente de seguridad puede producirse y que se producirá. La información obtenida a partir de Red Teaming y de los ejercicios de pruebas de la penetración de sitios activos ayuda a reforzar las defensas, a mejorar las estrategias de respuesta, formar a defensores y a obtener una mayor eficacia del programa de seguridad completo.

Las organizaciones no pueden identificar intervalos en la detección y respuestas de seguridad, y se centran únicamente en las estrategias de prevención de infracciones. Comprender no solo cómo proteger sino también detectar y responder a las infracciones es tan importante, si no lo es más, como tomar medidas para evitar que se produzca una infracción en primer lugar. Mediante el planeamiento de los peores casos, a través de juegos de guerra (ejercicios teóricos de simulación y de penetración) y de Red Teaming (ataques reales y penetración), las organizaciones pueden desarrollar las capacidades necesarias para detectar los intentos de penetración y mejorar significativamente las respuestas asociadas a las infracciones de seguridad.

###Red Teaming de Microsoft Enterprise Cloud 

En este artículo se detalla cómo Microsoft utiliza Red Teaming, un formulario de pruebas de penetración de sitios activos, en la infraestructura, servicios y aplicaciones administrados por Microsoft. También aprenderá cómo Microsoft simula las infracciones del mundo real, supervisa continuamente la seguridad y practica la respuesta a incidentes de seguridad para probar y mejorar la seguridad de Microsoft Azure y de Office 365 (Office 365). Sin embargo, tenga en cuenta que no se utiliza deliberadamente ningún dato de clientes finales durante Red Teaming y las pruebas de penetración de sitios activos. Las pruebas se realizan en la infraestructura y plataformas de Microsoft Azure y de Office 365, así como en los propios inquilinos, aplicaciones y datos de Microsoft. Nunca se utilizan los inquilinos, aplicaciones y datos del cliente hospedados en Microsoft Azure o en Office 365.

###Más allá de la prevención 

Las tecnologías y estrategias de prevención de seguridad no pueden garantizar la seguridad en todos los ataques. Dado el panorama de amenazas de hoy en día, es fundamental confirmar que una infracción ya se ha producido o que es solo cuestión de tiempo hasta que lo haga. Es más probable que una organización ya se haya puesto en peligro, pero simplemente no ha detectado aún. Si se trabaja con esta suposición, se reorganizarán las estrategias de detección y de respuesta para superar los límites de cualquier infraestructura, personas, procesos y tecnologías de la organización.

![Oferta de NSA](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig1.png)

Según el informe de investigación de infracción de datos 2014 de Verizon, en aproximadamente el 80 % de los casos la organización infractora no detectó el riesgo (párrafo 12, <http://www.verizonenterprise.com/DBIR/2014/>[)](http://www.verizonenterprise.com/DBIR/2014/). En su lugar, son notificados de la infracción por una parte externa, como un cliente, fuerzas del orden, servicio de terceros o agencia de detección de fraudes externa. Es más, estas métricas solo se extraen de incidentes en los que el atacante se detectó finalmente. No incluyen esos casos en los que el atacante ha pasado desapercibido hasta la fecha.

En las secciones siguientes, trataremos el cambio a una nueva estrategia de seguridad que se utilizará en los servicios en la nube de Microsoft, incluidos Microsoft Azure y Office 365. Esta estrategia de seguridad, llamada suposición de infracción, es una metodología y un cambio cultural que modifica la premisa básica del diseño, ingeniería y operaciones al sumir que los atacantes ya se han aprovechado de las vulnerabilidades, han accedido con privilegios y son activamente persistentes en los servicios de producción activos.

##Metodología de seguridad tradicional 

Las metodologías de seguridad tradicionales se han centrado en gran medida en la prevención. La prevención es una estrategia defensiva cuyo objetivo es eliminar las vulnerabilidades y, por tanto, mitigar las infracciones de seguridad antes de que ocurran. En la cartera de Microsoft Online Services (que incluye Microsoft Azure, Office 365, CRM Online y otros), esto implica mejoras continuas en los procesos de seguridad con nuestros programas [*Security Development*](http://www.microsoft.com/security/sdl/default.aspx) [*Operational Security Assurance*](http://www.microsoft.com/download/details.aspx?id=40872) (OSA) (Control de seguridad operativa del desarrollo de seguridad). El modelado de amenazas, el análisis de código estático y las pruebas de seguridad son útiles para enumerar, reducir y administrar las superficies de ataque, pero no eliminan todos los riesgos de seguridad.

Un ejemplo de una estrategia de prevención es cómo Microsoft limita el acceso del operador o administrador a los empleados que tienen una necesidad demostrada de acceso y que cumplen los requisitos de elegibilidad (por ejemplo, han pasado una comprobación de antecedentes, cumplen todos los requisitos de cumplimiento y seguridad, realizan un trabajo o una función que requieren acceso, etc.). Además, los administradores no mantienen ningún permiso permanente y, en su lugar, se les asigna acceso de Just-In-Time (JIT) [1] y Just Enough Administration (JEA) [2]. Otros ejemplos incluyen la separación del entorno de correo electrónico del empleado desde el entorno de producción y el uso de estaciones de trabajo reforzadas, especializadas y muy seguras para realizar operaciones confidenciales.

Siempre que sea posible, la intervención humana se sustituye por procesos automatizados, muy auditados y basados en herramientas. Algunos ejemplos de funciones rutinarias incluyen la implementación, depuración, la recopilación de datos de diagnóstico y administración de servicios. Microsoft Online Services sigue invirtiendo en la automatización de operaciones y en la seguridad de los sistemas para reducir la exposición a posibles riesgos de seguridad.

Desde una perspectiva de la tecnología de las operaciones, las inversiones en la prevención de infracciones limitan la exposición, pero nunca pueden eliminarla por completo. Por lo tanto, aunque la adopción y desarrollo continuado de los programas de seguridad de Microsoft (como SDL y OSA) y las tecnologías siguen siendo un medio importante para ayudar a evitar infracciones de seguridad, todos deben aceptar que es inevitable que las infracciones de seguridad afecten a la organización, a los servicios y a los usuarios.

###Amenazas nuevas y emergentes 

Durante los últimos cinco o más años, una categoría de amenaza específica se ha convertido en la más tratada. La amenaza persistente avanzada (APT) fue un término acuñado para hacer referencia a los intentos patrocinados por el Estado o nación para infiltrarse en redes militares, en la base industrial de defensa y en redes del gobierno con el objetivo específico de extraer información confidencial. En la actualidad , el término APT se utiliza sobre todo en círculos de seguridad y en los medios para describir cualquier ataque específicamente dirigido a una organización individual o que se cree que es de naturaleza técnica sobre todo, con independencia de si el ataque era realmente avanzado o persistente. Entre las características comunes de una APT se incluyen:

- Planificación avanzada

- Selecciones específicas o secuenciales

- Reconocimiento efectivo

- Uso de herramientas de prácticas

- Ingeniería social

Los adversarios actuales tienen importantes recursos y orquestación a su disposición. Con independencia de sofisticación del adversario, las tendencias de incidentes de seguridad desde 2009 hasta hoy son una indicación clara de que están en aumento una mayor probabilidad de eventos y riesgos. La mayor sofisticación y la naturaleza específica de las amenazas de seguridad, junto con su frecuencia creciente sugiere que, tarde o temprano, las infracciones de seguridad afectarán a todos los usuarios y organizaciones.

En el panorama actual de amenazas, un enfoque orientado únicamente a la prevención no es suficiente para abordar a los adversarios determinados y persistentes. Además, con las herramientas de seguridad habituales, como antivirus y sistemas de detección de intrusos (IDS), es difícil capturar o mitigar la gran variedad de infracciones de hoy en día. Los controles del perímetro de la red pueden mantener alejados a los aficionados, pero los atacantes con talento y motivados siempre encontrarán los medios para entrar en dichos perímetros virtuales. Como resultado, las organizaciones están con demasiada frecuencia mal preparadas cuando se enfrentan a la necesidad de responder a la profundidad y extensión de una infracción.

Con la evolución de TI y la adopción de la nube, los límites de la empresa ya no pueden definirse por un perímetro de red administrado física o virtualmente a través de firewalls. Los datos corporativos, incluidos los datos y aplicaciones confidenciales, pueden encontrarse prácticamente en todas partes: en local, en centros de datos privados, en la nube, con socios y en una variedad de dispositivos de los usuarios. Todas ellos requieren una estrategia de seguridad fundamentalmente diferente, así como un cambio en las metodologías de seguridad utilizadas por la mayoría de las organizaciones.

La respuesta de incumplimiento siempre ha presentado muchos desafíos, como la identificación del ámbito de la infracción, las notificaciones oportunas a las partes interesadas y clientes, la investigación de pérdida de datos y la recuperación de los recursos en peligro. Mediante una combinación de los adversarios actuales y de la evolución de TI, la respuesta a las infracciones nunca ha sido un desafío mayor que ahora. Por lo tanto, en lugar del enfoque tradicional en simplemente evitar infracciones, supone una estrategia eficaz de seguridad que determina y adversarios persistentes correctamente infrinja las defensas.

##Metodología de la suposición de infracción 

El panorama actual de amenazas requiere que las estrategias de seguridad eficaces equilibren las inversiones en la prevención con la detección y respuesta. A través de un análisis detallado de las tendencias de seguridad, Microsoft comenzó abogar y a resaltar la necesidad de inversiones adicionales en los procesos de seguridad reactivos y en las tecnologías que se centran en la detección y respuesta a las nuevas amenazas, en lugar de únicamente en la prevención de esas amenazas. Además, como resultado de los cambios en el panorama de amenazas y de un análisis en profundidad, Microsoft ha decidido refinar su estrategia de seguridad más allá que solo evitar las infracciones de seguridad a una estrategia mejor preparada para tratar las infracciones cuando se produzcan: una estrategia que considera la predicción de los principales eventos de seguridad no como una cuestión de "si" ocurren, sino de "cuándo".

Mientras que los procedimientos de suposición de infracción de Microsoft han estado vigentes durante muchos años, la mayoría de los clientes no son conscientes de los trabajos en segundo plano realizados para reforzar la nube de Microsoft. La suposición de infracción es un modo de pensar que guía las inversiones en seguridad, las decisiones de diseño y las prácticas de seguridad operativa. La suposición de infracción limita la confianza en las aplicaciones, los servicios, las identidades y las redes al tratarlos a todos ellos, tanto internos como externos, como no seguros y que probablemente ya estén en peligro. Aunque la estrategia de la suposición de infracción no ha surgido de una infracción en uno los servicios en la nube o empresariales de Microsoft, se reconoció que muchas organizaciones del sector se estaban infringiendo a pesar de todos los intentos para prevenirlo.

Aunque prevenir las infracciones es una parte fundamental de las operaciones de cualquier organización, esas prácticas deben probarse y ampliarse continuamente para tratar eficazmente a los adversarios modernos, como APT. A fin de que las organizaciones se preparen para una infracción, se debe crear y mantener procedimientos de respuesta de seguridad sólidos, repetibles y exhaustivamente probados.

Aunque los procesos de seguridad para prevenir las infracciones, tales como el modelado de amenazas, revisiones de código y pruebas de seguridad, pueden ser comunes en los ciclos de vida de desarrollo seguro, la suposición de infracción ofrece numerosas ventajas que ayudan a la seguridad global de la cuenta al aplicar y medir las capacidades reactivas en caso de infracción. En Microsoft, nos propusimos lograrlo gracias a ejercicios de juegos de guerra continuados y a pruebas de penetración de sitios activos de nuestros planes de respuesta de seguridad, con la intención de mejorar la funcionalidad de detección y respuesta.

![Prevención de infracciones y suposición de infracción](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig2.png)

*Figura 1: Modelos de prevención y suposición de infracción*

Con la suposición de infracción, el enfoque de seguridad cambia para identificar y abordar las brechas en:

- Detección de ataque y penetración

- Respuesta al ataque y penetración

- Recuperación de la pérdida de datos, manipulación o puesta en peligro

- Prevención de futuros ataques y penetración

La suposición de infracción comprueba que los mecanismos de protección, detección y respuesta se implementan correctamente, incluso reducir las amenazas potenciales de "atacantes informados" (con recursos legítimos, como cuentas y máquinas comprometidas).

La naturaleza de los modelos de informática híbridos y de nube agrega importancia al desempeño de la prueba de penetración del rol al mantener un entorno operativo seguro, incluida la preparación para tratar con las infracciones. Dado que muchos aspectos de la nube de Microsoft no están bajo el control directo de un cliente, Microsoft trabaja no solo para protegerse, sino también para detectar y responder a los ataques contra la infraestructura, la plataforma y los servicios.

Microsoft, por tanto, lleva a cabo ejercicios de juegos de guerra y un Red Teaming regular para evaluar y mejorar la disponibilidad de la suposición de infracción de Microsoft. A través de los modelos de ataques reales así como de las pruebas de penetración, Microsoft puede probar su capacidad para controlar los ataques, identificar brechas en la detección y respuesta y centrarse en los recursos para abordar esas brechas. Al practicar la respuesta de los incidentes de seguridad y realizar la supervisión, análisis forense y recuperación continuos, Microsoft se esfuerza por desarrollar las funcionalidades esenciales necesarias para tratar las infracciones.

###Ejecución de la suposición de infracción 

La suposición de infracción en Servicios en la nube de Microsoft se llevó a cabo inicialmente a través de juegos de guerra y después de ejercicios de infracciones reales, denominados infracciones del Equipo rojo, diseñados para simular ataques reales. Las infracciones del Equipo rojo prueban las capacidades de Microsoft para responder a los ataques específicos y persistentes con el objetivo de reducir significativamente el tiempo medio de detección (MTTD) [3] y el tiempo medio de recuperación (MTTR) [4].


![Ciclo de ejecución de la suposición de infracción](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig3.png)

*Ilustración 2: Ciclo de ejecución de la suposición de infracción*

Los juegos de guerra son ejercicios teóricos de simulación que sirven de simulacro de incendio frente a un incendio real. Los ejercicios del Equipo rojo ofrecen completas simulaciones de infracciones que proporcionan, de la forma más realista posible, incidentes de seguridad que preparan a las personas implicadas en el tratamiento de las infracciones reales.

Las infracciones de juegos de guerra y del Equipo rojo ofrecen oportunidades de respuesta a incidentes de seguridad para se practiquen antes de un evento real: cuando más se practique más preparado se estará para un incidente o infracción de seguridad verdaderos.


###Juegos de guerra 

Antes de dedicar recursos a todas las infracciones del Equipo rojo, en Microsoft, comenzamos con ejercicios teóricos de simulación llamados juegos de guerra. Los ejercicios de juegos de guerra son semejantes al [modelado de amenazas de SDL](http://msdn.microsoft.com/magazine/dd148644.aspx)[,](http://msdn.microsoft.com/magazine/dd148644.aspx)[ ](http://msdn.microsoft.com/magazine/dd148644.aspx)aunque orientados a los procesos de respuesta de seguridad y al personal de una organización o servicio que tratan con un ataque. La intención de los juegos de guerra es mejorar los procedimientos de respuesta a incidentes de seguridad al implicar a personal de diferentes grupos internos de Microsoft, empleados en seguridad, ingeniería e incluso operaciones. A medida que empezamos juegos de guerra y seguimos profundizando cada vez más en ellos, quedó claro qué grupos o representantes faltaban y necesitábamos que participaran.

Se agregó a cualquier persona o grupo que era probable que resultara necesario y estuviera implicado en una infracción real como parte interesada en nuestros ejercicios de juegos de guerra. La lista de representantes creció más allá de la seguridad, la ingeniería y los operaciones y terminó por incluir a representantes de casi todos los grupos de la empresa como Jurídico, Recursos humanos, Marketing y hasta a ejecutivos. La participación de amplio conjunto de personas y grupos contribuyó a forjar las relaciones necesarias entre disciplinas y unidades de negocio que garantizaron el desarrollo de planes de respuesta a infracciones exhaustivos. En muchos casos, estos ejercicios también sirvieron para educar y concienciar a todo Microsoft sobre los riesgos y las necesidades asociados con la respuesta a las infracciones.

El objetivo principal de los ejercicios de juegos de guerra es garantizar la integridad de los procedimientos operativos normalizados (SOPs) y mejorar la detección y la respuesta. Los ejercicios de juegos de guerra se dividen en tres (3) fases principales:

-   Identificación del escenario de ataque

-   Proceso de simulación de ataque y respuesta

-   Análisis final

La identificación del escenario de ataque comenzaba con la elaboración de una lista de ataques potenciales para modelar. Estos escenarios de ataque adoptaban muchas formas diferentes y con frecuencia combinaban varios escenarios de ataque, tales como:

-   Denegación de servicio

-   Ataque de malware

-   Ejecución remota de código

-   Datos de clientes en peligro

-   Atacante interno

-   Servicio en peligro

Con frecuencia, los escenarios se elegían y priorizaban entre los que se conocían como habituales o después de revisar los incidentes de seguridad antes notificados (conocidos por afectar a Microsoft o a nuestros competidores u observados en el sector).

Una vez elegido un escenario de ataque, uno o varios de los miembros de grupos, normalmente un miembro del equipo de seguridad, desempeñaban el rol de adversario (ofensa) y describían la ejecución del escenario de ataque. Esto es el principio del proceso de simulación de ataque y respuesta.

Los demás miembros que participaban en el juego de guerra se encargaban del rol defensivo y describían cómo podrían detectar el escenario de ataque o responder ante él, haciendo referencia a los procedimientos operativos normalizados ya existentes (o identificando los que faltaran o estuvieran incompletos). A medida que el grupo de defensa respondía con pasos para detectar, investigar, mitigar o recuperarse del ataque simulado, el de ofensa respondía con métodos de contraataque que a menudo invalidaban las suposiciones de la defensa y forzaban a los defensores a pensar más a fondo sobre la detección y la respuesta.

Por ejemplo, si la defensa sugería identificar y bloquear la dirección IP que los atacantes usaban en el ataque, la ofensa tendría en cuenta con qué facilidad se podrían cambiar las direcciones IP de origen o aprovechar una infraestructura de ataque disponible normalmente, como redes de robots (botnets), para originar su ataque en varias direcciones IP. Este constante ir y venir durante los ejercicios teóricos de simulación conduce a un aumento significativo de SOP e identificación de las numerosas necesidades de inversión de seguridad, así como muchos debates valiosos.

A pesar de su gran utilidad, los juegos de guerra tienen limitaciones en cuanto a lo que se puede medir, especialmente en lo referente a enfrentarse a ataques dirigidos continuados y a adversarios persistentes. Por lo tanto, Microsoft se dispuso a evaluar y medir realmente el nivel de preparación frente a ataques sofisticados procedentes de adversarios determinados y persistentes. Además de enfrentarse a las infracciones de seguridad reales, no se nos ocurrió ninguna alternativa mejor que hacer precisamente eso, cometer infracciones de seguridad reales, mediante un concepto conocido como Red Teaming. Red Teaming se refiere al uso de tácticas de infracción reales para llevar a cabo ataques y penetración. Red Teaming hace real el aspecto teórico de los juegos de guerra.

### Red Teaming 

La estrategia de seguridad de suposición de infracción se ejecuta con dos (2) grupos principales: el equipo rojo (los atacantes) y el equipo azul (defensores).[5] Este acercamiento, denominado Red Teaming, consiste en probar las operaciones y los sistemas de Microsoft Azure y Office 365 usando las mismas tácticas, técnicas y procedimientos (TTP) que los adversarios reales. Se ataca la infraestructura de producción activa, sin que los equipos de ingeniería u operaciones de infraestructura y plataforma lo conozcan con antelación.

Esto permite probar la funcionalidad de seguridad para detección y respuesta, y ayuda a identificar las vulnerabilidades de producción, los errores de configuración, las suposiciones no válidas u otros problemas de seguridad de una manera controlada. Cada infracción del equipo de rojo va seguida de la comunicación completa entre el equipo rojo y el azul para identificar las carencias, solventar lo que se haya encontrado y mejorar notablemente la respuesta a la infracción.

#### El equipo rojo 

El equipo rojo es un grupo de personal a jornada completa en Microsoft que se centra en llevar a cabo una infracción de la infraestructura de Microsoft, la plataforma y las aplicaciones y los inquilinos de Microsoft. Son el adversario dedicado (un grupo de hackers éticos) que emprenden ataques dirigidos y persistentes contra Online Services (la infraestructura, las plataformas y las aplicaciones de Microsoft, pero no las aplicaciones ni los datos de los clientes finales).

El rol de un Equipo rojo es atacar y penetrar en entornos siguiendo los mismos pasos que la cadena de eliminación de un adversario[6], como se muestra en la figura 3:

![Fases de la infracción](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig4.png)

*Ilustración 3: Fases de la infracción*

Por lo tanto, investigar y comprender los incidentes en el sector y las tendencias en el panorama de amenazas para estar al tanto sobre las técnicas y las herramientas de ataque más recientes usadas por los adversarios es una parte fundamental del enfoque de cualquier Equipo rojo. Microsoft Online Services, una de las propiedades de Internet más atacadas del mundo, genera una gran cantidad de datos sobre las amenazas emergentes. El Equipo rojo emplea esta investigación e inteligencia no solo para modelar, sino también para ejecutar tácticas reales relativas a la cadena de eliminación de un adversario.

Además de la investigación y el modelado de los adversarios conocidos, el Equipo rojo desarrolla y deriva sus propias técnicas novedosas para poner en peligro Microsoft Azure y Office 365 con herramientas de penetración y métodos de ataque personalizados. Al igual que los adversarios determinados, el Equipo rojo utiliza amenazas emergentes y combinadas para crear peligros y cambiará de táctica cuando se encuentre con nuevos obstáculos o defensas.

Puesto que los atacantes con talento y motivación rompen las defensas perimetrales, el Equipo rojo también debe hacerlo. Los controles de perímetro pueden mantener a los aficionados fuera, pero los adversarios persistentes siempre conseguirán entrar. Una vez dentro, es habitual que el Equipo rojo adquiera privilegios de usuario interno que emplean para adentrarse lateralmente aún más en la infraestructura. Además, al igual que los adversarios más avezados, el Equipo rojo establece una cabeza de playa desde la que mantener la persistencia y además puede modificar continuamente su aproximación para escapar a la detección.

Por ejemplo, puede que el Equipo rojo instale herramientas personalizadas (bots, control remoto, etc.) para poder acceder de forma continuada a un recurso en peligro y recuperar información siempre que quiera. La mecánica de este tipo de ataque permite al Equipo rojo no solo extraer datos confidenciales, sino también aprovechar esos datos en peligro (de nuevo, solo los datos de Microsoft, no los de los clientes).

Debido a la naturaleza confidencial y crítica del trabajo, los empleados que trabajan en equipos de Red de Microsoft se mantienen a muy altos estándares de seguridad y cumplimiento. Deben superar más validaciones, un examen de antecedentes y formación para poder participar en cualquier escenario de ataque. Aunque los ataques del Equipo rojo no van dirigidos deliberadamente a los datos de clientes, mantienen los mismos requisitos para el acceso a los datos de clientes

(ATCD) que el personal de operaciones de servicio que implementa, mantiene y administra Microsoft Azure y Office 365. Además, los Equipos rojos solo atacan la infraestructura y las plataformas administradas de Microsoft. En lugar de atacar los datos o las aplicaciones de los clientes finales, los Equipos rojos atacan inquilinos, aplicaciones y datos en la nube que Microsoft posee y administra.

No obstante, los Equipos rojos se rigen por un estricto código de conducta. Los cinco (5) principios fundamentales del código de conducta del Equipo rojo son:

1.  No afectar de forma intencionada el contrato de nivel de servicio (SLA) de clientes ni causarles tiempo de inactividad.

2.  No acceder de forma intencionada a los datos de clientes ni modificarlos.

3.  No realizar acciones destructivas de forma deliberada.

4.  No debilitar las protecciones de seguridad establecidas.

5.  Proteger la información sobre vulnerabilidades y otros datos esenciales dentro del Equipo rojo y compartirla solo con aquellos que deban conocer dicha información.

Además, los Equipos rojos de Office 365 y Azure deben seguir un conjunto documentado de reglas de interacción (CERT), que se han desarrollado para asegurarse de que se cumpla el código de conducta. Estas reglas de interacción están aprobadas por los directivos de Microsoft.

Existen varias métricas de seguridad que es fundamental seguir cuando cada Equipo rojo lleve a cabo sus infracciones:

-   Tiempo medio hasta poner en peligro (MTTC)

-   Tiempo medio de elevación de privilegios o "derrota total" (MTTP)

Con Tiempo medio de poner en peligro, se mide desde el comienzo de los ejercicios hasta el momento en que el Equipo rojo consigue poner en peligro un recurso. En esta fase, el Equipo rojo normalmente está estableciendo su posición inicial en el entorno de destino. Tiempo medio de elevación de privilegios, o como algunos adversarios prefieren llamarlo "derrota total", es el tiempo desde el comienzo del ejercicio hasta la puesta en peligro completa. Por ejemplo, en entornos de Active Directory, es el momento en que el Equipo rojo adquiere privilegios de administrador de dominio o pone en peligro el controlador de dominio.

Se puede considerar MTTP como el fin del juego, ya que la mayoría de las organizaciones no están preparadas para enfrentarse a este nivel de infracción. Sin embargo, también merece la pena tener en cuenta que los escenarios de fin de juego no son los únicos objetivos o escenarios que se demuestran con Red Teaming. Los objetivos de infracción del Equipo rojo pueden incluir el robo de datos, la puesta en peligro del nivel de inquilinos o la denegación de servicio, entre otros. Además, las métricas MTTC y MTTP se pueden calcular por ejercicio o también se pueden seguir y calcular por recurso de destino o tipo de recurso para mayor granularidad.

El rol de los Equipos rojos de Microsoft es identificar las carencias en los controles de seguridad del objetivo. El seguimiento de MTTC y MTTP permite a Microsoft determinar una línea base sobre la que seguir mejorando de forma constante. Al aprovechar los puntos débiles en la seguridad al mismo tiempo que llama la atención sobre las carencias en la recuperación y la supervisión de seguridad, Red Teaming mejora el proceso y las herramientas de respuesta a incidentes (sigue más información). Más importante aún es que Red Teaming produce un impacto demostrable, mediante puestas en peligro, que demuestra la necesidad de la suposición de infracción. Como resultado, Red Teaming enumera los riesgos para el negocio y justifica los recursos, las prioridades y las necesidades de inversión para la defensa.

#### El Equipo azul 

El Equipo azul está compuesto por un equipo dedicado de respuesta de seguridad o por miembros de las organizaciones de operaciones, ingeniería y respuesta a incidentes de seguridad. Independientemente de su composición, son independientes y trabajan por separado del Equipo rojo. El Equipo azul sigue procesos de seguridad establecidos y usa las herramientas y tecnologías más modernas para detectar ataques y penetración, y responder ante ellos.

Al igual que en los ataques del mundo real, el Equipo azul no sabe cuándo o cómo se producen los ataques del Equipo rojo o qué métodos pueden usar. Su trabajo, ya sea un ataque del Equipo rojo o un ataque de buena fe, consiste en detectar y responder a todos los incidentes de seguridad. Por este motivo, el Equipo azul está de guardia 24 horas al día, 365 días al año, y debe reaccionar a las infracciones del Equipo rojo del mismo modo que lo haría con cualquier otro adversario.

Cuando un adversario, por ejemplo un Equipo rojo, ha infringido un entorno, el Equipo azul debe:

-   Reunir las evidencias dejadas por el adversario

-   Detectar la evidencia como indicación de peligro

-   Alertar a los equipos de ingeniería y de operación adecuados

-   Evaluar las alertas para determinar si merece una mayor investigación

-   Recopilar el contexto del entorno para definir el ámbito de la infracción

-   Elaborar un plan de corrección que refrene o expulse al adversario

-   Ejecutar el plan de corrección y recuperarse de la infracción

Estos pasos conforman la cadena de eliminación de respuesta a incidentes de seguridad que se ejecuta en paralelo a la cadena de eliminación del adversario, tal como se muestra en la Figura 4:

![Fases de respuesta a la infracción](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig5.png)

*Ilustración 4: Fases de respuesta a la infracción*

En resumen, las infracciones del Equipo rojo permiten al Equipo azul ejercitar su capacidad para detectar y responder a los ataques reales de principio a fin. Y lo que es más importante, permite una respuesta a los incidentes de seguridad practicados antes de una infracción real (de un adversario mucho menos cooperador). Además, como resultado de las infracciones del Equipo rojo, el Equipo azul mejora su conocimiento de la situación, lo que puede ser valioso cuando se tenga que lidiar con futuras infracciones (ya sea del Equipo rojo o de otro adversario).

A lo largo del proceso de detección y respuesta, el Equipo azul genera inteligencia de acción y obtiene visibilidad de las condiciones reales de los entornos que está intentando proteger. Con frecuencia, esto se logra mediante los análisis forense y de datos, realizados por el Equipo azul, al responder a los ataques del Equipo rojo y mediante el establecimiento de indicadores de amenazas, como indicadores de riesgo.

De forma muy parecida a cómo el Equipo rojo identifica las carencias de seguridad, los Equipos azules identifican carencias en su capacidad para detectar y responder. Además, puesto que los ataques del Equipo rojo modela ataques reales, el Equipo azul puede evaluar con precisión su capacidad o incapacidad para tratar con adversarios determinados y persistentes.

Por último, las infracciones del Equipo rojo miden la preparación y el impacto de la respuesta a las infracciones de la organización.

Las métricas normalmente evaluadas por el Equipo azul incluyen:

- Tiempo estimado para detección (ETTD)

- Tiempo estimado de recuperación (ETTR)

El motivo de estos "tiempos estimados" es que durante una infracción, independientemente de si se ha producido la detección o cuándo, el Equipo azul no puede estar 100 % seguro de cuando el Equipo rojo realizó una incursión con éxito. Del mismo modo, el Equipo azul no puede estar completamente seguro de si ha expulsado al Equipo rojo y se ha recuperado totalmente de una infracción. El hecho de que no haya evidencias de la presencia del Equipo rojo no es prueba de la expulsión de adversario y la recuperación de la infracción. Por eso la respuesta a un incidente de seguridad es tan complicad: es similar a juntar un rompecabezas, una pieza cada vez, sin saber qué aspecto debe tener la imagen final y qué piezas pueden faltar para una imagen completa.

Con un mayor conciencia del panorama de amenazas global y una mayor visibilidad de las condiciones reales en el entorno en el que funcionan, el Equipo azul puede aumentar los procedimientos de respuesta ante incidentes de seguridad para mejorar su preparación para tratar una infracción.


#### Rojo contra azul 

En lugar de las pruebas de penetración tradicionales, las infracciones del Equipo rojo intentan simular amenazas reales, incluida la inherente "niebla de guerra" [7]. Es decir, el Equipo azul no sabe lo que hace el Equipo rojo y un viceversa. Cuando el Equipo rojo pone en peligro un recurso, puede desencadenar una alerta o un incidente de seguridad sin darse cuenta de que lo ha hecho.

Por el contrario, el Equipo rojo puede desencadenar alertas intencionadamente para comprobar y validar las suposiciones de supervisión o como una táctica de diversión. Asimismo, durante un ejercicio el Equipo rojo puede observar la actividad de respuesta, como el restablecimientos de contraseñas, los recursos puestos en peligro que se recuperan, los mecanismos de persistencia quitados o incluso experimentar una expulsión completa (aunque eso es poco frecuente).

En estos temas, las infracciones del Equipo rojo pueden observar los efectos de la detección y la recuperación. Sin embargo, la detección y recuperación no pueden ser evaluadas con precisión por el Equipo rojo o el Equipo azul por separado. El tiempo medio para detección (MTTD) y el tiempo medio para respuesta (MTTR) solo se pueden calcular al final de un ejercicio cuando se produce una divulgación completa entre el Equipo rojo y el azul. Para impactar totalmente a la detección y la respuesta, un Equipo rojo debe:

- Emular una gama amplia de TTP basados en inteligencia acerca de los adversarios a los que se enfrenta (o se enfrentará) una organización

- Emplear TTP en la cadena de eliminación que ejercerán y validarán las detecciones y la respuesta

- Emplear la sofisticación técnica y la sofisticación operativa

- Usar la repetición para asegurarse de que las mejoras de detección y respuesta son suficientes para combatir a adversarios determinados y persistentes

Para garantizar que el ejercicio incluye la iniciación y la interacción con el Equipo azul, el Equipo rojo puede desencadenar intencionadamente la detección y la respuesta. Esto suele ocurrir cuando el Equipo rojo ha realizado todos sus objetivos principales y si no ha observado ninguna actividad por parte del Equipo azul.

Un ejemplo de desencadenamiento de la detección y la respuesta es causar intencionadamente un examen antimalware en un recurso, como el controlador de dominio (DC), para generar una alerta mediante un archivo de prueba EICAR [8]. La presencia de una alerta de seguridad inesperada, especialmente en un recurso crítico como el controlador de dominio, debe activar los procedimientos de respuesta del Equipo azul. La creación de un incidente de seguridad garantiza que se produzca una infracción de principio a fin: desde poner en peligro hasta responder. A menos que el Equipo azul interactúe, no puede haber una respuesta total a la infracción.

Lo ideal es que el Equipo azul no sepa (inicialmente) si un incidente fue desencadenado por un atacante real o desde el Equipo rojo interno. Siempre que sea posible, el Equipo azul debe tratar los incidentes de seguridad como un incidente real; al menos hasta que se pueda establecer y confirmar la atribución al Equipo rojo. Este enfoque de simulaciones de infracciones reales es el mejor método para crear defensas reales.


#### Análisis final de la infracción del Equipo rojo 

Al final de cada infracción del Equipo rojo, el Equipo rojo y el azul se unen para realizar un análisis final, en el que se evalúa la infracción y la respuesta ante ella. En esta etapa, ambos equipos comparten tácticas y lecciones aprendidas, al tiempo que proporcionan una divulgación completa. El Equipo rojo proporciona todos los detalles sobre cuándo y cómo generó una infracción en el entorno, los recursos que el Equipo rojo puso en peligro y controló, si el Equipo azul expulsó total o parcialmente al Equipo rojo y si la recuperación de la infracción se ha completado.

Además, el Equipo azul proporciona todos los detalles sobre cómo y cuándo se detectó al Equipo rojo (si se hizo), los recursos puestos en peligro, qué mecanismos de persistencia del Equipo rojo se detectaron y qué pasos de respuesta se realizaron para expulsar al Equipo rojo y recuperarse de la infracción. Este es uno de los elementos más críticos y únicos de Red Teaming: el Equipo rojo es uno de los pocos adversarios que proporciona detalles de su infracción y comentarios sobre el éxito o fracaso de la detección y la respuesta. La capacidad de los equipos rojo y azul de comparar notas es vital para asegurarse de que se identifican todas las carencias en la detección y respuesta.

Solo durante el análisis final, cuando tiene lugar una divulgación completa, se pueden calcular las métricas de tiempo medio para detección (MTTD) y de tiempo medio para recuperación (MTTR) reales; por ejemplo, comparar la diferencia entre el MTTC del Equipo rojo y el ETTD del Equipo azul para calcular el MTTD real. Estas métricas de detección y respuesta, durante y después del incidente, son útiles para enumerar los riesgos asociados a las carencias en la detección y recuperación de la seguridad identificados como parte del ejercicio.

En gran parte del resto del análisis final el Equipo rojo y el azul colaboran para identificar las vulnerabilidades y las necesidades de inversión que podrían mejorar la protección, la detección y la respuesta. Con cada infracción del Equipo rojo, el Equipo rojo y el azul identifican las inversiones en seguridad necesarias para protegerse contra ataques futuros, ralentizar a los adversarios y acelerar la detección y la recuperación. En otras palabras, aumentar el MTTC y disminuir el MTTD y MTTR.

El paso final durante el análisis final es para que el Equipo rojo y el azul proporcionan informes detallados por escrito. Estos informes incluyen escalas de tiempo de las infracciones de los equipos rojo y azul, un resumen del impacto del ejercicio en el negocio y una lista detallada de las vulnerabilidades, las conclusiones y las inversiones necesarias para mejorar la detección de infracciones y la respuesta.

### Principios de seguridad 

Una ventaja clave de Red Teaming es la consolidación de una seguridad experta y recursos de pirateo éticos. Al igual que en cualquier otra profesión, el personal de operaciones y los ingenieros se especializan en determinadas áreas, que pueden no incluir habilidades contra ataques y penetraciones. En Microsoft, los ingenieros y los operadores son expertos en SDL/OSA y Common Engineering Criteria (CEC), pero el Equipo rojo tiene la responsabilidad de probar los límites externos de un entorno de producción que de otra forma sería muy complicado comprobar.

Al invertir en Red Teaming con el único objetivo de entender, comunicar y aprovechar las últimas amenazas durante ataques a sitios web activos, Microsoft se esfuerza continuamente para probar y mejorar sus capacidades de reacción y seguir los principios de seguridad clave:

- Resistir a la tendencia a delimitar la estrategia de seguridad a **escenarios de ataque estáticos** o suponer que los adversarios solo procederán de **una posición fija**

- Usar **capas de controles de seguridad complementarios**; el efecto es acumulativo, lo que mejora las defensas

- El **número y distribución** de los controles de seguridad es más importante que la eficacia individual

- Intentar **retrasar y responder** a un ataque, en lugar de **evitarlo**

## Resumen 

Las compañías del sector se enfrentan a la cruda realidad de que pueden estar viviendo en un peligro constante. Esto se ve empeorado por el hecho de que un gran número de compañías no tiene constancia de que ellos también han tenido infracciones. El actual panorama de amenazas requiere reducir la exposición a ataques, incluidas las amenazas internas. El cambio más imperativo requiere que las organizaciones reduzcan considerablemente el tiempo medio de detección y recuperación de una infracción.

Estas notas del producto resaltan la necesidad de las organizaciones de tener una mayor agilidad para solucionar las carencias en la seguridad. Mediante una estrategia de seguridad de suposición de infracción, Microsoft continúa un objetivo interminable de inversiones más amplias y profundas en seguridad. Todas las organizaciones pueden beneficiarse de la adopción de estrategias de seguridad similares para combatir amenazas nuevas y evolucionadas.

Microsoft desarrolla funcionalidades de administración de incidentes de seguridad mediante la planeación por adelantado de los peligros y la simulación de ataques y penetraciones con juegos de guerra y un Equipo rojo activo. Red Teaming convierte escenarios teóricos en reales al exponer las carencias en la seguridad, aprovechar las vulnerabilidades activas y proporcionar evidencias concretas de la necesidad de suposición de infracción.

El desafiante mundo de formación de Blue Teaming ejercita la capacidad de detectar y responder ante los ataques. Al realizar ejercicios de infracciones del Equipo rojo contra el Equipo azul, las organizaciones de seguridad pueden centrarse en vectores de ataque clave, desarrollar contramedidas y madurar sus mecanismos de respuesta críticos necesarios para prepararse para los adversarios determinados y persistentes.

Red Teaming se ha convertido en uno de los elementos más esenciales para desarrollar y proteger la infraestructura, las plataformas y los servicios de Microsoft. Los Equipos rojos de Microsoft Azure y Office 365 suplantan a adversarios sofisticados y permiten a Microsoft validar y mejorar la seguridad, fortalecer las defensas y proporcionar una mayor eficacia a sus programas de seguridad de la nube empresarial.

Mediante penetraciones y ataques regulares a sitio web activos, las infracciones del Equipo rojo proporcionan los medios críticos para practicar la respuesta ante incidentes de seguridad, además de medir con precisión la disponibilidad y el impacto de los ataques reales. Los clientes pueden estar seguros de que Microsoft mejora continuamente la protección, la detección y la respuesta en su lucha para proporcionar servicios en la nube más seguros.

## Referencias y lecturas adicionales 

Los siguientes recursos están disponibles para proporcionar más información general acerca de los servicios de Microsoft Azure y Office 365, así como elementos específicos a los que se hace referencia en el texto principal:

- [Página principal de Microsoft Azure](http://azure.microsoft.com): información general y vínculos sobre Microsoft Azure

- [Página principal de Microsoft Office](http://products.office.com): información general y vínculos sobre Microsoft Office

- [Centro de confianza de Microsoft Azure](https://www.microsoft.com/TrustCenter/CloudServices/Azure)

- [Centro de confianza de Microsoft Office 365](https://www.microsoft.com/TrustCenter/CloudServices/Office-365)

- [Centro de respuesta de seguridad de Microsoft](http://www.microsoft.com/security/msrc/default.aspx)

- Microsoft Azure u Office 365, [donde pueden notificarse las vulnerabilidad de seguridad de Microsoft, incluidos problemas con Microsoft Azure u Office 365] o [**http://www.microsoft.com/security/msrc/default.aspx*](http://www.microsoft.com/security/msrc/default.aspx)[ ](http://www.microsoft.com/security/msrc/default.aspx) o por correo electrónico a **secure@microsoft.com*

- [Entrevista con Mark Russinovich sobre la seguridad en Microsoft Azure y la actualización de las pruebas de penetración](http://blogs.technet.com/b/matthewms/archive/2013/07/02/an-interview-with-mark-russinovich-on-windows-azure-amp-security-update-on-penetration-testing.aspx)

- [Rojo contra azul: pruebas de penetración de seguridad internas de Microsoft Azure y Office 365](http://azure.microsoft.com/documentation/videos/red-vs-blue-internal-security-penetration-testing-of-microsoft-azure/)



## ANEXO 

### Diferencia entre Red Teaming y las pruebas de penetración 

Las técnicas de pruebas de penetración estándar usan herramientas comunes para intentar realizar ataques, pero rara vez aprovechan todas las funcionalidades de un adversario experto.

Las pruebas de penetración tradicionales solo representan un punto en el tiempo y finalizan sin forzar más el entorno y la respuesta ante incidentes del modo que lo hace un adversario real. Red Teaming es continuo, y no se detiene cuando se completa la prueba. Red Teaming encontrará cosas que otras metodologías de pruebas de penetración a menudo no encuentran (resumidas en la tabla 1, a continuación):

**PRUEBAS DE PENETRACIÓN DE TRADICIONALES FRENTE A RED TEAMING **

| | Pruebas de penetración tradicionales | Red Teaming |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Persistencia** | Se detiene cuando ha tenido éxito en poner en peligro el entorno | Establece un mecanismo de persistencia para mantener el acceso y evaluar la integridad de la recuperación de la infracción |
| **Herramientas** | Solo usa lo que está disponible en ese momento y, a continuación, debe esperar a que el usuario interactúe en el siguiente ciclo de prueba | Investiga constantemente nuevos ataques y vulnerabilidades e implementa un ataque con nuevas herramientas en cuanto se descubren |
| **Actividad posterior a la infracción** | Son "blanco sombrero" (solo intentan entrar y, si lo hacen, normalmente no hacen nada más) | Aprovecha la infracción para extraer y aprovechar datos críticos para lanzar ataques futuros |
| **Escape y evasión** | Las pruebas estáticas no pueden intentar evadir la detección y detención si se las encuentra | Pruebas dinámicas que intentan evitar la detección |
| **Adversarios expertos** | Tiende a forzar los sistemas de seguridad que se sabe que están implementados (ya que puede tener un conocimiento completo de los sistemas de TI), comprueba si están implementados correctamente o usa vulnerabilidades de seguridad conocidas | Realiza pruebas para ver qué se ha implementado *y* qué no para encontrar vulnerabilidades; usa el conocimiento interno y las herramientas personalizadas para atacar los sistemas, incluido el desarrollo de vulnerabilidades de seguridad personalizadas y desconocidas |
| **Sitio web activo** | Casi nunca ataca los sistemas de producción completos; suele usarse más a menudo para probar los sistemas antes de la implementación | Ataca los sistemas de producción e incluye todas las capas de la pila |
| **Motivación** | Realiza suposiciones sobre el entorno; no puede probar aquello a lo que no se le dice que vaya | No realiza ninguna suposición; intenta poner en peligro todo; gira y cambia de táctica si es necesario |
| **Resultados** | Muestra las pruebas que se realizaron y los resultados (éxito o error) | Mide y evalúa con precisión el MTTC, el MTTP, el MTTD y el MTTR. Proporciona evidencia detallada sobre cuándo y cómo se infringió la seguridad del entorno, los recursos que se pusieron en peligro y si la detección y respuesta obtuvieron resultados satisfactorios. |


**Tabla 1: Resumen de las diferencias entre las pruebas de penetración tradicionales y Red Teaming**


##¿Por qué atacar la producción? 

Las pruebas en preproducción invariablemente nunca coinciden con la producción. Los cambios realizados durante la implementación, las operaciones de servicio en curso y las diferencias en cómo usan los clientes la producción contribuyen a variar el estado. Esta variación existe por una buena razón, ya que la producción se enfrenta a diferentes necesidades con respecto a la carga esperada y el tiempo de inactividad aceptable, así como los requisitos de seguridad, puesta en peligro y normativas.

El objetivo de Red Teaming son sistemas reales mediante las pruebas de penetración de sitios web activos, en lugar de simplemente atacar un entorno de desarrollo y pruebas en un laboratorio con cargas de trabajo hipotéticas o incluso componentes reales con datos simulados. Es una de las principales diferencias entre Red Teaming y las estrategias de seguridad más tradicionales: servidores reales, inquilinos reales y datos reales. Las pruebas de penetración de producción de sitios activos proporcionan una medida mucho más confiable y precisa de los efectos en los clientes y el negocio asociados a una infracción.

La penetración de producción de sitios activos ofrece ventajas adicionales. Pone a prueba capacidades defensivas de detección y respuesta. Los ataques reales prueban la fuerza de las protecciones implementadas en la producción y las funcionalidades de detección y respuesta (o su ausencia). Obliga a estas inversiones para aceptar el hecho de que producción está sufriendo un ataque, puede que esté en peligro y representa una función que fuerza que la respuesta ante incidentes de seguridad se convierta en métodos de procedimientos regulares que afronten ataques dirigidos y adversarios persistentes.

##Ejecución de un Equipo rojo 

La adopción de la suposición de infracción no requiere invertir en los propios recursos del Equipo rojo. Muchas de las ventajas pueden obtenerse simplemente modificando su enfoque de las pruebas de seguridad de infraestructura y aplicaciones y la implementación. Por ejemplo, entender que puede haber una infracción de datos activa, mucho más allá de la puesta en peligro inicial, puede ayudar a diseñar medidas de seguridad para proteger la información y los recursos críticos, incluso en caso de infracción.

Sin embargo, Red Teaming es una manera eficaz de probar la veracidad de los procesos y las medidas de seguridad, aumentar el conocimiento del entorno actual y hacer que los ingenieros y servicios de operaciones y respuesta de seguridad y administración sean conscientes de las amenazas emergentes. Para determinar cuáles deben ser las prioridades de inversión, considere lo siguiente:

-   **Prevención de infracciones frente a suposición de infracción**: una no sustituye a la otra. La prevención de infracciones es la implementación crítica de las funcionalidades de seguridad para defenderse contra los ataques. La suposición de infracción va más allá de dichas protecciones para representar la detección y la respuesta antes, durante y después de una infracción.

-   **Identificar las características más valiosas**: clasificar por orden de prioridad las inversiones con el enfoque de suposición de infracción. ¿Cuáles son los datos más críticos para el negocio? ¿Dónde se almacenan esos datos? ¿Cuáles son los recursos más importantes para la organización?

-   **Identificar los ejercicios relevantes**: clasificar por orden de prioridad los ejercicios de seguridad en función de las tendencias de infracción y el TTP de los atacantes aplicable a su entorno.

-   **Obtener un acuerdo**: especialmente en el nivel ejecutivo y asegurar una aprobación legal. Además, los distintos grupos de trabajo y divisiones de la compañía deben tener en cuenta y aceptar el trabajo que está haciendo; iniciar ataques contra las operaciones que nunca se han atacado antes podría provocar interrupciones del servicio imprevistas y problemas de satisfacción del cliente.


Una vez que haya creado un plan para realizar Red Teaming, debe proteger las operaciones:

-   **Personal**: asegúrese de que tiene suficientes recursos expertos en ambos lados de la suposición de infracción (el Equipo rojo y el azul). El Equipo azul debe tratar todos los incidentes de seguridad, incluidas las infracciones del Equipo rojo, como un incidente real. Tenga en cuenta el equilibrio de recursos a tiempo completo frente a la subcontratación, incluida la seguridad de la propiedad intelectual producida y puesta en peligro durante Red Teaming. Si va a interactuar con otro proveedor, *compruebe su ámbito*, responsabilidad y reputación.

-   **Filtro**: además de que los miembros del equipo tengan los conocimientos técnicos necesarios, deben ser de confianza; investigue exhaustivamente a los posibles empleados, incluida una detallada comprobación de los antecedentes.

-   **Formación** : si va a contratar empleados a tiempo completo, deben estar formados en procedimientos éticos y de seguridad, privacidad y puesta en peligro estándar, además de los requisitos únicos de su negocio o sector (los organismos externos deben estar certificados para garantizar actividades de formación regulares).

-   **Auditoría**: es fundamental que actividad del Equipo rojo se supervise, registre y audite correctamente antes y durante los ejercicios del Equipo rojo. Esto funciona como una 'tarjeta de salida de la cárcel' en caso de que se acuse al Equipo rojo de daños no derivados del ejercicio; puede ayudar a asegurar que el Equipo rojo sigue un código de conducta estricto y que se rige por las reglas de contratación. Además, estos registros de auditoría y registro a menudo son fundamentales para calcular el MTTC, el MTTD y el MTTR, así como para proporcionar un informe detallado después del ejercicio.

-   **Seguridad operativa**: es absolutamente crítico que los miembros del Equipo rojo observen una estricta seguridad operativa con respecto a sus TTP, herramientas, hallazgos de seguridad, informes, vulnerabilidades de seguridad, puertas traseras y otros recursos. En caso de que un atacante acceda a los recursos del Equipo rojo, podrían reconstruir y reproducir rápidamente los ataques contra vulnerabilidades conocidas con un impacto devastador.

##Información adicional

Las pruebas de penetración a escala empresarial requieren la participación de varias partes de una organización. Puesto que este documento cubre la guía de procedimientos, está destinado a los responsables de evaluar los riesgos de seguridad de los entornos, los sistemas y los procesos. Tenga en cuenta que los detalles proporcionados en este documento son una visión general de cómo los equipos de seguridad de Microsoft Azure y Office 365 realizan Red Teaming y pruebas de penetración de sitios web activos, no instrucciones acerca de cómo los clientes deben atacar la infraestructura de la nube, las plataformas o los servicios de Microsoft.

Si bien Microsoft realiza pruebas de penetración regulares para mejorar los procesos y controles de seguridad en la nube, somos conscientes de que las evaluaciones de seguridad también son una parte importante de las actividades de desarrollo e implementación de aplicaciones de nuestros clientes. Por tanto, hemos establecido una directiva que permite a los realizar pruebas de penetración autorizadas en sus aplicaciones que tengan hospedadas en Microsoft Azure.

Puesto que no es posible distinguir estas pruebas de un ataque real, es fundamental que los clientes realicen las pruebas de penetración solo después de obtener la aprobación previa del departamento de soporte al cliente de Microsoft Azure. Las pruebas de penetración deben realizarse de acuerdo con los términos y condiciones de Microsoft. Las solicitudes de pruebas de penetración deben enviarse mediante una notificación por adelantado 7 días antes como mínimo. Para más información, consulte el [Centro de confianza de Microsoft]https://www.microsoft.com/TrustCenter/CloudServices/Azure).

## Referencias

[1] Acceso y elevación Just-in-Time (JIT): elevación que se concede según sea necesario y solo cuando se necesita. A menudo, para implementarlo se exigen una o varias aprobaciones antes de conceder acceso.

[2] Just Enough Administration (JEA): una solución diseñada para ayudar a proteger los sistemas al permitir a determinados usuarios realizar tareas administrativas sin concederles derechos de administrador y, después, realizar auditorías de todas las acciones de dichos usuarios. Para más información, consulte el [artículo sobre Just Enough Administration en la galería de TechNet](https://gallery.technet.microsoft.com/Just-Enough-Administration-6b5ad370)

[3] En el contexto de una infracción de seguridad, MTTD hace referencia al tiempo necesario para detectar una infracción.

[4] MTTR se refiere a cuánto tiempo lleva recuperarse de una infracción una vez detectada.

[5] Equipos rojo y azul a tiempo completo y separados del personal de Microsoft Azure y Office 365.

[6] Consulte [“Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains”](http://www.lockheedmartin.com/content/dam/lockheed/data/corporate/documents/LM-White-Paper-Intel-Driven-Defense.pdf) (Defensa de redes informáticas realizadas mediante inteligencia informada mediante análisis de campañas de adversarios y cadenas de eliminación de intrusiones), de Lockheed Martin, para más información.

[7] Niebla de guerra: utilizado para describir la incertidumbre sobre la situación sufrida por los participantes con respecto a su propia funcionalidad, la funcionalidad del adversario y la intención del adversario.

[8] Archivo de prueba EICAR: también conocido como archivo de prueba antivirus estándar EICAR, es un archivo informático desarrollado por el Instituto Europeo para la Investigación de los Antivirus Informáticos (EICAR) y la Organización para la Investigación de los Antivirus Informáticos (CARO) para probar la respuesta ante programas antimalware. En lugar de usar malware real, lo que podría causar un daño real, este archivo de prueba permite probar el software antimalware sin utilizar un virus informático real.


*(c) 2014 Microsoft Corporation. Todos los derechos reservados. Este documento se ofrece "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. El usuario asume el riesgo de usarlo. Algunos ejemplos se proporcionan únicamente con fines ilustrativos y son ficticios. No hay ninguna asociación real intencionada ni inferida. Este documento no proporciona ningún derecho legal a la propiedad intelectual de ningún producto de Microsoft. Puede copiar y usar este documento con fines internos y de referencia.*

<!---HONumber=AcomDC_0218_2016-->