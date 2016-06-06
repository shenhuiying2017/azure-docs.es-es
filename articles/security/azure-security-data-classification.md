<properties
   pageTitle="Clasificación de datos para Azure | Microsoft Azure"
   description="En este artículo se ofrece una introducción a los fundamentos de la clasificación de datos y se hace hincapié en sus ventajas, especialmente en el contexto de la informática en la nube y con el uso de Microsoft Azure."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="yurid"/>

# Clasificación de datos para Azure

En este artículo se ofrece una introducción a los fundamentos de la clasificación de datos y se hace hincapié en sus ventajas, especialmente en el contexto de la informática en la nube y con el uso de Microsoft Azure.

## Fundamentos de la clasificación de datos

Para clasificar correctamente los datos de una organización, es necesario comprender el amplio abanico de necesidades de la organización y conocer de forma exhaustiva dónde residen sus recursos de datos.
 
Los datos existen en uno de estos tres estados básicos:

- En reposo 
- En curso 
- En tránsito 
 
Los tres estados requieren soluciones técnicas únicas para la clasificación de datos, aunque sus principios aplicados deberían ser los mismos para todos ellos. Los datos que se clasifican como confidenciales deben mantener su confidencialidad en reposo, en proceso y en tránsito.
 
Además, los datos pueden ser estructurados o no estructurados. Los procesos típicos de clasificación para los datos estructurados que se encuentran en bases de datos y hojas de cálculo son de menor complejidad y se dedica menos tiempo a administrarlos, en comparación con los datos no estructurados, como documentos, código fuente y correos electrónicos.

> [AZURE.TIP] Para más información sobre las funcionalidades de Azure y los procedimientos recomendados para el cifrado de datos, lea [Azure Data Security and Encryption Best Practices](azure-security-data-encryption-best-practices.md) (Procedimientos recomendados de cifrado y seguridad de datos en Azure).

Por lo general, las organizaciones tendrán más datos no estructurados que estructurados. Con independencia de si los datos son estructurados o no, es importante que administre su confidencialidad. Cuando se implementa correctamente, la clasificación de datos ayuda a garantizar que los datos confidenciales o sensibles se administren con mayor supervisión que los recursos de datos que se consideran públicos o de distribución libre.

### Control del acceso a los datos 

Con frecuencia la autenticación y la autorización se confunden y no se comprende bien su función. En realidad, son bastante diferentes, como se muestra en la ilustración siguiente.

![Control y acceso a datos](./media/azure-security-data-classification/azure-security-data-classification-fig1.png)

### Autenticación 

La autenticación normalmente consta de dos partes como mínimo: un identificador o nombre de usuario para identificar a un usuario y un token, como una contraseña, para confirmar que la credencial de nombre de usuario es válida. El proceso no proporciona al usuario autenticado acceso a ningún elemento ni servicio, sino que comprueba que el usuario sea quien dice ser.

> [AZURE.TIP] [Azure Active Directory](../active-directory/active-directory-whatis.md) proporciona servicios de identidad basados en la nube que permiten autenticar y autorizar a los usuarios.

### Autorización
 
La autorización es el proceso de proporcionar a un usuario autenticado la capacidad de acceder a una aplicación, un conjunto de datos, un archivo de datos o algún otro objeto. Para asignar a los usuarios autenticados derechos para usar, modificar o eliminar elementos a los que tengan acceso, es preciso prestar atención a la clasificación de datos.

Para lograr una autorización correcta, se necesita implementar un mecanismo que valide las necesidades de usuarios individuales para acceder a archivos e información teniendo en cuenta una combinación del rol, la directiva de seguridad y la directiva de riesgo. Por ejemplo, quizá no todos los empleados necesiten acceso a determinadas aplicaciones de línea de negocio (LOB) y es probable que solo un pequeño subconjunto de los empleados deba acceder a los archivos de Recursos Humanos (RR. HH.). Sin embargo, para que las organizaciones puedan controlar quién tiene acceso a los datos, además de cuándo y cómo, se debe haber instaurado un sistema eficaz para autenticar a los usuarios.

