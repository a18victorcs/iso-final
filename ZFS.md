# ZFS
## Instalación servidor
- **apt install zfsutils-linux**
- **zfs -version** para ver la versión del zfs
- si nos dá error al ejecutar el comando para ver que versión se nos ha instalado tenemos que ejecutar los siguientes comandos:
  - **apt install module-assistant**
  - **m-a prepare**
  - **modprobe zfs**
- **fdisk -l | grep sd** para ver los discos que tenemos añadidos
- **zpool create -m /mnt/zdata zdata raidz /dev/sdc /dev/sdd** para crear un raid z1 que es como un RAID5, con la opción -m indicamos el punto de montaje
- **zpool list** para listar los pool que tenemos creados
- **zpool status** para ver el estado de los pool que tenemos creados
- **zpool destoy zdata** para eliminar un pool
- **zfs set mountpoint=/mnt/zDatos zdata** para cambiar el punto de montaje sin destruir el pool
- No hace falta meter los pool en el fichero fstab, ya que se quedan montados aunque reiniciemos el equipo
- **df -h** para mostrar las unidades montadas y el punto de montaje
- **zfs create /zdata/carpeta_compartida** para crear un volumen llamado común
- **zfs create -o mountpoint=/mnt/carpeta_compartida zdata/datos** para crear un volumen llamado datos dentro del pool zdata montado dentro de carpeta_compartida. Esto es de tipo file system. De este modo no es necesario formatearlo ni montarlo en el fichero fstab ya que lo hace de forma automática.
- **zfs list** para listar los volúmenes
- **zfs destoy zdata/datos** para destruírlo
- **zfs create -s -V 2GB zdata/carpeta_compartida** crear un volumen de 2 GB llamado personales dentro del pool zdata, -V tipo de volumen, de esta forma es necesario formatear el volumen y montarlo en el fichero fstab
- **mkfs.ext4 /dev/zvol/zdata/carpeta_compartida** para formatear el volumen personales
- **zfs snapshot zdata/carpeta_compartida@snap-año/mes/día** para crear snapshot y ordenarla
- **zfs list -t snapshot /zdata/carpeta_compartida** para listar los archivos de tipo snapshot
- **zfs rollback zdata/carpeta_compartida@snap-año/mes/dia** para restaurar snapshots.
- **zfs rollback -r zdata/carpeta_compartida@snap-año/mes/dia** para restaurar todas las intantáneas anteriores hasta la que queremos restaurar
- **zfs destoy /zdata/carpeta_compartida@snap-año/mes/dia** para destruir una snapshot
### Quotas
- **zfs set quota=2G zdata/carpeta_compartida** establecer unha quota de 2GB
- **zfs get quota /zdata/carpeta_compartida** ver que quota tiene el volumen
- **zfs set quota=none zdata/carpeta_compartida** establecer la quota a none, esto sirve para borrar la quota
- **zfs set userquota@usuario=2G zdata/carpeta_compartida** establecer quota unicamente para un usuario concretor
- **zfs userspace zdata/carpeta_compartida** ver la quota por usuario
- **zfs set groupquota@group=2G zdata/carpeta_compartida** establecer quota unicamente para un grupo concreto
- **zfs groupspace zdata/carpeta_compartida** ver la quota por grupo
- **zfs set userquota@usuario=none zdata/carpeta_compartida** borrar las quotas de los usuarios
- **zfs set groupquota@group=2G zdata/carpeta_compartida** borrar las quotas de los grupos

## Compartir archivos con nfs
- **zfs set sharenfs="rw=ip,ro=ip" zdata/carpeta_compartida** para compartir una carpeta.
- **zfs get sharenfs zdata/carpeta_compartida** para obtener información a cerca de la compartición que acabamos de hacer.
- **zfs unshare zdata/carpeta_compartida** para dejar de compartir la carpeta. si hacemos un zfs get sharenfs zdata/carpeta_compartida nos va a seguir apareciendo porque lo hemos dejado de compartir pero no borramos la compartición. Sin embargo si hacemos un showmount -e no nos aparece nada.
- **zfs sharenfs=off zdata/carpeta_compartida** borra la compartición
- **zfspol status** mirar el estado del pool
- **zpool replace zdata /dev/sdc /dev/sde** reemplazar un disco por otro
- **zpool offline zdata /dev/sde** poner el pool offline
- **zpool online zdata /dev/sde** poner el pool onlie

## Compartir archivos por samba
- *hay que configurar el servidor samba*
- **zfs set sharesmb = on zdata/carpeta_compartia** para compartir por samba
- **smbclient -L //ip** para ver las carpetas compartidas
- **zfs sharesmb = off zdata/carpeta_compartida** dejar de compartir
- **zfs get sharsmb zdata/carpeta_compartida** comprobar que ya no está compartiendo
- **zfs destoy zdata/carpeta_compartida** destruírlo
- **nano /etc/security/pam_mount.conf.xml** editamos este fichero e introducimos después de "volume definitions"
- ```<volume server="ip" gid="10000" fstype="cifs" path="carpeta_compartida" mountpoint="/home/ejemplo.local/%(USER)/carpeta_en_cliente/>
### Cliente samba
- **apt install samba-common cifs-utils smbclient**
