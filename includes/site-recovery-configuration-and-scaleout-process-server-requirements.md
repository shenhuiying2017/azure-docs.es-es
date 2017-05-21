| **Hardware** | |
| --- |---|
| Número de núcleos de la CPU| 8 |
| RAM| 12 GB|
| Número de discos | 3 <br><br> - Disco del sistema operativo<br> - Disco de caché del servidor de procesos<br> - Unidad de retención (conmutación por recuperación)|
| Espacio libre en el disco (caché del servidor de procesos) | 600 GB
| Espacio libre en el disco (disco de retención) | 600 GB|
| **Software** | |
| Versión del sistema operativo | Windows Server 2012 R2 |
| Configuración regional del sistema operativo | Español (es-es)|
| Versión de VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Roles de Windows Server | No habilite los siguientes roles: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| **Red** | |
| Tipo de tarjeta de interfaz de red | VMXNET3 |
| Tipo de dirección IP | Estática |
| Acceso a Internet | El servidor debe acceder a la siguiente dirección URL, ya sea directamente o a través de un servidor proxy: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (no es necesario para servidores de procesos de escalado horizontal) <br> - time.nist.gov <br> - time.windows.com |
| Puertos | 443 (orquestación del canal de control)<br>9443 (Transporte de datos)|