> [AZURE.TIP] En Microsoft Azure, asegúrese de aprovechar el control de acceso basado en rol (RBAC) de Azure para conceder únicamente el nivel de acceso que los usuarios necesiten para realizar su trabajo. Lea [Uso de asignaciones de roles para administrar el acceso a los recursos de Azure Active Directory](../active-directory/role-based-access-control-configure.md) para más información.

### Roles y responsabilidades en la informática en la nube 

Aunque los proveedores de nube pueden ayudar a administrar los riesgos, los clientes deben asegurarse de que la administración y el cumplimiento de la clasificación de datos se implementen correctamente para proporcionar el nivel adecuado de servicios de administración de datos.
 
Las responsabilidades en cuanto a la clasificación de datos varían en función de qué modelo de servicio en la nube esté implantado, tal como se muestra en la ilustración siguiente. Los tres modelos principales de servicio en la nube son infraestructura como servicio (IaaS), plataforma como servicio (PaaS) y software como servicio (SaaS). La implementación de los mecanismos de clasificación de datos también varía en función de la dependencia del proveedor de la nube y las expectativas que se tengan respecto a él.

![Roles](./media/azure-security-data-classification/azure-security-data-classification-fig2.png)

Aunque el cliente sea responsable de clasificar sus propios datos, los proveedores de nube deberían comprometerse por escrito acerca de cómo van a proteger y mantener la privacidad de los datos del cliente almacenados en la nube.

- Los requisitos de los **proveedores de IaaS** se limitan a garantizar que el entorno virtual admita las funcionalidades de clasificación de datos y los requisitos de cumplimiento del cliente. Los proveedores de IaaS desempeñan un papel menos importante en la clasificación de datos porque solo deben asegurarse de que los datos del cliente satisfagan los requisitos de cumplimiento. Aun así, los proveedores deben asegurarse de que sus entornos virtuales cumplan los requisitos de la clasificación de datos, además de proteger sus centros de datos.
- Las responsabilidades de los **proveedores de PaaS** pueden ser variadas, ya que la plataforma se podría usar con un enfoque por niveles para proporcionar seguridad para una herramienta de clasificación. Los proveedores de PaaS pueden ser responsables de la autenticación y posiblemente de algunas reglas de autorización, y deben proporcionar funcionalidades de seguridad y clasificación de datos a su capa de aplicación. De forma muy similar a los proveedores de IaaS, los de PaaS deben asegurarse de que su plataforma cumpla los requisitos de clasificación de datos pertinentes.
- Con frecuencia, los **proveedores de SaaS** se considerarán parte de una cadena de autorización y tendrán que asegurarse de que los datos almacenados en la aplicación SaaS se puedan controlar en función del tipo de clasificación. Las aplicaciones de SaaS pueden usarse para las aplicaciones LOB, y por su propia naturaleza deben proporcionar los medios para autenticar y autorizar los datos que se utilizan y almacenan. 

## Proceso de clasificación 

Muchas organizaciones que comprenden la necesidad de la clasificación de datos y desean implementarla se enfrentan a un desafío básico: ¿por dónde empezar?

