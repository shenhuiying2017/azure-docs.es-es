<properties 
 pageTitle="Tamaños de máquina virtual" 
 description="Enumera los distintos tamaños de máquinas virtuales y sus capacidades." 
 services="virtual-machines" 
 documentationCenter="" 
 authors="KBDAzure" 
 manager="timlt" 
 editor=""/>
<tags 
ms.service="virtual-machines" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="vm-multiple" 
 ms.workload="infrastructure-services"
 ms.date="05/28/2015" 
 ms.author="kathydav"/>

# Tamaños de máquinas virtuales

## Información general

En este artículo se describen los tamaños y las opciones disponibles para los recursos de proceso basados en la máquina virtual que puede usar para ejecutar las aplicaciones y cargas de trabajo. También proporciona las consideraciones de implementación que se deben tener en cuenta al planear usar estos recursos.

Las máquinas virtuales y servicios en la nube de Azure son dos de los distintos tipos de recursos de proceso que ofrece Azure. Para obtener más explicaciones, consulte [Cálculo de las opciones de hospedaje proporcionadas por Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926).

>[AZURE.NOTE]Para ver límites relacionados de Azure, consulte [Suscripción de Azure y límites de servicio, cuotas y restricciones](../azure-subscription-service-limits.md)

## Consideraciones de planeación

Las máquinas virtuales están disponibles en dos niveles: básico y estándar. Ambos tipos ofrecen opciones de tamaños, pero el nivel básico no proporciona algunas capacidades disponibles en el nivel estándar, como el equilibrio de carga y la escala automática. El nivel estándar de los tamaños estándar consta de la serie A, serie D, serie DS y serie G.

*   Las máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquinas virtuales de la serie D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

*   Las máquinas virtuales de la serie DS pueden usar almacenamiento Premium, que proporciona un almacenamiento de alto rendimiento y una baja latencia para cargas de trabajo con uso intensivo de E/S. Utiliza unidades de estado sólido (SSD) para hospedar los discos de una máquina virtual y ofrece una caché de disco SSD local. El almacenamiento Premium está en Vista previa y está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage-premium-storage-preview-portal.md)

*   Las máquinas virtuales de la serie G ofrecen el mayor tamaño y el mejor rendimiento, y se ejecutan en hosts con procesadores de la familia Intel Xeon E5 V3.

