---
title: "GC-07"
date: 2026-05-04T23:21:08+02:00
# draft: true
---

NixOS, a diferencia de otras distribuciones de Linux que se configuran con archivos dispersos por todo el sistema (`/etc`, `/usr`), se configura en archivos `.nix`. Por ejemplo: Para alterar el estado de tu Debian o Windows, correrías algo como `sudo apt-get install python` o harías varios *clicks* y *Siguiente;Siguiente;Siguiente*, eso crearía por detrás una serie de archivos y configuraciones en lugares que nunca te interesaron, alteraría variables de entorno que no te importan ni sabés cuales son , entre otras cosas que desconocés. Cuando borrás ese programa, rezá que deje tu sistema más o menos como lo encontró. En NixOS es distinto,si querés hacer lo mismo en Nix, en vez de hacerlo de manera imperativa con un comando, editás un archivos `.nix` dónde declarás tu paquete y porqué no sus configuraciones, y seguido de un `sudo nixos-rebuild switch` tenés tu programa instalado. Nix lee esa declaración, construye el nuevo sistema de forma determinista y lo activa de forma atómica. Atómica ? Sí, [*"A-tó-mi-ca"*](https://youtu.be/WIr23sXx1AI?t=9), es una forma elegante de decir que el cambio o se aplica completamente, o no se aplica en absoluto, no hay grises. Ese archivo `.nix` es, literalmente, la fuente de verdad de tu sistema operativo, el estado de tu sistema operativo. La gran particularidad (y desventaja para muchos) es que para poder cumplir con ciertas garantías, NixOS usa un sistema de archivos muy particular, el Nix Store (`/nix/store`). En lugar de instalar los programas en las rutas tradicionales (`/usr/bin/`, `/bin/` o `C:\Archivos de programa`), Nix guarda cada programa (o más precisamente, para cada *output* de una *derivation*) en una carpeta con un *hash* único que representa su contenido exacto (e.g `realpath $(which nvim)` -> `/nix/store/n1bm101nhgw33fv114ibkv10xvh8fgp1-neovim-0.11.7/bin/nvim`). Ese hash, no es siempre el mismo, si no que, como la mayoría de los *hashes*, se usa para denotar unicidad, individualidad, si algo cambia aunque sea mínimamente, el hash cambia y se genera una carpeta nueva. Estos hashes no están aislado, si no que entre ellos forman una suerte de Árbol de Merkle Acíclico. Básicamente una estructura donde: 
1. Las hojas son hashes de datos. 
2. Cada nodo interno es el hash de sus hijos.
3. Cambiar cualquier hoja cambia todos los hashes hacia arriba, hasta la raíz. 

Ya te podés imaginar la relación con el Nix Store:
1. Cada "programa" en el store es un nodo.
2. Las dependencias de ese programa son referencias o *edges* (flechitas en criollo) hacia otros nodos (programas). 
3. El hash de un programa incluye los hashes de sus dependencias, por lo que cambiar cualquier dependencia, cambia todo el programa. 

Al instante de aprender esto, obviamente quise graficarlo: `nix-store -q --graph $(which openssl) | dot -Tpng > graph.png` y te da algo [así](/images/posts/nix_openssl.png) (si no tenés `dot` probá con `nix-shell -p graphviz`) y guarda con hacer esto con programas grandes porque podés estar una vida esperando. Lo más noble de este árbol, es que si dos paquetes comparten una dependencia, solo existe una copia en el store, pero si dos paquetes dependen de versiones distintas de un programa/librería, no pasa nada, porque estos están vive en carpetas distintas, con *hashes*, etc. En cualquier otro sistema, tendrías una sola versión de ese programa (e.g `/usr/lib/libfoo.so`) y de ahí los virtual envs como `nvm`, `virtualenvs`, `Flatpak`, `AppImage`, `LD_LIBRARY_PATH` y soluciones con las que tenés que lidear vos o el manejador de paquetes (*package manager*) de tu OS (`apt`, `apk`, `pacaman`, `yum`, etc.)

# Velocidrone

No todo es ___________ en el mundo Nix. .. Hace aproximadamente un año, me levanté un domingo con, aparentemente, mucha paciencia y les levanté un ticket a la gente de "Bat Cave Games". Ellos hacen un ~juego~ simulador muy bueno para drones de carrera, [VelociDrone](https://www.velocidrone.com/). Su simulador está hecho en Unity y soporta Windows, OSX (Intel, M1 y M2) y Linux (Redhat y Debian).

![GC07](/images/posts/nix_gc07.jpg)
