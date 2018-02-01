| **Componente** | **Requisito** |
| --- |---|
| Núcleos de CPU| 8 |
| RAM | 12 GB|
| Número de discos | 3, incluidos el disco del sistema operativo, el disco de memoria caché del servidor de procesos, la unidad de retención para la conmutación por recuperación |
| Espacio libre en el disco (caché del servidor de procesos) | 600 GB
| Espacio libre en el disco (disco de retención) | 600 GB|
| Sistema operativo  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Configuración regional del sistema operativo | Español (es-es)|
| Versión de VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Roles de Windows Server | No habilite estos roles: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Directivas de grupo| No habilite estas directivas de grupo: <br> - Impedir el acceso al símbolo del sistema <br> - Impedir el acceso a herramientas de edición del Registro <br> - Confiar en la lógica de datos adjuntos de archivos <br> - Activar la ejecución de scripts <br> [Más información](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| IIS | - Ningún sitio web predeterminado debe existir previamente <br> - Habilitar la [Autenticación anónima](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> - Habilitar la configuración de [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx)  <br> - Ningún sitio web o aplicación que escuche en el puerto 443 deben existir previamente<br>|
| Tipo de NIC | VMXNET3 (cuando se implementa como una máquina virtual VMware) |
| Tipo de dirección IP | estática |
| Acceso a Internet | El servidor necesita acceder a estas direcciones URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (no es necesario para servidores de procesos de escalado horizontal) <br> - time.nist.gov <br> - time.windows.com |
| Puertos | 443 (orquestación del canal de control)<br>9443 (Transporte de datos)|
