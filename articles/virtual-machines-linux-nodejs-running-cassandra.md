<properties linkid="services-linux-cassandra-with-linux" urlDisplayName="Cassandra with Linux" pageTitle="Run Cassandra with Linux on Azure" metaKeywords="" description="Explains how to run a Cassandra cluster on Linux in Azure Virtual Machines." metaCanonical="" services="virtual-machines" documentationCenter="nodejs" title="Running Cassandra with Linux on Azure and Accessing it from Node.js" authors="hanuk" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanuk"></tags>

# <span></span></a>Ejecución de Cassandra con Linux en Azure y acceso desde Node.js

**Autor:** Hanu Kommalapati

## Tabla de contenido

-   [Información general][]
-   [Esquema de implementación de Cassandra][]
-   [Implementación compuesta][]
-   [Implementación de Máquina virtual de Azure][]
-   [Tarea 1: Implementación del clúster de Linux][]
-   [Tarea 2: Configuración de Cassandra en cada máquina virtual][]
-   [Tarea 3: Acceso al clúster de Cassandra desde Node.js][]
-   [Conclusión][]

## <span id="overview"></span> </a>Información general

Azure proporciona un servicio de base de datos NoSQL a través del almacenamiento de tablas de Azure, el que permite un almacenamiento sin esquemas de objetos de negocios. Es posible utilizar este servicio desde Node.JS, .NET, Java y cualquier otro lenguaje que admita HTTP y REST. Sin embargo, existen otras bases de datos NoSQL conocidas, como Cassandra y Couchbase, que no podrían ejecutarse en PaaS de Azure debido a su modelo de servicio en la nube sin estado. Máquinas virtuales de Azure ahora permite ejecutar estas bases de datos NoSQL en Azure sin necesidad de cambiar la base de código. La idea de este documento es mostrar cómo ejecutar un clúster de Cassandra en Máquinas virtuales y tener acceso a él desde Node.js. No abarca la implementación de Cassandra en operaciones de producción reales, donde es necesario tener en cuenta el clúster de Cassandra con varios centros de datos con las estrategias asociadas de copia de seguridad y recuperación. En este ejercicio utilizaremos Ubuntu 12.04, versión de Linux, y Cassandra 1.0.10; sin embargo, el proceso se puede ajustar para cualquier distribución de Linux.

## <span id="schematic"></span> </a>Esquema de implementación de Cassandra

La funcionalidad de Máquinas virtuales de Azure permite ejecutar bases de datos NoSQL como [Cassandra][] en la nube pública de Microsoft de manera tan simple como sería ejecutarlas en un entorno de nube privada, con la excepción de una diferencia en la configuración de la red virtual específica para la infraestructura de Máquinas virtuales de Azure. En el momento de la redacción de este documento, Cassandra no se encuentra disponible como servicio administrado en Azure y, por tanto, este artículo tratará de la configuración de un clúster de Cassandra en Máquinas virtuales y del acceso a él desde otra instancia de Linux hospedada también dentro de Máquinas virtuales. Los fragmentos de código de Node.js que se muestran también se pueden utilizar desde un servicio web o una aplicación web hospedados en PaaS. Una de las principales fortalezas de Azure es que permite el modelo de aplicación compuesta que puede aprovechar lo mejor de los mundos de IaaS y PaaS.

Existen dos modelos de implementación posibles para el entorno de aplicación de Cassandra: una implementación de Máquinas virtuales autocontenida y una implementación compuesta. En una implementación compuesta, se consumirá un clúster de Cassandra hospedado en Máquinas virtuales desde una aplicación web (o servicio web) de Azure hospedada en PaaS mediante una interfaz de Thrift a través del equilibrador de carga. A pesar de que cada nodo de Cassandra transmite la solicitud a otros nodos del mismo nivel ante la eventualidad de un error de espacio clave, el equilibrador de carga ayuda con el equilibrio de carga básico de las solicitudes. Además, el equilibrador de carga crea un espacio aislado protegido con firewall para controlar mejor los datos.

## <span id="composite"></span> </a> Implementación compuesta

