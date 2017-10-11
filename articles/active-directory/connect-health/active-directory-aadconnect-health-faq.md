---
title: "Preguntas más frecuentes (P+F) sobre Azure Active Directory Connect Health: Azure | Microsoft Docs"
description: "Las preguntas más frecuentes son preguntas y respuestas sobre Azure AD Connect Health. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad."
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 902e5bdfbbf04ab70989be8c41e16eb69e475908
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Preguntas más frecuentes sobre Azure AD Connect Health
Este artículo incluye respuestas a preguntas más frecuentes (P+f) sobre Azure Active Directory (Azure AD) Connect Health. Estas preguntas más frecuentes abarcan cuestiones sobre cómo usar el servicio; por ejemplo, el modelo de facturación, las funcionalidades, las limitaciones y el soporte técnico.

## <a name="general-questions"></a>Preguntas generales
**P: Administro varios directorios de Azure AD. ¿Cómo puedo cambiar al que tiene Azure Active Directory Premium?**

Para cambiar entre distintos inquilinos de Azure AD, seleccione el **nombre de usuario** que actualmente ha iniciado sesión en la esquina superior derecha y luego elija la cuenta adecuada. Si la cuenta no aparece aquí, seleccione **Cerrar sesión** y luego use las credenciales de administrador global del directorio que tiene Azure Active Directory Premium habilitado para iniciar sesión.

**P: ¿Qué versión de los roles de identidad es compatible con Azure AD Connect Health?**

En la tabla siguiente se enumeran los roles y las versiones de sistema operativo compatibles.

