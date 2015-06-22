<properties 
 pageTitle="Tamaños de los servicios en la nube" 
 description="Muestra los diferentes tamaños para los roles web y de trabajo." 
 services="cloud-services" 
 documentationCenter="" 
 authors="Thraka" 
 manager="timlt" 
 editor=""/>
<tags 
 ms.service="cloud-services" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="na" 
 ms.workload="tbd"
 ms.date="05/20/2015" 
 ms.author="adegeo"/>
 
# Tamaños de los servicios en la nube

## Información general 

En este tema se describen las opciones y los tamaños disponibles para las instancias de rol de servicio en la nube (roles web y roles de trabajo). También ofrece consideraciones de implementación que hay que tener en cuenta a la hora de planear usar estos recursos.

Las máquinas virtuales y los servicios en la nube de Azure son dos de los distintos tipos de recursos de proceso ofrecidos por Azure. Para obtener una explicación, vea [Opciones de hospedaje de proceso ofrecidas por Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926).

>[AZURE.NOTE]Para ver los límites relacionados con Azure, vea [Suscripción de Azure y límites de servicio, cuotas y restricciones](../azure-subscription-service-limits.md)

###Tamaños de instancias de rol web y de trabajo

Las siguientes consideraciones pueden ayudarle a decidirse por un tamaño:

*   Las instancias se pueden configurar ahora para usar una máquina virtual de la serie D. Están diseñadas para ejecutar aplicaciones que exigen una mayor capacidad de proceso y un rendimiento de disco temporal. Las máquinas virtuales de la serie D ofrecen procesadores más rápidos, una mayor proporción de memoria al núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, vea el anuncio en el blog de Azure, [Nuevos tamaños de máquina virtual de la serie D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

*   Los roles web y los roles de trabajo requieren más espacio en el disco temporal que las máquinas virtuales de Azure debido a los requisitos del sistema. Los archivos del sistema reservan 4 GB de espacio para el archivo de paginación de Windows y 2 GB de espacio para el archivo de volcado de memoria de Windows.

*   El disco del sistema operativo contiene el sistema operativo invitado de Windows e incluye la carpeta Archivos de programa (incluidas las instalaciones realizadas a través de tareas de inicio a menos que especifique otro disco), cambios del Registro, la carpeta System32 y .NET Framework.

*   El disco de recursos local contiene registros de Azure y archivos de configuración, Diagnósticos de Azure (que incluye sus registros de IIS) y cualquier recurso de almacenamiento local que defina.

*   El disco de aplicaciones es donde se extrae su .cspkg e incluye su sitio web, los archivos binarios, el proceso de host de rol, las tareas de inicio, web.config, etc.

*   Los tamaños de máquina virtual A8/A10 y A9/A11 tienen las mismas capacidades. Las instancias de máquina virtual A8 y A9 incluyen un adaptador de red adicional que está conectado a una red de acceso directo a memoria remota (RDMA) para la comunicación rápida entre máquinas virtuales. Las instancias A8 y A9 están diseñadas para aplicaciones informáticas de alto rendimiento que requieren una comunicación constante y de baja latencia entre nodos durante la ejecución, por ejemplo, las aplicaciones que usan la interfaz de especificación de mensajes (MPI). Las instancias de máquina virtual A10 y A11 no incluyen el adaptador de red adicional. Las instancias A10 y A11 están diseñadas para aplicaciones informáticas de alto rendimiento que no requieren la comunicación constante y de baja latencia entre nodos, también conocidas como aplicaciones paramétricas o embarazosamente paralelas.

|Tamaño|Núcleos<br>de CPU|Memoria|Tamaños de disco|
|---|---|---|---|
|ExtraSmall|1|768 MB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 19 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|Pequeña|1|1,75 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 224 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|Mediano|2|3,5 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 489 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|Grande|4|7 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 999 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|ExtraLarge|8|14 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 2.039 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|A5|2|14 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 489 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|A6|4|28 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 999 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|A7|8|56 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 2.039 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>
|A8|8|56 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 1,77 TB</p><p>Aplicaciones = aprox. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</p></blockquote>|
|A9|16|<p>112 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 1,77 TB</p><p>Aplicaciones = aprox. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</p></blockquote>|
|A10|8|<p>56 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 1,77 TB</p><p>Aplicaciones = aprox. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</p></blockquote>|
|A11|16|<p>112 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 1,77 TB</p><p>Aplicaciones = aprox. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</p></blockquote>|
|Standard_D1|1|3,5 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 50 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|Standard_D2|2|7 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 100 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|Standard_D3|4|14 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 200 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|Standard_D4|8|28 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 400 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|Standard_D11|2|14 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 100 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|Standard_D12|4|28 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 200 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|Standard_D13|8|56 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 400 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|
|Standard_D14|16|112 GB|<p>SO = tamaño del SO invitado</p><p>Recurso local = 800 GB</p><p>Aplicaciones = aprox. 1,5 GB</p>|

### Otras referencias

#### Conceptos

[Configurar un servicio en la nube para Azure](https://msdn.microsoft.com/library/hh124108)

[Configurar tamaños para los servicios en la nube](https://msdn.microsoft.com/library/ee814754)

#### Otros recursos

[Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md)

 [Acerca las instancias informáticas intensivas A8, A9, A10 y A11](http://go.microsoft.com/fwlink/p/?linkid=328042).

 [Tamaños de máquinas virtuales](../virtual-machines-size-specs.md)

<!---HONumber=58--> 