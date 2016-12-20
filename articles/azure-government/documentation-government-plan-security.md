---
title: Seguridad de Azure Government | Microsoft Docs
description: "Proporciona información general de los servicios disponibles en Azure Government"
services: Azure-Government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
editor: 
ms.assetid: c3645bda-bf35-4232-a54d-7a0bfab2d594
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/18/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a9cc2bc044caddec63645932067f7a346945db78
ms.openlocfilehash: 11d0a67a4c368b50e2f2e8648c4fb2db8e0421c2


---
# <a name="security"></a>Seguridad
## <a name="principles-for-securing-customer-data-in-azure-government"></a>Principios para proteger los datos del cliente en Azure Government
Azure Government proporciona una serie de características y servicios que puede usar para crear soluciones en la nube que satisfagan sus necesidades de datos regulados/controlados. Una solución de cliente compatible puede ser la implementación eficaz de las funcionalidades de Azure Government estándar, junto con unos sólidas procedimientos de seguridad de datos.

Al hospedar una solución en Azure Government, Microsoft controla muchos de estos requisitos en la infraestructura en la nube.

En el diagrama siguiente se muestra el modelo de defensa en profundidad de Azure. Por ejemplo, Microsoft ofrece infraestructura en la nube básica DDOS, junto con funcionalidades de cliente, por ejemplo, dispositivos de seguridad para necesidades de aplicación específicas de cliente DDOS.

![texto alternativo](./media/azure-government-Defenseindepth.png)

En esta página se describen los principios fundamentales para proteger servicios y aplicaciones, y se proporciona orientación y procedimientos recomendados sobre cómo aplicar estos principios; en otras palabras, cómo deben los clientes hacer un uso inteligente de Azure Government a fin de cumplir las obligaciones y responsabilidades necesarias para una solución que administra información ITAR.

 Los principios generales para proteger los datos del cliente son los siguientes:

* Protección de datos del cliente mediante cifrado
* Administración de secretos
* Aislamiento para restringir el acceso a datos

### <a name="protecting-customer-data-using-encryption"></a>Protección de datos del cliente mediante cifrado
La mitigación de los riesgos y el cumplimiento de las obligaciones normativas están fomentando cada vez más la atención y la importancia del cifrado de datos. Utilice una implementación de cifrado eficaz para mejorar las medidas de seguridad actuales de red y aplicación, y reducir el riesgo general de su entorno en la nube.

#### <a name="encryption-at-rest"></a>Cifrado en reposo
Cifrado de datos en reposo se aplica a la protección del contenido del cliente, incluido en el almacenamiento en disco. Hay varias maneras de hacerlo:

#### <a name="storage-service-encryption"></a>Cifrado del servicio de almacenamiento
Cifrado del servicio de Almacenamiento de Azure se habilita en el nivel de cuenta de almacenamiento, lo que conlleva que los blobs en bloques y los blobs en páginas se cifren automáticamente al escribir en Almacenamiento de Azure. Cuando se lean los datos desde el Almacenamiento de Azure, el servicio de almacenamiento los descifrará antes de devolverlos. Utilice este método para proteger los datos sin tener que modificar o agregar código a las aplicaciones.

#### <a name="client-side-encryption"></a>Cifrado de cliente
Cifrado de cliente está integrado en las bibliotecas de cliente de almacenamiento de Java y de .NET, que pueden utilizar las API de Almacén de claves de Azure, por lo que su implementación resulta sencilla. Utilice el Almacén de claves de Azure para obtener acceso a los secretos del Almacén de claves de Azure para usuarios específicos mediante Azure Active Directory.

