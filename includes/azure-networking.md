# Conexión a Azure

Una conexión común a Internet es la forma más fácil de conectarse a las
aplicaciones y los datos de Azure. No obstante, esta solución básica no
siempre es la mejor. De hecho, Azure también ofrece tecnologías para que
los usuarios puedan conectarse a los centros de datos de Azure. En este
artículo se analizan estas tecnologías.
## Tabla de contenido

* [Red virtual de Azure](#Vnet)
* [Administrador de tráfico de Azure](#TrafficMngr)

<a  name="Vnet" ></a>
##Red virtual de Azure


Azure le permite crear máquinas virtuales (VM) que se ejecutan en los
centros de datos de Microsoft. Supongamos que una organización quiere
utilizar dichas VM para ejecutar aplicaciones empresariales u otro
software que utilizarán los empleados de la empresa. Es posible que, por
ejemplo, desee crear una granja de SharePoint en la nube, o bien
ejecutar una aplicación para la administración de inventarios. A fin de
facilitar el trabajo a los usuarios en la medida de lo posible, le
gustaría que fuera posible obtener acceso a las aplicaciones como si se
ejecutaran en el centro de datos de la propia empresa.

Existe una solución estándar para este tipo de problema, que consiste en
crear una red privada virtual (VPN). Esto es lo que hacen las
organizaciones de todos los tamaños para conectar, por ejemplo, los PC
de las sucursales con el centro de datos de la empresa principal. Este
mismo enfoque puede servir para las VM de Azure, como se muestra en la
ilustración 1.

<a  name="Fig1" ></a>

![01_Networking](./media/azure-networking/Networking_01Networking.png)

**Ilustración 1: la Red virtual de Azure permite crear una red virtual
en la nube conectada al centro de datos ubicado en las instalaciones de
la empresa.**

Como se muestra en la ilustración, la Red virtual de Azure le permite
crear un límite lógico en torno a un grupo de VM, denominado *red
virtual o VNET*, en un centro de datos de Azure. De esta forma, puede
establecer posteriormente una conexión IPsec entre la VNET y la red
local. Las VM de una VNET se pueden crear con la utilización de máquinas
virtuales de Azure o de servicios en la nube de Azure, o bien con ambas
opciones. En otras palabras, puede tratarse de VM creadas con la
tecnología de la infraestructura como servicio (IaaS) de Azure o con su
tecnología basada en la plataforma como servicio (PaaS).
Independientemente de la opción que elija, para crear una conexión IPsec
necesita un dispositivo de puerta de enlace de VPN, hardware
especializado conectado a la red local y también los servicios del
administrador de red. Tras haber establecido la conexión, parece como si
las VM de Azure que se ejecutan en la VNET estuvieran integradas en la
red de la organización.

Como se refleja en la [ilustración 1](#Fig1), asigna direcciones IP para
las VM de Azure desde el mismo espacio de direcciones IP utilizado en su
propia red. En el escenario que explicamos aquí, en el que se utilizan
direcciones IP privadas, las VM de la nube no son más que otra subred
IP. El software que se ejecuta en la red local considerará estas VM como
si fueran locales, como sucede con las VPN tradicionales. Además, es
importante destacar que, habida cuenta de que la conexión se establece a
nivel de IP, las máquinas virtuales y físicas de ambos lados pueden
ejecutar cualquier sistema operativo. Las VM de Azure que ejecutan
Windows Server o Linux pueden interactuar con las máquinas locales que
ejecutan Windows, Linux u otros sistemas. También se pueden utilizar
herramientas de administración estándar, como System Center y otras,
para administrar las VM en la nube y las aplicaciones que estas
contienen.

Resulta útil utilizar la Red virtual de Azure en muchas situaciones.
Como bien se ha mencionado, este enfoque permite a los usuarios de
entornos empresariales obtener un acceso más sencillo a las aplicaciones
de la nube. Un aspecto importante de esta facilidad de uso es la
capacidad de integrar las VM de Azure en un dominio de Active Directory
local para asignar a los usuarios un inicio de sesión único para las
aplicaciones que ejecutan. Si lo prefiere, también puede crear un
dominio de Active Directory en la nube y conectarlo posteriormente a una
red local.

Al crear una VNET en un centro de datos de Azure, se le permite obtener
acceso a un amplio grupo de recursos bajo demanda. Puede crear VM bajo
demanda, pagar por ellas mientras se ejecutan y, a continuación,
quitarlas (y dejar de pagar) cuando ya no las necesite. Esto puede
resultar útil para escenarios donde se necesita obtener acceso rápido a
una máquina preconfigurada, como en el caso de los equipos de desarrollo
que compilan software nuevo. En lugar de esperar a que un administrador
local configure los recursos que estos equipos necesitan, pueden
crearlos por su cuenta en la nube pública.

Y al igual que la red virtual hace que las VM de Azure parezcan locales
para los recursos de las propias instalaciones, también se da el caso
contrario: el software que se ejecuta en la red local ahora parece ser
local para las aplicaciones que se ejecutan en la VNET de Azure.
Supongamos que le gustaría transferir una aplicación local a Azure, por
ejemplo, porque ha determinado que será más económico gestionarla en la
nube. Pero, &amp;iquest;qué sucede si la ley exige que los datos que la
aplicación utiliza se almacenen en las propias instalaciones? En una
situación como esta, la utilización de la red virtual permite que la
aplicación de la nube considere un sistema de base de datos ubicado en
las propias instalaciones como si fuera local, por lo que resulta fácil
obtener acceso a él. Independientemente del escenario que elija, el
resultado es el mismo: Azure se convierte en una extensión de su propio
centro de datos.

<a  name="TrafficMngr" ></a> 
##Administrador de tráfico de Azure

Imaginemos que ha compilado una aplicación de Azure correctamente. La
aplicación la utilizan muchas personas en varios países de todo el
mundo. Es estupendo, pero como suele ocurrir, el éxito plantea nuevos
problemas. En este caso, por ejemplo, es muy probable que la aplicación
se ejecute en varios centros de datos de Azure en diferentes partes del
mundo. &amp;iquest;Cómo puede distribuir el tráfico de las solicitudes
de usuario de manera inteligente entre estos centros de datos para que
los usuarios siempre disfruten de la mejor experiencia?

El Administrador de tráfico de Azure está diseñado para resolver este
problema. En la ilustración 2 se muestra cómo.

<a  name="Fig3" ></a>

![03_TrafficManager](./media/azure-networking/Networking_03TrafficManager.png)

**Ilustración 2: el Administrador de tráfico de Azure distribuye de
forma inteligente las solicitudes de los usuarios entre instancias de
una aplicación que se ejecuta en diferentes centros de datos de Azure.**

En este ejemplo, la aplicación se ejecuta en VM distribuidas entre
cuatro centros de datos: dos en los Estados Unidos, uno en Europa y otro
en Asia. Supongamos que un usuario desea acceder a la aplicación desde
Berlín. Si utiliza el Administrador de tráfico, a continuación
explicamos lo que ocurre.

Como de costumbre, el sistema del usuario busca el nombre de DNS de la
aplicación (paso 1). Esta consulta se dirige al sistema DNS de Azure
(paso 2), que a continuación busca la directiva del Administrador de
tráfico para esta aplicación. El propietario de una aplicación de Azure
concreta crea cada política, bien mediante una interfaz gráfica o una
API REST. Cuando se crea, la directiva especifica una de las tres
opciones de equilibrio de carga:

* **Rendimiento:** todas las solicitudes se envían al centro de datos
  con la latencia más baja desde el sistema del usuario.
* **Conmutación por error:** todas las solicitudes se envían al centro
  de datos especificado por el autor de esta directiva, a menos que el
  centro de datos no esté disponible. En este caso, las solicitudes se
  dirigen a otros centros de datos en el orden de prioridad definido por
  el autor de la directiva.
* **Round robin:** todas las solicitudes se distribuyen de manera
  equitativa entre todos los centros de datos en que se ejecuta la
  aplicación.

Cuando tiene la directiva correcta, el Administrador de tráfico
determina a qué centro de datos debe remitirse esta solicitud en función
de qué opción se especifique de entre las tres disponibles (paso 3). A
continuación, devuelve la ubicación del centro de datos elegido al
usuario (paso 4), que accede a dicha instancia de la aplicación (paso
5).

Para este trabajo, el Administrador de tráfico debe tener una imagen
actual de qué instancias de la aplicación están en funcionamiento en
cada centro de datos. Para que esto sea posible, el Administrador de
tráfico hace ping periódicamente en cada copia de la aplicación mediante
el comando GET HTTP y, a continuación, registra si recibe una respuesta.
Si una instancia de aplicación deja de responder, el Administrador de
tráfico dejará de dirigir usuarios a dicha instancia hasta que vuelva a
responder a los pings.

No todas las aplicaciones son lo suficientemente grandes ni globales
como para necesitar el Administrador de tráfico. No obstante, para las
que sí lo necesitan, puede tratarse de un servicio muy útil.

