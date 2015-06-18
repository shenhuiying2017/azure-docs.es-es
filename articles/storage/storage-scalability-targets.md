<properties 
   pageTitle="Objetivos de escalabilidad y rendimiento del almacenamiento | Azure"
   description="Obtenga información sobre los objetivos de escalabilidad y rendimiento de una cuenta de Almacenamiento de Azure, incluida la capacidad, la velocidad de solicitudes y el ancho de banda entrante y saliente. Comprenda los objetivos de rendimiento para las particiones en cada uno de los servicios de Almacenamiento de Azure."
   services="storage"
   documentationCenter="na"
   authors="tamram"
   manager="na"
   editor="na" /> <tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="04/24/2015"
   ms.author="tamram" />

# Objetivos de escalabilidad y rendimiento del almacenamiento de Azure

Este tema trata cuestiones de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure. Para ver un resumen de otros límites de Azure, consulte [Suscripción de Azure y límites de servicio, cuotas y restricciones](../azure-subscription-service-limits.md).

>[AZURE.NOTE]Todas las cuentas de almacenamiento se ejecutan en la nueva topología de red plana y admiten los objetivos de escalabilidad y rendimiento descritos a continuación, independientemente de cuándo se crearon. Para obtener más información sobre la arquitectura de red plana de Almacenamiento de Azure y sobre la escalabilidad, consulte el Blog del equipo de Almacenamiento de Azure.

<!-- -->

>[AZURE.IMPORTANT]Los objetivos de escalabilidad y rendimiento que mencionamos aquí son objetivos exigentes, pero se pueden lograr. En todos los casos, la velocidad de solicitudes y el ancho de banda obtenido por la cuenta de almacenamiento depende del tamaño de los objetos almacenados, de los patrones de acceso utilizados y del tipo de carga de trabajo que realiza la aplicación. Asegúrese de probar el servicio para determinar si el rendimiento se ajusta a sus requisitos. Si es posible, evite picos en la tasa de tráfico y asegúrese de que este se distribuya equitativamente entre las particiones.

>Cuando la aplicación alcanza el límite de lo que puede administrar una partición para la carga de trabajo, Almacenamiento de Azure comenzará a responder con el código de error 503 (servidor ocupado) o el código de error 500 (tiempo de espera de operación). Cuando esto ocurre, la aplicación debe utilizar una directiva de retroceso exponencial para los reintentos. El retroceso exponencial permite que disminuya la carga de la partición y evita los picos de tráfico en esa partición.

Si las necesidades de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, compile la aplicación de forma que use varias cuentas de almacenamiento y divida los datos entre esas cuentas de almacenamiento. Una sola suscripción de Azure permite 100 cuentas de almacenamiento. Consulte [Detalles de precios de almacenamiento](http://azure.microsoft.com/pricing/details/storage/) para obtener información sobre los precios por volumen.

## Objetivos de escalabilidad para cuentas de almacenamiento

Los objetivos de escalabilidad para una cuenta de almacenamiento dependen de la región en la que se creó. Las secciones siguientes describen los objetivos de escalabilidad para regiones de Estados Unidos y para regiones de Europa y Asia.

Para obtener más detalles sobre los objetivos de arquitectura y escalabilidad de las cuentas de almacenamiento, consulte el [Blog del equipo de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/04/windows-azure-s-flat-network-storage-and-2012-scalability-targets.aspx).

### Objetivos de escalabilidad para las cuentas de almacenamiento estándar en regiones de Estados Unidos

En la tabla siguiente se describen los objetivos de escalabilidad para las cuentas de almacenamiento estándar en regiones de Estados Unidos, según el nivel de redundancia elegido:

<table>
<tr>
<th>Capacidad total de la cuenta</th>
<th>Velocidad de solicitudes (suponiendo un tamaño de objeto de 1 KB)</th>
<th>Ancho de banda total de una cuenta de almacenamiento con redundancia geográfica</th>
<th>Ancho de banda total de una cuenta de almacenamiento con redundancia local</th>
<tr>
<td>500 TB</td>
<td>Hasta 20.000 entidades o mensajes por segundo</td>
<td><ul>
<li>*Entrada: hasta 10 gigabits por segundo</li>
<li>*Salida: hasta 20 gigabits por segundo</li>
</ol>
</td>
<td><ul>
<li>*Entrada: hasta 20 gigabits por segundo</li>
<li>*Salida: hasta 30 gigabits por segundo</li>
</tr>
</table>

*Entrada se refiere a todos los datos (solicitudes) enviados a una cuenta de almacenamiento.

*Salida se refiere a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

### Objetivos de escalabilidad para las cuentas de almacenamiento estándar en regiones de Europa y Asia

En la tabla siguiente se describen los objetivos de escalabilidad para las cuentas de almacenamiento en regiones de Europa y Asia, según el nivel de redundancia elegido:

<table>
<tr>
<th>Capacidad total de la cuenta</th>
<th>Velocidad de solicitudes (suponiendo un tamaño de objeto de 1 KB)</th>
<th>Ancho de banda total de una cuenta de almacenamiento con redundancia geográfica</th>
<th>Ancho de banda total de una cuenta de almacenamiento con redundancia local</th>
<tr>
<td>500 TB</td>
<td>Hasta 20.000 entidades o mensajes por segundo</td>
<td><ul>
<li>*Entrada: hasta 5 gigabits por segundo</li>
<li>*Salida: hasta 10 gigabits por segundo</li>
</ol>
</td>
<td><ul>
<li>*Entrada: hasta 10 gigabits por segundo</li>
<li>*Salida: hasta 15 gigabits por segundo</li>
</tr>
</table>

*Entrada se refiere a todos los datos (solicitudes) enviados a una cuenta de almacenamiento.

*Salida se refiere a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

### Objetivos de escalabilidad para cuentas de Almacenamiento Premium

En la tabla siguiente se describen los objetivos de escalabilidad para las cuentas de Almacenamiento Premium disponibles en las regiones Oeste de EE. UU., Este de EE. UU. 2 y Europa occidental:

<table>
<tr>
<th>Capacidad total de la cuenta</th>
<th>Ancho de banda total de una cuenta de Almacenamiento Premium con redundancia local</th>
<tr>
<td><ul>
<li>Capacidad de disco: 35 TB</li>
<li>Capacidad de instantánea: 10 TB</li>
</td>
<td>Hasta 50 gigabits por segundo de entrada y salida</td>
</table>

*Entrada se refiere a todos los datos (solicitudes) enviados a una cuenta de almacenamiento.

*Salida se refiere a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

Para obtener más información acerca de los discos de Almacenamiento Premium, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure](storage-premium-storage-preview-portal.md).

