---
title: Conceptos sobre la seguridad con X.509 en Azure IoT Hub | Microsoft Docs
description: "Concepto: información sobre el valor de los certificados de entidades de certificación X.509 en la fabricación y autenticación de dispositivos IoT."
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 34d4be431b76d5ba8258d932cb21ed6f4818863e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Explicación de los conceptos de certificados de entidad de certificación X.509 en el sector de IoT

En este artículo se describe el valor de los certificados de entidades de certificación X.509 en la fabricación de dispositivos IoT y en la autenticación en IoT Hub.  Incluye información sobre la configuración de la cadena de suministro y las ventajas destacadas.

En este artículo se describe:

* Qué son los certificados de entidad de certificación X.509 y cómo conseguirlos
* Cómo registrar el certificado de entidad de certificación X.509 en IoT Hub
* Cómo configurar una cadena de suministro de fabricación para la autenticación basada en entidades de certificación X.509
* Cómo los dispositivos firmados con certificados de entidades de certificación X.509 en IoT Hub

## <a name="overview"></a>Información general

La autenticación con entidades de certificación X.509 es un enfoque de autenticación de dispositivos en IoT Hub con un método que simplifica significativamente la creación de identidades de dispositivo y la administración del ciclo de vida en la cadena de suministro.

Un atributo distintivo de la autenticación mediante la entidad de certificación X.509 es una relación de uno a muchos que un certificado de entidad de certificación tiene con sus dispositivos de nivel inferior.  Esta relación permite registrar una vez cualquier número de dispositivos en IoT Hub mediante el registro de un certificado de entidad de certificación X.509; de lo contrario, los certificados exclusivos de los dispositivos deben registrarse previamente en cada dispositivo antes de que un dispositivo pueda conectarse. Esta relación de uno a varios también simplifica las operaciones de administración del ciclo de vida de los certificados de los dispositivos.

Otro atributo importante de la autenticación mediante la autenticación con la entidad de certificación X.509 es la simplificación de la logística de la cadena de suministro.  La autenticación segura de los dispositivos requiere que cada dispositivo tenga un secreto exclusivo, como una clave como base de confianza. En la autenticación basada en certificados, este secreto es una clave privada. Un flujo de fabricación de dispositivos típico implica varios pasos y administradores.  La administración segura de claves privadas de dispositivos entre varios administradores y mantener la confianza resulta difícil y caro.  El uso de las entidades de certificación soluciona este problema con el registro de cada administrador en una cadena de confianza criptográfica, en lugar de protegerlas con claves privadas de dispositivo.  A su vez, cada administrador firma los dispositivos en el paso correspondiente del proceso del flujo de fabricación.  El resultado general es una cadena de suministro óptima con responsabilidad integrada a través del uso de la cadena de confianza criptográfica.  Es importante destacar que este proceso garantiza la máxima seguridad cuando los dispositivos protegen sus claves privadas exclusivas.  Para ello, se recomienda usar los módulos de seguridad de hardware (HSM) capaces de generar claves privadas internamente que nunca se publicarán.

En este artículo se proporciona una vista de un extremo a otro sobre cómo usar la autenticación mediante entidades de certificación X.509, desde la configuración de la cadena de suministro hasta la conexión del dispositivo, mediante el uso de un ejemplo real para consolidar la información.

## <a name="introduction"></a>Introducción

El certificado de entidad de certificación X.509 es un certificado digital cuyo titular puede firmar otros certificados.  Este certificado digital es X.509 porque se ajusta a un certificado de formato estándar establecido por el estándar RFC 5280 de IETF y se trata de una entidad de certificación (CA) porque su titular puede firmar otros certificados.

El uso de la entidad de certificación X.509 se entiende mejor en relación con un ejemplo concreto.  Pensemos en la empresa Company-X, fabricante de productos Smart-X-Widgets diseñados para instalaciones profesionales. Company-X externaliza la instalación y fabricación.  Contrata al fabricante Factory-Y para fabricar Smart-X-Widgets y al proveedor de servicios Technician-Z para la instalación. Company-X quiere que Factory-Y envíe el producto Smart-X-Widget directamente a Technician-Z para la instalación y que se conecte directamente a la instancia de IoT Hub de Company-X después de la instalación sin que Company-X tenga que intervenir más. Para ello, Company-X debe completar una serie de operaciones de instalación una única vez para preparar Smart-X-Widget para la conexión automática.  Teniendo en cuenta el escenario de un extremo a otro, el resto del artículo se estructura como sigue:

* Adquisición del certificado de entidad de certificación X.509

