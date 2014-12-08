# Proceso

Azure permite implementar y supervisar el código de la aplicación
que se ejecuta dentro de un centro de datos de Microsoft. Al crear una aplicación
y ejecutarla en Azure, el código y la configuración juntos
se denominan un servicio hospedado de Azure. Los servicios hospedados son fáciles de
administrar, escalar verticalmente, volver a configurar y actualizar con las nuevas versiones del
código de la aplicación. Este artículo se centra en el modelo
de aplicación de servicio hospedado de Azure.<a id="compare" name="compare"></a>

## Tabla de contenido<a id="_GoBack" name="_GoBack"></a>

-   [Beneficios del modelo de aplicación de Azure][]
-   [Conceptos básicos de los servicios hospedados][]
-   [Consideraciones acerca del diseño de servicios hospedados][]
-   [Diseño de aplicaciones para escala][]
-   [Definición y configuración de servicios hospedados][]
-   [Archivo de definición de servicio][]
-   [Archivo de configuración de servicio][]
-   [Creación e implementación de servicios hospedados][]
-   [Referencias][]

## <a id="benefits"> </a>Beneficios del modelo de aplicación de Azure

Al implementar la aplicación como un servicio hospedado, Azure
crea una o varias máquinas de virtuales (VM) que contienen el
código de la aplicación y arranca las máquinas virtuales en máquinas físicas que residen en
uno de los centros de datos de Azure. Cuando el cliente solicita a la aplicación hospedada
que especifique el centro de datos, un equilibrador de carga distribuye estas
solicitudes por igual a las máquinas virtuales. Mientras la aplicación se hospeda en 
Azure, obtiene tres beneficios principales:

-   **Alta disponibilidad.** Alta disponibilidad significa que Azure garantiza
    que la aplicación se está ejecutando lo máximo posible y es capaz de
    responder a las solicitudes de los clientes. Si la aplicación termina (debido a
    una excepción no controlada, por ejemplo), Azure lo
    detectará y reiniciará la aplicación automáticamente. Si la
    máquina en la que se ejecuta la aplicación experimenta algún tipo de
    error de hardware, Azure también lo detectará y
    creará automáticamente una nueva máquina virtual en otra máquina física que está en funcionamiento
    y ejecutará el código desde allí. NOTA: Para que su aplicación
    obtenga un contrato de nivel de servicio de Microsoft del 99,95%,
    debe tener al menos dos máquinas virtuales ejecutando el código de la aplicación. Esto
    permite que una máquina virtual procese solicitudes de los clientes mientras Azure mueve
    el código de una máquina virtual con error a una máquina virtual nueva, en buen estado.

-   **Escalabilidad.** Azure permite cambiar fácil y dinámicamente
    el número de máquinas virtuales que ejecutan el código de la aplicación para controlar la
    carga real que tiene la aplicación. Esto permite
    ajustar la aplicación a la carga de trabajo que los clientes están
    poniendo en ella, al tiempo que solo pagan por las máquinas virtuales que necesitan cuando las
    necesitan. Cuando desea cambiar el número de máquinas virtuales, Azure
    responde en minutos, lo que permite cambiar dinámicamente el
    número de máquinas virtuales que se ejecutan tantas veces como desee.

-   **Manejabilidad.** Dado que Azure es oferta de plataforma como servicio
    (PaaS), administra la infraestructura (el hardware,
    las electricidad y las redes) necesaria para mantener estas máquinas
    en funcionamiento. Azure también administra la plataforma, garantizando un
    sistema operativo actualizado con todas las revisiones y
    actualizaciones de seguridad correctas, así como actualizaciones de componentes como .NET
    Framework e Internet Information Server. Dado que todas las máquinas virtuales se
    ejecutan en Windows Server 2008, Azure proporciona características
    adicionales, como supervisión de diagnóstico, soporte de escritorio remoto,
    firewalls y configuración del almacén de certificados. Todas estas características
    se proporcionan sin coste adicional. De hecho, al ejecutar la
    aplicación en Azure, se incluye la licencia del sistema operativo
    Windows Server 2008. Dado que todas las máquinas virtuales están ejecutando
    Windows Server 2008, cualquier código que se ejecuta en Windows Server 2008 funciona
    bien cuando se ejecuta en Azure.