## Particiones de Almacenamiento de Azure

En la tabla siguiente se describen los objetivos de rendimiento para una sola partición en cada servicio:

<table>
<tr>
<th>Rendimiento de un blob</th>
<th>Rendimiento de una cola (mensajes de 1 KB)</th>
<th>Rendimiento de una partición de tabla (entidades de 1 KB)</th>
<th>Rendimiento de un archivo (vista previa)</th><tr>
<td>Hasta 60 MB por segundo o hasta 500 solicitudes por segundo</td>
<td>Hasta 2000 mensajes por segundo</td>
<td>Hasta 2000 entidades por segundo</td>
<td>Hasta 60 MB por segundo</td>
</table>

Cada objeto que contiene datos almacenados en Almacenamiento de Azure (blobs, mensajes, entidades y archivos) pertenece a una partición y se identifica mediante una clave de partición. La partición determina cómo Almacenamiento de Azure equilibra entre servidores la carga de blobs, mensajes, entidades y archivos para satisfacer las necesidades de tráfico de esos objetos. La clave de partición es única dentro de la cuenta de almacenamiento y se utiliza para ubicar un blob, un mensaje o una entidad.

Para las tablas, todas las entidades con el mismo valor de clave de partición se agrupan en la misma partición y se almacenan en el mismo servidor de particiones. Se trata de un punto importante a tener en cuenta en el diseño de la aplicación. La aplicación debería equilibrar las ventajas de escalabilidad que supone propagar las entidades por varias particiones con las ventajas de acceso que supone agrupar las entidades en una sola partición. Una ventaja fundamental de la agrupación de entidades en las particiones es que permite realizar operaciones atómicas entre entidades de la misma partición, ya que la partición está en un solo servidor.

Las particiones afectan al equilibrio de carga y la escalabilidad de cada uno de los servicios de almacenamiento de las maneras siguientes:

- **Blobs**: la clave de partición de un blob es el nombre del contenedor + el nombre del blob. Esto significa que cada blob tiene su propia partición. Los blobs, por tanto, se pueden distribuir en varios servidores para escalar horizontalmente el acceso a ellos. Aunque los blobs pueden agruparse lógicamente en contenedores de blob, esta agrupación no afecta a las particiones.

- **Mensajes**: la clave de partición de un mensaje es el nombre de la cola, por lo que todos los mensajes de una cola se agrupan en una sola partición y se sirven mediante un solo servidor. Las distintas colas pueden ser procesadas por distintos servidores para equilibrar la carga de todas las colas que tenga una cuenta de almacenamiento.

- **Entidades**: la clave de partición de una entidad es el nombre de tabla + la clave de partición, donde la clave de partición es el valor de la propiedad obligatoria **PartitionKey** definida por el usuario para la entidad.

	Las entidades de una tabla pueden tener los mismos valores de clave de partición, y en este caso se agrupan en la misma partición. Una aplicación puede realizar transacciones atómicas en bloque sobre entidades de la misma partición, puesto que reciben servicio desde el mismo servidor.

	Las entidades que, estando en la misma tabla, pertenecen a distintas particiones pueden tener la carga equilibrada entre distintos servidores, lo que permite disponer de una tabla grande con mayor escalabilidad.

## Otras referencias

- [Detalles de precios de almacenamiento](http://azure.microsoft.com/pricing/details/storage/)
- [Almacenamiento de Azure: un servicio de almacenamiento en nube altamente disponible con gran coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md)


<!--HONumber=54--> 