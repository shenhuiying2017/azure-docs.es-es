<properties
   pageTitle="Requisitos previos técnicos para la creación de una plantilla de soluciones para Marketplace | Microsoft Azure"
   description="Información de los requisitos para crear una plantilla de solución para implementar y vender en Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio; v-divte" />

# Requisitos previos técnicos para la creación de una plantilla de soluciones para Azure Marketplace
Lea el proceso minuciosamente antes de empezar y comprenda dónde y por qué se realiza cada paso. Tanto como sea posible, debe preparar la información de su compañía y otros datos, descargar las herramientas necesarias o crear componentes técnicos antes de comenzar el proceso de creación de la oferta. Estos elementos deben estar claros tras la revisión de este artículo.

## Definición de la topología y evaluación de la plataforma
En este momento puede que ya esté familiarizado con los artefactos de proceso de Azure y esté pensando ¿qué debo crear exactamente? ¿Imágenes de máquinas virtuales, extensiones, plantillas ARM?

Antes de intentar asignar la aplicación a los artefactos de Azure, le ayudará a comprender las diferentes topologías que tienen sentido para la solución y que probablemente también recomendaría a un cliente que considerase implementar su aplicación de forma local. La mayoría de las empresas por lo general llevan a cabo una variación de las cuatro fases siguientes: evaluación, prueba de concepto, preproducción y producción. Por lo general, trabaja con ellos a través de estas distintas fases mediante la evaluación de sus requisitos y la recomendación del diseño exacto y de la escala necesarios para sus escenarios. Aunque no todos los clientes son iguales, por lo general esperamos que haya topologías y tamaños comunes que recomiende en función del tamaño de la implementación de cliente. De esta forma, incluso antes de empezar a desarrollar soluciones, piense en los distintos diseños que tengan sentido para su aplicación y lo que podría satisfacer de las necesidades del cliente sin personalizaciones importantes. Nos damos cuenta que siempre habrá clientes que necesiten algo muy preciso, pero tenemos que buscar el caso común.

Para cada tipo de implementación (evaluación, prueba de concepto, preproducción y producción), piense en el diseño y en la topología exactos que necesita para su aplicación. ¿Cuáles son los diferentes niveles? ¿Qué recursos son necesarios para cada nivel y de qué tamaño? ¿Cuáles son los bloques de creación necesarios para crear estos recursos? Una implementación de producción, o incluso un entorno de desarrollo y pruebas, puede variar considerablemente de tamaño según sus necesidades y el cliente. Decida qué tipo de topología pequeña, mediana, grande y muy grande le gustaría para la carga de trabajo y considere los requisitos de disponibilidad, escalabilidad y rendimiento necesarios para tener éxito.

Cuando haya pensado en los distintos diseños de la topología y sus tamaños correspondientes (es posible que tenga casi todo esto terminado debido a su experiencia con las implementaciones locales con los clientes), es el momento de ver lo que Azure tiene que ofrecer.

Evalúe sus requisitos de topología con lo que le ofrece el Administrador de recursos de Azure y Proceso en el Administrador de recursos de Azure. Aunque la mayoría de los clientes comenzará con implementaciones más pequeñas, **recomendamos encarecidamente evaluar los requisitos de rendimiento, de escala y de capacidad de la topología más grande en Proceso en el Administrador de recursos de Azure aunque inicialmente no esté incorporando una topología de gran tamaño.** De esta forma se asegurará de que los clientes que comienzan a adoptar las soluciones más pequeñas tienen espacio suficiente para crecer satisfactoriamente a implementaciones de mayor escala. Cualquier limitación de escala, rendimiento o capacidad deben elevarse a los homólogos de Microsoft para asegurarse de que seguimos mejorando en las áreas que necesite, como socio nuestro de Marketplace y, por consiguiente, para los clientes.

> [AZURE.TIP]**Recomendado:** evalúe y cree una plantilla para que coincida con el mayor tamaño previsto de implementación de la aplicación con el fin de ofrecer oportunidades de crecimiento a sus clientes finales.