## <a id="concepts"> </a>Conceptos básicos de los servicios hospedados

Cuando la aplicación se implementa como un servicio hospedado en Azure,
se ejecuta como uno o más *roles*. Un *rol* simplemente hace referencia a los archivos
y a la configuración de la aplicación. Puede definir uno o varios roles para su
aplicación, cada uno con su propio conjunto de configuración y archivos
de aplicación. Para cada rol de la aplicación, puede especificar el
número de máquinas virtuales, o *instancias de rol*, que se ejecutarán. En la siguiente ilustración se muestran dos
ejemplos sencillos de una aplicación modelada como un servicio hospedado usando
roles e instancias de rol.

##### Ilustración 1: Un único rol con tres instancias (VM) que se ejecutan en un centro de datos de Azure

![image][0]

##### Ilustración 2: Dos roles, cada uno de ellos con dos instancias (máquinas virtuales), que se ejecutan en un centro de datos de Azure

![image][1]

Las instancias de rol suelen procesar las solicitudes de los clientes de Internet que entran en el
centro de datos a través de lo que se denomina un *extremo de entrada*. Un único rol
puede tener 0 o más extremos de entrada. Cada extremo indica un protocolo
(HTTP, HTTPS o TCP) y un puerto. Es habitual configurar un rol para
que tenga dos extremos de entrada: HTTP, que escucha en el puerto 80 y HTTPS, que escucha
en el puerto 443. La siguiente ilustración muestra un ejemplo de dos roles diferentes
con distintos extremos de entrada que dirigen las solicitudes de los clientes a ellos.

![image][2]

Al crear un servicio hospedado en Azure, se le asigna una
dirección IP direccionable públicamente que los clientes pueden utilizar para tener acceso a él. Tras
crear el servicio hospedado, también debe seleccionar un prefijo de URL que se
asigna a esa dirección IP. Este prefijo debe ser único, ya que
básicamente está reservando la URL *prefijo*.cloudapp.net para que nadie más
pueda tenerlo. Los clientes se comunican con las instancias de rol a través de la
URL. Normalmente, no distribuirá ni publicará la URL
*prefijo*.cloudapp.net de Azure. En su lugar, adquirirá un nombre DNS del
registrador DNS que elija y configurará su nombre DNS para redirigir
solicitudes de los clientes a la dirección URL de Azure. Para obtener información, consulte
[Configuración de un nombre de dominio personalizado en Azure.][].

## <a id="considerations"> </a>Consideraciones acerca del diseño de servicios hospedados

Al diseñar una aplicación para que se ejecute en un entorno de nube, hay
varias consideraciones que es necesario tener en cuenta, como la latencia,
la alta disponibilidad y la escalabilidad.

Decidir dónde ubicar el código de la aplicación es una consideración
importante cuando se ejecuta un servicio hospedado en Azure. Es
habitual implementar su aplicación en centros de datos que están más cercanos a
sus clientes a reducir la latencia y obtener el mejor rendimiento posible.
Sin embargo, puede elegir un centro de datos más cercano a su empresa o a
los datos si tiene problemas jurisdiccionales o legales sobre
los datos y en el lugar donde residen. Hay seis centros de datos alrededor del
mundo capaces de hospedar el código de la aplicación. La siguiente tabla muestra
las ubicaciones disponibles:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**País/región**

</td>
<td style="width: 200px;">
**Subregiones**

</td>
</tr>
<tr>
<td>
Estados Unidos

</td>
<td>
Centromeridional y centroseptentrional

</td>
</tr>
<tr>
<td>
Europa

</td>
<td>
Noroccidental

</td>
</tr>
<tr>
<td>
Asia

</td>
<td>
Sudoriental

</td>
</tr>
</tbody>
</table>
Al crear un servicio hospedado, seleccione una subregión que indique la
ubicación en que desea que su código se ejecute.