#### <a name="encryption-in-transit"></a>Cifrado en tránsito
El cifrado básico disponible para la conectividad con Azure Government es compatible con el protocolo de seguridad de nivel de transporte (TLS) 1.2 y los certificados X.509. Los algoritmos criptográficos del Estándar federal de procesamiento de información (FIPS) 140-2 de nivel 1 también se utilizan para las conexiones de red de infraestructura entre centros de datos de Azure Government.  Las máquinas virtuales de Windows Server 2012 R2 y Windows 8-plus y los recursos compartidos de archivos de Azure pueden usar SMB 3.0 para el cifrado entre la máquina virtual y el recurso compartido de archivos. Utilice Cifrado de cliente para cifrar los datos antes de transferirlos al almacenamiento en una aplicación cliente y descifrarlos una vez transferidos desde este servicio.

#### <a name="best-practices-for-encryption"></a>Procedimientos recomendados para el cifrado
* Máquinas virtuales de IaaS: utilice Cifrado de discos de Azure. Active el cifrado del servicio de Almacenamiento para cifrar los archivos VHD que se usan para hacer una copia de seguridad de estos discos en el Almacenamiento de Azure, pero esto solo cifra datos recién escritos. Esto significa que si crea una máquina virtual y habilita el cifrado del servicio de Almacenamiento en la cuenta de almacenamiento que contiene el archivo VHD, se cifrarán solo los cambios, no el archivo VHD original.
* Cifrado de cliente: es el método más seguro para cifrar los datos, porque estos se cifran antes del tránsito y en reposo. Sin embargo, requiere que agregue código a sus aplicaciones mediante el almacenamiento y quizá no desee hacerlo. En esos casos, puede utilizar HTTPS para los datos en tránsito y el Cifrado del servicio de almacenamiento para cifrar los datos en reposo. Cifrado de cliente supone también más carga en el cliente; así pues, debe tener en cuenta este factor en los planes de escalabilidad, especialmente si está cifrando y transfiriendo una gran cantidad de datos.

### <a name="protecting-customer-data-by-managing-secrets"></a>Protección de datos de cliente mediante la administración de secretos
Una administración de claves segura es fundamental para proteger los datos en la nube. Los clientes deben esforzarse por simplificar la administración de claves y mantener el control de las claves usadas por los servicios y aplicaciones en la nube para cifrar los datos.

#### <a name="best-practices-for-managing-secrets"></a>Procedimientos recomendados para la administración de secretos
* Use el Almacén de claves para minimizar los riesgos de los secretos expuestos mediante archivos de configuración codificados de forma rígida, scripts o en código fuente. El Almacén de claves de Azure cifra claves (por ejemplo, claves de cifrado para Cifrado de discos de Azure) y secretos (por ejemplo, contraseñas) almacenándolas en módulos de seguridad de hardware (HSM) validados de FIPS 140-2 nivel 2. Para tener mayor seguridad, puede importar o generar las claves en estos HSM.
* Las plantillas y el código de aplicación solo deben contener referencias URI a los secretos (lo que significa que los secretos reales no están en los repositorios de código, configuración o código fuente). Esto impide ataques de suplantación de identidad de claves en repositorios internos o externos, como los robots de recolección de GitHub.
* Utilice controles RBAC seguros dentro del Almacén de claves. Si un trabajador de confianza deja la empresa o se traslada a un nuevo grupo dentro de la empresa, se le debería impedir el acceso a los secretos.

Para más información, consulte la [documentación pública de Azure Key Vault](../key-vault/index.md).

### <a name="isolation-to-restrict-data-access"></a>Aislamiento para restringir el acceso a datos
El aislamiento consiste en usar límites, segmentación y contenedores para restringir el acceso a datos solo para usuarios, servicios y aplicaciones autorizados. Por ejemplo, la separación entre los inquilinos es un mecanismo de seguridad esencial para plataformas multiinquilino en la nube como Microsoft Azure. El aislamiento lógico ayuda a impedir que un inquilino interfiera en las operaciones de cualquier otro inquilino.

#### <a name="environment-isolation"></a>Aislamiento de entorno
El entorno de Azure Government es una instancia física independiente del resto de la red de Microsoft. Esto se logra mediante una serie de controles físicos y lógicos que incluyen lo siguiente:

