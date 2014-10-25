# Guía de seguridad de Azure

## Descripción breve

Al desarrollar aplicaciones para Azure, la identidad y el
acceso constituyen los principales problemas de seguridad que hay que tener presentes.
En este tema se explican los principales problemas de seguridad relacionados con la identidad y
el acceso en la nube y cómo se pueden proteger de la mejor manera sus aplicaciones en la nube.

## Información general

La seguridad de una aplicación depende directamente de su superficie. Cuanta más superficie
exponga la aplicación, mayores serán los problemas de seguridad. Por
ejemplo, una aplicación que se ejecuta como un proceso por lotes desatendido se expone
menos, desde una perspectiva de seguridad, que un sitio web disponible públicamente.

Cuando se traslada a la nube, gana en tranquilidad en lo relativo a
infraestructura y redes, ya que ahora se administran en centros de datos
con prácticas, herramientas y tecnología de seguridad de primer nivel. Por otra parte,
la nube expone intrínsecamente más superficie de su aplicación, cuyas vulnerabilidades de seguridad
podrían aprovechar los atacantes. Esto es
debido a que muchas tecnologías y servicios en la nube se exponen como extremos
y no como componentes en memoria. Por red se puede obtener acceso al
almacenamiento de Azure, al bus de servicio, a Base de datos SQL (anteriormente SQL Azure) y a
muchos otros servicios a través de sus extremos.

En las aplicaciones en nube, la responsabilidad de diseñar, desarrollar y mantener las
aplicaciones en nube con los máximos estándares de seguridad para mantener a raya a los
atacantes recae sobre los desarrolladores.
Considere el siguiente diagrama (extraído de [Azure Security
Notes PDF][] de J.D. Meier): observe cómo la parte de infraestructura la está abordando el proveedor de nube
(en nuestro caso, Azure), dejando más trabajo de
seguridad a los desarrolladores de aplicaciones:

![Protección de la aplicación][]

La buena noticia es que todas las prácticas, principios y técnicas de desarrollo
de seguridad con las que está familiarizado se
siguen aplicando al desarrollar aplicaciones en la nube. Considere las áreas clave siguientes que deben
abordarse:

-   Todas las entradas se validan por tipo, longitud, rango y patrones
    de cadena para evitar ataques por inserción, y todos los datos que refleja la aplicación
    se inmunizan correctamente.
-   Deben protegerse los datos confidenciales, tanto almacenados como
    transmitidos por la red, para mitigar la divulgación de información y las amenazas por alteración de datos.
-   La auditoría y el registro deben implementarse correctamente para mitigar las
    amenazas sin repudio.
-   La autenticación y la autorización deben implementarse mediante los
    mecanismos contrastados que ofrezca la plataforma para evitar las amenazas de
    suplantación de identidad y elevación de privilegios.

Para obtener una lista completa de amenazas, ataques, vulnerabilidades y
contramedidas, consulte [Cheat Sheet: Web
Application Security Frame][] y [Security Guidance for Applications Index][].

En la nube, los mecanismos de autenticación y de control de acceso son muy
diferentes de los que están disponibles en las aplicaciones locales. Hay muchas
más opciones de autenticación y acceso ofrecidas por las
aplicaciones en nube que pueden llevar a confusión y, como resultado, a implementaciones
defectuosas. Sin contar con que se introduce más confusión al definir lo que significa una
aplicación en nube. Por ejemplo, la aplicación se puede implementar en la nube, aunque
su mecanismo de autenticación lo proporcione Active Directory. Por otro lado,
la aplicación se puede implementar en local pero con mecanismos de
autenticación en la nube (por ejemplo, por el Control de acceso de
Azure Active Directory
(conocido anteriormente como Servicio de control de acceso o ACS)).

## Amenazas, vulnerabilidades y ataques

Una amenaza es un posible resultado no deseado que se desea evitar,
como la divulgación de información confidencial o un servicio que deja de estar disponible.
Es una práctica habitual clasificar las amenazas con el acrónimo "STRIDE", por sus iniciales en inglés:

-   Suplantación o robo de identidad
-   Manipulación de datos
-   Repudio de acciones
-   Divulgación de información
-   Denegación de servicio
-   Elevación de privilegios

Las vulnerabilidades son errores que nosotros, como desarrolladores, introducimos en el código
y que permiten a los atacantes explotar la aplicación. Por ejemplo, el envío de
datos confidenciales en texto no cifrado puede dar lugar a una amenaza de divulgación de información
por un ataque de rastreo del tráfico.

El objetivo de los ataques es aprovechar las vulnerabilidades para causar daño a
una aplicación. Por ejemplo, un ataque de scripts de sitios, o XSS, es un
ataque que explota una salida no inmunizada. Otro ejemplo es la
interceptación en la red para capturar credenciales enviadas sin cifrar. Estos
ataques pueden conducir a una amenaza de suplantación de identidad. Dicho simplemente,
hay que considerar las amenazas, vulnerabilidades y ataques como algo perjudicial.
Considere los diagramas siguientes como una vista panorámica
de los aspectos negativos relacionados con una aplicación web implementada en Azure (extraído de [Azure Security Notes PDF][Azure Security
Notes PDF]
de J.D. Meier):

![Amenazas, vulnerabilidades y ataques][]

Usted, como desarrollador, tiene control sobre las vulnerabilidades. Cuantas menos
vulnerabilidades se introduzcan, menos opciones tendrá el
atacante para aprovecharlas.

Las vulnerabilidades relacionadas con la identidad y el acceso le dejan expuesto a todas las
amenazas descritas en el modelo STRIDE. Por ejemplo, un mecanismo de autenticación incorrectamente
implementado puede conducir a una suplantación de identidad y, como resultado de ello, a la divulgación de
información, manipulación de datos, operaciones de privilegios elevados o,
incluso, al cierre total del servicio. Tenga en cuenta las siguientes
cuestiones que pueden indicar posibles vulnerabilidades en su implementación de identidades
y acceso en las aplicaciones en la nube:

-   ¿Está enviando credenciales sin cifrar por Internet a sus
    servicios de Azure?
-   ¿Almacena las credenciales de los servicios de Azure sin cifrar?
-   ¿Sus credenciales de los servicios de Azure siguen directivas de
    contraseñas seguras?
-   ¿Confía en Azure para comprobar las credenciales en lugar de
    utilizar mecanismos de verificación personalizados?