## Bloques de creación de Proceso de Azure
Comencemos por identificar qué artefactos del proceso están disponibles para usted y cómo pensar en la aplicación en términos de estos artefactos.

### 1\. Uso de máquinas virtuales existentes
Los bloques de creación para cualquier máquina virtual son las imágenes de la máquina virtual. Las imágenes de máquina virtual son el perfil de almacenamiento completo de una máquina virtual, que contiene un VHD del sistema operativo y ninguno o más discos de datos. Puede crear una imagen de máquina virtual para empaquetar la aplicación de software con los bits del sistema operativo, además de con cualquier dato adicional necesario. Por ejemplo, hoy en día en Azure Marketplace, hay una serie de imágenes de máquina virtual para SQL Server, que empaqueta el sistema operativo Windows, con la aplicación de SQL Server. Aunque este documento no describirá con detalle la creación y la incorporación de imágenes de máquina virtual en Azure Marketplace, es importante entender qué es una imagen de máquina virtual, ya que probablemente será el bloque de creación de su solución de aplicación. Consulte los pasos para [crear una imagen de máquina virtual para Marketplace](marketplace-publishing-vm-image-creation.md).

### 2\. Extensión de máquina virtual
A veces, una imagen no es lo que estaba buscando, ya que no desea poseer ni administrar el sistema operativo subyacente, como un proveedor de la aplicación. En Azure Marketplace, hay una serie de compañías de sistemas operativos que publican los sistemas operativos Windows, Linux y Unix de forma periódica. Puede encontrar imágenes de Windows Server, Ubuntu, para CentOS, CoreOS, Oracle, Linux, etc. En lugar de publicar su propia imagen de máquina virtual, mediante el empaquetado del sistema operativo y de la aplicación, puede escribir un controlador de extensión de la máquina virtual o aprovechar uno existente. Una extensión de la máquina virtual es un mecanismo de software para simplificar la configuración y administración, que aprovecha el agente invitado (un proceso protegido y ligero) en una máquina virtual de Azure. Por ejemplo, la extensión de scripts personalizados, toma un script personalizado como el nombre sugiere y lo ejecuta en la máquina virtual. Esto permite que cualquier tipo de configuración admita un script genérico.

En un escenario de imagen tradicional, debe tener instalados todos los bits en la imagen desde el principio, haber realizado cualquier configuración necesaria antes o incluso después del inicio de la máquina virtual (con una tarea de inicio). Esta imagen se puede personalizar considerablemente y por tanto no debería ser fácil volver a usarla. En algunos casos, esto puede ser más adecuado. En otros, especialmente con aplicaciones ligeras o configuraciones más complicadas que requieren que se realice una vez aprovisionada la máquina virtual, el modelo de extensión puede ayudar. A veces, los clientes implementan una combinación de extensiones y de imágenes de máquina virtual para tener ambas soluciones preinstaladas y también para habilitar una configuración compleja posterior al inicio.

Las imágenes y las extensiones son muy útiles configurar una máquina virtual, como bien sabe. Sin embargo, una máquina virtual no es suficiente para ofrecer alta disponibilidad a los clientes en la nube y para escalarla con objeto de satisfacer las necesidades de un tráfico creciente. Por lo tanto, para cualquier carga de trabajo de producción, se recomienda planear una solución de alta disponibilidad para varias máquinas virtuales mediante conjuntos de disponibilidad y equilibrio de carga.

>[AZURE.TIP]**Recomendado:** todas las cargas de trabajo de producción en Marketplace deben implementarse con varias máquinas virtuales que atienden el tráfico en un conjunto de disponibilidad (con tres dominios de error) y con un equilibrador de carga.

