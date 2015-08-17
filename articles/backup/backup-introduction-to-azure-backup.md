<properties
	pageTitle="Introducción a la Copia de seguridad de Azure"
	description="Este artículo proporciona información general sobre el servicio de copia de seguridad de Azure que permite a los clientes realizar copias de seguridad en Azure."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="trinadhk"/>

# Introducción a la Copia de seguridad de Azure
Este artículo proporciona una introducción de alto nivel de la solución de copia de seguridad integrada en la nube de Microsoft, que permite a los clientes realizar una copia de seguridad de sus datos presente en local o en Azure.

## ¿Qué es la Copia de seguridad de Azure?
La Copia de seguridad de Azure es un servicio de Azure de varios inquilinos que le permite realizar la copia de seguridad de los datos presentes en cualquier lugar: en local o en Azure. Reemplaza su solución de copia de seguridad local o remota existente por una oferta confiable, segura y rentable basada en la nube. También ofrece la flexibilidad de protección de los activos que se ejecutan en la nube. La Copia de seguridad de Azure se basa en una infraestructura de clase mundial escalable, duradera y de alta disponibilidad. Con esta solución, puede hacer una copia de seguridad de datos y aplicaciones desde sus servidores de System Center Data Protection Manager (SCDPM), los servidores de Windows, las máquinas cliente de Windows o las máquinas virtuales de Azure IaaS. La Copia de seguridad de Azure y SCDPM son las tecnologías fundamentales que constituyen la solución de copia de seguridad integrada en la nube de Microsoft.

> [AZURE.VIDEO what-is-azure-backup]

## Punto de diseño de la nube
Las soluciones de copia de seguridad tradicionales han evolucionado para tratar la nube como un extremo similar al disco o la cinta. Aunque este enfoque es simple, fácil de implementar y proporciona una experiencia coherente, tiene usos limitados y no aprovecha al máximo la plataforma subyacente. Esto se traduce en una solución ineficaz y costosa para los clientes finales. Al tratar Azure como "simplemente un extremo de almacenamiento", las soluciones de copia de seguridad son capaces de aprovechar la riqueza y la eficacia de la plataforma de nube pública. La Copia de seguridad de Azure, por otro lado, ofrece un verdadero servicio que usa construcciones de la nube para proporcionar una solución de copia de seguridad eficaz y asequible. Se integra con la solución de copia de seguridad local (SCDPM) para proporcionar una solución híbrida integral.

Las ventajas de este enfoque son:

+ Arquitectura de almacenamiento de nube eficaz que proporciona almacenamiento de datos de bajo coste y resistentes

+ No intrusivo, escala automática del servicio con garantías de alta disponibilidad

+ Forma coherente de realizar copias de seguridad en implementaciones locales, híbridas y de IaaS

Las características clave de esta solución son:

1. **Servicio confiable**: mediante la adopción de la Copia de seguridad de Azure, obtendrá un servicio de copia de seguridad que es altamente disponible. El servicio es para varios inquilinos, por lo que no tiene que preocuparse de administrar el proceso subyacente o el almacenamiento.

2. **Almacenamiento confiable**: la Copia de seguridad de Azure se basa en el almacenamiento en nube confiable, respaldado por exigentes SLA. No tiene que preocuparse por los gastos de capital u operación en el mantenimiento del almacenamiento. Además, tendrá la opción de realizar la copia de seguridad en almacenamiento LRS (almacenamiento localmente redundante) o GRS (almacenamiento de replicación de geoárea). Si bien LRS permite 3 copias de los datos de la misma geoárea que protege frente a errores de hardware local, GRS proporciona 3 copias adicionales (un total de 6 copias) en un centro de datos emparejado. Esto garantiza que los datos de copia de seguridad estén altamente disponibles. Incluso si hay un desastre en el sitio de Azure, los datos de copia de seguridad están seguros con nosotros.

3. **Seguridad**: los datos de copia de seguridad de Azure están cifrados desde el principio, durante la transmisión y en el almacenamiento en Azure. La clave de cifrado se almacena en el origen y nunca se transmite o se almacena en Azure. La clave de cifrado es necesaria para restaurar datos, y solo el cliente tiene acceso completo a los datos en el servicio.

4. **Protección remota**: en lugar de pagar soluciones de copia de seguridad de cinta fuera del sitio, los clientes pueden hacer la copia de seguridad en Azure, lo que proporciona una atractiva solución similar a la de la cinta, pero con un costo muy bajo.

5. **Simplicidad**: la Copia de seguridad de Azure proporciona una interfaz conocida que se puede escalar para proteger una implementación de cualquier tamaño. A medida que evoluciona el servicio, las características como la administración central le permitirán administrar su infraestructura de copia de seguridad desde una sola ubicación.

6. **Rentabilidad**: los precios de la Copia de seguridad de Azure incluyen una cuota de administración de copia de seguridad por instancia y el costo de almacenamiento (precio de blob de bloque) consumido en Azure. A diferencia de otra oferta de copia de seguridad basada en la nube, la Copia de seguridad de Azure no cobra a sus clientes por ninguna operación de restauración. Además, los clientes no tienen que pagar ningún costo de transferencia de datos de salida (saliente) durante una operación de restauración.

7. **Copia de seguridad en la nube**: Copia de seguridad de Azure ofrece copias de seguridad coherentes con las aplicaciones basadas en VSS de máquinas virtuales de IaaS de Azure que se ejecutan sin necesidad de apagar la máquina virtual. También puede realizar la copia de seguridad de máquinas virtuales de Linux en Azure con coherencia del sistema de archivos.


## Aplicación y cargas de trabajo

| Carga de trabajo | Máquina de origen | Solución de Copia de seguridad de Azure |
| --- | --- |---|
| Archivos y carpetas | Windows Server, cliente Windows | Agente de copia de seguridad de Azure |
| Archivos y carpetas | Windows Server, cliente Windows | System Center DPM |
| Máquina virtual de Hyper-V (Windows) | Windows Server | System Center DPM |
| Máquina virtual de Hyper-V (Linux) | Windows Server | System Center DPM |
| Microsoft SQL Server | Windows Server | System Center DPM |
| Microsoft SharePoint | Windows Server | System Center DPM |
| Microsoft Exchange | Windows Server | System Center DPM |
| Máquinas virtuales de IaaS de Azure (Windows) | - | Copia de seguridad de Azure || Máquinas virtuales de IaaS de Azure (Linux) | - | Copia de seguridad de Azure |

## Pasos siguientes
+ La pregunta frecuente sobre el servicio de Copia de seguridad de Azure aparece [aquí](backup-azure-backup-faq.md).
+ Visite el [Foro de Copia de seguridad de Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=August15_HO6-->