-   ¿Limita las sesiones de autenticación de servicios de Azure o la duración
    de los tokens a un marco temporal razonable?
-   ¿Comprueba los permisos para cada punto de entrada de Azure de su
    aplicación distribuida en la nube?
-   ¿Es posible controlar la seguridad del sistema en caso de que se produzca un error sin exponer
    información confidencial o sin permitir el acceso ilimitado?

## Contramedidas

La mejor contramedida frente un ataque es utilizar los mecanismos de identidades y
acceso ofrecidos por la plataforma en lugar de implementar los suyos
propios. Considere las siguientes tecnologías de identidades y acceso importantes:

**Windows Identity Foundation (WIF).** WIF es una biblioteca en tiempo de ejecución .NET
incluida en .NET Framework 4.5 (también está disponible como una descarga
independiente para .NET 3.5/4.0). WIF se encarga del trabajo duro de
controlar protocolos como WS-Federation y WS-Trust y del control de tokens, como el lenguaje de marcado
de aserción de seguridad (SAML), por tanto no es preciso escribir código muy complejo
relacionado con la seguridad de su aplicación. Los recursos
siguientes proporcionan información detallada sobre WIF:

-   [Windows Identity Foundation 4.5 Samples][] en Muestras de código para desarrollador de MSDN.
-   [Windows Identity Foundation 4.5 tools for Visual Studio 11 Beta][] en
    Muestras de código para desarrollador de MSDN.
-   [Windows Identity Foundation runtime (.Net 3.5/4.0)][] en MSDN.
-   [Muestras de código de Windows Identity Foundation 3.5/4.0 y plantillas de Visual Studio
    2008/2010][] en MSDN.

**Servicio de control de acceso de Azure AD (anteriormente conocido como ACS)**.
El servicio de control de acceso de Azure AD es un servicio en la nube que
proporciona un servicio de token de seguridad (STS) y permite la federación con distintos
proveedores de identidades (IdP) como un Active Directory corporativo o IdP
de Internet, como proveedores de identidades de Windows Live ID/cuenta de
Microsoft, Facebook, Google, Yahoo! y Open ID 2.0. Los recursos siguientes ofrecen información
detallada sobre el servicio de control de acceso de Azure AD:

-   [Access Control Service 2.0][]
-   [Scenarios and Solutions Using ACS][]
-   [Procedimientos de ACS][]
-   [A Guide to Claims-Based Identity and Access Control][]
-   [Identity Developer Training Kit][]
-   [MSDN-hosted Identity Developer Training Course][]

**Servicios de federación de Active Directory (AD FS).**Servicios de federación de
Active Directory (AD FS) 2.0 ofrece soporte para las soluciones de
identidades para notificaciones que implican la
tecnología de Windows Server y Active Directory. AD FS 2.0 admite los protocolos WS-Trust, WS-Federation y
SAML. Los recursos siguientes ofrecen información detallada sobre AD
FS:

-   [AD FS 2.0 Content Map][]
-   [Web SSO Design][]
-   [Federated Web SSO Design][]

**Firmas de acceso compartido de Azure.** Las firmas de acceso compartido
le permiten ajustar el acceso a un recurso de contenedor o blob. Los
recursos siguientes proporcionan información detallada sobre las firmas
de acceso compartido.

-   [Administrar el acceso a los recursos de almacenamiento de Azure][]
-   [New Storage Feature: Shared Access Signatures][]
-   [Shared Access Signatures Are Easy These Days][]

## Mapa de escenarios

Esta sección ofrece un breve resumen sobre los escenarios clave que se cubren en este tema.
Úsela como un mapa para determinar la solución de identidades adecuada para su
aplicación.

-   <b>Aplicación de formularios Web Forms ASP.NET con autenticación federada.</b>En
    este escenario, controlará el acceso a su aplicación de formularios
    Web Forms ASP.NET Web Forms utilizando la identidad de Internet como Live ID/cuenta de Microsoft
    o una identidad corporativa administrada en Windows Server Active Directory.
-   **Servicio WCF (SOAP) con autenticación federada.** En este
    escenario, controlará el acceso a su servicio WCF (SOAP) mediante identidades de servicio administradas
    por el servicio de control de acceso de Azure AD.
-   **Servicio WCF (SOAP) con autenticación federada, identidades en
    Active Directory.** En este escenario, controlará el acceso a su servicio web de WCF
    (SOAP) utilizando identidades administradas por Windows
    Server Active Directory corporativo.
-   **Servicio WCF (REST) con autenticación federada.** En este
    escenario, controlará el acceso a su servicio WCF (REST) mediante identidades
    de servicio administradas por el servicio de control de acceso de Azure AD.
-   **Servicio WCF (REST) con Live ID/cuenta de Microsoft, Facebook,
    Google, Yahoo!, Open ID.** En este escenario controlará el acceso a su
    servicio WCF (REST) mediante la identidad de Internet como Live ID /
    cuenta de Microsoft.
-   <b>Aplicación web ASP.NET para el servicio REST WCF mediante el token SWT compartido.</b> En este
    escenario dispondrá de una aplicación distribuida con la aplicación web
    ASP.NET front-end y el servicio REST auxiliar y desea que el contexto
    del usuario final fluya por los niveles físicos.
-   **Autorización de control de acceso basado en roles (RBAC) en
    aplicaciones y servicios para notificaciones.** En este escenario querrá implementar
    la lógica de autorización en su aplicación basada en roles.
-   **Autorización basada en notificaciones en aplicaciones y
    servicios para notificaciones.** En este escenario querrá implementar la lógica de autorización
    en su aplicación basada en reglas de autorización complejas.
-   **Escenarios de identidades y acceso del servicio de almacenamiento de Azure.** En este
    escenario tendrá que compartir de forma segura el acceso a los blobs y
    contenedores de almacenamiento de Azure.
-   **Escenarios de identidades y accesos de Base de datos SQL de Azure.**
    La base de datos SQL únicamente admite la autenticación de SQL Server. No se admite
    la autenticación Windows (seguridad integrada). Los usuarios deben
    proporcionar credenciales (nombre de usuario y contraseña) cada vez que se conectan a una
    base de datos SQL.
-   **Escenarios de identidades y acceso del bus de servicio de Azure.** En este
    escenario tendrá que obtener acceso de forma segura a las colas del bus de servicio de Azure.
-   **Escenarios de identidades y acceso de la caché en memoria.** En este escenario tendrá que
    obtener acceso de forma segura a los datos administrados por la caché en memoria.
