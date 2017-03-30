1. Copie el instalador en una carpeta local (por ejemplo, C:\Temp) del servidor que desea proteger. Ejecute los siguientes comandos como administrador en un símbolo del sistema:

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Para instalar Mobility Service, ejecute el siguiente comando:

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP address of the configuration server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumentos de la línea de comandos del instalador de Mobility Service

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log file path>]<br/>
```

  | Parámetro|Tipo|Descripción|Valores posibles|
  |-|-|-|-|
  |/Role|Obligatorio|Especifica si se debe instalar Mobility Service|Agente </br> MasterTarget|
  |/InstallLocation|Obligatorio|Ubicación en que se instala Mobility Service|Cualquier carpeta del equipo|
  |/CSIP|Obligatorio|Dirección IP del servidor de configuración| Cualquier dirección IP válida|
  |/PassphraseFilePath|Obligatorio|Ubicación de la frase de contraseña |Cualquier ruta de acceso local o UNC válida|
  |/LogFilePath|Opcional|Ubicación del registro de instalación|Cualquier carpeta válida del equipo|

#### <a name="example"></a>Ejemplo

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```
