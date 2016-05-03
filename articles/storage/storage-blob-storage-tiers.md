<properties
	pageTitle="Almacenamiento de blobs de Azure: niveles de acceso Esporádico y Frecuente | Microsoft Azure"
	description="Información general de los niveles de Almacenamiento de blobs de Azure para el almacenamiento rentable de datos de objeto basado en patrones de acceso."
	services="storage"
	documentationCenter=""
	authors="sribhat-msft"
	manager="vamshik"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/24/2016"
	ms.author="sribhat"/>


# Almacenamiento de blobs de Azure: niveles de acceso Esporádico y Frecuente

## Información general

Almacenamiento de blobs de Azure es una solución rentable y escalable para almacenar en la nube grandes cantidades de datos no estructurados. Dada su capacidad de almacenamiento masivo y su escalabilidad, Almacenamiento de blobs almacena de forma fácil y rentable petabytes de datos y miles de millones de objetos por cliente. Para más información, consulte [Introducción a Almacenamiento de Microsoft Azure](storage-introduction.md).

En la actualidad, los datos almacenados en la nube crecen a un ritmo exponencial y un aspecto importante de la administración del costo de las crecientes necesidades de almacenamiento es organizar los datos en niveles basándose en atributos como la frecuencia de acceso, el período de retención, etc. Según los patrones de uso y los comentarios de los usuarios, hemos visto que los datos almacenados en la nube pueden ser bastante diferentes en cuanto a la forma en que se generan, se procesan y se accede a ellos a lo largo de su duración. A algunos datos se accede y modifican dinámicamente a lo largo de su duración, a otros se accede muy a menudo al principio de su duración, pero la frecuencia de acceso se reduce drásticamente con el paso del tiempo y, por último, otros empiezan inactivos en la nube y casi nunca (o nunca) se accede a ellos una vez almacenados. En general, denominamos **datos de acceso frecuente** a aquellos a los que se accede con mucha frecuencia y que necesitan ser muy duraderos y estar disponibles muy frecuentemente. Por otro lado, los **datos de acceso esporádico** son aquellos a los que rara vez se accede y cuya duración es larga. Los datos de acceso esporádico pueden tolerar una disponibilidad ligeramente inferior, pero aun así requieren una gran durabilidad y un tiempo de acceso y unas características de rendimiento similares a las de los datos de acceso frecuente. En el caso de los datos de acceso esporádico, un contrato de nivel de servicio de una disponibilidad ligeramente inferior y unos costos de acceso mayores unas compensaciones aceptables por unos costos de almacenamiento mucho menores.

Todos los escenarios de acceso a datos descritos anteriormente se benefician de un nivel de almacenamiento diferenciado que se optimiza para un patrón de acceso concreto. Almacenamiento de blobs de Azure ahora satisface esta necesidad de niveles de almacenamiento diferenciados para datos con distintos patrones de acceso y modelos de precios.

## Cuentas de Almacenamiento de blobs

Las **cuentas de Almacenamiento de blobs** son cuentas de almacenamiento especiales para almacenar los datos no estructurados como blobs (objetos) en Almacenamiento de Azure. Con las cuentas de Almacenamiento de blobs, ahora se puede elegir entre los niveles de acceso de uso esporádico y frecuente para almacenar los datos a los que se accede con menor frecuencia a menor costo y aquellos a los que se accede con mayor frecuencia a un coste de acceso menor. Las cuentas de Almacenamiento de blobs son similares a las cuentas de almacenamiento de uso general existentes y comparten las excelentes características de rendimiento, escalabilidad, disponibilidad y durabilidad que se usan hoy en día, incluida la coherencia total de la API con blobs en bloques y blobs en anexos.

Las cuentas de Almacenamiento de blobs exponen el atributo **Access Tier**, que permiten especificar el nivel de acceso como **Frecuente** o **Esporádico**, en función de los datos almacenados en la cuenta. Si hay un cambio en el patrón de uso de los datos, también se puede cambiar de nivel de acceso en cualquier momento.