-   **Escenarios de identidades y acceso de Azure Marketplace.** En este
    escenario tendrá que obtener acceso de forma segura a los conjuntos de datos de
    Azure Marketplace.

## Escenarios de identidades y acceso de Azure

En esta sección se describen los escenarios comunes de identidades y acceso para las distintas
arquitecturas de aplicaciones. Utilice el mapa de escenarios para una rápida
orientación.

### Aplicación de formularios Web Forms ASP.NET con autenticación federada

En este escenario de arquitectura de aplicaciones, la aplicación web se puede
implementar en Azure o de manera local. La aplicación requiere que
sus usuarios se autentiquen mediante el Active Directory corporativo o por
los proveedores de identidades de Internet.

Para resolver este escenario, utilice el servicio de control de acceso de Azure AD y Windows
Identity Foundation.

![Control de acceso de Azure AD][]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Crear mi primera aplicación ASP.NET para notificaciones mediante ACS][]
-   [Eliminación de Hospedar páginas de inicio de sesión en su aplicación web de ASP.NET][]
-   [Eliminación de Implementar la autorización de notificaciones en una aplicación ASP.NET para notificaciones mediante WIF y ACS][]
-   [Eliminación de Implementar control de acceso basado en roles (RBAC) en una aplicación ASP.NET compatible con notificaciones usando WIF y ACS][]
-   [Eliminación de Configurar la confianza entre ACS y las aplicaciones web de ASP.NET mediante certificados X.509][]
-   [Code Sample: Formularios simples ASP.NET][]

### Servicio WCF (SOAP) con identidad de servicio

En este escenario de arquitectura de aplicaciones se puede
implementar el servicio WCF (SOAP) en Azure o en local. Una aplicación web o incluso otro
servicio web puede tener acceso al servicio como servicio
auxiliar. Es preciso controlar el acceso a este servicio mediante la identidad específica de la
aplicación. Suponga que es el tipo de cuenta del grupo de aplicaciones
que ha usado en IIS; aunque la tecnología sea diferente, los
métodos son similares en que se obtiene acceso al servicio usando una cuenta de ámbito frente a la cuenta de
usuario final.

Utilice la característica de identidad de servicio en el servicio de control de acceso de Azure AD.
Es similar a la cuenta del grupo de aplicaciones de IIS que se estaba utilizando al
implementar las aplicaciones en Windows Server e IIS. Configure el servicio
de control de acceso de Azure AD para enviar tokens de SAML que se tratará por WIF en
el servicio WCF (SOAP).

![Servicio WCF (SOAP)][]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Agregar identidades de servicio con un certificado X.509, una contraseña o una clave simétrica][]
-   [Eliminación de Autenticarse con un certificado de cliente en un servicio WCF protegido con ACS][]
-   [Eliminación de Autenticar con nombre de usuario y contraseña a un servicio WCF protegido con ACS][]
-   [Code Sample: Autenticación de certificados WCF][]
-   [Code Sample: Autenticación de nombre de usuario de WCF][]

### Servicio WCF (SOAP) con autenticación federada, identidades en Active Directory

En este escenario de arquitectura de aplicaciones se puede
implementar el servicio WCF (SOAP) en Azure o en local. Necesita controlar el acceso a
este servicio mediante una identidad administrada por un Active Directory (AD) de
Windows Server corporativo.

Utilice el servicio de control de acceso de Azure AD configurado por federación con los servicios de federación de
Active Directory de Windows Server. En este caso no tiene que configurar la identidad
de servicio con el servicio de control de acceso de Azure AD. El agente que debe
tener acceso al servicio WCF (SOAP) proporciona credenciales a AD FS y, si la autenticación es
correcta, se envía el token mediante AD FS. El token se envía
entonces al servicio de control de acceso de Azure AD y se vuelve a enviar
al agente. El agente utiliza el token para enviar la solicitud al servicio
WCF (SOAP).

![Servicio WCF (SOAP) con Active Directory][]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Agregar identidades de servicio con un certificado X.509, una contraseña o una clave simétrica][]
-   [Eliminación de Configurar AD FS 2.0 como proveedor de identidades][]
-   [Eliminación de Usar el servicio de administración para configurar AD FS 2.0 como proveedor de identidades empresariales][]
-   [Code Sample: Autenticación federada WCF con AD FS 2.0][]

### Servicio WCF (REST) con identidades de servicio

En este escenario se puede implementar el servicio WCF (REST) en
Azure o en local. Una aplicación web o incluso otro servicio web
puede tener acceso al servicio como servicio auxiliar. Debe controlar el acceso al servicio
mediante una identidad específica de la aplicación.
Suponga que es el tipo de cuenta del grupo de aplicaciones que ha usado en IIS; aunque la
tecnología sea diferente, los métodos son similares en que se obtiene acceso al servicio usando una cuenta del ámbito
de la aplicación y no con la cuenta de usuario final.

Utilice la característica de identidad de servicio en el servicio de control de acceso de Azure AD.
Configure el servicio de control de acceso de Azure AD para enviar tokens de web
simples (SWT). Para tratar el token SWT en el servicio REST, puede implementar un
controlador de tokens personalizado y
conectarlo al proceso WIF, o bien puede
analizarlo "manualmente" sin utilizar la infraestructura WIF.

Considere el diagrama siguiente (WIF es opcional):

![Servicio REST][]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Configurar la confianza entre el servicio WCF y ACS mediante claves simétricas][]
-   [Eliminación de Autenticar el servicio WCF de REST implementado en Azure mediante ACS][]
-   [Code Sample: ASP.NET Web Service][]
-   [Code Sample: Windows Phone 7 Application][Code Sample: ASP.NET Web Service]
-   [REST WCF With SWT Token Issued By Azure Access Control Service (ACS)][]

### Servicio WCF (REST) con Live ID/cuenta de Microsoft, Facebook, Google, Yahoo!, Open ID

En este escenario se puede implementar el servicio WCF (REST) en
Azure o en local. Tiene que controlar el acceso a este servicio mediante una identidad de Internet
pública, como Live ID/cuenta de Microsoft o Facebook.

Utilice el servicio de control de acceso de Azure AD para enviar tokens de SWT. Para tratar el token
SWT en el servicio REST, puede implementar un
controlador de tokens personalizado y conectarlo al proceso WIF, o bien puede analizarlo "manualmente"
sin utilizar la infraestructura WIF.