* Seguridad de barreras físicas con cámaras y dispositivos biométricos.
* Uso de credenciales específicas y autenticación multifactor por parte del personal de Microsoft, lo que requiere un acceso lógico al entorno de producción.
* Toda la infraestructura de servicios de Azure Government se encuentra en Estados Unidos.

#### <a name="per-customer-isolation"></a>Aislamiento por cliente
Azure implementa el control de acceso de red y la segregación mediante aislamiento de VLAN, ACL, equilibradores de carga y filtros IP.

Los clientes pueden aislar aún más sus recursos mediante suscripciones, grupos de recursos, redes virtuales y subredes.

## <a name="screening"></a>Filtrado
La acreditación FedRAMP High y Department of Defense (DoD) Impact Level 4 anunciada recientemente. Esto ha generado la barra de seguridad y cumplimiento normativo en el entorno de Azure Government.

Ahora filtramos todos nuestros operadores en la comprobación de la agencia nacional con ley y crédito (NACLC) como se define en la sección 5.6.2.2 de la guía de requisitos de seguridad (SRG) de informática en la nube de DoD:

> [!NOTE]
> La investigación preliminar mínima necesaria para el personal de CSP con acceso a la información de Level 4 y 5 basada en una “confidencialidad no crítica” (p. ej., ADP-2 de DoD) es la comprobación de la agencia nacional con ley y crédito (NACLC) (para contratistas “confidenciales no críticos”) o la investigación preliminar de riesgo moderado (MBI) para una designación de posición de “riesgo moderado”.
> 
> 

En la siguiente tabla se resume nuestro filtrado actual para los operadores de Azure Government:

| Filtrados y comprobaciones preliminares de Azure Gov | Description |
| --- | --- |
| Ciudadanía estadounidense |Comprobación de ciudadanía estadounidense. |
| Comprobación preliminar en la nube de Microsoft (cada dos años) |Búsqueda del número del seguro social, comprobación de antecedentes penales, lista de la Oficina de Control de Bienes Extranjeros (OFAC), lista del Departamento de Industria y Seguridad (BIS) y lista de personas excluidas de la Oficina de Controles Comerciales de Defensa. |
| Comprobación de la agencia nacional con ley y crédito (NACLC) (cada cinco años) |Agrega la comprobación preliminar de huella digital en las bases de datos del FBI. Para obtener información adicional, vaya al<a href="https://www.opm.gov/investigations/background-investigations/federal-investigations-notices/1997/fin97-02/"> sitio de Office Personnel Management</a>. |
| <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/CJIS"> Servicios de información de justicia penal (CJIS) </a> |CJIS es un filtrado del gobierno local y del FBI que procesa registros de huella digital y valida los antecedentes penales del personal operativo al que podría proporcionarse acceso a datos fundamentales de información de justicia penal (CJI).  Cada estado realiza su propia comprobación preliminar y posterior aprobación de todos los empleados que pueden tener acceso a CJI. |

En el caso del personal de operaciones de Azure, se aplican los siguientes principios de acceso:

* Las tareas están claramente definidas, con responsabilidades independientes para solicitar, aprobar e implementar cambios.
* El acceso tiene lugar a través de interfaces definidas que tienen una funcionalidad específica.
* El acceso es Just-In-Time (JIT) y solo se concede para cada incidente o para un evento de mantenimiento específico, y siempre con una duración limitada.
* El acceso está basado en reglas, con roles definidos a los que solo se asignan los permisos necesarios para la solución de problemas.

Los estándares de filtrado incluyen la validación de la ciudadanía estadounidense de todo el personal operativo y de soporte técnico de Microsoft antes de concederse el acceso a los sistemas hospedados en Azure Government. El personal de soporte técnico que debe transferir datos usa las funcionalidades seguras en Azure Government. La transferencia de datos segura requiere un conjunto independiente de credenciales de autenticación para obtener acceso. Por ejemplo, para tener acceso a los metadatos del sistema, el personal de operaciones usa herramientas de administración interna basadas en web específicas, API de solo lectura y elevación de JIT.

## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>




<!--HONumber=Nov16_HO3-->


