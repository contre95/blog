---
title: "Shell Ops"
date: 2026-05-03T13:48:32+02:00
tags:
    - Tech
    - Self-hosted
---

![Dan Case v4.1](/images/posts/bash_banner.jpg)

Desde el 2018 que empecé a hostear servicios en casa. Empecé con Plex y un `docker-compose.yml`, migré a Podman con `podman play kube` y ahora kubernetes con K3S. La evolución del server estuvo siempre determinada por mi interés o mis ganas de jugar con una u otra tecnología. Siempre le escapé a soluciones sofisticadas o enlatadas como Flux o Argo, nunca tuve un NAS (por ahora) y nunca tuve más de uno o dos nodos corriendo. A mis ojos, el server, es sinónimo de entretenimiento infinito, y estoy seguro que es el caso de [muchos](https://reddit.com/r/homelab) [otros](https://www.reddit.com/r/selfhosted/). Puedo escribir horas de lo que tengo montado, hablar de NixOS, K3s, redes y filosofar al respecto, pero hoy no va a ser el caso. Hoy te voy a contar un poco algunas soluciones *shellisticas* que me hacen el día a día más fácil, en un múndo que uno elije hacerse difícil.

# Servicios

Los servicios van y vienen, creo que tengo más servicios instalados en el cluster que apps en el celular. Mientras escribo esto, tengo corriendo **67 servicios** (`ls -d1 services/* | wc -l`). Para crear nuevos servicios uso `opencode` el 100% de las veces, últimamente con **Big Pickle**, pero cualquiera de los modelos que ofrece gratis [opencode.ai](https://opencode.ai/docs/zen/) van bien. Tengo definido un Markdown que explica un poco cómo definir un nuevo servicio. Ahí se detallan cosas como: qué estructuras de carpetas seguir y cuáles no, algunos casos bordes y cómo solucionarlos, que **annotations** tienen que tener los servicios, cuándo y cómo crear secretos, qué volúmenes montar y en dónde, entre otras muchas cosas. Es fantástico.

![Services](/images/posts/bash_svc.jpg)

Para que un servicio aparezca en el dashboard, cada `ingress.yaml` tiene un set de anotaciones que hace que aparezca en el cuando está desplegado el *Ingress*, para eso uso [Homepage](https://gethomepage.dev/). No sé si es el más lindo o el más avanzado, mi único requisito es que no tenga que agregarlos a mano y pueda manejarlo enteramente desde la definición de cada servicio (desde su *Manifest*), para ello se usan las siguientes anotaciones: 
```yaml
gethomepage.dev/enabled: "true"
gethomepage.dev/name: <AppName>
gethomepage.dev/description: <short description>
gethomepage.dev/group: <group> # e.g. Tools, Media, Social, Monitoring
gethomepage.dev/icon: <app>.png
gethomepage.dev/pod-selector: "app=<app-name>"
```

Las métricas y los logs siguen la misma estrategia, se recolectan y calculan por *namespace* y así las puedo filtrar y distinguir en **Grafana**. Cada vez que se crea un nuevo *namespace*, automáticamente tengo métrics y logs en el mismo dashboard.

![Services](/images/posts/bash_metrics.jpg)

# Mi estado es cluster y el cluster es mi estado

Solo rindo tributo a `etcd` y pago mis impuestos con `kubectl apply`. Para organizar a mis funcionarios, `kustomize` me da una mano. Cada servicio lo tengo en su carpeta y vive en su propio `namespace`, no uso *Helm* para ningún servicio y le intento escapar cuando puedo, tampoco tengo ningún *custom generator* de kustomize y me limito simplemente a `k apply -k services/<service_folder>` cuando quiero cambiar algún que otro funcionario a dedo según se me da la gana.

Si necesito hacer una reforma estatal, les doy franco a todos mis funcionarios con este comando:

`ls -d services/*/ | xargs basename -a | xargs -I{} kubectl scale deployment --all -n {} --replicas=0` 

Para asegurarme que la casa de gobierno esté vacía, y solo queden corriendo algunas funciones básicas, simplemente hago `k get pods -A`:

```
NAMESPACE     NAME                                      READY   STATUS      RESTARTS        AGE
kube-system   cilium-envoy-gkhvx                        1/1     Running     4 (3d15h ago)   18d
kube-system   cilium-kn2mz                              1/1     Running     4 (3d15h ago)   18d
kube-system   cilium-operator-dfc689dfb-4tbpf           1/1     Running     4 (3d15h ago)   18d
kube-system   coredns-695cbbfcb9-2hstz                  1/1     Running     0               3d15h
kube-system   helm-install-cilium-lss5r                 0/1     Completed   0               18d
kube-system   helm-install-traefik-crd-pbvbj            0/1     Completed   0               18d
kube-system   helm-install-traefik-nbhg8                0/1     Completed   0               18d
kube-system   hubble-relay-6cdd9bfb76-6rcq7             1/1     Running     4 (3d15h ago)   18d
kube-system   hubble-ui-ffdc7bfb5-9z9d9                 2/2     Running     8 (3d15h ago)   18d
kube-system   local-path-provisioner-546dfc6456-8v9wg   1/1     Running     4 (3d15h ago)   18d
kube-system   metrics-server-c8774f4f4-7c9qn            1/1     Running     5 (3d15h ago)   18d
kube-system   traefik-6b4c7c8d94-xbb55                  1/1     Running     4 (3d15h ago)   18d
```
Y muy cada tanto, si tengo algún que otro funcionario con un proyecto importante, lo hago trabajar igual, como por ejemplo: `k apply -k services/booklore`

Que vuelvan a trabajar es un poco más complicado, porque muchos dependen del personal de seguridad para entrar a casa de gobierno. Esos días les doy franco a todos menos a ellos y no, no les pago extra:

`ls -d services/*/ | xargs basename -a | grep -vE '^(REDACTED2|REDACTED1|homepage|cert-manager)$' | xargs -I{} kubectl scale deployment --all -n {} --replicas=0` 

Cuando eso pasa, casa rosada se ve un poco así: 

```
k get pods -A
NAMESPACE      NAME                                      READY   STATUS      RESTARTS        AGE
cert-manager   cert-manager-5f9ddb88b4-dm6nw             1/1     Running     0               4s
cert-manager   cert-manager-cainjector-9bb5d7d75-cs2xb   1/1     Running     0               4s
cert-manager   cert-manager-webhook-7fc8569958-lqr66     0/1     Running     0               4s
homepage       homepage-7b8f8f5fbb-zcfkv                 1/1     Running     0               117m
kube-system    cilium-envoy-gkhvx                        1/1     Running     4 (3d17h ago)   18d
kube-system    cilium-kn2mz                              1/1     Running     4 (3d17h ago)   18d
kube-system    cilium-operator-dfc689dfb-4tbpf           1/1     Running     4 (3d17h ago)   18d
kube-system    coredns-695cbbfcb9-2hstz                  1/1     Running     0               3d17h
kube-system    helm-install-cilium-lss5r                 0/1     Completed   0               18d
kube-system    helm-install-traefik-crd-pbvbj            0/1     Completed   0               18d
kube-system    helm-install-traefik-nbhg8                0/1     Completed   0               18d
kube-system    hubble-relay-6cdd9bfb76-6rcq7             1/1     Running     4 (3d17h ago)   18d
kube-system    hubble-ui-ffdc7bfb5-9z9d9                 2/2     Running     8 (3d17h ago)   18d
kube-system    local-path-provisioner-546dfc6456-8v9wg   1/1     Running     4 (3d17h ago)   18d
kube-system    metrics-server-c8774f4f4-7c9qn            1/1     Running     5 (3d17h ago)   18d
kube-system    traefik-6b4c7c8d94-xbb55                  1/1     Running     4 (3d17h ago)   18d
REDACTED2      REDACTED2-7cd9cd7f56-sg57h                1/1     Running     0               93m
REDACTED1      REDACTED1-6cccd8684f-rrvlc                1/1     Running     0               92m
```

Cuando quiero borrar un `namespace` completo y re-crear un servicio tengo un script `./deploy.sh` que usa `fzf`. Simplemente con `find ./services -mindepth 1 -maxdepth 1 -type d -printf '%f\n' | sort | fzf` elijo el servicio que desplegar de zero. 

![Services](/images/posts/bash_deploy.gif)

Como toda organización, tengo un *directorio* con *ficheros* de sus empleados y cualquier otra interacción con los funcionarios la hago mayormente de manera imperativa, desde la terminal los comandos y les pido información. Para facilitar la interacción tengo varios aliases y hago un uso exhaustivo de `fzf`. Algunos de los aliases que uso más seguidos son:

Un `tail -f` de los logs de un servicio
```nix
kogs = "ns=$(kubectl get ns -o jsonpath='{.items[*].metadata.name}'|tr ' ' '\n'|fzf)&&pod=$(kubectl get pods -n \"$ns\" --field-selector=status.phase=Running -o jsonpath='{.items[*].metadata.name}'|tr ' ' '\n'|fzf)&&kubectl logs -n \"$ns\" \"$pod\" --all-containers=true -f";
```
Resetear todos los servicios
```nix
krda = "kubectl get deployments --all-namespaces | tail +2 | awk '{ cmd=sprintf(\"kubectl rollout restart deployment -n %s %s\", $1, $2) ; system(cmd) }'";
```
O un servicio en particular
```nix
krd = "ns=$(kubectl get ns -o jsonpath='{.items[*].metadata.name}' | tr ' ' '\n' | fzf) && \ dep=$(kubectl get deployments -n \"$ns\" -o jsonpath='{.items[*].metadata.name}' | tr ' ' '\n' | fzf) && \ kubectl rollout restart deployment \"$dep\" -n \"$ns\"";
```
Cuando usaba `podman`, `kogs` era `pogs` y `cogs` cuando usaba `docker` y lo mismo aplica para el resto de los aliases, esa consistencia me deja dormir tranquilo. Siempre que me veo haciendo algo seguido, termina en un alias. La flexibilidad que te da que tus soluciones dependan de carpetas y archivos es un *trade-off* que no quiero resignar. Y para mí, la `cli` termina siendo la interfaz definitiva, como dijo Emperor Sh: *["Do it in shell"](https://sanctum.geek.nz/etc/emperor-sh-and-the-traveller.txt)*.

| ![](/images/posts/bash_meme.jpg)| ![](/images/posts/bash_meme2.jpg) |
|---|---|

Como dije en [Jardín de Música](/es/posts/music_hoarding/), creo que hay valor en coleccionar, clasificar y cuidar. Esto es simplemente otra expresión de eso, pero, en vez de canciones, con *scriptcitos* y *hacks* que terminan decorando tu entorno y, por sobre todo, haciéndolo más tuyo.