El objetivo de una implementación compuesta es maximizar el uso de PaaS mientras se mantiene la superficie de la máquina virtual en un valor mínimo absoluto, en un esfuerzo por ahorrar en la sobrecarga impuesta por la administración de la infraestructura de las máquinas virtuales. Debido a la sobrecarga de administración del servidor, implemente solo los componentes que requieran un comportamiento con estado que no se puedan modificar fácilmente debido a diversos motivos, entre otros, el plazo de comercialización, la falta de visibilidad del código fuente y un bajo nivel de acceso al SO.

![Diagrama de la implementación compuesta][]

## <span id="deployment"></span> </a>Implementación de Máquina virtual de Azure

![Implementación de una máquina virtual][]

En los diagramas anteriores, un clúster de Cassandra de cuatro nodos se implementa dentro de Máquinas virtuales tras un equilibrador de carga que está configurado para permitir el tráfico de Thrift. La aplicación PaaS hospedada en Azure tiene acceso al clúster a través de bibliotecas Thrift específicas para el lenguaje. Existen bibliotecas para lenguajes como Java, C#, Node.js, Python y C++. La implementación de Máquinas virtuales autocontenida que se muestra en el segundo diagrama consume datos por aplicaciones que se ejecutan dentro de otro servicio en la nube hospedado en Máquinas virtuales.

## <span id="task1"></span> </a>Tarea 1: Implementación del clúster de Linux

La secuencia típica para crear un clúster es:

![Diagrama de secuencia para crear un clúster][]

**Paso 1: Generación de par de claves de SSH**

Azure necesita una clave pública X509 con codificación PEM o DER en el momento del aprovisionamiento. Genere un par de claves públicas/privadas con las instrucciones ubicadas en [Utilización de SSH con Linux en Azure][]. Si planea utilizar putty.exe como un cliente SSH en Windows o en Linux, debe convertir la clave privada RSA con codificación PEM en formato PPK mediante puttygen.exe. Puede encontrar las instrucciones para hacerlo en [Generación de par de claves SSH para implementación de máquinas virtuales de Linux en Azure][].

**Paso 2: Creación de una máquina virtual de Ubuntu**

Para crear la primera máquina virtual de Ubuntu, inicie sesión en el portal de vista previa de Azure, haga clic en **New**, **Virtual Machine**, **From Gallery**, **Unbuntu Server 12.xx** y, a continuación, haga clic en la flecha derecha. Si necesita un tutorial en que se describe cómo crear una máquina virtual de Linux, consulte [Creación de una máquina virtual que ejecuta Linux][].

Luego, escriba la siguiente información en la pantalla VM Configuration:

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Nombre del campo</th>
<th align="left">Valor del campo</th>
<th align="left">Comentarios</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Virtual Machine Name</td>
<td align="left">hk-cas1</td>
<td align="left">El nombre de host de la máquina virtual</td>
</tr>
<tr class="even">
<td align="left">New User Name</td>
<td align="left">localadmin</td>
<td align="left">&quot;admin&quot; es un nombre de usuario reservado en Ubuntu 12.xx</td>
</tr>
<tr class="odd">
<td align="left">New Password</td>
<td align="left"><em>contraseña segura</em></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Confirm Password</td>
<td align="left"><em>contraseña segura</em></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Size</td>
<td align="left">Pequeña</td>
<td align="left">Seleccione la máquina virtual basada en las necesidades de E/S.</td>
</tr>
<tr class="even">
<td align="left">Secure using SSH Key for Authentication</td>
<td align="left">Haga clic en la casilla</td>
<td align="left">Seleccione la casilla si desea protección con una clave SSH</td>
</tr>
<tr class="odd">
<td align="left">Certificate</td>
<td align="left"><em>nombre de archivo del certificado de clave pública</em></td>
<td align="left">Clave pública SSH con codificación DER o PEM generada con OpenSSL u otras herramientas</td>
</tr>
</tbody>
</table>

Escriba la siguiente información en la pantalla VM Mode:

