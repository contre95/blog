---
title: "GC-07"
date: 2026-05-04T23:21:08+02:00
# draft: true
---

# Nix OS

NixOS es la distro de Linux que uso hace aproximadamente unos 3 años, después de haber estado como 9 años con Arch. Cuando arranqué con NixOS, no existía la IA, o no al menos no era tan potente como lo es ahora. Configurarlo fue bastante tedioso, pero mi objetivo era claro, manejar la cantidad innecesaria de computadoras que siempre tengo bajo mi poder. Hoy, son solo tres: `desktop`, `notebook` y `server`. Empecé con mi `desktop`, siguió la `notebook` y por último, tras descubrir que Michael Stepelberg [siquiera lo consideró](https://mastodon.online/@contre/114406315423731145) para su server opté por instalarlo en mi `server` también.

NixOS, a diferencia de otras distribuciones de Linux que se configuran con archivos dispersos por todo el sistema (`/etc`, `/usr`), se configura en archivos `.nix`. Por ejemplo: Para alterar el estado de tu Debian o Windows, correrías algo como `sudo apt-get install python` o harías varios _clicks_ y _Siguiente;Siguiente;Siguiente_, eso crearía por detrás una serie de archivos y configuraciones en lugares que nunca te interesaron, alteraría variables de entorno que no te importan ni sabés cuáles son, entre otras cosas que desconocés. Cuando borrás ese programa, rezá que deje tu sistema más o menos como lo encontró. En NixOS es distinto, si querés hacer lo mismo en Nix, en vez de hacerlo de manera imperativa con un comando, editás un archivo `.nix` dónde declarás tu paquete y porqué no sus configuraciones, y seguido de un `sudo nixos-rebuild switch` tenés tu programa instalado. Nix lee esa declaración, construye el nuevo sistema de forma determinista y lo activa de forma atómica. Atómica ? Sí, [_"A-tó-mi-ca"_](https://youtu.be/WIr23sXx1AI?t=9), es una forma elegante de decir que el cambio o se aplica completamente, o no se aplica en absoluto, no hay grises. Ese archivo `.nix` es, literalmente, la fuente de verdad de tu sistema operativo, el estado de tu sistema operativo. La gran particularidad (y desventaja para muchos) es que para poder cumplir con ciertas garantías, NixOS usa un sistema de archivos muy particular, el Nix Store (`/nix/store`). En lugar de instalar los programas en las rutas tradicionales (`/usr/bin/`, `/bin/` o `C:\Archivos de programa`), Nix guarda cada programa (o más precisamente, para cada _output_ de una _derivation_) en una carpeta con un _hash_ único que representa su contenido exacto (e.g `realpath $(which nvim)` -> `/nix/store/n1bm101nhgw33fv114ibkv10xvh8fgp1-neovim-0.11.7/bin/nvim`). Ese hash, no es siempre el mismo, si no que, como la mayoría de los _hashes_, se usa para denotar unicidad, individualidad, si algo cambia aunque sea mínimamente, el hash cambia y se genera una carpeta nueva. Estos hashes no están aislados, sino que entre ellos forman una suerte de Árbol de Merkle Acíclico. Básicamente una estructura donde:

1. Las hojas son hashes de datos.
2. Cada nodo interno es el hash de sus hijos.
3. Cambiar cualquier hoja cambia todos los hashes hacia arriba, hasta la raíz.

Ya te podés imaginar la relación con el Nix Store:

1. Cada "programa" en el store es un nodo.
2. Las dependencias de ese programa son referencias o _edges_ (flechitas en criollo) hacia otros nodos (programas).
3. El hash de un programa incluye los hashes de sus dependencias, por lo que cambiar cualquier dependencia, cambia todo el programa.

Al instante de aprender esto, obviamente quise graficarlo: `nix-store -q --graph $(which openssl) | dot -Tpng > graph.png` y te da algo [así](/images/posts/nix_openssl.png) (si no tenés `dot` probá con `nix-shell -p graphviz`) y guarda con hacer esto con programas grandes porque podés estar una vida esperando. Lo más noble de este árbol, es que si dos paquetes comparten una dependencia, solo existe una copia en el store, pero si dos paquetes dependen de versiones distintas de un programa/librería, no pasa nada, porque estos viven en carpetas distintas, con _hashes_, etc. En cualquier otro sistema, tendrías una sola versión de ese programa (e.g `/usr/lib/libfoo.so`) y de ahí los virtual envs como `nvm`, `virtualenvs`, `Flatpak`, `AppImage`, `LD_LIBRARY_PATH` y soluciones con las que tenés que lidear vos o el manejador de paquetes (_package manager_) de tu OS (`apt`, `apk`, `pacman`, `yum`, etc.)


 Pero no todo es color de rosa en el mundo Nix, y si algo es seguro, es que Nix no tiene un solo color. Esta complejidad, tiene un costo y es, para mi, uno de los *trade-off* más fuertes que tiene **NixOS**, el no ser **FHS** (*Filesystem Hierarchy Standard*) compliant. Este estándar define cómo debe organizarse el sistema de archivos en sistemas tipo **Unix/Linux** y el gran problema, es el que la mayoría de los programas que uno corre en Linux, siguen, lógicamente, este estándar.

# Velocidrone

Hace rato que estoy con ganas de comprarme un drone chiquito (mejor conocido como Tiny Whoop) para boludear en casa. Manejar estos bichos no es tarea fácil, por lo que primero hay que practicar arduamente con un simulador si no querés estrolarte contra la pared al día 1 de comprar tu drone. Spoiler: te vas a estrolar igual. Uno de los que me recomendaron fue [VelociDrone](https://www.velocidrone.com/). El simulador está hecho en Unity y soporta Windows, OSX (Intel, M1 y M2) y Linux (Redhat y Debian) pero, muy a mi pesar, no anda en NixOS. Eso no iba a detenerme a practicar, entonces les levanté un ticket a la gente de "Bat Cave Games" preguntando si podían dar soporte. A lo que ellos contestaron:

![mail](/images/posts/velocidrone_mail.jpg)

Claramente, no iban a dar soporte. No pasa nada, loco, está todo bien. Pero ¿por qué no anda? ¿Realmente alcanza con instalar las dependencias de Unity en NixOS? ¿Cómo se instala un programa en NixOS?
Primero que nada, tenés que ver si tu paquete existe. Esto lo hacés en [search.nixos.org/pacakges][https://search.nixos.org/packages], si no tenés suerte, toca codear un poquito en Nix. Pero antes, te recomiendo buscar si alguien no codeo Nix antes por vos, para eso te recomiendo, lamentablemente, Github. En Github podés buscar repositorios públicos, y como en Nix es todo declarativo hay mucha info que podés simplemente copiar y pegar. Para buscar el keyword `lang:nix` es clave. En este caso, mi *query* (`?q=`) fue `lang:nix velocidrone`.

![GC07](/images/posts/nix_gc07.jpg)


La foto de arriba es lo que yo denomino un `GC-07` y determina que existen, al menos, siete Gordos Compu que están en la misma que vos. Un `GC-XX` puede ocasionar un sinfín de emociones: desde una tristeza y desolación inconmensurables, si es un `GC-00`, hasta una mera indicación de que estás en el camino correcto, si es un `GC-N` con `N > 100`. O puede, como en este caso, dar un pequeño ápice de esperanza de que lo que querés hacer, y como lo querés hacer, se puede; que alguien en este ciber-mundo ya lo intentó.
