---
title: "Arreglando el iPod"
date: 2026-04-24T20:00:47+02:00
tags:
  - Dump & Rant
  - Tech
  - Music
---
![iPodFix](/images/posts/ipod_fix_banner.jpg)

De alguna manera logré romper mi iPod con `dd`. Arrancaba en modo USB/Disco con el bootloader de Rockbox diciendo `No Partition found`. Para recuperarlo tuve que restaurarlo desde iTunes/Finder en una Mac (APM), lo que borró el disco entero y escribió una nueva tabla de particiones Apple Partition Map con el firmware original de Apple. Después tuve que volver a instalar Rockbox — lo cual resultó más complicado de lo esperado por la placa iFlash Solo. Este post cubre los conceptos que importan al hacer esto, no solo los comandos. Es básicamente un dump de las cosas que valió la pena entender mientras debugueaba y finalmente arreglaba el iPod.

## Especificaciones del dispositivo

| Propiedad | Valor |
|---|---|
| Modelo | iPod Classic 7ª Gen |
| Mod de almacenamiento | Placa iFlash Solo |
| Tamaño de la SD | ~955 GiB (~1 TB) |
| Tamaño de sector lógico/físico | 4096 bytes / 4096 bytes (nativo, por iFlash) |
| Tabla de particiones tras restaurar en Mac | Apple Partition Map (APM) |

---

## El hardware