|Rol| Sistema operativo/versión|
|--|--|
|Active Directory Federation Services (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Versión 1.0.9125 o posterior.|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Tenga en cuenta que las características proporcionadas por el servicio pueden diferir según el rol y el sistema operativo. En otras palabras, todas las características pueden no estar disponibles para todas las versiones del sistema operativo. Vea las descripciones de las características para obtener más información.

**P: ¿Cuántas licencias necesito para supervisar mi infraestructura?**

* El primer Agente de Connect Health requiere al menos una licencia Premium de Azure AD.
* Cada agente adicional registrado requiere otras 25 licencias Premium de Azure AD.
* El número de agentes es equivalente al número total de agentes registrados en todos los roles supervisados (AD FS, Azure AD Connect o AD DS).

La información de licencia se encuentra también en la [página de precios de Azure AD](https://aka.ms/aadpricing).

Ejemplo:

| Agentes registrados | Licencias necesarias | Ejemplo de configuración de supervisión |
| ------ | --------------- | --- |
| 1 | 1 | 1 servidor de AAD Connect |
| 2 | 26| 1 servidor de Azure AD Connect y 1 controlador de dominio |
| 3 | 51 | 1 servidor de Active Directory Federation Services (AD FS), 1 proxy de AD FS y 1 controlador de dominio |
| 4 | 76 | 1 servidor de AD FS, 1 proxy de AD FS y 2 controladores de dominio |
| 5 | 101 | 1 servidor de Azure AD Connect, 1 servidor de AD FS, 1 proxy de AD FS y 2 controladores de dominio |


## <a name="installation-questions"></a>Preguntas sobre instalación

**P: ¿Qué impacto tiene la instalación del agente de Azure AD Connect Health en los servidores individuales?**

El impacto de instalar el Agente de Microsoft Azure AD Connect Health, AD FS, los servidores proxy de aplicación web, los servidores de Azure AD Connect (Sync) y los controladores de dominio es mínimo con respecto al consumo de CPU, memoria, ancho de banda de red y almacenamiento.

Las cifras siguientes son una estimación:

* Consumo de CPU: aumento de ~1-5 %.
* Consumo de memoria: hasta 10 % de la memoria total del sistema.

> [!NOTE]
> Si el agente no puede comunicarse con Azure, almacena los datos localmente hasta un límite máximo definido. El agente sobrescribe los datos "en caché" en función de "los servidos menos recientemente".
>
>

* Almacenamiento en búfer local para Agentes de Azure AD Connect Health: ~20 MB.
* Para servidores de AD FS, se recomienda que aprovisione un espacio en disco de 1,024 MB (1 GB) de forma que el canal de auditoría de AD FS para Agentes de Azure AD Connect Health procese todos los datos de auditoría antes de que se sobrescriban.

**P: ¿Tendré que reiniciar los servidores durante la instalación de los agentes de Azure AD Connect Health?**

No. La instalación de los agentes no requerirá que reinicie el servidor. Sin embargo, la instalación de algunos de los requisitos previos podría requerir el reinicio del servidor.

Por ejemplo, en Windows Server 2008 R2, la instalación de .Net 4.5 Framework requiere un reinicio del servidor.

**P: ¿Funciona Azure AD Connect Health mediante un proxy HTTP de paso?**

Sí. Para las operaciones en curso, puede configurar el agente de mantenimiento para usar un proxy HTTP para reenviar solicitudes HTTP salientes.
Lea más sobre cómo [configurar el proxy HTTP para los agentes de mantenimiento](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Si necesita configurar a un proxy durante el registro del agente, puede que deba modificar de antemano su configuración del proxy de Internet Explorer.

1. Abra Internet Explorer > **Configuración** > **Opciones de Internet** > **Conexiones** > **Configuración de LAN**.
2. Seleccione **Usar un servidor proxy para la LAN**.
3. Seleccione **Opciones avanzadas** si tiene distintos puertos de proxy para HTTP y HTTPS/Secure.

**P: ¿Admite Azure AD Connect Health autenticación básica cuando se conecta a servidores proxy HTTP?**

No. Actualmente no se admite un mecanismo para especificar un nombre de usuario y una contraseña arbitrarios para la autenticación básica.

**P: ¿Qué puertos de firewall debo abrir para que funcione el agente de Azure AD Connect Health?**

Consulte la [sección de requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para obtener la lista de puertos de firewall y otros requisitos de conectividad.

**P: ¿Por qué veo dos servidores con el mismo nombre en el portal de Azure AD Connect Health?**

Cuando quita un agente de un servidor, el servidor no se quita automáticamente del portal de Azure AD Connect Health. Si quita de forma manual un agente de un servidor o quita el propio servidor, necesita eliminar manualmente la entrada del servidor del portal de Azure AD Connect Health.

Puede restablecer la imagen inicial de un servidor o crear un nuevo servidor con los mismos detalles (por ejemplo, el nombre de la máquina). Si no ha quitado el servidor ya registrado del portal de Azure AD Connect Health y había instalado al agente en el nuevo servidor, podría ver dos entradas con el mismo nombre.

En este caso, elimine manualmente la entrada que pertenece al servidor anterior. Los datos para este servidor deben estar obsoletos.

## <a name="health-agent-registration-and-data-freshness"></a>Registro del agente de mantenimiento y actualización de datos

**P: ¿Cuáles son las causas comunes de los errores de registro del agente de mantenimiento y cómo puedo solucionarlos?**

El agente de mantenimiento puede presentar errores de registro debido a las siguientes razones posibles:

* El agente no puede comunicarse con los puntos de conexión necesarios porque un firewall está bloqueando el tráfico. Esto es especialmente frecuente en servidores proxy de aplicación web. Asegúrese de que haya permitido la comunicación de salida a los puertos y puntos de conexión necesarios. Consulte la [sección de requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para más información.
* La comunicación de salida se somete a una inspección de SSL mediante el nivel de red. Como consecuencia, el certificado que usa el agente se sustituya por la entidad/servidor de inspección y los pasos para completar el registro del agente producen error.
* El usuario no tiene acceso para realizar el registro del agente. Los administradores globales tienen acceso de forma predeterminada. Puede usar el [control de acceso basado en roles](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) para delegar el acceso a otros usuarios.

**P: Recibo una alerta que dice "Los datos del servicio de mantenimiento no están actualizados". ¿Cómo puedo solucionar el problema?**

Azure AD Connect Health genera la alerta si no recibe todos los puntos de datos del servidor en las últimas dos horas. Los motivos de esta alerta pueden ser varios.

* El agente no puede comunicarse con los puntos de conexión necesarios porque un firewall está bloqueando el tráfico. Esto es especialmente frecuente en servidores proxy de aplicación web. Asegúrese de que haya permitido la comunicación de salida con los puertos y puntos de conexión necesarios. Consulte la [sección de requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para más información.
* La comunicación de salida se somete a una inspección de SSL mediante el nivel de red. Esto da lugar a que el certificado que usa el agente se reemplace por la entidad/servidor de inspección y que el proceso no pueda cargar los datos en el servicio Azure AD Connect Health.
* Puede usar el comando de conectividad integrado en el agente. [Más información](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service).
* Los agentes también admiten la conectividad saliente a través de un servidor proxy HTTP no autenticado. [Más información](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy).

## <a name="operations-questions"></a>Preguntas sobre operaciones
**P: ¿Necesito habilitar la auditoría en los servidores proxy de aplicación web?**

No, la auditoría no debe habilitarse en los servidores proxy de aplicación web.

**P: ¿Cómo se resuelven las alertas de Azure AD Connect Health?**

Las alertas de Azure AD Connect Health se resuelven en una condición satisfactoria. Los Agentes de Azure AD Connect Health detectan e informan de las condiciones de éxito al servicio de manera periódica. En el caso de algunas alertas, la supresión depende del tiempo. En otras palabras, si la misma condición de error no se observa dentro de un plazo de 72 horas desde la generación de la alerta, esta se resuelve de forma automática.

**P: recibo un aviso de que "La solicitud de autenticación de prueba (transacción sintética) no pudo obtener un token". ¿Cómo puedo solucionar el problema?**

Azure AD Connect Health para AD FS genera esta alerta cuando se produce un error en el servidor ADFS para obtener un token como parte de una transacción sintética iniciada por el agente de mantenimiento. El agente de mantenimiento utiliza el contexto de sistema local e intenta obtener un token para un usuario de confianza de autoservicio. Se trata de una prueba minuciosa para asegurarse de que AD FS se encuentra en estado de emisión de tokens.

A menudo, se produce un error en esta prueba porque el agente de mantenimiento no puede resolver el nombre de la granja de servidores de AD FS. Esto puede suceder si los servidores de AD FS están detrás de equilibradores de carga de red y la solicitud consigue iniciarse desde un nodo que esté detrás del equilibrador de carga (en lugar de un cliente normal que aparece delante del equilibrador de carga). Esto puede corregirse actualizando el archivo "hosts" en "C:\Windows\System32\drivers\etc" para incluir la dirección IP del servidor de AD FS o una dirección IP de bucle invertido (127.0.0.1) para el nombre de la granja de servidores de AD FS (por ejemplo, sts.contoso.com). Al agregar el archivo de host, se interrumpe la llamada de red, lo que permite que el agente de mantenimiento obtenga el token.

**P: he recibido un correo electrónico que indica que mis máquinas no se han revisado para contrarrestar los ataques de secuestro de datos recientes. ¿Por qué he recibido este correo electrónico?**

El servicio Azure AD Connect Health analiza todas las máquinas que supervisa para garantizar que se instalaron las revisiones necesarias. El correo electrónico se envía a los administradores de inquilinos si al menos una máquina no tiene las revisiones críticas. Se usó la siguiente lógica para tomar esta decisión.
1. Busque todas las revisiones instaladas en la máquina.
2. Compruebe si está presente al menos una de las revisiones de la lista definida.
3. Si es así, la máquina virtual está protegida. En caso contrario, la máquina está en riesgo de sufrir un ataque.

Puede usar el siguiente script de PowerShell para realizar esta comprobación manualmente. Implementa la lógica anterior.

```
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```



## <a name="related-links"></a>Vínculos relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalación del Agente de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)
* [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adds.md)
* [Historial de versiones de Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