Para lograr alta disponibilidad y escalabilidad, es sumamente importante
que los datos de la aplicación se mantengan en un repositorio central accesible
a múltiples instancias de rol. Para ayudarle, Azure ofrece
varias opciones de almacenamiento como blobs, tablas y bases de datos SQL. Consulte
el artículo [Ofertas de almacenamiento de datos en Azure][] para obtener más
información acerca de estas tecnologías de almacenamiento. La siguiente ilustración muestra cómo
el equilibrador de carga en el centro de datos de Azure distribuye
las solicitudes de los clientes en diferentes instancias de rol, que tienen acceso al
mismo almacenamiento de datos.

![image][3]

Normalmente, deseará colocar el código de la aplicación y los datos en el
mismo centro de datos, ya que esto permite la baja latencia (mejor rendimiento)
cuando el código de la aplicación accede a los datos. Además, no se
cobra por el ancho de banda cuando los datos se mueven dentro del mismo centro
de datos.

## <a id="scale"> </a>Diseño de aplicaciones para escala

A veces, puede que desee tomar una aplicación (por ejemplo, un sitio web
sencillo) y hospedarla en Azure. Pero con frecuencia, la
aplicación puede estar formada por varios roles que funcionan conjuntamente. Por
ejemplo, en la figura siguiente, hay dos instancias del rol Sitio
web, tres instancias del rol Procesamiento de pedidos y una instancia del
rol Generador de informes. Estos roles funcionan todos juntos y
el código de todos ellos se puede empaquetar junto e implementarlo como una única
unidad en Azure.

![image][4]

La razón principal para dividir una aplicación en distintos roles
que se ejecutan en su propio conjunto de instancias de rol (es decir, máquinas virtuales) es escalar los
roles de forma independiente. Por ejemplo, durante la temporada de vacaciones, muchos
clientes pueden adquirir productos de su empresa, por lo que es posible
que desee aumentar el número de instancias de rol que se ejecutan su rol Sitio web,
así como el número de instancias de rol que ejecutan el rol Procesamiento
de pedidos. Después de la temporada de vacaciones, le pueden devolver muchos productos,
así que quizás necesite muchas instancias Sitio web, pero menos instancias
Procesamiento de pedidos. Durante el resto del año, es posible que solo necesite unas
cuantas instancias Sitio web y Procesamiento de pedidos. Durante todo esto, es
posible que solo necesite una instancia Generador de informes. La flexibilidad de
las implementaciones basadas en roles en Azure permite adaptar fácilmente su
aplicación a sus necesidades empresariales.

Es habitual que las instancias de rol del servicio hospedado
se comuniquen entre sí. Por ejemplo, el rol Sitio web acepta un
pedido de un cliente pero, a continuación, descarga el procesamiento del pedido a las instancias
del rol Procesamiento de pedidos. La mejor forma de pasar trabajo de un conjunto de
instancias de rol a otro conjunto de instancias es mediante la tecnología de colas
proporcionada por Azure, el Servicio de cola o
Colas del Bus de servicio Es fundamental utilizar una cola
aquí. La cola permite al servicio hospedado escalar sus roles
independientemente, lo que permite equilibrar la carga de trabajo y el costo. Si el
número de mensajes de la cola aumenta con el tiempo, puede escalar
el número de instancias del rol Procesamiento de pedidos. Si el número de
mensajes de la cola disminuye con el tiempo, puede reducir el
número de instancias del rol Procesamiento de pedidos. De este modo, solo paga
las instancias necesarias para controlar la carga de trabajo real.

La cola también proporciona confiabilidad. Cuando se reduce el número de
instancias del rol Procesamiento de pedidos, Azure decide qué instancias
terminará. Puede decidir terminar una instancia que está
procesando un mensaje de la cola. Sin embargo, dado que el procesamiento del
mensaje no se completa correctamente, el mensaje se vuelve visible
de nuevo para otra instancia del rol Procesamiento de pedidos, que lo recoge y
lo procesa. Gracias a la visibilidad del mensaje en la cola, se garantiza que los
mensajes finalmente se procesen. La cola también actúa como un equilibrador de carga
al distribuir eficazmente sus mensajes a todas las instancias de rol
que le solicitan mensajes.