* Registro del certificado de entidad de certificación X.509 en IoT Hub

* Registro de dispositivos en la cadena de certificados de confianza

* Conexión del dispositivo

## <a name="acquire-the-x509-ca-certificate"></a>Adquisición del certificado de entidad de certificación X.509

Company-X tiene la opción de adquirir un certificado de entidad de certificación X.509 de una entidad de certificación raíz pública o de crear uno a través de un proceso autofirmado.  Una opción sería mejor que la otra en función del escenario de aplicación.  Con independencia de la opción elegida, el proceso engloba dos pasos fundamentales; por un lado, la generación de un par de clave pública/priada y, por otro, el registro de la clave pública en un certificado.

![img-csr-flow](./media/csr-flow.png)

Los detalles sobre cómo llevar a cabo estos pasos difieren entre los distintos proveedores de servicios.

### <a name="purchasing-an-x509-ca-certificate"></a>Compra de un certificado de certificación X.509

Adquirir un certificado de entidad de certificación tiene la ventaja de que una entidad de certificación raíz bien conocida actúa como un tercero de confianza para garantizar la legitimidad de los dispositivos IoT cuando estos se conectan. Company-X elegiría esta opción si pretende que Smart-X-Widget interactúe con productos o servicios de terceros después de la conexión inicial a IoT Hub.

Para adquirir un certificado de entidad de certificación X.509, Company-X elegiría un proveedor de servicios de certificados raíz. Una búsqueda en Internet de la frase "CA raíz" ofrecería buenos proveedores potenciales.  La entidad de certificación raíz guiará a Company-X en la creación del par de claves pública/privada y en la generación de una solicitud de firma de certificado (CSR) para sus servicios.  Una CSR es el proceso formal de aplicación de un certificado de una entidad de certificación.  El resultado de esta compra es un certificado que se puede usar como un certificado de entidad.  Dada la ubicuidad de los certificados X.509, es probable que se haya dado el formato adecuado al certificado con el estándar RFC 5280 de IETF.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Creación de un certificado de entidad de certificación X.509 autofirmado

El proceso para crear un certificado de entidad de certificación X.509 autofirmado es similar a la compra, con la excepción de que implica a un firmante de terceros como la entidad de certificación raíz. En el ejemplo, Company-X firmará su certificado de entidad en lugar de una entidad de certificación raíz.  Company-X puede elegir esta opción para realizar pruebas hasta que la empresa esté lista para adquirir un certificado de entidad. Company-X también puede usar un certificado autofirmado de entidad de certificación X.509 en producción, si Smart-X-Widget no está diseñado para conectarse a los servicios de terceros fuera de IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registro del certificado X.509 en IoT Hub

Company-X debe registrar la entidad de certificación X.509 en IoT Hub donde servirá para autenticar Smart-X-Widgets a medida que se conectan.  Se trata de un proceso único que ofrece la capacidad de autenticar y administrar cualquier número de dispositivos Smart-X-Widget.  Este proceso es único porque hay una relación de uno a muchos entre el certificado de entidad y los dispositivos y, además, constituye una de las principales ventajas derivadas del uso del método de autenticación con entidad de certificación C.509.  La alternativa es cargar huellas digitales de certificado individuales para cada dispositivo Smart-X-Widget, lo que se agrega como suplemento a los costos operativos.

El registro del certificado de entidad de certificación X.509 es un proceso de dos pasos, que comprende la carga del certificado y la prueba de posesión del certificado.