Es importante observar que, para implementar este escenario, la aplicación
tiene que utilizar el control del explorador web para recopilar las
credenciales de los usuarios finales. Esta circunstancia lo hace inadecuado para aquellos escenarios en los que se obtiene acceso al servicio
REST desde una aplicación web ASP.NET. Solo es adecuado en aquellos
escenarios en los que se obtiene acceso al servicio REST mediante la aplicación
cliente del usuario, como una aplicación Windows Phone 7 o un
cliente de escritorio enriquecido. El motivo principal para asumir el control de explorador web es que las identidades de
Internet no admiten de forma nativa los escenarios de perfiles activos (escenarios de
servicios web). Las identidades de Internet admiten principalmente escenarios
de perfiles pasivos (aplicaciones web) que dependen de los redireccionamientos del explorador: por este motivo es por lo que resulta práctico
el control de explorador web.

Considere el diagrama siguiente (WIF es opcional y, por tanto, no se muestra):

![WIF es opcional][]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Authenticate to a REST WCF Service Deployed to Azure Using ACS][Eliminación de Autenticar el servicio WCF de REST implementado en Azure mediante ACS]
-   [Eliminación de Configurar Google como proveedor de identidades][]
-   [Eliminación de Configurar Facebook como proveedor de identidades][]
-   [Eliminación de Configurar Yahoo! como proveedor de identidades][]
-   [Code Sample: Windows Phone 7 Application][Code Sample: ASP.NET Web Service]
-   [REST WCF With SWT Token Issued By Azure Access Control Service (ACS)][]

### Aplicación web ASP.NET para el servicio REST WCF mediante el token SWT compartido

En este escenario dispondrá de una aplicación distribuida con la aplicación web
ASP.NET front-end y el servicio REST auxiliar y desea mantener el
contexto del usuario final por los niveles físicos. A veces es necesario
al implementar la lógica o el registro de autorización en función de la identidad
del usuario final en el servicio REST auxiliar.

Configure el servicio de control de acceso de Azure AD para enviar un token de SWT. El token de
SWT se envía a la aplicación web front-end de ASP.NET y, a continuación, se comparte con
el servicio REST auxiliar. En este caso, solo hay un usuario de confianza
configurado en el servicio de control de acceso de Azure AD. Sin embargo, hay algunas
reservas al respecto:

-   Como WIF no proporciona de serie un controlador de tokens de SWT,
    debe implementar uno personalizado para utilizarse con la aplicación web
    ASP.NET. Debe confiar en que WIF admita el protocolo WS-Federation que
    depende de las redirecciones del explorador
    en lugar de implementarlo usted mismo.
-   Al implementar un controlador de tokens personalizado de SWT,
    asegúrese de que se está abordando el token de arranque para comprobar que se retiene.
    En caso contrario, no podrá
    compartirlo ni enviarlo al servicio REST auxiliar.
-   No tiene que utilizar WIF en un servicio REST, sino que puede analizar
    el token "manualmente" ya que no hay necesidad de tratar las redirecciones en
    este caso.

![Aplicación web ASP.NET][]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Configurar Google como proveedor de identidades][]
-   [Eliminación de Configurar Facebook como proveedor de identidades][]
-   [Eliminación de Configurar Yahoo! como proveedor de identidades][]
-   [ASP.NET Web App To REST WCF Service Delegation Using Shared SWT Token][]

### Control de acceso basado en roles (RBAC) en aplicaciones y servicios para notificaciones

En este escenario tendrá que implementar la autorización en su
aplicación o servicio web en función de los roles de usuario: el usuario con los roles requeridos obtendrá
acceso y a aquellos que no los tengan se les denegará. En pocas palabras,
la aplicación tiene que responder a esta simple pregunta: ¿tiene el usuario el rol
X?

Hay varias formas de resolver este escenario. Puede utilizar el servicio de control de acceso de
Azure AD,
administrador de autenticación de notificaciones de WIF, asignación de samlSecurityTokenRequirement o administrador de roles de cliente.

WIF se utiliza en todos los casos. WIF admite el método
IPrincipal.IsInRole("MyRole"). En la mayoría de los casos, lo fundamental es asegurarse de que hay una
notificación de tipo de rol con la URI
<http://schemas.microsoft.com/ws/2008/06/identity/claims/role> en el
token, para que WIF pueda comprobar correctamente la pertenencia al rol cuando
se llama al método IsInRole.

**Servicio de control de acceso de Azure AD**. En esta implementación
se utiliza el motor de reglas de transformación de notificaciones del servicio de control de acceso de Azure AD. Mediante
las reglas del motor de reglas de transformación de notificaciones puede
transformar cualquier notificación entrante en una notificación de tipo de rol de tal forma que, cuando el token
llega a la aplicación o a un servicio WIF, pueda analizar esta notificación de rol para asegurarse de que la llamada al método
IsInRole es correcta.

![][]

**ClaimsAuthenticationManager de WIF**. En esta implementación se utiliza
ClaimsAuthenticationManager como punto de extensibilidad de WIF. Con este
método transformará cualquier notificación entrante arbitraria en un
tipo de notificación de rol en la aplicación. La complejidad de la transformación solo está
limitada por el código que se escriba.

![][1]

**Asignación de samlSecurityTokenRequriement**. En esta implementación se utiliza la
configuración samlSecurityTokenRequirement en web.config para indicar a WIF qué
tipos de notificación se comportan como si fuera tipos de notificación de rol. Por ejemplo,
si el token lleva una notificación de tipo de grupo, puede asignarla a un tipo de notificación
de rol. Con este método solo pueden conseguir asignaciones simples.

![][2]

**RoleManager personalizado.** En esta implementación implementa un RoleManager
personalizado. WIF se utiliza para inspeccionar las notificaciones entrantes al implementar
los métodos de la interfaz de RoleManager personalizados, como GetAllRoles().

![][3]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Implementar control de acceso basado en roles (RBAC) en una aplicación ASP.NET compatible con notificaciones usando WIF y ACS][]
-   [Eliminación de Implementar la lógica de transformación de tokens mediante reglas][]
-   [Authorization With RoleManager For Claims Aware (WIF) ASP.NET Web Applications][]
-   Ejemplo de código: Uso de notificaciones en IsInRole en [Windows Identity Foundation SDK][]

### Autorización basada en notificaciones en aplicaciones y servicios para notificaciones

