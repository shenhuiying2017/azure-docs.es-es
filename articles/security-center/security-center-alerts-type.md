---
title: Alertas de seguridad por tipo en Azure Security Center | Microsoft Docs
description: "En este artículo se describen los distintos tipos de alertas de seguridad disponibles en Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 19f71e0d5a8a4642b86ae60a3ab2a4042fa2990e
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017

---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Comprensión de las alertas de seguridad en Azure Security Center
Este artículo ayuda a comprender los distintos tipos de alertas de seguridad y aspectos relacionados disponibles en Azure Security Center. Para más información sobre cómo administrar las alertas y los incidentes, consulte [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md).

> [!NOTE]
> Para configurar las detecciones avanzadas, actualice a la versión estándar de Azure Security Center. Hay disponible una versión de evaluación gratuita de 60 días. Para actualizar a este nivel, seleccione el **plan de tarifa** en la [directiva de seguridad](security-center-policies.md). Para más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).
>

## <a name="what-type-of-alerts-are-available"></a>¿Qué tipo de alertas está disponible?
Azure Security Center usa una variedad de [funcionalidades de detección](security-center-detection-capabilities.md) para alertar a los clientes de posibles ataques contra sus entornos. Estas alertas contienen información valiosa sobre lo que desencadenó la alerta, los recursos objetivo y el origen del ataque. La información incluida en las alertas varía según el tipo de análisis utilizado para detectar la amenaza. Los incidentes también pueden contener información contextual adicional que puede resultar útil para la investigación de la amenaza.  En este artículo se proporciona información acerca de los siguientes tipos de alertas:

* Análisis del comportamiento de la máquina virtual (VMBA)
* Análisis de red
* Análisis de recursos
* Información contextual

## <a name="virtual-machine-behavioral-analysis"></a>Análisis del comportamiento de la máquina virtual
Azure Security Center puede utilizar el análisis de comportamiento para identificar los recursos en peligro a partir del análisis de registros de eventos de la máquina virtual. Por ejemplo, eventos de creación de proceso y eventos de inicio de sesión. Además, existe una correlación con otras señales que se comprueban para fundamentar las pruebas de que se trata una campaña de gran difusión.

> [!NOTE]
> Para más información acerca de cómo actúan las funcionalidades de detección de Security Center, consulte [Funcionalidades de detección de Azure Security Center](security-center-detection-capabilities.md).
>

### <a name="crash-analysis"></a>Análisis de bloqueos
El análisis de la memoria de volcado de bloqueos es un método utilizado para detectar malware sofisticado que puede eludir las soluciones de seguridad tradicionales. Diversos tipos de malware intentan reducir la posibilidad de ser detectados por productos antivirus no escribiendo nunca en el disco o mediante el cifrado de los componentes de software escritos en el disco. Esto hace que el malware sea difícil de detectar mediante los enfoques tradicionales de antimalware. Sin embargo, este tipo de malware se puede detectar mediante un análisis de memoria, ya que, para poder funcionar, tiene que dejar rastros en la memoria.

En el momento en que el software se bloquea, un volcado de memoria captura una porción de la memoria. El bloqueo puede deberse a malware o problemas de aplicaciones generales o del sistema. Mediante el análisis de la memoria durante el volcado, Security Center puede detectar las técnicas utilizadas para aprovechar las vulnerabilidades del software, acceder a información confidencial y permanecer subrepticiamente en una máquina en peligro. Esto se consigue con un impacto mínimo en el rendimiento de los hosts, ya que el análisis se realiza en el back-end de Security Center.

Los siguientes campos son comunes a los ejemplos de alerta de volcado de memoria que aparecen más adelante en este artículo:

* DUMPFILE (ARCHIVO DE VOLCADO): nombre del archivo de volcado
* PROCESSNAME (NOMBRE DEL PROCESO): nombre del proceso de bloqueo
* PROCESSVERSION (VERSIÓN DEL PROCESO): versión del proceso de bloqueo