| Nombre del campo                      | Valor del campo                           | Comentarios                                                                                                                                |
|---------------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Standalone Virtual VM                 | "marque" el botón de selección            | Esta opción es para la primera máquina virtual; para las máquinas virtuales subsiguientes, utilizaremos la opción "Connect to Existing VM" |
| DNS Name                              | *nombre único*.cloudapp.net               | Defina un nombre de equilibrador de carga independiente de la máquina                                                                      |
| Cuenta de almacenamiento              | *cuenta de almacenamiento predeterminada* | Utilice la cuenta de almacenamiento predeterminada que creó                                                                                |
| Region/Affinity Group/Virtual Network | Oeste de EE. UU.                          | Seleccione una región desde donde las aplicaciones web tienen acceso al clúster de Cassandra                                               |

Repita el proceso anterior para todas las máquinas virtuales que formarán parte del clúster de Cassandra. En este punto, todas las máquinas formarán parte de la misma red y podrán hacer ping entre sí. Si el ping no funciona, revise la configuración del firewall de la máquina virtual (por ejemplo, las tablas de direcciones IP) para asegurarse de que se admite el protocolo ICMP. Asegúrese de deshabilitar el protocolo ICMP una vez que se prueba correctamente la conectividad de la red, para así disminuir el vector de ataques.

**Paso 3: Incorporación de un extremo de Thrift con equilibrio de carga**

Una vez realizados el paso 1 y el paso 2, cada máquina virtual ya debe tener definido un extremo SSH. Ahora agreguemos el extremo Thrift con equilibrio de carga con un puerto público de 9160. Esta es la secuencia:

a. En la vista de detalles de la primera máquina virtual, haga clic en "Add Endpoint"

b. En la pantalla "Add endpoint to virtual machine", seleccione el botón de radio "Add endpoint"

c. Haga clic en la flecha derecha

d. En la pantalla "Specify endpoint details", escriba lo siguiente

<table>

<tr>

<th>
Nombre del campo

</th>

<th>
Valor del campo

</th>

<th>
Comentarios

</th>

</tr>

<tr>

<td>
Nombre

</td>

<td>
cassandra

</td>

<td>
Puede ser cualquier nombre de extremo único

</td>

</tr>

<tr>

<td>
Protocol

</td>

<td>
TCP

</td>

<td>
</td>

</tr>

<tr>

<td>
Public Port

</td>

<td>
9160

</td>

<td>
Puerto Thrift predeterminado

</td>

</tr>

<tr>

<td>
Private Port

</td>

<td>
9160

</td>

<td>
A menos que haya cambiado este valor en cassandra.yaml

</td>

</tr>

</table>
Después de los pasos anteriores, la primera máquina virtual mostrará el extremo cassandra con el campo LOAD BALANCED en "NO". Ignórelo por el momento, porque cambiará a "YES" una vez que agreguemos este extremo a las máquinas virtuales subsiguientes

</p>
e. Ahora seleccione la segunda máquina virtual y repita el proceso anterior para agregar el extremo, solo con la pequeña diferencia que seleccionará "Load-balance traffic on an existing endpoint" y utilice "cassandra-960" en el cuadro desplegable. En esta etapa, la asignación de extremo a ambas máquinas virtuales cambiará el estado de LOAD BALANCED de "NO" a "YES".

Repita el paso "e" para los nodos subsiguientes en el clúster.

Ahora que las máquinas virtuales están listas, es momento de configurar Cassandra en cada una de ellas. Como Cassandra no es parte estándar de muchas de las distribuciones de Linux, recurriremos a un proceso de implementación manual.

[Observe que utilizamos un enfoque manual para la instalación del software en cada máquina virtual. Sin embargo, es posible acelerar el proceso al configurar una máquina virtual de Cassandra completamente funcional, capturarla como la imagen base y crear instancias adicionales a partir de esta imagen base. Las instrucciones para capturar la imagen de Linux se encuentran en [Captura de una imagen de una máquina virtual que ejecuta Linux][].]

## <span id="task2"></span> </a>Tarea 2: Configuración de Cassandra en cada máquina virtual