Los iPod Classic de 6ª y 7ª generación (2007–2009) son los más difíciles de abrir — tienen clips metálicos muy ajustados por toda la junta. Rompí dos a propósito (sin querer) para que sea más fácil abrirlo la próxima vez. [Este tipo](https://www.youtube.com/watch?v=_QdiSpw98Xw) comparte el sentimiento.

| [![](/images/posts/ipod_1.jpg)](/images/posts/ipod_1.jpg) | [![](/images/posts/ipod_2.jpg)](/images/posts/ipod_2.jpg) | [![](/images/posts/ipod_4.jpg)](/images/posts/ipod_4.jpg) |
|---|---|---|

Otra cosa que casi rompo es el conector de la batería, el que está marcado con un círculo en la tercer foto. El socket de plástico blanco tiene 3 pines soldados a la PCB que lo mantienen rígido, mientras que la traba negra desliza de abajo hacia arriba para bloquear o desbloquear el cable flex que le da corriente al circuito. Saqué ese flex tantas veces que el plástico blanco se despegó de la PCB (los pines no, por suerte). Ahora, abrir el iPod es un trabajo de 3 hombres: uno sostiene el plástico blanco, mientras que el otro levanta la traba negra y saca el flex.

---

## Tablas de particiones: APM, MBR y por qué importa

Cuando una Mac restaura un iPod via iTunes/Finder escribe un **Apple Partition Map (APM)** — el formato de tabla de particiones legacy de Apple, anterior al MBR (usado en la mayoría de las PCs) y al GPT (usado en las Macs modernas). Restaurarlo e instalar el OS de stock fue muy fácil, lo conectás a una Mac, una Macbook Air de las nuevas en mi caso, y listo. Todos los iPod classic restaurados de esta forma vuelven a tener su APM. Y ahora vuelta a 0: a instalar el bootloader de Rockbox.

Me vi tentado a instalar Rockbox en la Mac pero decidí volver a la linuxera, el problema en Linux es que el kernel no expone automáticamente las sub-particiones APM como dispositivos de bloque. Después de la restauración en Mac, el iPod aparecía como `/dev/sda` (954.8G) **sin nodos de dispositivo de partición** — sin `sda1`, `sda2`, etc. Herramientas como `parted` pueden ver las particiones, pero no se pueden montar directamente.

```sh
sudo parted /dev/sda print
```

El output:
- Partition table type: `mac`
- Partition 1: 254 KB — partition map metadata
- Partition 2: ~955 GiB — main data partition (HFS+, filesystem not auto-detected by Linux without kpartx)


Para exponerlas como dispositivos de bloque, traté con `kpartx` que te crea los mapeos manualmente:

```sh
sudo kpartx -av /dev/sda
# crea /dev/mapper/sda1 y /dev/mapper/sda2
```

Lo hice con `kpartx` pq es lo que recomendó la IA,  si no era`ipodpatcher` que en mi caso fue ejecutado internamente por Rockbox Utility pq `ipodpatcher` no estaba en NixOS. Esto convierte la tabla de particiones de **APM → MBR**, e imagino yo que ahí es donde hace su magia para bootear al OS de stock o a Rockbox con MENU+SELECT o SELECT+PLAY/PAUSE. Luego de instalar el bootloader el dispositivo ya aparece con sus particiones normales (`sdc1`, `sdc2`) sin necesitar `kpartx`. Por suerte pude usar rockbox-utility, porque `ipodpatcher` no estaba disponible en NixOS.

---

## HFS+ y el journal y por qué no importa

La restauración en Mac formatea la partición de datos como **HFS+ con Journal**. El journal es un log de escritura anticipada que ayuda a recuperar posibles crashes — pero también hace que Linux monte la partición **en modo solo lectura** por defecto, porque no puede escribir de forma segura en un sistema HFS+ con journal sin riesgo de corrupción.
 
Te estarás preguntando por qué hay una partición HFS+ y qué es HFS+? Te pego lo que me dice la IA : HFS+ (Hierarchical File System Plus). Es una versión mejorada del antiguo HFS y se usó durante muchos años en Macs, iPods y otros productos de Apple antes de que fuera reemplazado por APFS.

Para obtener acceso de escritura, simplemente la tenés montar forzada (la partición) y cagarte en el journal, a esta altura si explota algo, arrancamos de nuevo.  


```sh
sudo mkdir -p /mnt/ipod
sudo mount -t hfsplus -o force,rw /dev/mapper/sda2 /mnt/ipod

nix-shell -p hfsprogs --run "sudo fsck.hfsplus -J /dev/mapper/sda2"
```

`fsck.hfsplus -J` reprocesa el journal y lo marca como deshabilitado. Después de esto la partición se monta como read/write normalmente. Este paso solo es necesario si querés inspeccionar o modificar la partición HFS+ — Rockbox Utility reemplaza toda la tabla de particiones de todas formas.

¿Por qué no importa? Porque esto solo lo necesitaba Rockbox Utility para detectar el device, y necesitaba poder escribir para instalar el bootloader en él porque ese es un check que tiene la GUI. Si no estás en NixOS, tal vez con el `ipodpatcher` no hacía falta. Si para este entonces ya tenés Rockbox, esta partición ya no la vas a ver.

---

## El layout de iFlash Solo


**Parentesis**: *¿Cómo traduzco este título en criollo? ¿ "El esquema del iFlash Solo" ?  Como escribo en castellano, si le hablo a Claudio en inglés y tuve que copiar el "¿" de internet porque no sé hacerlo en el teclado ? Anyway (En fin)*

> **👀 ->** Antes de comprar o cambiar tarjetas SD, verificá la compatibilidad con la placa iFlash Solo en la página oficial:
[https://www.iflash.xyz/store/iflash-compatibility/](https://www.iflash.xyz/store/iflash-compatibility/)

Para Linux, el [iFlash Solo](https://www.iflash.xyz/store/iflash-solo/) se ve como un único disco. Internamente, sin embargo, tiene su propia área de almacenamiento reservada para firmware, separada de la tarjeta SD. Esto es lo que representa la partición `sdc2` (192 MB) después de que corre Rockbox Utility, el **bootloader** de Rockbox vive ahí, no en la SD en sí. Esto fue una gran fuente de confusión al principio. Imaginate mi sorpresa cuando saqué la SD, la formaté a 0, la volví a meter al iPod y volví a ver la pantalla del bootloader. Casi salgo corriendo.

```
Device     Boot Start       End   Sectors   Size Id Type
/dev/sdc1  *    49278 250347518 250298241 954.8G  c W95 FAT32 (LBA)   ← música + firmware Rockbox (.rockbox/)
/dev/sdc2          63     49214     49152   192M 3f unknown            ← bootloader Rockbox
```

> "Partition table entries are not in disk order" es normal en el layout del iPod.

Esta distinción importa porque el **Bootloader** y el **Firmware** se instalan por separado, por eso cuando actualizás Rockbox no tenés que instalar todo de vuelta. 
- **Bootloader** (`sdc2`): se instala una vez via *Bootloader installation* de Rockbox Utility — parchea el área reservada del iFlash, convierte APM → MBR
- **Firmware** (`sdc1`): el directorio `.rockbox/` (codecs, temas, `rockbox.ipod`) copiado en la partición de datos FAT32. Esto último, estoy seguro que podés simplemente copiar y pegar la carpeta y es lo mismo. No quise probar.

---

## FAT32 y la restricción de sectores de 4096 bytes

El iFlash Solo reporta un **native physical sector size of 4096 bytes** (en lugar de los 512 bytes estándar). Esto se propaga al formateo FAT32 — `mkfs.vfat` va a ignorar cualquier intento de configurar sectores lógicos más pequeños:

```sh
sudo mkfs.vfat -F 32 -S 512 -s 8 /dev/sdc1
# Warning: sector size was set to 4096 (minimal for this device)
```

No es un bug, es una restricción de hardware. La consecuencia es que Rockbox  puede fallar al leer el sistema de archivos porque su driver FAT asume sectores de 512 bytes. Si Rockbox arranca pero muestra `No partition found` después de formatear e instalar el firmware, esta es la causa probable (acá la cagué).

Así monté la partición formateada con permisos de escritura:

```sh
sudo mount -t vfat -o uid=$(id -u),gid=$(id -g),umask=022 /dev/sdc1 /mnt/ipod
```

---

## Entorno Nix Shell por si sos un gordito Nix como yo

```nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {
  packages = with pkgs; [
    sg3_utils       # utilidades SCSI/ATA
    xxd             # hex dump
    hdparm          # parámetros de unidad ATA
    hfsprogs        # fsck.hfsplus para manipular el journal HFS+
    parted          # inspección de tabla de particiones
    multipath-tools # kpartx para mapeo de particiones APM
  ];
}
```

---

## Algunos comandos full dopaminérgicos

```sh
# Inspeccionar tabla de particiones
sudo fdisk -l /dev/sdX
sudo parted /dev/sdX print

# Exponer particiones APM como dispositivos de bloque
sudo kpartx -av /dev/sdX
sudo kpartx -dv /dev/sdX   # eliminar mapeos

# Montar HFS+ (forzar rw, saltear journal)
sudo mount -t hfsplus -o force,rw /dev/mapper/sdX2 /mnt/ipod

# Deshabilitar journal HFS+
sudo fsck.hfsplus -J /dev/mapper/sdX2

# Formatear FAT32 (iFlash va a forzar sectores de 4096 bytes igual)
sudo mkfs.vfat -F 32 /dev/sdX1

# Montar FAT32 con permisos de usuario
sudo mount -t vfat -o uid=$(id -u),gid=$(id -g),umask=022 /dev/sdX1 /mnt/ipod

# Lanzar Rockbox Utility en NixOS
nix-shell -p rockbox-utility --run "RockboxUtility"

# Desmontar de forma segura antes de desconectar
sudo umount /mnt/ipod
```
