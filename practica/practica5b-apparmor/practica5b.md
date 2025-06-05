# Ya te aviso que esta práctica es una poronga

## 2.​ Verifique si apparmor se encuentra habilitado con el comando aa-enabled. Si no se encuentra habilitado verifique el kernel que está ejecutando (el kernel de Debian de la VM lo trae habilitado por defecto).
Cuando ejecutás `aa-enabled`, este comando informa si AppArmor está habilitado en el kernel. Su salida puede tener varios formatos:
- **Yes**: AppArmor está habilitado.
- **No**: AppArmor no está habilitado.
- **S?**: Significa "Status unknown", y suele indicar que el módulo del kernel no está correctamente cargado, o que el sistema no tiene montado /sys correctamente, o incluso que hay un problema con permisos/capacidades en el entorno donde se ejecuta.

## 3.​ Utilice la herramienta aa-status para determinar:
**a.​ ¿Cuántos perfiles se encuentran cargados?**  
```bash
# fragmento del comando aa-status
31 profiles are loaded.
```

**b.​ ¿Cuántos procesos y cuáles procesos de tu sistema tienen perfiles definidos?**  
```bash
# fragmente del comando aa-status
2 processes have profiles defined.
2 processes are in enforce mode.
   /usr/sbin/dhclient (399) /{,usr/}sbin/dhclient
   /usr/sbin/dhclient (400) /{,usr/}sbin/dhclient
```
- Hay 2 procesos actualmente en ejecución que tienen un perfil AppArmor aplicado.  
- Ambos están en modo enforce, lo que significa que AppArmor restringe activamente sus acciones de acuerdo a su perfil.
- Los procesos son instancias del cliente DHCP (dhclient), con PIDs 399 y 400.


## 5.​ Ejecute insecure_service manualmente usando el usuario root y verifique que puede acceder libremente al filesystem en http://localhost:8080 (o la IP correspondiente donde se ejecuta el servicio).
vamos a ejecutar el servicio manualmente, **sin estar bajo el control de systemd**, para poder observar su comportamiento sin restricciones impuestas por el gestor de servicios. Para ejecutarlo manualmente vamos a la ruta donde estaba definido el servicio `/opt/sistemasoperativos/insecure_service`
- `/opt` se usa para instalar software adicional que no forma parte del sistema base.
- Este paso establece una línea base de comportamiento sin AppArmor: lo que el servicio puede hacer sin limitaciones