En este escenario tiene que implementar una lógica de autorización compleja en
la aplicación o el servicio web y el método IsInRole() no es
adecuado para sus necesidades de autorización. Si el método de autorización
depende de los roles, considere en este caso implementar el control de acceso basado en
roles descrito en la sección anterior.

Utilice ClaimsAuthorizationManager como punto de extensibilidad de WIF.
ClaimsAuthorizationManager permite las llamadas de comprobación
del acceso externo para que el código de la aplicación se vea más despejado y se
pueda mantener mejor que cuando se implementan las comprobaciones de acceso en el código de la aplicación.

![][4]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Implementar la lógica de transformación de tokens mediante reglas][]
-   [Eliminación de Implementar la autorización de notificaciones en una aplicación ASP.NET para notificaciones mediante WIF y ACS][Eliminación de Implementar la autorización de notificaciones en una aplicación ASP.NET para notificaciones mediante WIF y ACS]
-   Ejemplo de código: Autorización basada en notificaciones en [Windows Identity Foundation SDK][Windows Identity Foundation SDK]

## Escenarios de identidades y acceso del servicio de almacenamiento de Azure

En este escenario tiene que compartir de forma segura el acceso a los
blobs y contenedores de Azure.

Utilice las firmas de acceso compartido. Para tener acceso a su cuenta de
servicio de almacenamiento desde su propia aplicación, utilice el hash compartido, disponible en el portal de
Azure, al configurar y administrar las cuentas de servicio
de almacenamiento. Cuando desea dar a otra persona acceso a los blobs y
contenedores de la cuenta de servicio de almacenamiento, utilice las URL de las firmas de
acceso compartido.

Preste especial atención a la administración de forma segura de la información para evitar su
exposición; además, preste especial atención a la duración de las firmas de
acceso compartido.

![][5]

Consulte los recursos siguientes para resolver este escenario:

-   [Administrar el acceso a los recursos de almacenamiento de Azure][]
-   [New Storage Feature: Shared Access Signatures][]
-   [Shared Access Signatures Are Easy These Days][]

## Escenarios de identidades y acceso de Base de datos SQL de Azure

Base de datos SQL solo admite la autenticación de SQL Server. No se admite
la autenticación Windows (seguridad integrada). Los usuarios deben
proporcionar credenciales (nombre de usuario y contraseña) cada vez que se conectan a una
base de datos SQL. Preste especial atención al administrar el nombre de usuario y la
contraseña para evitar la divulgación de información.

![][6]

Consulte los recursos siguientes para resolver este escenario:

-   [Instrucciones y limitaciones de seguridad (Base de datos SQL de Azure)][]
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante sqlcmd][]
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante ADO.NET][]
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante ASP.NET][]
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante WCF Data Services][]
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante PHP][]
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante JDBC][]
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante ADO.NET Entity Framework][]

## Escenarios de identidades y acceso del bus de servicio de Azure

El bus de servicio y el servicio de control de acceso de Azure AD
tienen una relación especial en que cada espacio de nombres de
servicio del bus de servicio está asociado con un espacio de nombres de servicio del control de acceso del mismo
nombre, con el sufijo "-sb". El motivo de esta relación especial radica en la forma en que el
bus de servicio y el control de acceso administran sus relaciones
de confianza mutua y los secretos criptográficos asociados. Consulte los
recursos mencionados a continuación para obtener más información.

![][7]

Consulte los recursos siguientes para resolver este escenario:

-   [Securing Service Bus with ACS][] (vídeo)
-   [Securing Service Bus with ACS][8] (diapositivas)
-   [Autenticación y autorización del Service Bus con el servicio de control de acceso][]

## Escenarios de identidades y acceso de la caché en memoria

La caché en memoria (anteriormente conocida como caché de Azure) se basa en el
servicio de control de acceso de Azure AD para su autenticación. Utiliza las teclas compartidas
disponibles a través del portal de administración. Utilice las claves del código o
archivos de configuración al acceder a la caché. Asegúrese de almacenar las claves
de forma segura para evitar la divulgación de información.

![][9]

Consulte los recursos siguientes para resolver este escenario:

-   [Direccionamiento del Configuración de un cliente de caché mediante programación (almacenamiento en caché compartido de Azure AppFabric)][]
-   [Direccionamiento del Configuración de un cliente de caché mediante el archivo de configuración de la aplicación (Azure Shared Caching)][]
-   [Muestras de Azure AppFabric][] (sección de ejemplos de almacenamiento en caché)

## Escenarios de identidades y acceso de Azure Marketplace

Todos los accesos al conjunto de datos de Azure Marketplace, ya sean gratuitos o
de pago, deben autenticar al usuario antes de conceder el acceso. Cuando
crea una aplicación, debe incluirse en el código el proceso
de autenticación. Considere los siguientes escenarios comunes:

### Yo obtengo acceso a mi conjunto de datos

En este escenario está creando una aplicación que consume conjuntos de datos
de su suscripción de Marketplace. Usted es el usuario de la aplicación.
La aplicación se puede implementar en Azure, de forma local o en Marketplace.

Utilice la clave compartida disponible por la suscripción a
Marketplace. Obtiene la clave compartida en el portal de Marketplace.

![][10]

Consulte los recursos siguientes para resolver este escenario:

-   [Implementar la autenticación básica HTTP en la aplicación de Marketplace][]

### Los usuarios obtienen acceso a mis conjuntos de datos

En este escenario está creando una aplicación que permite a los usuarios
acceder a su conjunto de datos. La aplicación se puede implementar en Azure,
en local o en Marketplace.

Para resolver este escenario, utilice la delegación OAuth. A los usuarios se les pedirá
que indiquen sus credenciales de Live ID/cuenta de Microsoft y, a continuación,
pasarán por el proceso de consentimiento.

![][11]

Consulte los recursos siguientes para resolver este escenario:

-   [OAuth Web Client Example][]
-   [OAuth Rich Client Example][]

### La aplicación obtiene acceso a la API de Marketplace

En este escenario está creando una aplicación que obtiene acceso a la
API de Marketplace. La API de Marketplace requiere autenticación para
realizar llamadas correctamente. La aplicación se implementa en
Azure Marketplace.

![][12]

Consulte el kit de publicación de Marketplace conocer cómo
implementar la autenticación.

Consulte los recursos siguientes para resolver este escenario:

-   [Descargar el App Publishing Kit][]
-   [Introducción a Azure Marketplace para aplicaciones][]