**Paso 1: Requisitos previos a la instalación**

Cassandra requiere Máquina virtual Java y, por tanto, debe instalar la versión más reciente de JRE con el siguiente comando de elementos derivados de Debian, incluido Ubuntu:

    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer

**Paso 2: Instalación de Cassandra**

1.  Inicie sesión con SSH en la instancia de máquina virtual de Linux (Ubuntu).

2.  Utilice wget para descargar bits de Cassandra desde el espejo sugerido en (<http://cassandra.apache.org/download/>)[<http://cassandra.apache.org/download/>] al directorio "~/downloads" como apache-cassandra-bin.tar.gz. Observe que el número de versión no está incluido en el archivo descargado para asegurarse de que la publicación se mantenga independiente de la versión.

3.  Ejecute el siguiente comando para descomprimir el archivo en el directorio de inicio de sesión predeterminado:

        tar -zxvf downloads/apache-cassandra-bin.tar.gz

    El comando anterior expandirá el archivo al directorio apache-cassandra- [versión].

4.  Cree los dos siguientes directorios predeterminados para que contengan registros y datos:

        $ sudo chown -R /var/lib/cassandra
        $ sudo chown -R /var/log/cassandra

5.  Conceda permisos de escritura a la identidad de usuario bajo la cual se ejecutará Cassandra

        a.  sudo chown -R <user>:<group> /var/lib/cassandra
        b.  sudo chown -R <user>:<group> /var/log/cassandra
        To use current user context, replace the <user> and <group> with $USER and $GROUP

6.  Inicie Cassandra desde el directorio apache-cassandra-[versión]/bin con el siguiente comando:

        $ ./cassandra

El comando anterior iniciará el nodo Cassandra como un proceso en segundo plano. Utilice -cassandra "f" para iniciar el proceso en primer plano.

El registro puede mostrar un error relacionado con mx4j. Cassandra funcionará bien sin mx4j, pero este es necesario para administrar la instalación de Cassandra. Cierre el proceso de Cassandra antes del paso siguiente.

**Paso 3: Instalación de mx4j**

    a)  Download mx4j: wget [http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download](http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download) -O mx4j.tar.gz
    b)  tar -zxvf mx4j.tar.gz
    c)  cp mx4j-23.0.2/lib/*.jar ~/apache-cassandra-<version>/lib
    d)  rm -rf mx4j-23.0.2
    e)  rm mx4j.tar.gz

Reinicie en esta etapa el proceso de Cassandra

**Paso 4: Prueba de la instalación de Cassandra**

Ejecute el siguiente comando desde el directorio bin de Cassandra para conectarse a través del cliente Thrift:

    cassandra-cli -h localhost -p 9160

**Paso 5: Habilitación de Cassandra para conexiones externas**

De manera predeterminada, Cassandra solo está configurado para escuchar la dirección de bucle invertido, por lo que este cambio es obligatorio para las conexiones externas.

Edite "conf/cassandra.yaml" para cambiar los valores **listen\_address** y **rpc\_address** a la dirección IP o al nombre de host del servidor para que el nodo actual esté visible para los demás nodos, además de para los equilibradores de carga externos.

Repita los pasos 1 a 5 para todos los nodos en el clúster.

Ahora que todas las máquinas virtuales individuales cuentan con el software necesario, es momento de establecer comunicación entre los nodos a través de la configuración de inicialización. Revise la información que se encuentra en [][]<http://wiki.apache.org/cassandra/MultinodeCluster></a> para obtener detalles sobre la configuración de clúster de varios nodos.

**Paso 6: Configuración de clúster de varios nodos**

Edite cassandra.yaml para cambiar las siguientes propiedades en todas las máquinas virtuales:

**a) cluster\_name**

El nombre de clúster predeterminado está definido en "Test Cluster"; cámbielo por un nombre que refleje su aplicación. Ejemplo: "AppStore" . Si ya inició el clúster con "Test Cluster" para prueba durante la instalación, recibirá un error de coincidencia de nombre de clúster. Para corregir este error, elimine todos los archivos del directorio /var/lib/cassandra/data/system.

**b) seeds**

Los nodos nuevos utilizarán las direcciones IP especificadas aquí para aprender acerca de la topología en anillo. Defina los nodos más confiables como sus valores de inicialización en un formato separado por comas: "*host1*,*host2*" . Ejemplo de configuración: "hk-ub1,hk-ub2".

Aceptaremos los tokens predeterminados que proporcionan los servidores de inicialización, porque este no es nuestro foco en este ejercicio. Si desea conocer la generación óptima de tokens, consulte el script python ubicado en:
[][1]<http://wiki.apache.org/cassandra/GettingStarted></a>.

Reinicie Cassandra en todos los nodos para aplicar los cambios anteriores.

**Paso 7: Prueba del clúster de varios nodos**

La herramienta nodetool instalada en el directorio bin de Cassandra ayudará con las operaciones de clúster. Utilizaremos nodetool para comprobar la configuración de clúster a través del siguiente formato de comando:

    $ bin/nodetool -h <hostname> -p 7199 ring

Si la configuración es correcta, debe mostrar la información tal como aparece a continuación para un clúster de tres nodos:

<table>
<colgroup>
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Dirección</td>
<td align="left">CD</td>
<td align="left">Bastidor</td>
<td align="left">Status</td>
<td align="left">Estado</td>
<td align="left">Carga</td>
<td align="left">Propiedad</td>
<td align="left">Token</td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">149463697837832744402916220269706844972</td>
</tr>
<tr class="odd">
<td align="left">10.26.196.68</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Up</td>
<td align="left">Normal</td>
<td align="left">15,69 KB</td>
<td align="left">25,98%</td>
<td align="left">114445918355431753244435008039926455424</td>
</tr>
<tr class="even">
<td align="left">10.26.198.81</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Up</td>
<td align="left">Normal</td>
<td align="left">15,69 KB</td>
<td align="left">53,44%</td>
<td align="left">70239176883275351288292106998553981501</td>
</tr>
<tr class="odd">
<td align="left">10.26.198.84</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Up</td>
<td align="left">Normal</td>
<td align="left">18,35 KB</td>
<td align="left">25,98%</td>
<td align="left">149463697837832744402916220269706844972</td>
</tr>
</tbody>
</table>

En esta etapa, el clúster está preparado para clientes Thrift a través de la URL del servicio en la nube (el nombre DNS dado durante la creación de la primera máquina virtual) creada durante la tarea "Implementación del clúster de Linux".

## <span id="task3"></span> </a>Tarea 3: Acceso al clúster de Cassandra desde Node.js

Cree una máquina virtual de Linux en Azure con el proceso descrito en las tareas anteriores. Asegúrese de que esta máquina virtual es una máquina virtual independiente, porque la utilizaremos como cliente para tener acceso al clúster de Cassandra. Instalaremos Node.js, NPM y [cassandra-client][] desde GitHub antes de conectarse al clúster de Cassandra desde esta máquina virtual:

**Paso 1: Instalación de Node.js y NPM**

a) Instale los requisitos previos

    sudo apt-get install g++ libssl-dev apache2-utils make

b) Utilizaremos código fuente desde GitHub para compilar e instalar; antes de que podamos clonar el repositorio, deberemos instalar el tiempo de ejecución de núcleo de git:

    sudo apt-get install git-core

c) Clone el repositorio de nodos

    git clone git://github.com/joyent/node.git

d) El comando anterior creará el directorio con nombre "node". Ejecute la siguiente secuencia de comandos para compilar la instalación de node.js:

    cd node
    ./configure
    make
    sudo make install

e) Instale NPM desde archivos binarios estables mediante la ejecución del siguiente comando

    curl http://npmjs.org/install.sh | sh

**Paso 2: Instalación del paquete cassandra-client**

    npm cassandra-client 

**Paso 3: Preparación del almacenamiento Cassandra**

El almacenamiento Cassandra utiliza los conceptos de KEYSPACE y COLUMNFAMILY que se puede comparar aproximadamente con las estructuras DATABASE y TABLE en lenguaje RDBMS. KEYSPACE contendrá un conjunto de definiciones de COLUMNFAMILY. Cada COLUMNFAMILY contendrá un conjunto de filas y, a su vez, cada fila contiene varias columnas, tal como aparece a continuación en la vista compuesta:

![Filas y columnas][]

Utilizaremos el clúster de Cassandra anteriormente implementado para demostrar el acceso de Node.js a través de la creación y consulta de las estructuras de datos anteriores. Crearemos un script simple de Node.js que ejecuta la preparación básica del clúster para almacenar datos de cliente. Las técnicas que aparecen en el script se pueden utilizar fácilmente en servicios web o en una aplicación web de Node.js. Tenga en cuenta que los fragmentos de código solo tienen como objetivo mostrar cómo funcionan las cosas y, para las soluciones reales, el código que aparece tiene mucho espacio (por ejemplo, seguridad, registro, escalabilidad, etc.) para mejorar.

Permítanos definir las variables requeridas en el ámbito del script para que incluya PooledConnection desde el módulo cassandra-client y los parámetros de conexión de espacio clave y nombre de espacio clave que se utilizan con frecuencia:

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

Como preparación para almacenar los datos de cliente, primero necesitamos crear un KEYSPACE con el siguiente ejemplo de script:

    casdemo.js: 
    function createKeyspace(callback){
       var cql = 'CREATE KEYSPACE ' + ksName + ' WITH 
       strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
       var sysConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'],  
                             keyspace: 'system', use_bigints: false };
       var con = new pooledCon(sysConOptions);
       con.execute(cql,[],function(err) {
       if (err) {
         console.log("Failed to create Keyspace: " + ksName);
         console.log(err);
       }
       else {
         console.log("Created Keyspace: " + ksName);
         callback(ksConOptions, populateCustomerData);
       }
       });
       con.shutdown();
    } 

La función createKeysapce toma una función de devolución como el argumento que debe ejecutar la función de creación de COLUMNFAMILY como KEYSPACE es un requisito previo para la creación de la familia de columnas. Observe que necesitamos conectarnos con el KEYSPACE de "sistema" para la definición de KEYSPACE de aplicación. [Cassandra Query Language (CQL)][] se usa de manera coherente en la interacción con el clúster en todos estos fragmentos de código. Como el CQL compuesto en el script anterior no tenía ningún marcador de parámetro, utilizamos una colección de parámetros en blanco ("[]") con el método PooledConnection.execute().

Una vez que se crea correctamente el espacio clave, se ejecutará la función createColumnFamily(), que aparece en el siguiente fragmento de código, para crear las definiciones necesarias de COLUMNFAMILY:

    casdemo.js: 
    //Creates COLUMNFAMILY
    function createColumnFamily(ksConOptions, callback){
      var params = ['customers_cf','custid','varint','custname',
                    'text','custaddress','text'];
      var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
    var con =  new pooledCon(ksConOptions);
      con.execute(cql,params,function(err) {
          if (err) {
             console.log("Failed to create column family: " + params[0]);
             console.log(err);
          }
          else {
             console.log("Created column family: " + params[0]);
             callback();
          }
      });
      con.shutdown();
    } 

La plantilla parametrizada de CQL se combinará con el objeto params para generar CQL válido para la creación de COLUMNFAMILY. Una vez que COLUMNFAMILY se ha creado correctamente, la devolución de llamada suministrada, en este caso populateCustomerData(), se llamará como parte de la cadena de llamadas asincrónicas.

    casdemo.js: 
    //populate Data
    function populateCustomerData() {
       var params = ['John','Infinity Dr, TX', 1];
       updateCustomer(ksConOptions,params);

       params = ['Tom','Fermat Ln, WA', 2];
       updateCustomer(ksConOptions,params);
    }

    //update will also insert the record if none exists
    function updateCustomer(ksConOptions,params)
    {
      var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where 
                 custid=?';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,params,function(err) {
          if (err) console.log(err);
          else console.log("Inserted customer : " + params[0]);
      });
      con.shutdown();
    }

populateCustomerData() inserta un par de filas en COLUMNFAMILY, a saber, customers\_cf. En Cassandra Query Language, UPDATE insertará el registro si el registro todavía no está presente en el proceso, lo que hará redundante la instrucción INSERT CQL.

Hasta el momento, hemos cableado la cadena de devoluciones de llamadas: createKeyspace() a createColumnFamily() a populateCustomerData(). Ahora es momento de que ejecutemos el código a través del siguiente fragmento de código:

    casdemo.js:
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    createKeyspace(createColumnFamily);
    //rest of the not shown

Ejecute el siguiente comando desde el símbolo del sistema de shell para ejecutar el script:

    //the following command will create the KEYSPACE, COLUMNFAMILY and //inserts two customer records
    $ node casdemo.js

El método readCustomer() tendrá acceso al clúster hospedado en Azure y muestra el fragmento de código JSON recuperado después de ejecutar la consulta de CQL:

    casdemo.js: 
    //read the two rows inserted above
    function readCustomer(ksConOptions)
    {
      var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,[],function(err,rows) {
          if (err) 
             console.log(err);
          else 
             for (var i=0; i<rows.length; i++)
                console.log(JSON.stringify(rows[i]));
        });
       con.shutdown();
    } 

Modifique casdemo.js para agregar la función anterior y llamarla después de comentar el método createKeyspace() anteriormente llamado, tal como aparece a continuación:

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    //createKeyspace(createColumnFamily);
    readCustomer(ksConOptions)
    //rest of the code below not shown
        

## <span id="conclusion"></span> </a>Conclusión

La funcionalidad de Máquinas virtuales de Azure permite la creación de máquinas virtuales de Linux (imágenes proporcionadas por asociados de Microsoft) y Windows que permiten la migración sin cambios de aplicaciones y productos de servidor existentes. Un ejemplo de esto es el servidor de base de datos NoSQL de Cassandra que se analiza en este artículo. Los servicios en la nube hospedados de Azure y nubes privadas y nubes públicas de terceros desde entornos de sistema operativo de Windows y Linux pueden tener acceso al clúster de Cassandra configurado según este documento. En este artículo abarcamos Node.js como el cliente; sin embargo, es posible tener acceso a Cassandra desde .NET, Java y otros entornos de lenguaje.

  [Información general]: #overview
  [Esquema de implementación de Cassandra]: #schematic
  [Implementación compuesta]: #composite
  [Implementación de Máquina virtual de Azure]: #deployment
  [Tarea 1: Implementación del clúster de Linux]: #task1
  [Tarea 2: Configuración de Cassandra en cada máquina virtual]: #task2
  [Tarea 3: Acceso al clúster de Cassandra desde Node.js]: #task3
  [Conclusión]: #conclusion
  [Cassandra]: http://wiki.apache.org/cassandra/
  [Diagrama de la implementación compuesta]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png
  [Implementación de una máquina virtual]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png
  [Diagrama de secuencia para crear un clúster]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux4.png
  [Utilización de SSH con Linux en Azure]: http://www.windowsazure.com/es-es/manage/linux/how-to-guides/ssh-into-linux/
  [Generación de par de claves SSH para implementación de máquinas virtuales de Linux en Azure]: http://blogs.msdn.com/b/hanuk/archive/2012/06/07/generating-ssh-key-pair-for-linux-vm-deployment-on-windows-azure.aspx
  [Creación de una máquina virtual que ejecuta Linux]: http://www.windowsazure.com/es-es/manage/linux/tutorials/virtual-machine-from-gallery/
  [Captura de una imagen de una máquina virtual que ejecuta Linux]: https://www.windowsazure.com/es-es/manage/linux/how-to-guides/capture-an-image/
  []: http://wiki.apache.org/cassandra/MultinodeCluster
  [1]: http://wiki.apache.org/cassandra/GettingStarted
  [cassandra-client]: https://github.com/racker/node-cassandra-client
  [Filas y columnas]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux3.png
  [Cassandra Query Language (CQL)]: http://cassandra.apache.org/doc/cql/CQL.html