![img-pop-flow](./media/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Carga de certificado de entidad de certificación X.509

El proceso de carga del certificado de entidad de certificación X.509 consiste justamente en eso, en cargar el certificado en IoT Hub.  IoT Hub espera el certificado en un archivo. Company-X simplemente carga el archivo de certificado. El archivo de certificado NO DEBE contener ninguna clave privada bajo ningún concepto.  Los procedimientos recomendados de los estándares que rigen la infraestructura de clave pública (PKI) exige que el conocimiento de la clave privada de Company-X en este caso resida únicamente dentro de Company-X.

### <a name="proof-of-possession-of-the-certificate"></a>Prueba de posesión del certificado

El certificado de entidad de certificación X.509, al igual que cualquier certificado digital, es información pública que es susceptible de interceptación.  Por lo tanto, un intruso puede interceptar un certificado e intentar cargarlo como si fuera suyo.  En nuestro ejemplo, IoT Hub querría asegurarse de que el certificado de entidad de certificación que Company-X está cargando realmente pertenece a Company-X. Para ello, instará a Company-X a que demuestre que realmente posee el certificado mediante un [flujo de prueba de posesión](https://tools.ietf.org/html/rfc5280#section-3.1). El flujo de la prueba de posesión conlleva que IoT Hub genere un número aleatorio que Company-X debe firmar con su clave privada.  Si Company-X siguió los procedimientos recomendados de la infraestructura de clave pública y protegió su clave privada, entonces solo esta empresa podrá responder correctamente al desafío de la prueba de posesión.  IoT Hub procede con el registro del certificado de entidad de certificación X.509 tras superar satisfactoriamente el desafío de la prueba de posesión.

El registro de la entidad de certificación X.509 se completa tras obtener la respuesta correcta de IoT Hub al desafío de la prueba de posesión.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Registro de dispositivos en una cadena de certificados de confianza

IoT requiere que todos los dispositivos posean una identidad única.  Estas identidades se encuentran en los certificados de formulario para los esquemas de autenticación basada en certificado.  En el ejemplo, esto significa que cada Smart-X-Widget debe poseer un certificado de dispositivo único.  ¿Qué configuración realiza Company-X para esto en su cadena de suministro?

Una manera de ocuparse de esto consiste en generar previamente certificados para Smart-X-Widgets y confiar el conocimiento de las claves privadas exclusivas correspondientes de los dispositivos a los asociados de la cadena de suministro.  Para Company-X, esto significa confiar en Factory-Y y Technician-Z.  Aunque se trata de un método válido, presenta desafíos que deben superarse para garantizar la confianza de la siguiente forma:

1. La necesidad de compartir las claves privadas de los dispositivos con los asociados de la cadena de suministro, además de ignorar los procedimientos recomendados de la infraestructura de clave pública de no compartir nunca las claves privadas, encarece la generación de confianza en la cadena de suministro.  Esto supone que sea necesario instalar sistemas importantes como centros seguros que velen por las claves privadas de los dispositivos y procesos como las auditorías periódicas de seguridad.  Ambas medidas generan costos en la cadena de suministro.

2. La contabilidad segura de los dispositivos en la cadena de suministro y su posterior administración en la fase de implementación se convierten en una tarea uno a uno para cada par de clave a dispositivo desde el punto de la generación del certificado único del dispositivo (por tanto, a partir de la clave privada) para la retirada del dispositivo. Esto excluye la administración en grupo de los dispositivos, a menos que el concepto de grupos esté integrado de forma explícita en el proceso de algún modo. La contabilidad segura y la administración del ciclo de vida del dispositivo, por tanto, se convierten en operaciones con mucho peso.  En el ejemplo, Company-X soportaría esta carga.

La autenticación mediante certificados de entidad de certificación X.509 ofrece soluciones elegantes para afrontar los desafíos indicados con el uso de las cadenas de certificados.  Una cadena de certificados resulta de que una entidad de certificación firma una entidad de certificación intermedia que, a su vez, firma otra entidad de certificación intermedia, y así sucesivamente hasta que una entidad de certificación intermedia final firma un dispositivo.  En el ejemplo, Company-X firma a Factory-Y, que, a su vez, firma a Technician-Z, que finalmente firma Smart-X-Widget.

![img-cert-chain-hierarchy](./media/cert-chain-hierarchy.png)

La cascada anterior de certificados de la cadena presenta el traspaso lógico de autoridad.  Muchas cadenas de suministro siguen este traspaso lógico, en el que cada entidad de certificación intermedia se registra en la cadena a medida que recibe certificados de entidad de certificación ascendentes, y la última entidad de certificación firma en última instancia cada dispositivo e introduce todos los certificados de entidad de la cadena en el dispositivo. Esto resulta habitual cuando la empresa fabricante contratada con una jerarquía de fábricas encarga a una fábrica concreta que se ocupe de la fabricación.  Aunque la jerarquía puede contar de varios niveles (por ejemplo, según la ubicación geográfica, el tipo de producto o la línea de fabricación), solo la fábrica final interactúa con el dispositivo, pero la cadena se mantiene desde el principio de la jerarquía.

Las cadenas alternativas pueden tener diferentes entidades de certificación intermedias que interactúen con el dispositivo, en cuyo caso, la entidad de certificación que interactúa con el dispositivo introduce el contenido de la cadena de certificado en dicho punto.  También se aceptan modelos híbridos donde solo algunas de las entidades de certificación interactúen de forma física con el dispositivo.

En el ejemplo, tanto Factory-Y como Technician-Z interactúan con Smart-X-Widget.  Aunque Company-X es la empresa propietaria de Smart-X-Widget, realmente no interactúa físicamente con el dispositivo en toda la cadena de suministro.  La cadena de certificados de confianza para Smart-X-Widget, por tanto, implica que Company-X firme a Factory-Y, que, a su vez, firma a Technician-Z, que proporcionará la firma definitiva de Smart-X-Widget. La fabricación e instalación de Smart-X-Widget conllevan que Factory-Y y Technician-Z usen sus certificados de entidad de certificación intermedia correspondientes para firmar todos los dispositivos Smart-X-Widgets. El resultado final de todo el proceso consistiría en que Smart-X-Widgets dispondría de certificados de dispositivo exclusivos y que la cadena de certificados de confianza llegaría a obtener el certificado de entidad de certificación de Company-X.

![img-cert-mfr-chain](./media/cert-mfr-chain.png)

Se trata de un buen punto para revisar el valor del método de entidad de certificación X.509.  En lugar de generar previamente y traspasar los certificados de cada Smart-X-Widget a la cadena de suministro, Company-X solo tiene que firmar a Factory-Y una vez.  En lugar de tener que realizar un seguimiento de cada dispositivo durante su ciclo de vida, Company-X no puede controlar y administrar dispositivos a través de grupos que surgen de forma natural del proceso de la cadena de suministro, como por ejemplo los dispositivos instalados por Technician-Z después de julio de cualquier año.

Por último, pero no por ello menos importante, el método de autenticación mediante entidad de certificación introduce un control seguro en la cadena de suministro de la fabricación de dispositivos. Debido al proceso de la cadena de certificados, las acciones de cada miembro de la cadena se registran de forma criptográfica y se pueden verificar.

Este proceso se basa en determinados supuestos que deben tenerse en cuenta por motivos de integridad.  Requiere la creación independiente de pares de claves pública/privada exclusivos para los dispositivos y la protección de la clave privada dentro del dispositivo.  Afortunadamente, existen chips de silicio en forma de módulos de seguridad de hardware (HSM) capaces de generar claves internamente y de proteger las claves privadas.  Company-X solo debe agregar uno de esos chips a la lista de materiales de los componentes de Smart-X-Widget.

## <a name="device-connection"></a>Conexión del dispositivo

Las secciones anteriores constituyen la fase de preparación para la conexión del dispositivo.  Con solo registrar un certificado de entidad de certificación X.509 en IoT Hub una vez, ¿cómo pueden millones de dispositivos conectarse y autenticarse por primera vez?  Es sencillo. Con el mismo proceso de carga del certificado y el flujo de la prueba de posesión, anteriormente se registró el certificado de entidad de certificación X.509.

Los dispositivos fabricados para la autenticación mediante la entidad de certificación X.509 están equipados con certificados exclusivos de dispositivo y con una cadena de certificados de su cadena de suministro de fabricación correspondiente.  La conexión del dispositivo, incluso la primera vez, se realiza con un proceso de dos pasos: la carga de la cadena de certificados y la prueba de posesión.

Durante la carga de la cadena de certificados, el dispositivo carga en IoT Hub su certificado exclusivo de dispositivo junto con la cadena de certificados instalada en él.  Con el uso del certificado de entidad de certificación X.509 registrado previamente, IoT Hub puede validar de forma criptográfica un par de cosas: que la cadena de certificados cargada sea coherente internamente y que la cadena la haya originado el propietario válido del certificado de entidad de certificación X.509.  Solo con el proceso de registro de la entidad de certificación X.509, IoT Hub iniciaría un proceso de desafío-respuesta de prueba de posesión para garantizar que la cadena y también el certificado del dispositivo realmente pertenecen al dispositivo en que se carga dicho certificado.  Para ello, se genera un desafío aleatorio que el dispositivo debe firmar mediante su clave privada para que IoT Hub realice la validación.   Una respuesta satisfactoria insta a IoT Hub a que acepte el dispositivo como auténtico y permite su conexión.

En el ejemplo, cada dispositivo Smart-X-Widget cargaría su certificado exclusivo del dispositivo junto con los certificados de entidad de certificación X.509 de Factory-Y y Technician-Z y, después, respondería al desafío de la prueba de posesión desde IoT Hub.

![img-device-pop-flow](./media/device-pop-flow.png)

Tenga en cuenta que la base de confianza recae en la protección de las claves privadas, incluidas las claves privadas del dispositivo.  Por tanto, se hace especial hincapié en la importancia que reviste la seguridad de los chips de silicio en forma de módulos de seguridad de hardware (HSM) para proteger las claves privadas de los dispositivos, así como el procedimiento recomendado general de no compartir nunca las claves privadas, como en el caso de que, por ejemplo, una fábrica confíe a otra su clave privada.