## Mecanismos de seguridad

En esta sección se describen los mecanismos de seguridad para Windows Identity Foundation y el
servicio de control de acceso de Azure AD. Sírvase de ella como una lista de comprobación de seguridad básica
para estas tecnologías al diseñar e implementar su aplicación.

### Windows Identity Foundation

A continuación se indican los principales mecanismos de seguridad de WIF. La información siguiente es un
resumen extraído de [Consideraciones de diseño de WIF][] y [Windows Identity Foundation
(WIF) Security for ASP.NET Web Applications - Threats & Countermeasures][]
.

-   **IssuerNameRegistry**. Especifica los servicios de token de seguridad de confianza
    (STS). Asegúrese de que solo se muestran los STS de confianza.
-   **cookieHandler requireSsl="true"**. Especifica si las cookies
    de sesión transferidas en el protocolo SSL.
-   **wsFederation's requireHttps="true"**. Especifica si la comunicación del protocolo
    de federación con el proveedor de identidad se realiza
    sobre el protocolo de SSL.
-   **tokenReplayDetection enabled="true"**. Especifica si está habilitada la característica
    de detección de reproducción de tokens. Tenga presente que esta característica
    crea una afinidad de servidor ya que administra las copias locales de los tokens usados.
-   **audienceUris**. Especifica la audiencia prevista del token. Si la
    aplicación recibe un token que no estaba previsto para su aplicación,
    lo rechazará WIF.
-   **requestValidation** y **httpRuntime requestValidationType**.
    Habilita/deshabilita la característica de validación ASP.NET. Consulte la información
    descrita en [Windows Identity Foundation (WIF): A Potentially
    Dangerous Request.Form Value Was Detected from the Client][]

### Control de acceso de Azure AD

Considere los siguientes mecanismos de seguridad al implementar el servicio de control de acceso
de Azure AD. La información siguiente es un resumen extraído de [Instrucciones
de seguridad de ACS][] e [Instrucciones de administración de certificados y claves][].

-   **Expiración de tokens de STS**. Utilice el portal de administración
    del servicio de control de acceso de Azure AD para establecer una expiración agresiva del token.
-   **Validación de datos al utilizar la característica URL del error**. La característica
    URL del error del servicio de control de acceso de Azure AD requiere un acceso anónimo a la página de la
    aplicación donde envía los mensajes de error. Suponga que todos los datos que llegan a
    esta página son peligrosos porque proceden de un origen sin confianza.
-   **Cifrado de tokens para escenarios extremadamente confidenciales**. Para mitigar
    la amenaza de la divulgación de la información que está disponible en el token,
    considere cifrar los tokens.
-   **Cifrado de cookies mediante RSA al implementarlos en Azure**.
    WIF cifra las cookies mediante DPAPI por defecto. Crea una afinidad
    de servidor y puede dar lugar a excepciones cuando se implementa en una granja de servidores web y
    entornos de Azure. Utilice en su lugar RSA en escenarios de granjas de servidores web
    y de Azure.
-   **Certificados de firma de tokens**. Renueve periódicamente los certificados de firma de tokens
    para evitar la denegación de servicio. El servicio de control de acceso
    de Azure AD firma todos los tokens de seguridad que envía. Se utilizan los certificados X.509
    para la firma cuando se crea una aplicación que consume los tokens
    de SAMS enviados por ACS. Cuando expiren los certificados de firma,
    recibirá un error cuando intente solicitar un token.
-   **Claves de firma de tokens**. Renueve periódicamente las claves de firma de tokens para
    evitar la denegación de servicio. El servicio de control de acceso de Azure AD
    firma todos los tokens de seguridad que envía. Las claves de firma simétricas de 256 bits se utilizan
    al construir una aplicación que consume tokens de SWT enviados por
    ACS. Cuando expiren las claves de firma, recibirá un error al intentar
    solicitar un token.
-   **Certificados de cifrado de tokens**. Renueve periódicamente
    los certificados de cifrado de tokens para evitar la denegación de servicio. Se requiere el
    cifrado de tokens si una aplicación de usuarios de confianza es un
    servicio web que utiliza tokens de prueba de posesión en el protocolo WS-Trust;
    en otros casos, el cifrado de tokens es opcional. Cuando expiren los
    certificados de cifrado, recibirá un error al intentar solicitar un
    token.
-   **Certificados de descifrado de tokens**. Renueve periódicamente
    los certificados de descifrado de tokens para evitar la denegación de servicio. El servicio
    de control de acceso de Azure AD puede aceptar tokens cifrados de los proveedores
    de identidades de WS-Federation (por ejemplo, AD FS 2.0). Se utiliza para el descifrado un certificado X.509
    hospedado en el servicio de control de acceso de Azure AD.
    Cuando expiren los certificados de descifrado, recibirá errores al
    intentar solicitar un token.
-   **Credenciales de identidad de servicio**. Renueve periódicamente las credenciales de identidad
    de servicio para evitar la denegación de servicio. Las identidades de servicio utilizan
    credenciales que se configuran globalmente para el espacio de nombres del servicio de control de
    acceso de Azure AD, y que permiten que las aplicaciones
    o clientes se autentiquen directamente con el control de acceso de Azure AD y
    reciban un token. Hay tres tipos de credenciales con las que se pueden
    asociar la identidad de servicio del servicio de control de acceso de Azure AD: Clave
    simétrica, contraseña y certificado X.509. Comenzará recibiendo una
    excepción cuando expiren las credenciales.
-   **Credenciales de la cuenta del servicio de administración
    del control de acceso de Azure AD**. Renueve periódicamente las credenciales del servicio de administración para
    evitar la denegación de servicio. El servicio de administración del
    control de acceso de Azure AD es un componente clave que le permite administrar y configurar mediante programación
    diferentes valores para el espacio de
    nombres del control de acceso de Azure AD. Existen tres tipos de credenciales
    con las que se puede asociar la cuenta del servicio de administración. Son
    la clave simétrica, la contraseña y un certificado X.509. Comenzará
    recibiendo una excepción cuando expiren las credenciales.