### <a name="shellcode-discovered"></a>Detectado shellcode
Shellcode es la carga que se ejecuta después de que un malware haya aprovechado una vulnerabilidad en el software. Esta alerta indica que el análisis del volcado de memoria ha detectado código ejecutable que muestra un comportamiento normalmente realizado mediante cargas malintencionadas. Aunque el software no malintencionado puede tener este comportamiento, no es típico en las prácticas de desarrollo de software normal.

Este ejemplo de alerta de shellcode proporciona el siguiente campo adicional:

* ADDRESS: ubicación de la memoria del shellcode.

A continuación se muestra un ejemplo de este tipo de alerta:

![Alerta de shellcode](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>Detectado secuestro de módulo
Windows usa las bibliotecas de vínculos dinámicos (DLL) para que el software pueda utilizar la funcionalidad del sistema Windows común. El secuestro de DLL se produce cuando el malware cambia el orden de carga de las DLL para realizar cargas malintencionadas en la memoria, donde se puede ejecutar código arbitrario. Esta alerta indica que el análisis de volcado de memoria ha detectado un módulo con el mismo nombre que se carga desde dos rutas de acceso diferentes. Una de las rutas de acceso de carga procede de una ubicación binaria común del sistema de Windows.

Los desarrolladores de software legítimo ocasionalmente cambian el orden de carga DLL por razones no malintencionadas, como instrumentar, extender el sistema operativo Windows o aplicaciones de Windows. Para ayudar a diferenciar entre los cambios de código malintencionados y potencialmente benignos en el orden de carga de la DLL, Azure Security Center comprueba si un módulo cargado se ajusta a un perfil sospechoso. El resultado de esta comprobación se indica en el campo "SIGNATURE" (FIRMA) de la alerta y se refleja en la gravedad de la alerta, su descripción y los pasos de corrección. Para comprobar si el módulo es legítimo o malintencionado, analícelo en la copia del disco del módulo secuestrado. Por ejemplo, puede comprobar la firma digital del archivo, o ejecutar una exploración antivirus.

Además de los campos comunes que se describen en la anterior sección, "Shellcode detectado", esta alerta proporciona los siguientes campos:

* SIGNATURE (FIRMA): indica si el módulo secuestrador se ajusta a un perfil de comportamiento sospechoso.
* HIJACKEDMODULE (MÓDULO SECUESTRADO): nombre del módulo del sistema Windows secuestrado.
* HIJACKEDMODULEPATH (RUTA DEL MÓDULO SECUESTRADO): ruta de acceso del módulo del sistema Windows secuestrado.
* HIJACKINGMODULEPATH (RUTA DEL MÓDULO SECUESTRADOR): ruta de acceso del módulo secuestrador.

A continuación se muestra un ejemplo de este tipo de alerta:

![Alerta de secuestro de módulos](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Detectado módulo de Windows enmascarado
El malware puede usar nombres comunes de archivos binarios (por ejemplo, SVCHOST.EXE) o de módulos (por ejemplo, NTDLL.DLL) del sistema Windows para *mezclarse* y ocultar la naturaleza del software malintencionado a los administradores de sistemas. Esta alerta indica que el análisis del volcado de memoria ha detectado que el archivo de volcado contiene módulos que utilizan nombres de módulo del sistema Windows, pero no cumplen otros criterios típicos de módulos de Windows. El análisis de la copia en disco del módulo enmascarado puede proporcionar más información acerca de la naturaleza legítima o malintencionada de este módulo. El análisis puede incluir:

* La confirmación de que el archivo en cuestión se distribuye como parte de un paquete de software legítimo.
* La comprobación de la firma digital del archivo.
* La ejecución de un examen antivirus del archivo.

Además de los campos comunes que se describen en la anterior sección, "Shellcode detectado", esta alerta proporciona los siguientes campos adicionales:

* DETAILS (DETALLES): describe si los metadatos de los módulos son válidos y si el módulo se cargó desde una ruta de acceso del sistema.
* NAME (NOMBRE): nombre del módulo de Windows enmascarado.
* PATH (RUTA DE ACCESO): ruta de acceso para el módulo de Windows enmascarado.

Esta alerta también extrae y muestra determinados campos del encabezado PE del módulo, como "CHECKSUM" (SUMA DE COMPROBACIÓN) y "TIMESTAMP" (MARCA DE TIEMPO). Estos campos solo se muestran si los campos están presentes en el módulo. Consulte la [especificación de Microsoft PE y COFF](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) para más información sobre estos campos.

A continuación se muestra un ejemplo de este tipo de alerta:

![Alerta de Windows enmascarado](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>Detectados archivos binarios del sistema modificados
El malware puede modificar archivos binarios del sistema básicos para un acceso clandestino a los datos o persistir clandestinamente en un sistema en peligro. Esta alerta indica que el análisis del volcado de memoria ha detectado que se han modificado archivos binarios básicos del sistema operativo Windows en la memoria o el disco.

Los desarrolladores de software legítimo modifican ocasionalmente módulos del sistema en la memoria por razones no malintencionados, como desvíos o compatibilidad de aplicaciones. Para ayudar a diferenciar entre módulos malintencionados y potencialmente legítimos, Azure Security Center comprueba si el módulo modificado se ajusta a un perfil sospechoso. El resultado de esta comprobación se indica mediante la gravedad de la alerta, su descripción y los pasos de corrección.

Además de los campos comunes que se describen en la anterior sección, "Shellcode detectado", esta alerta proporciona los siguientes campos adicionales:

* MODULENAME (NOMBRE DEL MÓDULO): nombre del archivo binario del sistema modificado.
* MODULEVERSION (VERSIÓN DEL MÓDULO): versión del archivo binario del sistema modificado.

A continuación se muestra un ejemplo de este tipo de alerta:

![Alerta binaria del sistema](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>Proceso sospechoso ejecutado
Security Center identifica un proceso sospechoso que se ejecuta en la máquina virtual de destino y desencadena una alerta. La detección no busca el nombre específico, sino el parámetro del archivo ejecutable. Por lo tanto, incluso si el atacante cambia el nombre del archivo ejecutable, Security Center todavía puede detectar el proceso sospechoso.

A continuación se muestra un ejemplo de este tipo de alerta:

![Alerta de proceso sospechoso](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domain-accounts-queried"></a>Múltiples cuentas de dominio consultadas
Security Center puede detectar múltiples intentos de consultar cuentas de dominio de Active Directory, que es algo que suelen hacer los atacantes durante el reconocimiento de la red. Los atacantes pueden aprovechar esta técnica para consultar el dominio con el fin de identificar los usuarios, las cuentas de los administradores de dominio, los equipos que son controladores de dominio y, también, una posible relación de confianza de dominio con otros dominios.

A continuación se muestra un ejemplo de este tipo de alerta:

![Alerta de cuenta con múltiples dominios](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

### <a name="local-administrators-group-members-were-enumerated"></a>Se enumeraron los miembros del grupo Administradores locales

Security Center va a desencadenar una alerta cuando se active el evento de seguridad 4798, en Windows Server 2016 y Windows 10. Esto sucede cuando se enumeran los grupos de administradores locales, que es algo que los atacantes suelen hacer durante el reconocimiento de la red. Los atacantes pueden aprovechar esta técnica para consultar la identidad de los usuarios con privilegios administrativos.

A continuación se muestra un ejemplo de este tipo de alerta:

![Administrador local](./media/security-center-alerts-type/security-center-alerts-type-fig14-new.png)

### <a name="anomalous-mix-of-upper-and-lower-case-characters"></a>Mezcla anómala de mayúsculas y minúsculas

Security Center desencadenará una alerta cuando detecte el uso de una mezcla de mayúsculas y minúsculas en la línea de comandos. Algunos atacantes pueden utilizar esta técnica para eludir una regla de la máquina basada en hash o que distinga mayúsculas de minúsculas.

A continuación se muestra un ejemplo de este tipo de alerta:

![Mezcla anómala](./media/security-center-alerts-type/security-center-alerts-type-fig15-new.png)

### <a name="suspected-kerberos-golden-ticket-attack"></a>Sospecha de ataque de golden ticket de Kerberos

Un atacante puede usar una clave [krbtgt](https://technet.microsoft.com/library/dn745899.aspx) en peligro para crear "golden tickets" de Kerberos, lo que le permitiría suplantar a cualquier usuario que desee. Security Center va a desencadenar una alerta cuando detecte este tipo de actividad.

> [!NOTE] 
> Para más información sobre el golden ticket de Kerberos, consulte [Windows 10 credential theft mitigation guide](http://download.microsoft.com/download/C/1/4/C14579CA-E564-4743-8B51-61C0882662AC/Windows%2010%20credential%20theft%20mitigation%20guide.docx) (Guía de mitigación del robo de credenciales de Windows 10).

A continuación se muestra un ejemplo de este tipo de alerta:

![Golden ticket](./media/security-center-alerts-type/security-center-alerts-type-fig16-new.png)

### <a name="suspicious-account-created"></a>Cuenta sospechosa creada

Security Center desencadenará una alerta cuando se cree una cuenta que se parezca mucho a una cuenta integrada existente con privilegios administrativos. Los atacantes pueden usar esta técnica para crear una cuenta no autorizada para evitar que se les detecte con una comprobación realizada por un usuario.
 
A continuación se muestra un ejemplo de este tipo de alerta:

![Cuenta sospechosa](./media/security-center-alerts-type/security-center-alerts-type-fig17-new.png)

### <a name="suspicious-firewall-rule-created"></a>Regla de firewall sospechosa creada

Los atacantes podrían intentar eludir la seguridad del host mediante la creación de reglas de firewall personalizadas que permitan que aplicaciones malintencionadas se comuniquen con el comando y control o lancen ataques a la red a través del host en peligro. Security Center desencadenará una alerta cuando detecte que se ha creado una regla de firewall a partir de un archivo ejecutable en una ubicación sospechosa.
 
A continuación se muestra un ejemplo de este tipo de alerta:

![Regla de firewall](./media/security-center-alerts-type/security-center-alerts-type-fig18-new.png)

### <a name="suspicious-combination-of-hta-and-powershell"></a>Combinación sospechosa de HTA y PowerShell

Security Center desencadenará una alerta cuando detecte que un host de aplicación HTML (HTA) de Microsoft esté iniciando comandos de PowerShell. Se trata de una técnica utilizada por los atacantes para iniciar scripts malintencionados de PowerShell.
 
A continuación se muestra un ejemplo de este tipo de alerta:

![HTA y PS](./media/security-center-alerts-type/security-center-alerts-type-fig19-new.png)


## <a name="network-analysis"></a>Análisis de red
La detección de amenazas de Security Center recopila automáticamente información de seguridad de su tráfico (exportación de la información de flujo del protocolo de Internet) de IPFIX de Azure. Después, analiza estos datos (a menudo, relacionando la información de diferentes orígenes) para identificar las amenazas.

### <a name="suspicious-outgoing-traffic-detected"></a>Suspicious outgoing traffic detected (Tráfico saliente sospechoso detectado)
Se pueden detectar dispositivos de red y perfiles de la misma manera que otros tipos de sistemas. Los atacantes suelen comenzar con la exploración de puertos o con el barrido de puertos. En el ejemplo siguiente, obtendrá el tráfico de shell seguro (SSH) sospechoso de una máquina virtual. En este escenario, es posible un ataque por fuerza bruta SSH o por ataque circular contra un recurso externo.

![Alerta de tráfico saliente sospechoso](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

Esta alerta proporciona información que puede usar para identificar el recurso que se usó para iniciar este ataque. Esta alerta también proporciona información para identificar el equipo en peligro, la hora de detección, además del protocolo y puerto que se utilizó. Esta hoja también proporciona una lista de pasos de corrección que pueden utilizarse para mitigar este problema.

### <a name="network-communication-with-a-malicious-machine"></a>Network communication with a malicious machine (Comunicación de red con una máquina malintencionada)
Mediante el aprovechamiento de las fuentes de inteligencia de amenazas de Microsoft, Azure Security Center puede detectar equipos en peligro que se comunican con direcciones IP malintencionadas. En muchos casos, la dirección malintencionada es un centro de comando y control. En este caso, Security Center ha detectado que la comunicación se realizó mediante el malware Pony Loader (también conocido como [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![alerta de comunicación de red](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

Esta alerta proporciona información que le permite identificar el recurso que se usó para iniciar este ataque, el recurso atacado, la dirección IP víctima, la dirección IP del atacante y la hora de detección.

> [!NOTE]
> Las direcciones IP activas se quitaron de esta captura de pantalla por privacidad.
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>Posible ataque de denegación de servicio de salida detectado
El tráfico de red anómalo originado en una máquina virtual puede hacer que Security Center desencadene un tipo de ataque por potencial denegación de servicio.

A continuación se muestra un ejemplo de este tipo de alerta:

![Denegación de servicio saliente](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>Análisis de recursos
El análisis de los recursos de Security Center se centra en los servicios de la plataforma como servicio (PaaS), como la integración con la característica [detección de amenazas de Azure SQL Database](../sql-database/sql-database-threat-detection.md). En función de los resultados del análisis de estas áreas, Security Center desencadena una alerta relacionada con los recursos.

### <a name="potential-sql-injection"></a>Posible inyección de código SQL
La inyección de código SQL es un ataque en el que se inserta código malintencionado en cadenas que posteriormente se pasan a un servidor SQL Server para su análisis y ejecución. Como el servidor SQL Server ejecuta todas las consultas sintácticamente válidas que recibe, deben revisarse todos los procedimientos que crean instrucciones SQL en busca de vulnerabilidades por inyección de código. La detección de amenazas de SQL utiliza el aprendizaje automático, el análisis del comportamiento y la detección de anomalías para determinar eventos sospechosos que podrían estar teniendo lugar en sus instancias de Azure SQL Database. Por ejemplo:

* Un antiguo empleado ha intentado acceder a la base de datos
* Ataques de inyección de código SQL
* Acceso inusual a la base de datos de producción de un usuario en casa

![Alerta de posible inyección de código SQL](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

La información de esta alerta puede utilizarse para identificar el recurso atacado, la hora de detección y el estado del ataque. También proporciona un vínculo a más acciones de investigación.

### <a name="vulnerability-to-sql-injection"></a>Vulnerabilidad a la inyección de código SQL
Esta alerta se desencadena cuando se detecta un error de aplicación en una base de datos. Esta alerta puede indicar una posible vulnerabilidad a los ataques de inyección de código SQL.

![Alerta de posible inyección de código SQL](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Acceso inusual desde una ubicación desconocida
Esta alerta se desencadena cuando se detecta un evento de acceso desde una dirección IP desconocida en el servidor, que no se ha visto en el último período.

![Alerta de acceso inusual](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="contextual-information"></a>Información contextual
Durante una investigación, los analistas necesitan más contexto para llegar a un veredicto sobre la naturaleza de la amenaza y cómo mitigarla.  Por ejemplo, se detecta una anomalía de red, pero sin saber qué más sucede en la red o con respecto al recurso objetivo, es muy difícil conocer las medidas que se deben tomar a continuación. Para ayudar a los investigadores con esto, los incidentes de seguridad pueden incluir artefactos, eventos e información relacionada. La disponibilidad de información adicional variará según el tipo de amenaza detectada y la configuración de su entorno y no estará disponible para todos los incidentes de seguridad.

Si hay disponible información adicional, se mostrará en el incidente de seguridad por debajo de la lista de alertas. Podría contener datos como:

- Eventos de registro borrados
- Dispositivos Plug and Play desconocidos
- Alertas que no se pueden activar 

![Alerta de acceso inusual](./media/security-center-alerts-type/security-center-alerts-type-fig20.png) 


## <a name="see-also"></a>Consulte también
En este artículo, ha aprendido acerca de los distintos tipos de alertas de seguridad en Security Center. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Control de incidentes de seguridad en Azure Security Center](security-center-incident.md)
* [Funcionalidades de detección de Azure Security Center](security-center-detection-capabilities.md)
* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md)
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