Para las instancias del rol Sitio web, puede supervisar el tráfico entrante
y decidir escalar el número de ellos hacia arriba o abajo también. La
cola permite escalar el número de instancias del rol Sitio web
independientemente de las instancias del rol Procesamiento de pedidos. Esto es muy
eficaz y le proporciona mucha flexibilidad. Por supuesto, si su
aplicación consta de roles adicionales, podría agregar colas
adicionales como conducto para comunicarse entre ellos a fin de aprovechar
la misma escala y ventajas económicas.

## <a id="defandcfg"> </a>Definición y configuración de servicios hospedados

La implementación de un servicio hospedado en Azure requiere tener también un
archivo de definición del servicio y un archivo de configuración del servicio. Ambos
archivos son archivos XML y permiten especificar de forma declarativa
opciones de implementación para el servicio hospedado. El archivo de definición de servicio
describe todos los roles que componen el servicio hospedado y cómo se
comunican. El archivo de configuración de servicio describe el número de
instancias de cada rol y la configuración utilizada para configurar cada instancia
de rol.

## <a id="def"> </a>Archivo de definición de servicio

Como se mencionó anteriormente, el archivo de definición de servicio (CSDEF) es un archivo XML
que describe los distintos roles que componen su aplicación
completa. Aquí se puede encontrar el esquema completo del archivo XML:
[http://msdn.microsoft.com/es-es/library/windowsazure/ee758711.aspx][].
El archivo CSDEF contiene un elemento WebRole o WorkerRole para cada rol
que desee en la aplicación. La implementación de un rol como un rol web (mediante
el elemento WebRole) significa que el código se ejecutará en una instancia de rol
que contiene Windows Server 2008 e Internet Information Server (IIS).
La implementación de un rol como un rol de trabajo (mediante el elemento WorkerRole) significa
que la instancia de rol tendrá Windows Server 2008 en él (IIS no se
instalará).

Se puede crear e implementar un rol de trabajo que usa algún otro
mecanismo para escuchar solicitudes web entrantes (por ejemplo, el código
puede crear y usar un .NET HttpListener). Puesto que todas las instancias de rol
se están ejecutando en Windows Server 2008, el código puede realizar cualquier operación
que normalmente esté disponible para una aplicación que se ejecuta en Windows Server
2008.

En cada rol se indica el tamaño de VM deseado que las instancias de dicho
rol deben usar. La siguiente tabla muestra los distintos tamaños de máquina virtual disponibles
en la actualidad y los atributos de cada una:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Tamaño de VM**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**Disco**

</td>
<td>
**Pico  
E/S de red**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra pequeño**

</td>
<td>
1 x 1,0 GHz

</td>
<td>
768 MB

</td>
<td>
20GB

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Pequeña**

</td>
<td>
1 x 1,6 GHz

</td>
<td>
1,75 GB

</td>
<td>
225GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Mediano**

</td>
<td>
2 x 1,6 GHz

</td>
<td>
3,5 GB

</td>
<td>
490GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande**

</td>
<td>
4 x 1,6 GHz

</td>
<td>
7 GB

</td>
<td>
1 TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra grande**

</td>
<td>
8 x 1,6 GHz

</td>
<td>
14 GB

</td>
<td>
2 TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
Se le cobra por hora por cada máquina virtual como una instancia de rol,
así como por todos los datos que las instancias de rol envíen fuera del
centro de datos. No se cobra por los datos que entran en el centro de datos. Para obtener
más información, consulte [Precios de Azure][]. En general, es
recomendable utilizar muchas instancias de rol pequeñas en lugar de unas pocas
instancias grandes para que la aplicación sea más resistente a errores. Después de
todo, cuantas menos instancias de rol tenga, más perjudicial resultará un error en
una de ellas para su aplicación general. Además, como se mencionó antes,
debe implementar al menos dos instancias para cada rol para obtener el
contrato de nivel de servicio del 99,95% que proporciona Microsoft.

En el archivo de definición de servicio (CSDEF) también se especifican muchos
atributos de cada rol de la aplicación. Estos son algunos de los
elementos más útiles disponibles:

-   **Certificados**. Los certificados se usan para cifrar los datos o si
    el servicio web admite SSL. Es necesario cargar todos los certificados
    en Azure. Para obtener más información, consulte [Administración de certificados
    en Azure][]. Esta configuración de XML instala certificados previamente
    cargados en el almacén de certificados de la instancia de rol para que
    el código de aplicación pueda utilizarlos.

-   **Nombres de opciones de configuración**. Para los valores que desea que sus
    aplicaciones lean mientras se ejecutan en una instancia de rol. Los valores reales
    de la configuración se establecen en el archivo
    de configuración del servicio (CSCFG), que se puede actualizar en cualquier momento sin
    necesidad de volver a implementar el código. De hecho, puede codificar sus
    aplicaciones de manera que pueda detectar los valores de la
    configuración modificada sin tiempo de inactividad.

-   **Extremos de entrada**. Aquí puede especificar cualquier extremos HTTP, HTTPS o TCP
    (con puertos) que desee exponer al mundo exterior
    a través de su URL *prefijo*.cloadapp.net. Cuando Azure implementa su
    rol, configurará el firewall en la instancia de rol
    automáticamente.

-   **Extremos internos**. Aquí puede especificar los extremos HTTP o TCP
    que desea exponer a otras instancias de rol que se implementan como
    parte de la aplicación. Los extremos internos permiten que todas las
    instancias de rol dentro de la aplicación para comunicarse entre ellos, pero no son
    accesibles para cualquier instancia de rol que estén fuera de la aplicación.

-   **Módulos de importación**. Opcionalmente instalan componentes útiles en
    instancias de rol. Existen componentes para la supervisión de diagnósticos,
    escritorio remoto y Azure Connect (que permite que su
    instancia de rol tenga acceso a recursos locales a través de un canal seguro).

-   **Almacenamiento local**. Esto asigna un subdirectorio en la instancia de
    rol para que la aplicación la use. Se describe con más detalle
    en el artículo [Ofertas de almacenamiento de datos en Azure][].

-   **Tareas de inicio**. Las tareas de inicio proporcionan una forma de instalar
    componentes necesarios en una instancia de rol cuando arranca. Si se requiere,
    las tareas se pueden ejecutar con permisos elevados como administrador.

## <a id="cfg"> </a>Archivo de configuración de servicio

El archivo de configuración del servicio (CSCFG) es un archivo XML que describe
opciones que se pueden cambiar sin volver a implementar la aplicación. Aquí
se puede encontrar el esquema completo del archivo XML:
[http://msdn.microsoft.com/es-es/library/windowsazure/ee758710.aspx][].
El archivo CSCFG contiene un elemento Role para cada rol de la
aplicación. Estos son algunos de los elementos que se pueden especificar en el archivo CSCFG
:

-   **Versión de SO**. Este atributo permite seleccionar la versión
    del sistema operativo (SO) que desea usar para todas las instancias de rol que ejecuta
    el código de la aplicación. Este sistema operativo se conoce como *sistema operativo invitado* y cada
    nueva versión incluye las últimas revisiones de seguridad y actualizaciones
    disponibles en el momento en que se lanza el sistema operativo invitado. Si establece el valor
    del atributo osVersion en "\*", Azure actualiza
    automáticamente el sistema operativo invitado en todas las instancias de rol a medida que las nuevas versiones
    del sistema operativo invitado están disponibles. Sin embargo, puede optar por actualizaciones
    automáticas seleccionando una versión del sistema operativo invitado específica. Por ejemplo,
    establecer el atributo osVersion en un valor de
    "WA-GUEST-OS-2.8\_201109-01" hace que todas las instancias de rol obtengan
    lo que se describe en esta página web:
    [http://msdn.microsoft.com/es-es/library/hh560567.aspx][]. Para obtener más
    información acerca de las versiones del sistema operativo invitado, consulte [Administración de actualizaciones del
    sistema operativo invitado de Azure].

-   **Instancias**. El valor de este elemento indica el número de instancias
    de rol que desea aprovisionar ejecutando el código para un determinado
    rol. Puesto que puede cargar un nuevo archivo CSCFG en Azure
    (sin volver a implementar la aplicación), es muy sencillo
    cambiar el valor de este elemento y cargar un nuevo archivo CSCFG para
    aumentar o disminuir dinámicamente el número de instancias de rol
    que ejecutan el código de la aplicación. Esto permite escalar con facilidad la
    aplicación verticalmente para cubrir las demandas de carga de trabajo reales y, al mismo tiempo,
    controlar cuánto se cobra por ejecutar las instancias de rol.

-   **Valores de opciones de configuración**. Este elemento indica los valores de
    configuración (como se define en el archivo CSDEF). El rol puede leer estos
    valores mientras se está ejecutando. Estos valores de configuración suelen
    utilizarse para las cadenas de conexión a la base de datos de SQL o 
    al almacenamiento de Azure, pero pueden utilizarse para lo que desee.

## <a id="hostedservices"> </a>Creación e implementación de servicios hospedados

Crear un servicio hospedado requiere que primero vaya al [Portal de administración de Azure] y aprovisionar un servicio hospedado especificando
un prefijo DNS y el centro de datos en el que desea que se ejecute el
código. A continuación, en el entorno de desarrollo, se crea el archivo
de definición del servicio (CSDEF), se compila el código de la aplicación y se empaquetan (zip)
todos estos archivos en un archivo de service package (CSPKG). También debe
preparar el archivo de configuración del servicio (CSCFG). Para implementar su rol,
cargue los archivos CSPKG y CSCFG con la API de administración
de servicios de Windows Azure. Una vez implementado, Azure aprovisionará instancias
de rol en el centro de datos (basándose en los datos de configuración),
extraiga el código de la aplicación del paquete, cópielo en las instancias
de rol y arranque las instancias. Ahora el código ya está en funcionamiento.

En la siguiente ilustración se muestran los archivos CSPKG y CSCFG que crea en el
equipo de desarrollo. El archivo CSPKG contiene el archivo CSDEF y el
código de dos roles. Después de cargar los archivos CSPKG y CSCFG con la
API de administración de servicios de Windows Azure, Azure crea las instancias
de rol del centro de datos. En este ejemplo, el archivo CSCFG indicaba
que Azure debe crear tres instancias del rol \#1 y dos
instancias del rol \#2.

![image][5]

Para obtener más información sobre cómo implementar, actualizar y volver a configurar sus
roles, consulte el artículo [Implementación y actualización de aplicaciones de Azure][]
.<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Referencias

-   [Creating a Hosted Service for Azure][]

-   [Managing Hosted Services in Azure][]

-   [Migrating Applications to Azure][]

-   [Configurar un proyecto de Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Escrito por Jeffrey Richter (Wintellect)</p>

</div>

  [Beneficios del modelo de aplicación de Azure]: #benefits
  [Conceptos básicos de los servicios hospedados]: #concepts
  [Consideraciones acerca del diseño de servicios hospedados]: #considerations
  [Diseño de aplicaciones para escala]: #scale
  [Definición y configuración de servicios hospedados]: #defandcfg
  [Archivo de definición de servicio]: #def
  [Archivo de configuración de servicio]: #cfg
  [Creación e implementación de servicios hospedados]: #hostedservices
  [Referencias]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/es-es/develop/net/common-tasks/custom-dns/
  [Data Storage Offerings in Azure]: http://www.windowsazure.com/es-es/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Precios de Azure]: http://www.windowsazure.com/es-es/pricing/calculator/
  [Administración de certificados en Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/es-es/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/es-es/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/es-es/library/hh560567.aspx]: http://msdn.microsoft.com/es-es/library/hh560567.aspx
  [Administración de actualizaciones del sistema operativo invitado de Azure]: http://msdn.microsoft.com/es-es/library/ee924680.aspx
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Implementación y actualización de aplicaciones de Azure]: http://www.windowsazure.com/es-es/develop/net/fundamentals/deploying-applications/
  [Creating a Hosted Service for Azure]: http://msdn.microsoft.com/es-es/library/gg432967.aspx
  [Managing Hosted Services in Azure]: http://msdn.microsoft.com/es-es/library/gg433038.aspx
  [Migrating Applications to Azure]: http://msdn.microsoft.com/es-es/library/gg186051.aspx
  [Configurar un proyecto de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/ee405486.aspx