-   **Certificados de cifrado y de firma del
    proveedor de identidades de WS-Federation**. Consulte la validez del certificado del proveedor
    de identidades de WS-Federation para evitar la denegación de servicio. El certificado del
    proveedor de identidades de WS-Federation está disponible a través de sus metadatos.
    Al configurar el proveedor de identidades de
    WS-Federation, como AD FS, se configura el certificado de firma de WS-Federation a través de los metadatos de
    WS-Federation disponibles mediante URL o como un archivo. Una vez
    configurado el proveedor de identidades de WS-Federation,
    utilice el servicio de administración de control de acceso de Azure AD para consultar la validez de sus
    certificados. Cuando expire el certificado, comenzará a recibir
    excepciones.

## Hospedaje compartido usando Sitios web Azure

Todos los escenarios y soluciones descritos en este tema son válidos cuando la
aplicación se hospeda en Sitios web Azure.

## Máquinas virtuales de Azure

Todos los escenarios y soluciones descritos en este tema son válidos cuando la
aplicación se hospeda en Máquinas virtuales de Azure.

## Recursos

-   [Identity Developer Training Kit][13]
-   [MSDN-hosted Identity Developer Training Course][14]
-   [A Guide to Claims-based Identity and Access Control][]
-   [Access Control Service][]
-   [Procedimientos de ACS][]
-   [Secure Azure Web Role ASP.NET Web Application Using Access Control Service v2.0][]
-   [Azure AD Access Control Service (ACS) Academy Videos][]
-   [Microsoft Security Development Lifecycle][]
-   [SDL Threat Modeling Tool 3.1.8][]
-   [Security and Privacy Blogs][]
-   [Security Response Center][]
-   [Security Intelligence Report][]
-   [Security Development Lifecycle][Microsoft Security Development Lifecycle]
-   [Security Developer Center (MSDN)][]

  [Azure Security Notes PDF]: http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx
  [Protección de la aplicación]: ./media SecurityRX/01_SecuringTheApplication.gif
  [Cheat Sheet: Web Application Security Frame]: http://msdn.microsoft.com/es-es/library/ff649461.aspx
  [Security Guidance for Applications Index]: http://msdn.microsoft.com/es-es/library/ff650760.aspx
  [Amenazas, vulnerabilidades y ataques]: ./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
  [Windows Identity Foundation 4.5 Samples]: http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication
  [Windows Identity Foundation 4.5 tools for Visual Studio 11 Beta]: http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [Windows Identity Foundation runtime (.Net 3.5/4.0)]: http://www.microsoft.com/es-es/download/details.aspx?id=17331
  [Muestras de código de Windows Identity Foundation 3.5/4.0 y plantillas de Visual Studio 2008/2010]: http://www.microsoft.com/es-es/download/details.aspx?displaylang=en&id=4451
  [Access Control Service 2.0]: http://msdn.microsoft.com/library/gg429786.aspx
  [Scenarios and Solutions Using ACS]: http://msdn.microsoft.com/es-es/library/gg185920.aspx
  [Procedimientos de ACS]: http://msdn.microsoft.com/es-es/library/windowsazure/gg185939.aspx
  [A Guide to Claims-Based Identity and Access Control]: http://msdn.microsoft.com/es-es/library/ff423674.aspx
  [Identity Developer Training Kit]: http://www.microsoft.com/es-es/download/details.aspx?id=14347
  [MSDN-hosted Identity Developer Training Course]: http://msdn.microsoft.com/es-es/IdentityTrainingCourse
  [AD FS 2.0 Content Map]: http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx
  [Web SSO Design]: http://technet.microsoft.com/es-es/library/dd807033(WS.10).aspx
  [Federated Web SSO Design]: http://technet.microsoft.com/es-es/library/dd807050(WS.10).aspx
  [Administrar el acceso a los recursos de almacenamiento de Azure]: http://msdn.microsoft.com/es-es/library/ee393343.aspx
  [New Storage Feature: Shared Access Signatures]: http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures
  [Shared Access Signatures Are Easy These Days]: http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days
  [Control de acceso de Azure AD]: ./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
  [Eliminación de Crear mi primera aplicación ASP.NET para notificaciones mediante ACS]: http://msdn.microsoft.com/es-es/library/gg429779.aspx
  [Eliminación de Hospedar páginas de inicio de sesión en su aplicación web de ASP.NET]: http://msdn.microsoft.com/es-es/library/gg185926.aspx
  [Eliminación de Implementar la autorización de notificaciones en una aplicación ASP.NET para notificaciones mediante WIF y ACS]: http://msdn.microsoft.com/es-es/library/gg185907.aspx
  [Eliminación de Implementar control de acceso basado en roles (RBAC) en una aplicación ASP.NET compatible con notificaciones usando WIF y ACS]: http://msdn.microsoft.com/es-es/library/gg185914.aspx
  [Eliminación de Configurar la confianza entre ACS y las aplicaciones web de ASP.NET mediante certificados X.509]: http://msdn.microsoft.com/es-es/library/gg185947.aspx
  [Code Sample: Formularios simples ASP.NET]: http://msdn.microsoft.com/es-es/library/gg185938.aspx
  [Servicio WCF (SOAP)]: ./media/SecurityRX/04_WCF(SOAP)Service.gif
  [Eliminación de Agregar identidades de servicio con un certificado X.509, una contraseña o una clave simétrica]: http://msdn.microsoft.com/es-es/library/gg185924.aspx
  [Eliminación de Autenticarse con un certificado de cliente en un servicio WCF protegido con ACS]: http://msdn.microsoft.com/es-es/library/hh289316.aspx
  [Eliminación de Autenticar con nombre de usuario y contraseña a un servicio WCF protegido con ACS]: http://msdn.microsoft.com/es-es/library/gg185954.aspx
  [Code Sample: Autenticación de certificados WCF]: http://msdn.microsoft.com/es-es/library/gg185952.aspx
  [Code Sample: Autenticación de nombre de usuario de WCF]: http://msdn.microsoft.com/es-es/library/gg185927.aspx
  [Servicio WCF (SOAP) con Active Directory]: ./media/SecurityRX/05_AzureADAccessControl.gif
  [Eliminación de Configurar AD FS 2.0 como proveedor de identidades]: http://msdn.microsoft.com/es-es/library/gg185961.aspx
  [Eliminación de Usar el servicio de administración para configurar AD FS 2.0 como proveedor de identidades empresariales]: http://msdn.microsoft.com/es-es/library/gg185905.aspx
  [Code Sample: Autenticación federada WCF con AD FS 2.0]: http://msdn.microsoft.com/es-es/library/hh127796.aspx
  [Servicio REST]: ./media/SecurityRX/06_RESTService.gif
  [Eliminación de Configurar la confianza entre el servicio WCF y ACS mediante claves simétricas]: http://msdn.microsoft.com/es-es/library/gg185958.aspx
  [Eliminación de Autenticar el servicio WCF de REST implementado en Azure mediante ACS]: http://msdn.microsoft.com/es-es/library/hh289317.aspx
  [Code Sample: ASP.NET Web Service]: http://msdn.microsoft.com/es-es/library/gg983271.aspx
  [REST WCF With SWT Token Issued By Azure Access Control Service (ACS)]: http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0
  [WIF es opcional]: ./media/SecurityRX/07_WIFisOptional.gif
  [Eliminación de Configurar Google como proveedor de identidades]: http://msdn.microsoft.com/es-es/library/gg185976.aspx
  [Eliminación de Configurar Facebook como proveedor de identidades]: http://msdn.microsoft.com/es-es/library/gg185919.aspx
  [Eliminación de Configurar Yahoo! como proveedor de identidades]: http://msdn.microsoft.com/es-es/library/gg185977.aspx
  [Aplicación web ASP.NET]: ./media/SecurityRX/08_ASPNETWebApptoREST.gif
  [ASP.NET Web App To REST WCF Service Delegation Using Shared SWT Token]: http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82
  []: ./media/SecurityRX/09_RBAC.gif
  [1]: ./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
  [2]: ./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
  [3]: ./media/SecurityRX/12_CustomRoleManager.gif
  [Eliminación de Implementar la lógica de transformación de tokens mediante reglas]: http://msdn.microsoft.com/es-es/library/gg185955.aspx
  [Authorization With RoleManager For Claims Aware (WIF) ASP.NET Web
  Applications]: http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx
  [Windows Identity Foundation SDK]: http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504

  [4]: ./media/SecurityRX/13_ClaimsAuthorizationManager.gif
  [5]: ./media/SecurityRX/14_WindowsAzurestorage.gif
  [6]: ./media SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
  [Instrucciones y limitaciones de seguridad (Base de datos SQL de Azure)]: http://msdn.microsoft.com/es-es/library/windowsazure/ff394108.aspx#authentication
  [Direccionamiento del Conectar con Base de datos SQL de Azure mediante sqlcmd]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336280.aspx
  [Direccionamiento del Conectar con Base de datos SQL de Azure mediante ADO.NET]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336243.aspx
  [Direccionamiento del Conectar con Base de datos SQL de Azure mediante ASP.NET]: http://msdn.microsoft.com/es-es/library/windowsazure/ee621781.aspx
  [Direccionamiento del Conectar con Base de datos SQL de Azure mediante WCF Data Services]: http://msdn.microsoft.com/es-es/library/windowsazure/ee621789.aspx
  [Direccionamiento del Conectar con Base de datos SQL de Azure mediante PHP]: http://msdn.microsoft.com/es-es/library/windowsazure/ff394110.aspx
  [Direccionamiento del Conectar con Base de datos SQL de Azure mediante JDBC]: http://msdn.microsoft.com/es-es/library/windowsazure/gg715284.aspx
  [Direccionamiento del Conectar con Base de datos SQL de Azure mediante ADO.NET Entity Framework]: http://msdn.microsoft.com/es-es/library/windowsazure/ff951633.aspx
  [7]: ./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
  [Securing Service Bus with ACS]: http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS
  [8]: https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849
  [Autenticación y autorización del Service Bus con el servicio de control de acceso]: http://msdn.microsoft.com/es-es/library/hh403962.aspx
  [9]: ./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
  [Direccionamiento del Configuración de un cliente de caché mediante programación (almacenamiento en caché compartido de Azure AppFabric)]: http://msdn.microsoft.com/es-es/library/windowsazure/gg618003.aspx
  [Direccionamiento del Configuración de un cliente de caché mediante el archivo de configuración de la aplicación (Azure Shared Caching)]: http://msdn.microsoft.com/es-es/library/windowsazure/gg278346.aspx
  [Muestras de Azure AppFabric]: http://msdn.microsoft.com/es-es/library/ee706741.aspx
  [10]: ./media/SecurityRX/18_IAccessMyDataset.gif
  [Implementar la autenticación básica HTTP en la aplicación de Marketplace]: http://msdn.microsoft.com/es-es/library/gg193417.aspx
  [11]: ./media/SecurityRX/19_UsersAccessMyDatasets.gif
  [OAuth Web Client Example]: http://go.microsoft.com/fwlink/?LinkId=219162
  [OAuth Rich Client Example]: http://go.microsoft.com/fwlink/?LinkId=219163
  [12]: ./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif
  [Descargar el App Publishing Kit]: http://go.microsoft.com/fwlink/?LinkId=221323
  [Introducción a Azure Marketplace para aplicaciones]: https://datamarket.azure.com/
  [Consideraciones de diseño de WIF]: http://msdn.microsoft.com/es-es/library/ee517298.aspx
  [Windows Identity Foundation (WIF) Security for ASP.NET Web Applications - Threats & Countermeasures]: http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx
  [Windows Identity Foundation (WIF): A Potentially Dangerous Request.Form Value Was Detected from the Client]: http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx
  [Instrucciones de seguridad de ACS]: http://msdn.microsoft.com/es-es/library/gg185962.aspx
  [Instrucciones de administración de certificados y claves]: http://msdn.microsoft.com/es-es/library/hh204521.aspx
  [13]: http://go.microsoft.com/fwlink/?LinkId=214555
  [14]: http://go.microsoft.com/fwlink/?LinkId=214561
  [A Guide to Claims-based Identity and Access Control]: http://go.microsoft.com/fwlink/?LinkId=214562
  [Access Control Service]: http://msdn.microsoft.com/es-es/library/windowsazure/gg429786.aspx
  [Secure Azure Web Role ASP.NET Web Application Using Access Control Service v2.0]: http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx
  [Azure AD Access Control Service (ACS) Academy Videos]: http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx
  [Microsoft Security Development Lifecycle]: http://www.microsoft.com/security/sdl/default.aspx
  [SDL Threat Modeling Tool 3.1.8]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955
  [Security and Privacy Blogs]: http://www.microsoft.com/about/twc/en/us/blogs.aspx
  [Security Response Center]: http://www.microsoft.com/security/msrc/default.aspx
  [Security Intelligence Report]: http://www.microsoft.com/security/sir/
  [Security Developer Center (MSDN)]: http://msdn.microsoft.com/security/