El tamaño de la máquina virtual afecta a los precios. El tamaño también afecta a la capacidad de procesamiento, memoria y almacenamiento de la máquina virtual. Los costes de almacenamiento se calculan por separado según las páginas utilizadas en la cuenta de almacenamiento. Para obtener más información, consulte [Precios de Máquinas virtuales](http://go.microsoft.com/fwlink/p/?LinkId=398570) y [Precios de Almacenamiento de Azure](http://azure.microsoft.com/pricing/details/storage/). Para obtener más información sobre discos en máquinas virtuales de Azure, consulte [Acerca de discos de máquina virtual en Azure](https://msdn.microsoft.com/library/jj672979).

Las consideraciones siguientes pueden ayudarle a decidirse por un tamaño:

*   El tamaño A0\Basic_A0 solo está disponible con Azure SDK versión 1.3 o posterior.  

*   A1\Basic_A1 es el tamaño mínimo recomendado para cargas de trabajo de producción.

*   Seleccione una máquina virtual con 4 u 8 núcleos de CPU al utilizar SQL Server Enterprise Edition.

*   Puede que algunos de los hosts físicos de los centros de datos de Azure no admitan tamaños de máquinas virtuales grandes, como A5 – A11. En consecuencia, puede ver el mensaje de error **No se pudo configurar la máquina virtual <machine name>** o **No se pudo crear la máquina virtual <machine name>** al cambiar el tamaño de una máquina virtual existente a un nuevo tamaño, al crear una nueva máquina virtual en una red virtual creada antes de 16 de abril de 2013 o al agregar una nueva máquina virtual a un servicio en la nube existente. Consulte el tema [Error: "No se pudo configurar la máquina virtual"](http://social.msdn.microsoft.com/Forums/WAVirtualMachinesforWindows/thread/9693f56c-fcd3-4d42-850e-5e3b56c7d6be) en el foro de soporte técnico para ver una lista de soluciones alternativas para cada escenario de implementación.

*   Los tamaños de máquina virtual A8/A10 y A9/A11 tienen las mismas capacidades. Las instancias de máquina virtual A8 y A9 incluyen otro adaptador de red que se conecta a una red de acceso de memoria directa remota (RDMA) para una rápida comunicación entre máquinas virtuales. Las instancias A8 y A9 están diseñadas para aplicaciones informáticas de alto rendimiento que requieren una comunicación constante y de baja latencia entre nodos durante la ejecución, por ejemplo, aplicaciones que utilizan la interfaz de paso de mensajes (MPI). Las instancias de máquina virtual A10 y A11 no incluyen el adaptador de red adicional. Las instancias A10 y A11 están diseñadas para aplicaciones informáticas de alto rendimiento que no requieren una comunicación constante y de baja latencia entre nodos, también conocidas como aplicaciones paramétricas o embarazosamente paralelas.

Las siguientes tablas muestran los tamaños y las capacidades que ofrecen cada uno.

>[AZURE.NOTE]La capacidad de almacenamiento se representa mediante 1024^3 bytes como unidad de medida para GB. En ocasiones, esto se conoce como gibibyte o definición de base 2 Al comparar los tamaños que utilizan distintos sistemas de base, tenga en cuenta que los tamaños de base 2 podrían parecer más pequeños que los de base 10. No obstante, para cualquier tamaño específico (como 1 GB), un sistema de base 2 ofrece más capacidad que un sistema de base 10, ya que 1024^3 es mayor que 1000^3.

## Nivel básico

|Tamaño: Portal de administración\cmdlets y API|Núcleos de CPU|Memoria|Tamaños máximos de disco: máquina virtual|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (300 por disco)|
|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|<p>SO = 1023 GB</p><p>Temporal = 20 GB</p>|1|1x300|
|A1\Basic_A1|1|1,75 GB|<p>SO = 1023 GB</p><p>Temporal = 40 GB</p>|2|2x300|
|A2\Basic_A2|2|3,5 GB|<p>SO = 1023 GB</p><p>Temporal = 60 GB</p>|4|4x300|
|A3\Basic_A3|4|7 GB|<p>SO = 1023 GB</p><p>Temporal = 120 GB</p>|8|8x300|
|A4\Basic_A4|8|14 GB|<p>SO = 1023 GB</p><p>Temporal = 240 GB</p>|16|16x300|

## Nivel estándar
### Serie A y serie D

|Tamaño: Portal de administración\cmdlets y API|Núcleos de CPU|Memoria|Tamaños máximos de disco: máquina virtual|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)|
|---|---|---|---|---|---|
|A0\extra pequeño|1|768 MB|<p>SO = 1023 GB</p><p>Temporal = 20 GB</p>|1|1x500|
|A1\pequeño|1|1,75 GB|<p>SO = 1023 GB</p><p>Temporal = 70 GB</p>|2|2 x 500|
|A2\mediano|2|3,5 GB|<p>SO = 1023 GB</p><p>Temporal = 135 GB</p>|4|4x500|
|A3\grande|4|7 GB|<p>SO = 1023 GB</p><p>Temporal = 285 GB</p>|8|8x500|
|A4\extra grande|8|14 GB|<p>SO = 1023 GB</p><p>Temporal = 605 GB</p>|16|16x500|
|A5(igual)|2|14 GB|<p>SO = 1023 GB</p><p>Temporal = 135 GB</p>|4|4x500|
|A6(igual)|4|28 GB|<p>SO = 1023 GB</p><p>Temporal = 285 GB</p>|8|8x500|
|A7(igual)|8|56 GB|<p>SO = 1023 GB</p><p>Temporal = 605 GB</p>|16|16x500|
|A8(igual)|8|56 GB|<p><p>SO = 1023 GB</p><p>Temporal = 382 GB</p><blockquote><p>Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</p></blockquote>|16|16x500|
|A9(igual)|16|112 GB|<p><p>SO = 1023 GB</p><p>Temporal = 382 GB</p><blockquote><p>Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</p></blockquote>|16|16x500|
|A10(igual)|8|56 GB|<p><p>SO = 1023 GB</p><p>Temporal = 382 GB</p><blockquote><p>Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</p></blockquote>|16|16x500|
|A11(igual)|16|112 GB|<p><p>SO = 1023 GB</p><p>Temporal = 382 GB</p><blockquote><p>Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</p></blockquote>|16|16x500|
|Standard_D1(igual)|1|3,5 GB|<p>SO = 1023 GB</p><p>Temporal (SSD) =50 GB</p>|2|2 x 500|
|Standard_D2(igual)|2|7 GB|<p>SO = 1023 GB</p><p>Temporal (SSD) =100 GB</p>|4|4x500|
|Standard_D3(igual)|4|14 GB|<p>SO = 1023 GB</p><p>Temporal (SSD) =200 GB</p>|8|8x500|
|Standard_D4(igual)|8|28 GB|<p>SO = 1023 GB</p><p>Temporal (SSD) =400 GB</p>|16|16x500|
|Standard_D11(igual)|2|14 GB|<p>SO = 1023 GB</p><p>Temporal (SSD) =100 GB</p>|4|4x500|
|Standard_D12(igual)|4|28 GB|<p>SO = 1023 GB</p><p>Temporal (SSD) =200 GB</p>|8|8x500|
|Standard_D13(igual)|8|56 GB|<p>SO = 1023 GB</p><p>Temporal (SSD) =400 GB</p>|16|16x500|
|Standard_D14(igual)|16|112 GB|<p>SO = 1023 GB</p><p>Temporal (SSD) =800 GB</p>|32|32x500|


### Nivel estándar: serie DS*

|Tamaño: Portal de administración\cmdlets y API|Núcleos de CPU|Memoria|Tamaños máximos de disco: máquina virtual|Discos máximos de datos (1023 GB cada uno)|Tamaño de caché (GB)|E/S de disco máx. y ancho de banda|
|---|---|---|---|---|---|---|
|Standard_DS1(igual)|1|3,5|<p>SO = 1023 GB</p><p>Disco SSD local = 7 GB</p>|2|43|<p>3,200</p><p>32 MB por segundo</p>|
|Standard_DS2(igual)|2|7|<p>SO = 1023 GB</p><p>Disco SSD local = 14 GB</p>|4|86|<p>6,400</p><p>64 MB por segundo</p>|
|Standard_DS3(igual)|4|14|<p>SO = 1023 GB</p><p>Disco SSD local = 28 GB</p>|8|172|<p>12,800</p><p>128 MB por segundo</p>|
|Standard_DS4(igual)|8|28|<p>SO = 1023 GB</p><p>Disco SSD local = 56 GB</p>|16|344|<p>25,600</p><p>256 MB por segundo</p>|
|Standard_DS11(igual)|2|14|<p>SO = 1023 GB</p><p>Disco SSD local = 28 GB</p>|4|72|<p>6,400</p><p>64 MB por segundo</p>|
|Standard_DS12(igual)|4|28|<p>SO = 1023 GB</p><p>Disco SSD local = 56 GB</p>|8|144|<p>12,800</p><p>128 MB por segundo</p>|
|Standard_DS13(igual)|8|56|<p>SO = 1023 GB</p><p>Disco SSD local = 112 GB</p>|16|288|<p>25,600</p><p>256 MB por segundo</p>|
|Standard_DS14(igual)|16|112|<p>SO = 1023 GB</p><p>Disco SSD local = 224 GB</p>|32|576|<p>50,000</p><p>512 MB por segundo</p>|

*Las operaciones de entrada/salida máximas por segundo (E/S) y el rendimiento (ancho de banda) posibles con una máquina virtual de la serie DS se ven afectadas por el tamaño del disco. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage-premium-storage-preview-portal.md).

### Nivel estándar: serie G

|Tamaño: Portal de administración\cmdlets y API|Núcleos de CPU|Memoria|Tamaños máximos de disco: máquina virtual|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)|
|---|---|---|---|---|---|
|Standard_G1(igual)|2|28 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 384 GB</p>|4|4 x 500|
|Standard_G2(igual)|4|56 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 768 GB</p>|8|8 x 500|
|Standard_G3(igual)|8|112 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 1,536 GB</p>|16|16 x 500|
|Standard_G4(igual)|16|224 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 3,072 GB</p>|32|32 x 500|
|Standard_G5(igual)|32|448 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 6,144 GB</p>|64|<p>64 x 500</p>|

### Otras referencias

[Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md)
 
[Sobre las instancias informáticas intensivas A8, A9, A10 y A11](http://go.microsoft.com/fwlink/p/?linkid=328042).
 

<!---HONumber=58_postMigration-->