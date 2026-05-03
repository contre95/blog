---
title: "X4 Xteink"
date: 2026-05-01T20:00:47+02:00
tags:
  - Tech
---

![X4 Screen](/images/posts/x4_banner.jpg)

Este post va a ser parecido al del [iPod](/es/posts/ipod_fix/) pero sobre el Xteink X4, un poco más de hardware y enfocado a quien quiera arreglar su pantalla. El X4 es un poco menos conocido así que dejame contarte de qué va primero. [Xteink X4](https://www.xteink.com/products/xteink-x4) es un mini lector de tinta, como una Kindle pero que destaca por su tamaño, es chiquito. Pero el tamaño no es su única bondad, además es extremadamente barato. Lo podés conseguir en AliExpress por $65 pesos. Parte de porqué es tan barato, es porque es chino, que si te quedaste en la pre-historia, te cuento que esto no es sinónimo de malo, todo lo contrario. El X4 está hecho de un hardware muy sencillo, lo componen una ESP32-C3 Series, una batería de litio (1200 mAh) que dura semanas, WiFi, Bluetooth y una pantalla de tinta de 4.2 pulgadas (400x300) bastante común que podés conseguir por $18 pesitos, también en AliExpress.

## Lo quiero, ¿Qué hago?

Antes dejame decirte que este bichito no es para cualquiera. Si sos ese tipo humano que para consumir contenido necesita un portal/store de apps/películas/música/videos/libros, puede que esto no sea para vos. Principalmente porque lee solamente `.epub`, la conexión a internet es prácticamente nula y la interfaz es desastrosa. Bueno, nada de esas 3 cosas son del todo verdad, pero veamos por qué. El loco, además de bancarse un epub, también le podés pasar `.txt` y `.xtc` como formato, eso sí, para eso vas a tener que conseguir el archivo/libro de alguna u otra manera. Te recomiendo a los *Archivos de Anna* o algún que otro bot de Telegram, me dijeron que ahí se consiguen buenos libros de piratas. Para cargar esos libros vas a tener que hacerlo con un lector de tarjeta Micro SD y una computadora. Acá la segunda mentira ya que también lo podés hacer por Wifi, el problema es que la conexión no suele ser la mejor, vas a tener que escribir la contraseña en una interfaz horrible, y vas a tener que entrar por la IP que le haya dado tu router al aparatito. Por último, la interfaz: El OS con el que te viene está todo en Chino, que si estás leyendo esto, probablemente no hables, y es casi inutilizable. Los botones son confusos y la pantalla no es táctil.

![X4 Xteink fix](/images/posts/x4_banner2.jpg)

Al ser el hardware principal un ESP32-C3, apenas el X4 salió al mercado, la gente no hizo otra cosa que vibecodear una cantidad innecesaria de sistemas operativos, la mayoría usando [PlatformIO](https://platformio.org/). Si no sabés lo que es vibecodear, es cuando una o varias personas que no saben leer ni escribir código, sin ningún tipo de reparo ético, manifiestan su idea de software que les gustaría tener a un oráculo, que básicamente lo que hace es tirar mierda contra una pared hasta que eventualmente le sale un Pollock. Resulta que este oráculo es bastante bueno tirando mierda y ahora los Pollocks abundan. Para el X4 habré visto al menos 5 distintos sistemas operativos y algunos con varios forks (distintas versiones), el problema con estos, es que a diferencia del Pollock, no son un cuadro, y después de ser vibecodeados, necesitan manutención; Acto que al vibecoder le es completamente ajeno (-5 tokens para el vibecoder). Fin del rant, seguimos: De entre los sistemas que podés instalar, el que va muy bien es [Crosspoint](https://github.com/crosspoint-reader/crosspoint), ya van por la versión `1.2.0` y yo lo vengo usando al menos desde la `0.5.X`, si mal no recuerdo. Los updates son estables y las features no abundan pero funciona. Dado que estamos hablando de Firmware en este caso, considerá que es válido instalar Crosspoint y no volver a actualizar nunca más (+5 tokens para el vibecoder). Acordate que compraste este wachin para leer, no para [emular juego de gameboy](https://github.com/lualiliu/esp32-gameboy). Antes de contarte algunas cosas que te pueden servir si necesitás cambiar la pantalla, te dejo algunos links que estoy seguro que te van a ser útiles:

* [reddit.com/r/xteinkereader/](https://www.reddit.com/r/xteinkereader/)
* [readme.club](https://www.readme.club/)
* [AliExpress](https://es.aliexpress.com/item/1005011961291372.html) -> Comprarlo por AliExpress tarda infinitamente menos que por la página.
* [EPUB to XTC Converter](https://x4converter.rho.sh)
* [Image to BMP Converter](https://aryascripts.github.io/page-apps/bmp-convert/)
* [PDF/CBZ/CBR to XTC](https://xtcjs.app/pdf/)

## Arreglando la pantalla

![X4 screen open](/images/posts/x4_open.jpg)

Rompí ambos vidrios que trae el lector, la parte de atrás, que se partió en varios pedazos cuando se me cayó en las frías calles de Madrid, y la pantalla de adelante, pisándola cuando me levanté al baño semi dormido. La parte de atrás se solucionó comprando una funda y poniendo un protector de pantalla para que no se caigan los pedacitos. Para la pantalla de adelante tenés 3 opciones, o comprás la pantalla original, o comprás la pantalla genérica ([link](https://es.aliexpress.com/item/1005007173571196.html)) que compré yo o te comprás un X4 nuevo. Lo último es lo más viable, pero lo más aburrido. Acá te dejo un par que están en la misma -> [Post 1](https://www.reddit.com/r/xteinkereader/comments/1sikato/screen_replacement_for_the_xteink_x4), [Post 2](https://www.reddit.com/r/xteinkereader/comments/1sd3g93/x4_dead_screen/), [Post 3](https://www.reddit.com/r/xteinkereader/comments/1se0k8c/yet_another_broken_screen_post/)

Ya sea que compres cualquiera de las pantallas, vas a tener que desarmar la rota, es muy fácil: Desde el frente, ya podés desarmar toda la pantalla con cualquier tipo de cuchillito o herramienta fina, si podés calentarla mejor. Yo no tengo una de esas pistolas de calor, así que la calenté un poco en la cama de la impresora 3D. Ojo, tampoco te pongas a hacer huevos fritos. Lo más o menos jodido puede ser sacar/poner el flex. A diferencia de lo que me dijo ChatCCP, este flex no tiene una trabita (o "latch" en inglés), que muchos otros si tienen. Este va a presión. Yo lo que hice fue hacer presión con el dedo en el flex contra la PCB para que no se doble, mientras jalaba para afuera/adentro para sacarlo o volverlo a poner. Ponerlo es más jodido que sacarlo. Acordate de sacar foto a todo para ver qué tan adentro tiene que ir el flex del socket. 

![x4_border.gif](/images/posts/x4_border.gif)

Si comprás la pantalla genérica, vas a notar que no viene con todo el contorno que el X4 necesita. Sacárselo a la pantalla que te viene es prácticamente imposible, necesitás más calor y aún más paciencia. Lo que podés hacer es pegar la pantalla a la batería y usarlo como viene, pero queda feo y es propenso a romperse. Yo lo que hice fue diseñar e imprimir el contorno y luego pegar sobre la pantalla. Quedó bastante bien pero no perfecto. Mis habilidades con blender van y vienen según el modelo, pero creo que llegué a un resultado decente. Te dejo el `.stl` por si algún día estás en la misma -> [x4_screen_border.stl](/files/posts/x4_screen_border.stl)

