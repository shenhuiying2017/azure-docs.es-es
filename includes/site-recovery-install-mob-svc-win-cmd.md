1. Copie el instalador en una carpeta local (por ejemplo, C:\Temp) en el servidor que desea proteger y ejecute los siguientes comandos desde un símbolo del sistema con privilegios elevados.

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Ahora se puede instalar Mobility Service mediante la siguiente línea de comandos

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumentos de la línea de comandos del instalador de Mobility Service

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>
```

  | Parámetro|Tipo|Descripción|Valores posibles|
  |-|-|-|-|
  |/Role|Obligatorio|Especifica si se debe instalar Mobility Service|Agente </br> MasterTarget|
  |/InstallLocation|Obligatorio|Ubicación donde se instalará Mobility Service|Cualquier carpeta del equipo|
  |/CSIP|Obligatorio|Dirección IP del servidor de configuración| Cualquier dirección IP válida|
  |/PassphraseFilePath|Obligatorio|Ubicación donde se almacena la frase de contraseña |Cualquier ruta de acceso local o UNC válida|
  |/LogGilePath|Opcional|Ubicación del registro de instalación|Cualquier carpeta válida del equipo|

#### <a name="sample-usage"></a>Ejemplo de uso

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```


<!--HONumber=Jan17_HO3-->