Una manera sencilla y eficaz de implementar la clasificación de datos es utilizar el modelo PLANEAR, HACER, COMPROBAR, ACTUAR de [MOF](https://technet.microsoft.com/solutionaccelerators/dd320379.aspx). En la siguiente ilustración, se muestran las tareas necesarias para implementar correctamente la clasificación de datos en este modelo.

1. **PLANEAR**. Identificar los recursos de datos y al administrador de recursos de datos para implementar el programa de clasificación; desarrollar los perfiles de protección. 
2. **HACER**. Una vez acordadas las directivas de clasificación de datos, implementar el programa y las tecnologías de cumplimiento según sea necesario para los datos confidenciales.  
3. **COMPROBAR**. Comprobar y validar los informes para asegurarse de que las herramientas y los métodos que se usen cumplan de forma eficaz las directivas de clasificación. 
4. **ACTUAR**. Revisar el estado del acceso a los datos, así como los archivos y los datos que necesitan revisión mediante un método de revisión y reclasificación para adoptar los cambios y solventar los nuevos riesgos.  

![Planear, hacer, comprobar, actuar](./media/azure-security-data-classification/azure-security-data-classification-fig3.png)
 
###Selección de un modelo terminológico apto para sus necesidades
 
Existen varios tipos de procesos para clasificar datos, entre ellos los manuales; los basados en la ubicación que clasifican los datos según la ubicación de un usuario o del sistema; los basados en aplicaciones, como la clasificación específica de las bases de datos; y automatizados, que se usan en diversas tecnologías y se describen en la sección "Protección de datos confidenciales" más adelante en este artículo.
 
En este artículo se presentan dos modelos terminológicos generalizados que se basan en los extendidos y respetados en el sector. Estos modelos terminológicos, que ofrecen tres niveles de confidencialidad para la clasificación, se muestran en la tabla siguiente.

> [AZURE.NOTE] Cuando clasifique un archivo o un recurso que combine datos que normalmente se clasificarían en diferentes niveles, la clasificación general se debe establecer según el máximo nivel presente. Por ejemplo, un archivo que contenga datos sensibles y restringidos se debe clasificar como restringido.

| **Confidencialidad** | **Modelo terminológico 1** | **Modelo terminológico 2** |
|--------------------|---------------------------|-------------------------|
| Alto | Confidencial | Restringido |
| Mediano | Solo para uso interno | Sensible |
| Bajo | Público | Sin restricciones |

#### Confidencial (restringida) 

Entre la información que se clasifica como confidencial o restringida, se incluyen datos cuya pérdida o puesta en peligro puede ser catastrófica para una o varias personas u organizaciones. Esta información se proporciona con frecuencia solo "cuando se necesita saber" y abarca:

- Datos personales, entre ellos información de identificación personal, como los números de documento nacional de identidad o del seguro social, números de pasaporte, números de tarjeta de crédito, números del permiso de conducción, historias clínicas y números identificativos de pólizas de seguro de salud.  
- Registros financieros, incluidos los números de cuentas financieras, como números de cuentas corrientes o de inversión. 
- Material de negocios, como documentos o datos exclusivos o propiedad intelectual específica.  
- Datos jurídicos, incluido el material que podría considerarse cubierto por el secreto profesional entre abogado y cliente. 
- Datos de autenticación, incluidas claves criptográficas privadas, pares de nombre de usuario y contraseña u otras secuencias de identificación, como archivos de claves biométricas privadas. 

Los datos que se clasifican como confidenciales poseen con frecuencia requisitos reglamentarios y de cumplimiento para el tratamiento de los datos.

#### Solo para uso interno (sensible)
 
La información que se clasifica como de confidencialidad media incluye archivos y datos que no acarrearían repercusiones graves para un individuo o una organización si se pierden o destruyen. Tal información podría incluir:

- Correos electrónicos, la mayoría de los cuales se pueden eliminar o distribuir sin causar una crisis (excepto los buzones o correos electrónicos de personas que se identifiquen en la clasificación confidencial).  
- Documentos y archivos que no contienen datos confidenciales.
 
Por lo general, esta clasificación engloba todo lo que no es confidencial. Esta clasificación puede abarcar la mayor parte de los datos empresariales, ya que la mayoría de los archivos que se administran o utilizan a diario pueden clasificarse como sensibles. A excepción de los datos que se hacen públicos o que son confidenciales, todos los datos dentro de una organización empresarial se pueden clasificar como sensibles de forma predeterminada.

#### Pública (sin restricciones)
 
La información clasificada como pública incluye archivos y datos que no son esenciales para las operaciones o las necesidades del negocio. Esta clasificación también puede aplicarse a los datos que se hayan comunicado deliberadamente al público para su uso, como material de marketing o anuncios de prensa. Además, esta clasificación puede incluir datos como mensajes de correo electrónico no deseado almacenados por un servicio de correo electrónico.

### Definición de la propiedad de los datos
 
Es importante establecer una cadena de custodia clara para la propiedad de todos los recursos de datos. En la tabla siguiente se identifican los diferentes roles de propiedad de datos para las tareas de clasificación de datos y sus respectivos derechos.

| **Rol** | **Crear** | **Modificar o eliminar** | **Delegar** | **Lectura** | **Archivar y restaurar** |
|-----------------|---------------|---------------------|---------------|-------------|-----------------------|
| Propietario | X | X | X | X | X |
| Administrador de recursos de datos | | | X | | |
| Administrador | | | | | X |
| Usuario* | | X | | X | |
****Un administrador de recursos de datos puede conceder a los usuarios derechos adicionales, por ejemplo para editar y eliminar.*

> [AZURE.NOTE] En esta tabla no se proporciona una lista exhaustiva de roles y derechos, sino solo una muestra representativa.

El **propietario del recurso de datos** es el creador original de los datos, quien puede delegar la propiedad y asignar un administrador de recursos de datos. Cuando se crea un archivo, el propietario debería ser capaz de asignar una clasificación, lo que significa que tiene la responsabilidad de comprender qué se debe clasificar como confidencial según las directivas de su organización. Todos los datos del propietario de recursos de datos se pueden clasificar de forma automática como Solo para uso interno (sensible) a menos que sea responsable de tener en propiedad o crear tipos de datos confidenciales (restringidos). Con frecuencia, el rol del propietario cambiará una vez que se clasifiquen los datos. Por ejemplo, el propietario puede crear una base de datos de información clasificada y ceder sus derechos al administrador de recursos de datos.

> [AZURE.NOTE] Los propietarios de recursos de datos suelen usar una combinación de servicios, dispositivos y medios, algunos de los cuales son personales y otros pertenecen a la organización. Una directiva clara de la organización puede ayudar a asegurarse de que el uso de dispositivos como equipos portátiles y dispositivos inteligentes cumpla las instrucciones de clasificación de datos.

El propietario de los recursos (o un delegado) asigna al **administrador de recursos de datos** para que administre los recursos según lo acordado con su propietario o de acuerdo con los requisitos de directiva pertinentes. Idealmente, el rol de administrador de recursos de datos se puede implementar en un sistema automatizado. El administrador de recursos de datos se asegura de que se proporcionen los controles de acceso necesarios y se responsabiliza de la administración y la protección de los recursos delegados en su cuidado. Entre las responsabilidades del administrador de recursos de datos, se pueden incluir las siguientes:

- Proteger el recurso según las instrucciones o lo acordado con su propietario 
- Garantizar que se cumplan de directivas de clasificación 
- Informar a los propietarios de los recursos sobre cualquier cambio en los controles o los procedimientos de protección acordados antes de que surtan efecto 
- Informar al propietario del recurso sobre los cambios en las responsabilidades del administrador de recursos de datos o la eliminación de estas 
- Un **administrador** representa a un usuario que es responsable de garantizar que se mantenga la integridad, pero no es ni propietario, ni administrador de recursos de datos ni usuario del recurso de datos. De hecho, muchos de los roles de administrador proporcionan servicios de administración del contenedor de datos sin tener acceso a ellos. El rol de administrador incluye la copia de seguridad y la restauración de los datos, el mantenimiento de registros de los recursos y la selección, adquisición y operación de los dispositivos y el almacenamiento que contienen los recursos. 
- El usuario del recurso puede ser cualquier persona a la que se conceda acceso a los datos o a un archivo. El propietario suele delegar la asignación de acceso en el administrador de recursos de datos.  

### Implementación
  
Las consideraciones sobre la administración se aplican a todas las metodologías de clasificación. Estas consideraciones deben incluir detalles sobre quién, qué, dónde, cuándo y por qué se usaría, se cambiaría o se eliminaría un recurso de datos o se accedería a él. Para llevar a cabo toda la administración de los recursos, se debe comprender cómo considera una organización sus riesgos, pero se puede aplicar una metodología simple tal como se define en el proceso de clasificación de datos. Otras consideraciones para la clasificación de datos son la introducción de nuevas aplicaciones y herramientas, y la administración de los cambios una vez implementado un método de clasificación.

### Reclasificación
 
La reclasificación o el cambio del estado de clasificación de un recurso de datos debe hacerse cuando un usuario o el sistema determine que ha cambiado el perfil de riesgo o la importancia del recurso de datos. Esta tarea es importante para garantizar que el estado de clasificación siga siendo válido y actual. Gran parte del contenido que no se clasifica manualmente se puede clasificar de forma automática o el propietario o el administrador de recursos de datos puede hacerlo en función del uso.

### Reclasificación manual de datos
 
Idealmente, esta tarea garantizará que se capturen y se auditen los detalles de un cambio. El motivo más probable para realizar la reclasificación manual es por motivos de confidencialidad, porque los registros se mantienen en papel o porque es necesario revisar datos que en un principio se clasificaron incorrectamente. Como este documento trata sobre la clasificación de los datos y su movimiento a la nube, las tareas de reclasificación manual requerirían atención individualizada y lo ideal sería llevar a cabo una revisión de la administración de riesgos para satisfacer los requisitos de clasificación. Por lo general, este tipo de trabajo tendría como referencia la directiva de la organización en cuanto a qué se debe clasificar y el estado de clasificación predeterminado (todos los datos y archivos son sensibles, pero no confidenciales). Además, se aceptarían excepciones para los datos de alto riesgo.

### Reclasificación automática de datos
 
La reclasificación automática de datos usa la misma regla general que la clasificación manual. La excepción es que las soluciones automatizadas pueden garantizar que las reglas se sigan y se apliquen según sea necesario. La clasificación de datos puede realizarse como parte de una directiva de cumplimiento de clasificación de datos, que se puede aplicar cuando los datos estén almacenados, en uso y en tránsito mediante tecnología de autorización.

- Basada en la aplicación. El uso de determinadas aplicaciones establece de forma predeterminada un nivel de clasificación. Por ejemplo, los datos procedentes de software de administración de las relaciones con el cliente (CRM), de Recursos Humanos o de herramientas de administración de historias clínicas son confidenciales de forma predeterminada. 
- Basada en la ubicación. La ubicación de los datos puede ayudar a identificar su nivel de confidencialidad. Por ejemplo, es más probable que los datos almacenados por el departamento financiero o de Recursos Humanos sean de naturaleza confidencial.  
 
### Retención, recuperación y eliminación de datos 

La recuperación y la eliminación de datos, al igual que su reclasificación, es un aspecto esencial de la administración de los recursos de datos. Los principios para la recuperación y la eliminación de datos se definirían en una directiva de retención de datos y se aplicarían de la misma manera que la reclasificación de datos; esta tarea sería responsabilidad de los roles de administrador y administrador de recursos de datos en colaboración.

La falta de una directiva de retención de datos podría suponer la pérdida de datos o el incumplimiento de los requisitos reglamentarios o de descubrimiento legal. La mayoría de las organizaciones que carecen de una directiva de retención de datos claramente definida tienden a usar una directiva de retención que "mantiene todo" de forma predeterminada. Sin embargo, este tipo de directiva de retención conlleva riesgos adicionales en los escenarios de servicios en la nube.

Por ejemplo, una directiva de retención de datos para proveedores de servicios en la nube puede considerarse que dura tanto como la suscripción (mientras se pague el servicio, se conservan los datos). Es posible que un acuerdo de pago por retención de este tipo no tenga en cuenta las directivas de retención corporativas o reglamentarias. La definición de una directiva para datos confidenciales puede garantizar que los datos se almacenen y eliminen de acuerdo con los procedimientos recomendados. Además, se puede crear una directiva de archivado para establecer formalmente qué datos se entiende que deben eliminarse y cuándo.

En la directiva de retención de datos se deberían considerar los requisitos reglamentarios y de cumplimiento, así como los de retención legal corporativa. Los datos clasificados podrían suscitar preguntas sobre la duración de la retención y las excepciones para los datos que se hayan almacenado con un proveedor. Estas preguntas son más probables para aquellos datos que no se hayan clasificado correctamente.

> [AZURE.TIP] Para más información sobre las directivas de retención de datos de Azure y mucho más, lea el [Contrato Microsoft Online Subscription](https://azure.microsoft.com/support/legal/subscription-agreement/).

## Protección de datos confidenciales
  
Una vez clasificados los datos, la búsqueda y la implementación de maneras de proteger los datos confidenciales se convierten en parte integral de cualquier estrategia de implementación de la protección de datos. Para proteger los datos confidenciales, se debe prestar especial atención a cómo se almacenan los datos y cómo se transmiten en arquitecturas convencionales, así como en la nube.

En esta sección se proporciona información básica sobre algunas tecnologías que pueden automatizar las tareas de cumplimiento para ayudar a proteger los datos que se hayan clasificado como confidenciales.
 
Como se muestra en la siguiente ilustración, estas tecnologías se pueden implementar como soluciones locales o basadas en la nube, o de un modo híbrido, con algunas implementadas de forma local y otras en la nube. (Algunas tecnologías, como el cifrado y la administración de derechos, también se extienden a los dispositivos de los usuarios).

![Tecnologías](./media/azure-security-data-classification/azure-security-data-classification-fig4.png)

### Software de administración de derechos  

Una solución para evitar la pérdida de datos es un software de administración de derechos. A diferencia de los enfoques que intentan interrumpir el flujo de información en los puntos de salida de una organización, el software de administración de derechos funciona en los niveles profundos de las tecnologías de almacenamiento de datos. Los documentos se cifran y, para controlar quién puede descifrarlos, se usan controles de acceso que se definen en una solución de control de autenticación, como por ejemplo un servicio de directorio.

> [AZURE.TIP] Puede usar Azure Rights Management (Azure RMS) como solución de protección de información para proteger los datos en escenarios diferentes. Lea [What is Azure Rights Management?](https://docs.microsoft.com/rights-management/understand-explore/what-is-azure-rms) (¿Qué es Azure Rights Management?) para más información acerca de esta solución de Azure.

Algunas de las ventajas del software de administración de derechos son:

- Información sensible protegida. Los usuarios pueden proteger sus datos directamente mediante aplicaciones habilitadas para la administración de derechos. No se requiere ningún paso adicional: la experiencia de protección de datos es uniforme cuando se crean documentos, se envían correos electrónicos o se publican datos. 
- La protección acompaña a los datos. Los clientes permanecen en control de quién tiene acceso a sus datos, ya sea en la nube, en la infraestructura de TI existente o en el escritorio del usuario. Las organizaciones pueden elegir cifrar sus datos y restringir el acceso según sus requisitos empresariales. 
- Directivas de protección de información predeterminadas. Los administradores y los usuarios pueden utilizar directivas estándar para muchos escenarios empresariales habituales, como "Información confidencial de la compañía: solo lectura" y "No reenviar". Se admite un amplio conjunto de derechos de uso, como leer, copiar, imprimir, guardar, editar y reenviar, para aportar flexibilidad a la definición de derechos de uso personalizados. 

> [AZURE.TIP] Puede proteger los datos en Almacenamiento de Azure mediante el [Cifrado del servicio Almacenamiento de Azure](../storage/storage-service-encryption.md) para datos en reposo. También puede usar [Cifrado de discos de Azure](azure-security-disk-encryption.md) para ayudar a proteger los datos contenidos en discos virtuales usados para máquinas virtuales de Azure.

### Puertas de enlace de cifrado

Las puertas de enlace de cifrado operan en sus propios niveles para proporcionar servicios de cifrado mediante la redirección de todo el acceso a datos en la nube. Este enfoque no debe confundirse con el de una red privada virtual (VPN). Las puertas de enlace de cifrado están diseñadas para proporcionar una capa transparente a soluciones basadas en la nube.

Las puertas de enlace de cifrado pueden proporcionar un medio para administrar y proteger los datos que se hayan clasificado como confidenciales, ya que los cifran tanto en tránsito como en reposo.
 
Las puertas de enlace de cifrado se insertan en el flujo de datos entre los dispositivos de los usuarios y los centros de datos de aplicaciones para proporcionar servicios de cifrado y descifrado. Estas soluciones, al igual que las VPN, son en su mayor parte soluciones locales. Se diseñan para proporcionar a un tercero control sobre las claves de cifrado, lo que ayuda a reducir el riesgo de asignar la administración tanto de los datos como de las claves a un único proveedor. Estas soluciones están diseñadas, como el cifrado, para funcionar sin problemas y de forma transparente entre los usuarios y el servicio.

> [AZURE.TIP] Puede usar Azure ExpressRoute para ampliar sus redes locales a la nube de Microsoft por medio de una conexión privada dedicada. Lea [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md) para más información sobre esta funcionalidad. Otra opción para la conectividad entre entornos, entre su red local y Azure es una [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

### Prevención de la pérdida de datos 
La pérdida de datos (también conocida como fuga de datos) es un aspecto importante, y la prevención de la pérdida externa de datos desde dentro de la organización, ya sea accidental o malintencionada, es esencial para muchas organizaciones.
 
Las tecnologías de prevención de pérdida de datos (DLP) pueden ayudar a garantizar que soluciones tales como los servicios de correo electrónico no transmitan datos que se hayan clasificado como confidenciales. Las organizaciones pueden aprovechar los características de DLP en los productos existentes para evitar la pérdida de datos. Estas características utilizan directivas que se pueden crear fácilmente desde cero o mediante una plantilla suministrada por el proveedor de software.
 
Las tecnologías de DLP pueden realizar un análisis profundo del contenido por medio de coincidencias con palabras clave y diccionarios, la evaluación de expresiones regulares y otros exámenes del contenido para detectar aquel que infrinja las directivas de DLP de la organización. Por ejemplo, DLP puede ayudar a evitar la pérdida de los siguientes tipos de datos:

- Números de documento nacional de identidad o del seguro social 
- Información bancaria 
- Números de tarjeta de crédito  
- Direcciones IP 

Algunas tecnologías de DLP también proporcionan la capacidad de invalidar la configuración de DLP (por ejemplo, si una organización necesita transmitir información que contiene números del seguro social a un procesador de nóminas). Además, es posible configurar la DLP para que los usuarios reciban notificaciones antes de que intenten siquiera enviar información sensible que no se debe transmitir.

> [AZURE.TIP] Puede usar las funcionalidades de DLP en Office 365 para proteger los documentos. Lea [Office 365 compliance controls: Data Loss Prevention](https://blogs.office.com/2013/10/28/office-365-compliance-controls-data-loss-prevention/) (Controles de cumplimiento en Office 365: Prevención de pérdida de datos) para más información.

## Consulte también

- [Azure Data Security and Encryption Best Practices](azure-security-data-encryption-best-practices.md) (Procedimientos recomendados de cifrado y seguridad de datos en Azure)
- [Procedimientos recomendados para la administración de identidades y la seguridad del control de acceso en Azure](azure-security-identity-management-best-practices.md)
- [Blog del equipo de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/)
- [Centro de respuesta de seguridad de Microsoft](https://technet.microsoft.com/library/dn440717.aspx)

<!---HONumber=AcomDC_0525_2016-->