Para admitir estas numerosas topologías de implementación que su aplicación puede admitir y recomendar, Azure Marketplace ofrece el Administrador de recursos de Azure (ARM para abreviar) y plantillas ARM. El Administrador de recursos de Azure implementa y administra el ciclo de vida de una colección de recursos a través de lenguaje declarativo, basado en el modelo de plantilla. Esta plantilla es simplemente un archivo JSON con parámetros que expresa el conjunto de recursos y su relación que se usará para las implementaciones. Cada recurso se coloca en un grupo de recursos, que simplemente es un contenedor de recursos. El Administrador de recursos también ofrece un conjunto de capacidades adicionales muy valiosos para los clientes finales, incluido la auditoría de operación centralizada, el etiquetado de recursos por la factura del cliente, el control de acceso basado en recursos para una seguridad granular, compatibilidad para tres dominios de error de proceso para la mayor disponibilidad y todas sus operaciones son idempotentes.

> [AZURE.IMPORTANT]**Obligatorio:** dados los beneficios para los clientes finales, todo el contenido de Marketplace debe implementarse mediante las plantillas de Administrador de recursos de Azure.

Con ARM y plantillas ARM, puede expresar implementaciones más complejas de los recursos de Azure, como máquinas virtuales, cuentas de almacenamiento y redes virtuales, que parten de contenido de Marketplace, como imágenes de máquina virtual y extensiones de máquina virtual.

## Desarrollo de los bloques de creación
Una vez que haya averiguado las topologías de aplicación y las que va a desarrollar e incorporar a Marketplace en la primera etapa, es hora de identificar y desarrollar los bloques de creación de la máquina virtual. Como se comentó anteriormente, tendrá que decidir si quiere empaquetar el contenido de la máquina virtual como su propia imagen de máquina virtual o aprovechar una imagen ya existente en Azure Marketplace, configurarla con un controlador de extensión publicada o con su propio controlador. No hay una única solución correcta; depende de cuánto control se desea en torno al sistema operativo subyacente y de los requisitos sobre cómo instalar, configurar y administrar la aplicación. Puede encontrar una buena cantidad de información sobre imágenes y extensiones en MSDN y en los blogs de Azure. Una lista de artículos que pueden resultarle útiles puede encontrarse más abajo en la sección **Consulte también**.

Tenga en cuenta que para cualquier controlador de extensión o de imagen usado, primero debe publicarlo a través de Marketplace.

## Desarrollo de plantillas ARM
Si no está familiarizado con el Administrador de recursos de Azure y plantillas ARM, puede ser conveniente repasar parte de la documentación sobre Administrador de recursos de Azure y sobre Proceso en el Administrador de recursos de Azure. La orientación y los ejemplos indicados a continuación asumen un nivel básico de comprensión del lenguaje de definición de plantillas ARM y sobre cómo usar Proceso con ARM. En esta sección se describen principalmente los procedimientos recomendados que le ayudarán a desarrollar buenas plantillas específicamente para Azure Marketplace. Estas plantillas tienen en cuenta los requisitos que permiten una experiencia unificada, fácil de usar y relevante para los clientes tanto desde el Portal Ibiza como mediante programación.

Es probable que su solución se exprese con un conjunto de plantillas. Estas plantillas son fragmentos lógicos de la topología. Hay muchas razones para desglosar la expresión de la topología en varios archivos, desde la facilidad de desarrollo y la capacidad de administración, la reutilización de determinados subconjuntos, a las pruebas modulares y expansión de soluciones de plantilla. Después de trabajar con muchos partners, este desglose propuesto por el equipo de Azure consiste en dividir la topología en las plantillas de recursos comunes (como redes virtuales, cuentas de almacenamiento, etc.), llamadas plantillas de recursos compartidos, ninguna o varias plantillas para recursos opcionales, denominadas plantillas de recursos opcionales y una o varias plantillas para los recursos específicos de topología, denominadas plantillas de recursos de configuración conocida.

Una solución de plantilla completa en Marketplace constará de los siguientes elementos:

1. **Una plantilla principal (mainTemplate.json)** : este es el punto de entrada a la implementación general, expresada en el archivo de plantillas de JSON de ARM. Es el primer archivo que se evalúa para la solución de plantilla y el que se usó para unir (vincular) el resto de las plantillas. Este archivo también debe contener toda la entrada (parámetros) deseada del usuario final.
2. **Ninguna o más plantillas vinculadas**: el resto de las plantillas que están vinculadas a partir de la plantilla principal para expresar la topología de la solución completa.
3. **Ninguno o más archivos de script**: cualquier archivo de script que se espera como entrada a las extensiones que puede usar para configurar una máquina virtual.
4. **Un archivo de definición de la interfaz de usuario de creación (uiDefinition.json createUiDefinition.json)**: es el archivo que asigna los parámetros de la plantilla ARM en los elementos de la experiencia del usuario. Este archivo permite a los partners tener control sobre la interfaz de usuario representada a sus clientes finales en Ibiza. Todas las entradas necesarias que debe proporcionar el usuario final se expresarán en este archivo. Cada entrada le permite controlar todos los aspectos de la interfaz de usuario asociados a cada entrada, p. ej. el widget de interfaz de usuario, valores predeterminados, reglas de validación, los mensajes de error, etc. Cuando comience a desarrollar cada uno de estos archivos para expresar la topología completa, tenga en cuenta los siguientes criterios para asegurar la plantilla más adecuada para su oferta de Marketplace.

## Recomendaciones y requisitos adicionales

### Conjunto mínimo estandarizado de entrada de usuario
Los parámetros le permiten expresar los valores que debe proporcionar el usuario en el momento de la implementación de la plantilla. La entrada del usuario puede variar de una aplicación a otra e incluso de un nivel de topología a otro. La lista de parámetros debe proporcionar un buen equilibrio entre solicitar el menor número de preguntas necesarias y ofrecer al usuario los mecanismos correctos para controlar su implementación. Aunque no hay ningún requisito en relación a la lista completa de entrada de usuario, queremos asegurarnos de que todas las soluciones de plantilla de Marketplace tengan un conjunto de entradas comunes que muchos clientes quieren controlar. Asegúrese de que definió los parámetros para los valores siguientes:

1. **Ubicación**: la región de Azure donde se implementan los recursos.
2.	**Nombre de red virtual**: en aquellas implementaciones que crean una nueva red virtual, el nombre que se usará para crear ese recurso. En aquellas implementaciones que usan una red virtual existente, el nombre de la red virtual en la que se va a implementar.
3.	**Cuenta de almacenamiento**: en aquellas implementaciones que crean una nueva cuenta de almacenamiento, el nombre de la cuenta de almacenamiento que se va a crear. En aquellas implementaciones que usan una red virtual existente, el nombre de la cuenta de almacenamiento que se va a usar.
4.	**Nombre DNS**: nombre de dominio de la máquina virtual o un equilibrador de carga al que puede conectarse un usuario final.
5.	**Nombre de usuario**: el nombre de usuario para las máquinas virtuales y, posiblemente, para las aplicaciones. Se puede solicitar más de un nombre de usuario del usuario final, pero al menos uno debe solicitarse.
6.	**Contraseña**: la contraseña para las máquinas virtuales y, posiblemente, para las aplicaciones. Se puede solicitar más de una contraseña del usuario final para diferentes máquinas virtuales o aplicaciones, pero al menos una debe solicitarse.
7.	**Clave pública SSH (solo para las máquinas virtuales de Linux)**: la clave SSH para las máquinas virtuales. Se puede solicitar más de una clave del usuario final para diferentes máquinas virtuales o aplicaciones, pero al menos una debe solicitarse.

## Pasos siguientes
Ahora que revisió los requisitos previos y completado la tarea necesaria, puede continuar con la creación de su oferta de plantilla de soluciones como se detalla en la [Guía para crear una plantilla de soluciones](marketplace-publishing-solution-template-creation.md).

## Otras referencias
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
- [Procedimientos recomendados para la creación de una plantilla de soluciones](marketplace-publishing-solution-template-best-practices.md)

[link-acct]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=Oct15_HO3-->