> [AZURE.NOTE] El cambio del nivel de acceso puede conllevar cargos adicionales. Para más información, consulte la sección [Precios y facturación](storage-blob-storage-tiers.md#pricing-and-billing), que encontrará más adelante.

Entre los ejemplos de escenarios de uso del nivel de acceso frecuente se incluyen:

- Los datos que están en uso o a los que se espera que se acceda (se lean y se escriban) con frecuencia.
- Los datos que se almacenan provisionalmente para el procesamiento y la eventual migración al nivel de acceso esporádico.

Entre los ejemplos de escenarios de uso del nivel de acceso esporádico se incluyen:

- Conjuntos de datos de copia de seguridad, archivo y recuperación ante desastres.
- Contenido multimedia antiguo que no se ve con frecuencia, pero que se espera que esté disponible de inmediato cuando se acceda a él.
- Grandes conjuntos de datos que tengan que almacenarse de manera rentable mientras se recopilan más datos para su futuro procesamiento (*por ejemplo*, el almacenamiento a largo plazo de datos científicos o datos de telemetría sin procesar de una planta de producción)
- Datos originales (sin procesar) que se deben conservar, incluso después de que se hayan procesado hasta que tengan una forma final utilizable. (*p.ej.*, archivos multimedia sin formato después de su transcodificación a otros formatos)
- Datos de cumplimiento y de archivo que se deben almacenar durante mucho tiempo y a los que casi nunca se accede. (*por ejemplo,*, grabaciones de cámaras de seguridad, radiografías o resonancias antiguas en centros sanitarios, grabaciones de audio y transcripciones de llamadas de clientes de servicios financieros)

Para más información sobre las cuentas de almacenamiento, consulte [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md).

Para las aplicaciones que requieren silo el almacenamiento blobs en bloques o en anexos, se recomienda utilizar cuentas de Almacenamiento de blobs, con el fin de aprovechar el modelo de precios diferenciado de almacenamiento en niveles. Sin embargo, sabemos que esto puede no ser posible en determinadas circunstancias, donde la mejor solución es usar cuentas de almacenamiento de uso general, como:

- Necesita usar tablas, colas o archivos y desea que los blobs se almacenen en la misma cuenta de almacenamiento. Tenga en cuenta que la única ventaja técnica de almacenarlos en la misma cuenta es que tienen las mismas claves compartidas.
- Necesita usar el modelo de implementación clásico. Las cuentas de Almacenamiento de blobs solo están disponibles a través del modelo de implementación de Azure Resource Manager.
- Necesita usar blobs en páginas. Por lo general, se recomienda usar blobs en bloques, salvo que se necesiten específicamente blobs en páginas.
- Usa una versión de la [API de REST de servicios de almacenamiento](https://msdn.microsoft.com/library/azure/dd894041.aspx) anterior al 14-02-2014 o una biblioteca de cliente con una versión inferior a la 4.x y no puede actualizar la aplicación.

> [AZURE.NOTE] En la actualidad, las cuentas de Almacenamiento de blobs se admiten en la mayoría de las regiones de Azure, y en un futuro cercano se admitirán en más aún. En la página [Servicios por región](https://azure.microsoft.com/regions/#services), encontrará la lista actualizada de regiones disponibles.

## Comparación entre los niveles de acceso

La tabla siguiente compara los dos niveles de acceso:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250"> <col width="250"> <col width="250">
<tbody>
<tr>
	<td><strong><center></center></strong></td>
	<td><strong><center>Nivel de acceso frecuente</center></strong></td>
	<td><strong><center>Nivel de acceso esporádico</center></strong>&lt; /td
</tr>
<tr>
    <td><strong><center>Disponibilidad</center></strong></td>
	<td><center>99,9&#160;%</center></td>
	<td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilidad<br>(lecturas de RA-GRS)</center></strong></td>
	<td><center>99,99%</center></td>
	<td><center>99,9&#160;%</center></td>
</tr>
<tr>
    <td><strong><center>Gastos de uso</center></strong></td>
	<td><center>Mayores costos de almacenamiento<br>Menores costos de acceso y transacciones</center></td>
	<td><center>Menores costos de almacenamiento<br>Mayores costos de acceso y transacciones</center></td>
</tr>
<tr>
	<td><strong><center>Tamaño mínimo de objeto<center></strong></td>
	<td colspan="2"><center>N/D</center></td>
</tr>
<tr>
	<td><strong><center>Duración mínima del almacenamiento<center></strong></td>
	<td colspan="2"><center>N/D</center></td>
</tr>
<tr>
	<td><strong><center>Latencia<br>(tiempo hasta el primer byte)<center></strong></td>
	<td colspan="2"><center>milisegundos</center></td>
</tr>
<tr>
	<td><strong><center>Rendimiento y escalabilidad<center></strong></td>
	<td colspan="2"><center>Igual que las cuentas de almacenamiento de uso general</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Las cuentas de Almacenamiento de blobs admiten los mismos objetivos de rendimiento y escalabilidad que las cuentas de almacenamiento de uso general. Consulte [Azure Storage Scalability and Performance Targets](storage-scalability-targets.md) para obtener más información.

## Precios y facturación

Las cuentas de Almacenamiento de blobs usan un nuevo modelo de precios para el Almacenamiento de blobs que se basa en el nivel de acceso. Si se usa una cuenta de Almacenamiento de blobs, se aplicarán las siguientes consideraciones de facturación:

- **Costos de almacenamiento**: además de la cantidad de datos almacenados, el costo de almacenamiento de datos varía según el nivel de acceso. El costo por gigabyte es menor en el nivel de acceso esporádico que en el nivel de acceso frecuente.
- **Costos de acceso a datos**: en el nivel de acceso esporádico se cobrará un cargo de acceso a datos por gigabyte para lecturas y escrituras.
- **Costos de transacciones**: en ambos niveles hay un cargo por transacción. Sin embargo, el costo por transacción en el nivel de acceso esporádico es mayor que en el nivel de acceso frecuente.
- **Costos de transferencia de datos de replicación geográfica**: solo se aplica a las cuentas con replicación geográfica configurada, donde se incluyen GRS y RA-GRS. La transferencia de datos de replicación geográfica incurre en un cargo por gigabyte.
- **Costos de transferencia de datos salientes**: las transferencias de datos salientes (los datos que se transfieren fuera de una región de Azure) incurren en la facturación del uso del ancho de banda por gigabyte, lo que es coherente con las cuentas de almacenamiento de uso general.
- **Cambio de nivel de acceso a**: el cambio del nivel de acceso de esporádico a frecuente conllevará un cargo igual a la lectura de todos los datos existentes en la cuenta de almacenamiento para cada transición. Por otro lado, el cambio del nivel de acceso de Frecuente a Esporádico será gratis.

> [AZURE.NOTE] Para que los usuarios puedan probar los nuevos niveles de almacenamiento y validar la funcionalidad después del inicio, el cargo por cambiar el nivel de acceso de esporádico a frecuente no se cobrará hasta el 30 de junio de 2016. A partir del 1 de julio de 2016, se aplicará un cargo a todas las transiciones que pasen de ser esporádicas a frecuentes. Para más información sobre el modelo de precios de las cuentas de Almacenamiento de blobs, consulte la página [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/). Para más información sobre los cargos por la transferencia de datos salientes, consulte la página [Detalles de precios de Transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/).

## Inicio rápido

En esta sección se mostrarán los siguientes escenarios con el Portal de Azure:

- Cómo crear una cuenta de Almacenamiento de blobs.
- Cómo administrar una cuenta de Almacenamiento de blobs.

### Uso del portal de Azure

#### Creación de una cuenta de Almacenamiento de blobs mediante el Portal de Azure

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, seleccione **Nuevo** -> **Datos + almacenamiento** -> **Cuenta de almacenamiento**.

3. Escriba un nombre para la cuenta de almacenamiento.

4. Seleccione **Resource Manager** como modelo de implementación.

5. Seleccione **Almacenamiento de blobs** como tipo de cuenta de almacenamiento.

6. Seleccione el nivel de acceso: **Frecuente** o **Esporádico**. El valor predeterminado es **Frecuente**.

7. Seleccione la opción de replicación de la cuenta de almacenamiento: **LRS**, **GRS** o **RA-GRS**. El valor predeterminado es **RA-GRS**. Para obtener más información sobre las opciones de replicación del Almacenamiento de Azure, consulte [Replicación de Almacenamiento de Azure](storage-redundancy.md).

8. Seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.

9. Especifique un nuevo grupo de recursos o seleccione un grupo de recursos existente. Para más información sobre los grupos de recursos, consulte [Uso del Portal de Azure para implementar y administrar los recursos de Azure](../azure-portal/resource-group-portal.md).

10. Seleccione la ubicación geográfica para la cuenta de almacenamiento.

11. Haga clic en **Crear** para crear la cuenta de almacenamiento.

#### Cambio del nivel de acceso en una cuenta de Almacenamiento de blobs mediante el Portal de Azure

1.	Inicie sesión en el [Portal de Azure](https://portal.azure.com) y navegue a su cuenta de almacenamiento.

2.	Haga clic en **Todas las opciones** y después en **Configuración** para ver o cambiar la configuración de la cuenta.

3.	Especificar el nivel de acceso deseado: **Frecuente** o **Esporádico**.

## Migración a cuentas de Almacenamiento de blobs

El objetivo de esta sección es ayudar a los usuarios a realizar una transición sin problemas a las cuentas de Almacenamiento de blobs. Las cuentas de Almacenamiento de blobs son especiales para almacenar solo blobs. Las cuentas de almacenamiento de uso general existentes, que también permiten almacenar tablas, colas y archivos, no se pueden convertir en cuentas de Almacenamiento de blobs. Esto significa que para poder utilizar las características que ofrece la cuenta de Almacenamiento de blobs será preciso crear nuevas cuentas de Almacenamiento de blobs con el nivel de acceso adecuado y migrar los datos existentes a las cuentas recién creadas.

### Planificación de la migración de los datos existentes

Si va a mover los datos a una cuenta de Almacenamiento de blobs, es probable que desee aprovechar el nivel de acceso esporádico para ahorrar en costos de almacenamiento en los datos que se usen menos frecuentemente. El primer paso en la planificación de la migración de los datos de una cuenta de Almacenamiento de blobs al nivel de acceso esporádico es evaluar el patrón de uso existente para determinar si dicha migración realmente supondrá un beneficio. En general, deseará conocer:

- El patrón de consumo del almacenamiento: ¿cuántos datos se almacenan y la cantidad de datos almacenados cambia mensualmente?
- Los patrones de acceso de almacenamiento: ¿qué cantidad de datos se leen de la cuenta, y se escriben en ella (incluidos los datos nuevos)? ¿Cuántas transacciones se utilizan para el acceso a los datos, y qué tipo de transacciones?

Para supervisar las cuentas de almacenamiento existentes y para recopilar estos datos, consulte [Habilitar las métricas de almacenamiento de Azure y ver sus datos](storage-enable-and-view-metrics.md). Con estos datos, puede usar la [calculadora de precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para ayudarle a calcular los costes.

### Migración de datos existentes

Puede utilizar los métodos siguientes para migrar los datos existentes a las cuentas de Almacenamiento de blobs desde una solución de almacenamiento local, desde un proveedor de almacenamiento en la nube de terceros o desde sus cuentas existentes de almacenamiento de uso general en Azure:

#### AzCopy

AzCopy es una utilidad de línea de comandos de Windows diseñada para la copia de datos de alto rendimiento a y desde Almacenamiento de Azure. AzCopy se puede usar para copiar datos en una cuenta de Almacenamiento de blobs desde cuentas de almacenamiento de uso general existentes o para cargar datos desde un sistema de almacenamiento local en una cuenta de Almacenamiento de blobs.

Para más información, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md).

#### Biblioteca de movimiento de datos

La biblioteca de movimiento de datos de Almacenamiento de Azure para .NET se basa en el marco de movimiento de datos principal que se utiliza con AzCopy. La biblioteca está diseñada para operaciones de transferencia de datos de alto rendimiento, confiables y fáciles similares a AzCopy. Esto permite sacar todo el partido a las características que proporciona AzCopy en su aplicación nativa sin tener que ejecutar y supervisar instancias externas de AzCopy.

Para más información, consulte [Azure Storage Data Movement Library for .Net (Biblioteca de movimiento de datos de Almacenamiento de Azure para .Net)](https://github.com/Azure/azure-storage-net-data-movement)

#### API de REST o biblioteca de cliente

Puede crear una aplicación personalizada para migrar sus datos a una cuenta de Almacenamiento de blobs con una de las bibliotecas de cliente de Azure o la API de REST de servicios de almacenamiento de Azure. Almacenamiento de Azure proporciona bibliotecas de cliente enriquecidas para varios lenguajes y aplicaciones como .NET, Java, C++, Node.JS, PHP, Ruby y Python. Las bibliotecas de cliente ofrecen capacidades avanzadas, como lógica de reintentos, registro y cargas paralelas. También se puede desarrollar directamente en la API de REST, a la que se puede llamar con cualquier lenguaje que genere solicitudes HTTP/HTTPS.

Para más información, consulte [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md).

## Preguntas más frecuentes

1.	**¿Siguen disponibles las cuentas de almacenamiento existentes?**

    Sí, las cuentas de almacenamiento existentes siguen estando disponibles y no se han cambiado sus precios ni sus funciones. No tienen la capacidad de elegir un nivel de acceso y no tendrán funcionalidades de organización en niveles en el futuro.

2.	**¿Por qué y cuándo conviene empezar a usar cuentas de Almacenamiento de blobs?**

    Las cuentas de Almacenamiento de blobs están especializadas en el almacenamiento de blobs y nos permiten introducir nuevas características centradas en los blobs. Es más, las cuentas de Almacenamiento de blobs son el método recomendado para el almacenamiento de blobs, ya que se introducirán funcionalidades futuras como el almacenamiento jerárquico y la organización en niveles basadas en este tipo de cuenta. Sin embargo, cada usuario debe decidir en qué momento desea realizar la migración en función de sus requisitos empresariales.

3.	**¿Se puede convertir una cuenta de almacenamiento existente en una cuenta de Almacenamiento de blogs?**

    No. Una cuenta de Almacenamiento de blobs es un tipo de cuenta de almacenamiento diferente y es preciso crearla desde cero y migrar los datos, como ya se ha explicado.

4.	**¿Se pueden almacenar objetos en dos niveles de acceso en la misma cuenta?**

    De momento, no. El atributo de nivel de acceso se establece a un nivel de cuenta y se aplica a todos los objetos de dicha cuenta.

5.	**¿Se puede cambiar el nivel de acceso en una cuenta de Almacenamiento de blobs?**

    Sí. El nivel de acceso en la cuenta de almacenamiento de puede cambiar. El cambio del nivel de acceso a nivel de cuenta se aplicará a todos los objetos almacenados en la cuenta. El cambio de nivel de acceso de frecuente a esporádico no supondrá ningún gasto, mientras que el cambio de esporádico a frecuente tendrá un costo por GB por la lectura de todos los datos de la cuenta.

6.	**¿Con qué frecuencia se puede cambiar el nivel de acceso en una cuenta de Almacenamiento de blobs?**

    Aunque no hay ningún límite en la frecuencia en que se puede cambiar el nivel de acceso, tenga en cuenta que el cambio del nivel de acceso de esporádico a frecuente supone unos gastos significativos. Se recomienda no cambiar el nivel de acceso con frecuencia.

7.	**¿Es preciso cambiar las aplicaciones existentes para usar las cuentas de Almacenamiento de blobs?**

    Las API de las cuentas de Almacenamiento de blobs son totalmente coherentes con las cuentas de almacenamiento de uso general, siempre que use la versión más reciente (2014-02-14) de la [API de REST de servicios de almacenamiento](https://msdn.microsoft.com/library/azure/dd894041.aspx). Si se utiliza una versión anterior del protocolo, será preciso actualizar la aplicación para que use la nueva versión, con el fin de poder trabajar sin problemas con ambos tipos de cuentas de almacenamiento. En general, siempre se recomienda utilizar la versión más reciente, independientemente del tipo de cuenta de almacenamiento que se use.

8.	**¿Habrá un cambio en la experiencia del usuario?**

    Una cuenta de Almacenamiento de blobs almacena solo blobs, pero, por lo demás, es muy similar a una cuenta de almacenamiento de uso general y hereda todas las características claves de Almacenamiento de Azure, incluyendo una gran durabilidad y disponibilidad, escalabilidad, rendimiento y seguridad. Aparte de las características y restricciones concretas de las cuentas de Almacenamiento de blobs y sus niveles de acceso ya indicados, el resto es igual.

<!---HONumber=AcomDC_0427_2016-->