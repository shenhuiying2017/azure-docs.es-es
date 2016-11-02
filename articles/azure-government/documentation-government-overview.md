<properties
    pageTitle="Documentación de Azure Government | Microsoft Azure"
    description="Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="08/25/2016"
    ms.author="ryansoc"/>



#  <a name="azure-government-documentation-overview"></a>Información general sobre la documentación de Azure Government

##  <a name="introduction-to-azure-government-documentation"></a>Introducción a la documentación de Azure Government

En este sitio se describen las funcionalidades de los servicios de [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) y se proporcionan instrucciones generales aplicables a todos los clientes. Antes de incluir datos regulados específicamente en su suscripción de Azure Government, debe familiarizarse con las funcionalidades de Azure Government y, si tiene alguna pregunta, consultarla con su equipo de cuentas.

Para ver información actual sobre los servicios de Azure Government que se incluyen en acreditaciones y normas específicas, consulte la [página de cumplimiento del Centro de confianza de Microsoft Azure](http://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx) . Puede que también haya otros servicios de Microsoft disponibles pero no se encuentran dentro del ámbito de los servicios cubiertos por Azure Government, por lo que no se tratan en este documento. Los servicios de Azure Government podrían también permitirle usar diversos recursos, aplicaciones o servicios adicionales que proporcionan terceros, o Microsoft bajo términos de uso y directivas de privacidad independientes, que no se incluyen en el ámbito de este documento. El usuario es responsable de la revisión de los términos de las ofertas "complementarias", como las ofertas de Marketplace, a fin de asegurarse de que cumplen sus necesidades con respecto al cumplimiento.

Azure Government está disponible para entidades que administran los datos que están sujetos a determinados requisitos y regulaciones gubernamentales (como NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 y CJIS), donde se requiere el uso de Azure Government para cumplirlos. Los clientes de Azure Government están sujetos a una validación de idoneidad.

Las entidades que tengan dudas sobre su idoneidad para Azure Government deben consultar a su equipo de cuentas.

##  <a name="principles-for-securing-customer-data-in-azure-government"></a>Principios para proteger los datos del cliente en Azure Government

Azure Government proporciona una serie de características y servicios que puede usar para crear soluciones en la nube que satisfagan sus necesidades de datos regulados/controlados. Una solución de cliente compatible puede ser la implementación eficaz de las funcionalidades de Azure Government estándar, junto con unos sólidas procedimientos de seguridad de datos.
Al hospedar una solución en Azure Government, Microsoft controla muchos de estos requisitos en la infraestructura en la nube.

En el diagrama siguiente se muestra el modelo de defensa en profundidad de Azure. Por ejemplo, Microsoft ofrece infraestructura en la nube básica DDOS, junto con funcionalidades de cliente, por ejemplo, dispositivos de seguridad para necesidades de aplicación específicas de cliente DDOS.

![texto alternativo](./media/azure-government-Defenseindepth.png)

En esta página se describen los principios fundamentales para proteger servicios y aplicaciones, y se proporciona orientación y procedimientos recomendados sobre cómo aplicar estos principios; en otras palabras, cómo deben los clientes hacer un uso inteligente de Azure Government a fin de cumplir las obligaciones y responsabilidades necesarias para una solución que administra información ITAR.

Los principios generales para proteger los datos del cliente son los siguientes:
* Protección de datos del cliente mediante cifrado
* Administración de secretos
* Aislamiento para restringir el acceso a datos

##  <a name="protecting-customer-data-using-encryption"></a>Protección de datos del cliente mediante cifrado

La mitigación de los riesgos y el cumplimiento de las obligaciones normativas están fomentando cada vez más la atención y la importancia del cifrado de datos. Utilice una implementación de cifrado eficaz para mejorar las medidas de seguridad actuales de red y aplicación, y reducir el riesgo general de su entorno en la nube.

### <a name="<a-name="overview"></a>encryption-at-rest"></a><a name="Overview"></a>Cifrado en reposo
Cifrado de datos en reposo se aplica a la protección del contenido del cliente, incluido en el almacenamiento en disco. Hay varias maneras de hacerlo:

### <a name="<a-name="overview"></a>storage-service-encryption"></a><a name="Overview"></a>Cifrado del servicio de almacenamiento

Cifrado del servicio de Almacenamiento de Azure se habilita en el nivel de cuenta de almacenamiento, lo que conlleva que los blobs en bloques y los blobs en páginas se cifren automáticamente al escribir en Almacenamiento de Azure. Cuando se lean los datos desde el Almacenamiento de Azure, el servicio de almacenamiento los descifrará antes de devolverlos. Utilice este método para proteger los datos sin tener que modificar o agregar código a las aplicaciones.

### <a name="<a-name="overview"></a>azure-disk-encryption"></a><a name="Overview"></a>Cifrado de discos de Azure
El uso de Cifrado de discos de Azure permite cifrar los discos de datos y del sistema operativo usados por una máquina virtual de Azure. La integración con Almacén de claves de Azure le proporciona control y le ayuda a administrar las claves de cifrado del disco.

### <a name="<a-name="overview"></a>client-side-encryption"></a><a name="Overview"></a>Cifrado de cliente
Cifrado de cliente está integrado en las bibliotecas de cliente de almacenamiento de Java y de .NET, que pueden utilizar las API de Almacén de claves de Azure, por lo que su implementación resulta sencilla. Utilice el Almacén de claves de Azure para obtener acceso a los secretos del Almacén de claves de Azure para usuarios específicos mediante Azure Active Directory.

### <a name="<a-name="overview"></a>encryption-in-transit"></a><a name="Overview"></a>Cifrado en tránsito

El cifrado básico disponible para la conectividad con Azure Government es compatible con el protocolo de seguridad de nivel de transporte (TLS) 1.2 y los certificados X.509. Los algoritmos criptográficos del Estándar federal de procesamiento de información (FIPS) 140-2 de nivel 1 también se utilizan para las conexiones de red de infraestructura entre centros de datos de Azure Government.  Las máquinas virtuales de Windows Server 2012 R2 y Windows 8-plus y los recursos compartidos de archivos de Azure pueden usar SMB 3.0 para el cifrado entre la máquina virtual y el recurso compartido de archivos. Utilice Cifrado de cliente para cifrar los datos antes de transferirlos al almacenamiento en una aplicación cliente y descifrarlos una vez transferidos desde este servicio.

### <a name="<a-name="overview"></a>best-practices-for-encryption"></a><a name="Overview"></a>Procedimientos recomendados para el cifrado

* Máquinas virtuales de IaaS: utilice Cifrado de discos de Azure. Active el cifrado del servicio de Almacenamiento para cifrar los archivos VHD que se usan para hacer una copia de seguridad de estos discos en el Almacenamiento de Azure, pero esto solo cifra datos recién escritos. Esto significa que si crea una máquina virtual y habilita el cifrado del servicio de Almacenamiento en la cuenta de almacenamiento que contiene el archivo VHD, se cifrarán solo los cambios, no el archivo VHD original.
* Cifrado de cliente: es el método más seguro para cifrar los datos, porque estos se cifran antes del tránsito y en reposo. Sin embargo, requiere que agregue código a sus aplicaciones mediante el almacenamiento y quizá no desee hacerlo. En esos casos, puede utilizar HTTPS para los datos en tránsito y el Cifrado del servicio de almacenamiento para cifrar los datos en reposo. Cifrado de cliente supone también más carga en el cliente; así pues, debe tener en cuenta este factor en los planes de escalabilidad, especialmente si está cifrando y transfiriendo una gran cantidad de datos.

Para más información acerca de las opciones de cifrado en Azure, consulte la [Guía de seguridad de almacenamiento](/storage-security-guide).

##  <a name="protecting-customer-data-by-managing-secrets"></a>Protección de datos de cliente mediante la administración de secretos

Una administración de claves segura es fundamental para proteger los datos en la nube. Los clientes deben esforzarse por simplificar la administración de claves y mantener el control de las claves usadas por los servicios y aplicaciones en la nube para cifrar los datos.

### <a name="<a-name="overview"></a>best-practices-for-managing-secrets"></a><a name="Overview"></a>Procedimientos recomendados para la administración de secretos

* Use el Almacén de claves para minimizar los riesgos de los secretos expuestos mediante archivos de configuración codificados de forma rígida, scripts o en código fuente. El Almacén de claves de Azure cifra claves (por ejemplo, claves de cifrado para Cifrado de discos de Azure) y secretos (por ejemplo, contraseñas) almacenándolas en módulos de seguridad de hardware (HSM) validados de FIPS 140-2 nivel 2. Para tener mayor seguridad, puede importar o generar las claves en estos HSM.
* Las plantillas y el código de aplicación solo deben contener referencias URI a los secretos (lo que significa que los secretos reales no están en los repositorios de código, configuración o código fuente). Esto impide ataques de suplantación de identidad de claves en repositorios internos o externos, como los robots de recolección de GitHub.
* Utilice controles RBAC seguros dentro del Almacén de claves. Si un trabajador de confianza deja la empresa o se traslada a un nuevo grupo dentro de la empresa, se le debería impedir el acceso a los secretos.  

Para más información, consulte [Almacén de claves de Azure Government](/azure-government/azure-government-tech-keyvault)

##  <a name="isolation-to-restrict-data-access"></a>Aislamiento para restringir el acceso a datos

El aislamiento consiste en usar límites, segmentación y contenedores para restringir el acceso a datos solo para usuarios, servicios y aplicaciones autorizados. Por ejemplo, la separación entre los inquilinos es un mecanismo de seguridad esencial para plataformas multiinquilino en la nube como Microsoft Azure. El aislamiento lógico ayuda a impedir que un inquilino interfiera en las operaciones de cualquier otro inquilino.

### <a name="<a-name="overview"></a>environment-isolation"></a><a name="Overview"></a>Aislamiento de entorno
El entorno de Azure Government es una instancia física independiente del resto de la red de Microsoft. Esto se logra mediante una serie de controles físicos y lógicos, entre los que se incluyen la protección de barreras físicas con cámaras y dispositivos biométricos.  Uso de credenciales específicas y autenticación multifactor por parte del personal de Microsoft, lo que requiere un acceso lógico al entorno de producción.  Toda la infraestructura de servicios de Azure Government se encuentra en Estados Unidos.

#### <a name="<a-name="overview"></a>per-customer-isolation"></a><a name="Overview"></a>Aislamiento por cliente
Azure implementa el control de acceso de red y la segregación mediante aislamiento de VLAN, ACL, equilibradores de carga y filtros IP.

Los clientes pueden aislar aún más sus recursos mediante suscripciones, grupos de recursos, redes virtuales y subredes.

Para más información acerca del aislamiento en Microsoft Azure, consulte la [sección Aislamiento de la Guía de seguridad de Azure](/azure-security-getting-started/#isolation).

Para información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>



<!--HONumber=Oct16_HO2-->


