# 🐔 CHICKEN WAR – versión móvil (Android / iOS)

> hecho por renato y su duro esfuerzo de la IA

## 1. Análisis del proyecto original
| Aspecto | Resultado |
|---|---|
| Tipo | Un solo archivo `CHICKEN_WAR.html` (~50 KB) |
| Motor gráfico | **Three.js r128** (WebGL), sin texturas externas: todo se genera por código |
| Lenguaje | JavaScript (ES2015+) + HTML/CSS |
| Audio | Web Audio API (sonidos sintetizados, sin archivos) |
| Multijugador | PeerJS (WebRTC P2P) – salas con código |
| Dependencias | three.min.js (CDN) y peerjs (CDN, carga bajo demanda) |
| Controles originales | Teclado + ratón + bloqueo de puntero |

Como es un juego web, la adaptación real es: **HTML5 móvil + empaquetado nativo con Capacitor** (WebView nativo
de Android/iOS), con Three.js y PeerJS **incluidos dentro de la app** para que funcione sin internet (salvo el modo online).

## 2. Qué se adaptó
- **Controles táctiles**: joystick analógico (empujar al borde = correr), arrastrar la pantalla para apuntar,
  botones FUEGO (también permite apuntar mientras se mantiene), SALTO, RECARGAR, E (recoger), arma anterior/siguiente,
  barra de armas y pausa.
- **Botones personalizables**: Configuración → *Editar botones táctiles* (o desde Pausa). Se arrastran, cambian de tamaño
  y de opacidad; se guarda el diseño.
- **Menús**: botones grandes, rejilla de 2 columnas, safe-area (notch), soporte vertical/horizontal, teclado sin zoom.
- **HUD compacto** reubicado para no tapar los controles.
- **Orientación**: horizontal (bloqueada en la APK; en navegador pide girar).
- **Rendimiento / batería**: límite de FPS (30 / 60 / sin límite), resolución dinámica ajustada al límite elegido,
  calidad gráfica automática según el equipo, menos partículas en calidad baja, GPU en modo normal (no "high-performance"),
  render a 30 FPS en menús, pausa automática al salir de la app, pantalla encendida solo mientras se juega.
- **Guardado**: perfil (nombre, gallina, mapa, duración, bots, sensibilidad, volumen, calidad, FPS, botones táctiles),
  estadísticas (partidas, victorias, bajas) y **partida en curso** (botón *Continuar partida*: tiempo, bajas, armas,
  munición, vida y posición). Guardado automático cada 5 s y al cerrar/minimizar.
- **Créditos** dentro del juego (menú principal y pantalla *Créditos*): "hecho por renato y su duro esfuerzo de la IA".
- **Se conserva** todo el contenido: 7 armas, 5 mapas, personalización, bots, power-ups, física, sonidos, animaciones
  y multijugador online. El teclado y ratón siguen funcionando en PC.

## 3. Obtener el APK (la forma más fácil, sin instalar nada)
1. Crea una cuenta gratis en https://github.com y un repositorio nuevo (privado o público).
2. Sube TODO el contenido de esta carpeta (incluida la carpeta oculta `.github`). Si el navegador no sube carpetas ocultas,
   crea el archivo a mano: *Add file → Create new file* → nombre `.github/workflows/build-android.yml` y pega su contenido.
3. Ve a la pestaña **Actions → Compilar APK de Android → Run workflow** (también se lanza solo al subir cambios).
4. A los ~5 minutos entra en la ejecución y descarga **ChickenWar-APK** (contiene `app-debug.apk`).
5. Pasa el APK al teléfono, ábrelo y permite "instalar apps de orígenes desconocidos".

El APK de depuración se instala y juega con normalidad. Para publicar en Google Play hay que firmar una versión *release*.

## 4. Compilar en tu PC (alternativa)
Requisitos: Node 18+, JDK 17, Android Studio (SDK 34).
```bash
npm install
npm run android:init     # copia three/peerjs, crea android/, aplica horizontal + icono + inmersivo
npm run android:apk      # genera android/app/build/outputs/apk/debug/app-debug.apk
```
Para abrir el proyecto en Android Studio: `npx cap open android`.

## 5. iOS
Apple solo permite compilar iOS en un Mac con Xcode (y una cuenta de Apple Developer para instalarlo en un iPhone real).
```bash
npm install
npm run ios:init
npm run ios:open        # abre Xcode: elige tu equipo de firma y pulsa ▶
```
## 6. Probar rápido sin compilar
`www/index.html` (o `ChickenWar_Movil.html`) funciona directamente en el navegador del teléfono si se sirve por
HTTPS/HTTP (por ejemplo, subiéndolo a GitHub Pages). Carga Three.js desde CDN si no encuentra `lib/three.min.js`.

## 7. Estado de las pruebas (sé honesto con esto)
- ✅ Comprobado en Chromium móvil emulado (844×390 y 390×844): menús, joystick, botones, apuntado, pausa, editor de botones,
  guardado/carga, continuar partida, rotación, créditos. Sintaxis JS verificada.
- ⚠️ El motor 3D se simuló en esa prueba (sin internet no se podía descargar Three.js), así que **FPS reales y render en
  dispositivos físicos aún no están medidos**. Prueba en tu teléfono y ajusta *Calidad* y *Límite de FPS* en Configuración.
- ⚠️ El APK y el proyecto iOS no se pudieron compilar en el entorno donde se generó este proyecto (sin Android SDK ni red);
  el flujo de la sección 3 los compila por ti en la nube.

## 8. Cambios v1.1 (cámara táctil e interfaz móvil)
- **Cámara**: los deslizamientos se aplican una vez por fotograma con suavizado y aceleración opcionales; la escala depende de la altura de la pantalla (se siente igual en cualquier móvil/tablet); zona muerta de 4 px al tocar; lectura de todos los puntos intermedios del dedo.
- **Configuración nueva**: sensibilidad táctil, sensibilidad vertical, suavizado (Directo/Bajo/Alto), aceleración (Ninguna/Suave/Fuerte), eje vertical invertido y joystick fijo/flotante. Se guardan en el perfil.
- **Joystick flotante** (por defecto): toca en cualquier punto de la mitad izquierda y el joystick aparece bajo tu dedo.
- **HUD**: respeta el notch/bordes en horizontal (antes los márgenes seguros no se aplicaban), marcador compacto (top 3 + tu posición) y barra de armas más grande.
- **Menús**: Configuración en 2 columnas en horizontal y botón VOLVER siempre visible.
- ⚠️ Cambios verificados solo con comprobación de sintaxis; ajusta la sensibilidad en tu teléfono.

## 9. Cambios v1.2
- **Menú principal** más pequeño y compacto (botón JUGAR grande + 2 columnas). La ayuda pasó a **CÓMO JUGAR**.
- **Personalización** con pestañas (Color, Cresta, Sombrero, Gafas, Extra), panel pequeño a la izquierda y la gallina visible a la derecha. **Arrastra para girarla 360°** (con inercia; gira sola en reposo).
- **Ajustes** con pestañas (Partida, Cámara, Controles, Gráficos) y valores visibles en los deslizadores.
- **Chat en partida**: botón 💬 (movible en el editor de botones) o tecla Enter en PC. En online se envía a todos los jugadores de la sala; en solitario los bots responden a veces.

## 10. Cambios v1.3
- **Sonidos de disparo** rediseñados (capas de chasquido, cuerpo, golpe grave y eco/reverberación; cada arma suena distinta, con panorámica estéreo y atenuación por distancia). Compresor general para que no saturen.
- **Barra de escudo** (azul, sobre la de vida, hasta 100): el daño se descuenta primero del escudo y, cuando se acaba, pasa a la vida. Los demás jugadores ven tu escudo junto al nombre.
- **Pickups**: la vida ahora son **corazones rojos** (+40) y hay **escudos azules** con forma de escudo (+50). Se quitó el viejo power-up azul temporal.
- **Mapa más grande** (de 96×96 a 160×160) con más objetos y más pickups. Bots por defecto: 6.
- **Online**: el anfitrión elige el mapa en la pantalla de la sala antes de crearla y puede cambiarlo en partida desde Pausa; los invitados cambian al instante al mapa elegido. Los pickups ya aparecen en las mismas posiciones para todos.
- **Cosméticos**: la **capa** cae sobre la espalda y los hombros y la **mochila** va sobre el lomo (ya no quedan detrás de la cola). Además se corrigió que los accesorios de otros jugadores no se veían en online.
- ⚠️ Verificado solo con comprobación de sintaxis y prueba del audio con un AudioContext simulado; el render 3D no pudo probarse aquí. Prueba en el teléfono y ajusta Calidad/FPS si el mapa grande va justo.

## 11. Cambios v1.4
- **Personalizar gallina → pestaña "Trajes"**: trajes completos inspirados en *Hanako-kun* (uniforme oscuro con ribete rojo, botones dorados, gorra, papelito en la mejilla y dos llamas flotantes) y *Nene Yashiro* (vestido crema con falda, mangas abullonadas, faja con lazo, cabello claro con mechones de puntas verdes). El traje reemplaza sombrero, gafas y extra; el color de la gallina sigue aplicándose a la cabeza y alas. Se guarda en el perfil, se ve en online y algunos bots lo usan.
- ⚠️ Solo verificado con comprobación de sintaxis; revisa cómo se ven en el teléfono.

## 12. Cambios v1.5 (diseños, animaciones y cubeta de pollo frito)
- **Cuando un pollo muere se transforma en una cubeta de pollo frito** (rayas rojas y blancas, etiqueta "POLLO FRITO", muslos y nuggets dorados, vapor). Sale una nube de humo, plumas y trozos de pollo, con sonido de fritura. Al reaparecer vuelve a ser pollo con un efecto de rebote. Vale para ti, bots y jugadores online.
- **Pollo rediseñado**: ojos con brillo y parpadeo, pico de dos piezas, cresta de tres picos, barbilla, mejillas rosadas, pecho claro, cola de plumas, alas con punta y patas con dedos y muslos.
- **7 armas rediseñadas** con más detalle (miras, cargadores, lentes del francotirador, ojiva del cohete, huevo en el cañón, aletas de plumas).
- **Objetos del mapa**: casas con marcos, contraventanas, floreros y escalón; árboles más frondosos; cajas con travesaños; barriles y cactus más redondos; rocas con musgo; fardos con cuerdas; postes con brazo y farol; torres con aire acondicionado y baliza. Hierba/destellos y nubes (no en la arena futurista).
- **Recogibles** con haz de luz y anillo en el suelo.
- **Animaciones**: respiración, cabeceo al caminar, cola que se mueve, parpadeo, aplastamiento al aterrizar, retroceso del arma, destello de disparo, polvo al correr, estela de humo de cohetes, humo en explosiones y temblor de cámara al recibir daño o por explosiones cercanas.
- **Rendimiento**: los objetos estáticos del mapa se fusionan por material (muchas menos llamadas de dibujo).
- ⚠️ No se pudo probar el render 3D en esta entorno; si algo falla en tu móvil, avísame.

## 13. Cambios v1.6
- **Portada**: `www/cover.jpg` se muestra al abrir el juego (toca para entrar). Para cambiarla, reemplaza ese archivo.
- **5 armas nuevas**: Subfusil, Rifle de asalto, Granadas de maíz (explosivas), Ballesta y el arma especial **LÁSER DEL GALLO**.
- **LÁSER DEL GALLO**: aparece UNA sola vez en el centro del mapa (a los 12 s de empezar), con aviso en pantalla; al tomarlo se anuncia quién lo tiene y reaparece 2 minutos después. Se pierde al morir. En online avisa a todos y el reloj de 2 min corre en todas las pantallas.
- **Emotes** (GG, EZ, NOOB, JAJA, FUEGO, A FREÍR) sobre la cabeza del pollo: botón 😎 en pantalla (movible en el editor de botones) o teclas Z, X, C, V, B, N en PC. Se ven en online y los bots también los usan.
- Teclas 1–9 eligen arma en PC. ⚠️ No probado en pantalla real.

## 14. Cambios v1.7 (mira, puntería, apuntar y power-ups)
- **Mira dinámica**: punto central + 4 marcas que se **abren y cierran con la dispersión real del arma** (quieto < andando < corriendo < en el aire; al disparar seguido se abre y se recupera). Se pone **roja** cuando tienes a un enemigo justo bajo la mira (si no hay pared en medio) y muestra un **anillo de recarga**.
- **Disparo mucho más preciso**: antes la bala apuntaba a un punto fijo a 60 m, así que con la cámara al hombro fallaba a media/corta distancia. Ahora se lanza un rayo exacto desde el centro de la mira (pared, suelo o enemigo) y la bala sale del arma hacia ese punto. Además:
  - Las armas con caída (**Cañón de huevos, Granadas de maíz, Ballesta, balas**) compensan la gravedad: caen justo donde apuntas (antes el huevo/granada podía quedar 3-12 m corto).
  - Dispersión nueva: quieto es más preciso que antes; correr o saltar la empeora; la distribución se concentra al centro (los perdigones de escopeta/plumas mantienen su abanico).
- **Apuntar (ADS)**: **PC** → mantén clic derecho (o tecla Q para alternar). **Móvil** → botón **MIRA** (toca para activar/desactivar; movible en el editor de botones). Al apuntar: zoom por arma, cámara más cerca, dispersión ~70 % menor, caminas más lento y la sensibilidad baja con el zoom. El **Francotirador** pasa a **visor en primera persona** (zoom x3, sin dispersión si estás quieto; sin apuntar es impreciso).
- **Ayuda de apuntado táctil**: la cámara se frena un poco cuando la mira pasa sobre un enemigo (solo fricción; no corrige las balas).
- **Power-ups con forma nueva**: la velocidad ahora es un **zapato deportivo azul** 👟 y el daño x2 una **espada roja** ⚔️. Al recogerlos sale un aviso, suena un efecto y aparece un **indicador con icono y cuenta atrás** bajo el reloj. Con la velocidad la cámara se abre un poco (efecto de carrera).
- ⚠️ Los botones táctiles se **restablecen una vez** a la posición por defecto (hay un botón nuevo y las flechas de arma se movieron); vuelve a personalizarlos si quieres.
- ⚠️ Pruebas hechas: sintaxis, pruebas numéricas del rayo/dispersión/balística, y arranque del juego en Chromium (PC y móvil) con un motor 3D **simulado**, sin errores de JavaScript. El render 3D real y la sensación al apuntar no se pudieron probar aquí: ajusta en el código si hace falta `ZM` (zoom por arma), `CAMA`/`CAMS` (cámara al apuntar) y `calcSpread()` (dispersión).

## 15. Cambios v1.8 (amigos, presencia en línea e invitaciones sin código)
- **Menú principal → AMIGOS** (con insignia 🟢 = amigos en línea, 🔔 = solicitudes). Cada jugador tiene un **código de amigo fijo de 8 caracteres** (se guarda en el dispositivo; botones COPIAR y COMPARTIR).
- **Añadir amigos**: escribe el código de tu amigo → le llega una solicitud y, al aceptarla, ya son amigos. Si ambos se añaden a la vez, la amistad es automática. Puedes rechazar (no vuelve a molestar) o eliminar amigos.
- **Ver cuándo se conectan**: punto verde en la lista, estado ("En línea", "Jugando", "En una sala (3/8)", "Desconectado") y aviso 🟢 *«X se conectó»* al instante, incluso dentro de una partida.
- **Invitar sin código**: en la lista toca **INVITAR** (si no estás en sala, se crea una y se envía la invitación) o, dentro de una partida online, abre **Pausa → Invitar amigos a tu sala**. Al amigo le sale un aviso **UNIRME / IGNORAR** que lo mete directo a la sala. Si un amigo ya está en una sala, verás **UNIRSE** en su fila.
- **Privacidad**: solo tus amigos aceptados ven tu estado y tu sala, y solo ellos pueden invitarte. Los datos de amigos se guardan aparte del perfil (BORRAR DATOS GUARDADOS no los borra).
- **Cómo funciona (sin servidor propio)**: cada jugador se registra con su código en el servidor gratuito de PeerJS mientras la app está abierta; el estado y las invitaciones viajan directo entre dispositivos (WebRTC).
- ⚠️ Limitaciones: solo se ve "en línea" a quien tiene el juego abierto (no hay notificaciones push con la app cerrada); depende del servidor público de PeerJS; el código no tiene contraseña (no lo compartas con desconocidos). Para push y cuentas reales haría falta un backend propio (p. ej. Firebase).
- ⚠️ Pruebas: sintaxis del juego y protocolo completo (solicitud, aceptación, presencia, invitaciones, rechazo, eliminación, desconocidos) con un PeerJS simulado. **No probado aún en dos teléfonos reales**.

## 16. Cambios v1.9 (calidad visual)
- **Cielo con degradado** por mapa (antes color plano) y niebla más lejana y suave, acorde al mapa grande.
- **Iluminación**: sol cálido, luz ambiental tintada con el color del cielo de cada mapa.
- **Sombras suaves** (PCF soft) con corrección de bias: menos "dientes" y artefactos.
- **Antialiasing** activado en PC y en móviles con calidad Media/Alta (en Baja sigue apagado por rendimiento).
- **Viñeta** sutil sobre la pantalla (sin coste de GPU).
- ⚠️ Solo verificada la sintaxis; el render 3D real no se pudo probar aquí. Si va justo en tu móvil, baja *Calidad*.

## 17. Cambios v2.0 (animaciones y sonidos)
- **Sonidos nuevos**: pasos (con panorámica y distancia, también de bots y rivales), salto, aterrizaje con golpe y sacudida de cámara, marcador de impacto (tic normal / ding agudo en cabezazo), jingle al eliminar, quejido al recibir daño, cambio de arma, recarga en 3 tiempos (sacar, meter, cerrojo) y clic en los botones de los menús.
- **Ambiente**: música procedural suave y viento/pájaros mientras juegas (muy baja; sigue el volumen general).
- **Animaciones**: picoteo ocasional al estar quieto, inclinación hacia delante al correr, sacudida al recibir daño, arma que se levanta al cambiarla, arma inclinada y temblorosa al recargar, retroceso de cámara al disparar, destello rojo de pantalla al ser herido.
- **Menús**: paneles que entran con fundido y botones con efecto al pulsar.
- ⚠️ Solo verificada la sintaxis; los sonidos y animaciones no se pudieron escuchar/ver aquí. Si algo suena fuerte o la música molesta, dímelo y lo ajusto o añado un interruptor.

## 18. Cambios v2.1 (modo administrador)
- **Menú principal → 🔧 ADMIN**: pide el código de creador. Una vez correcto queda desbloqueado en ese dispositivo (botón *Bloquear admin* para quitarlo).
- **En partida**: escribe `/admin` en el chat (💬 en móvil, Enter en PC) para abrir/cerrar el panel. Si no está desbloqueado, el chat avisa de que hay que usar el código en el menú.
- **Poderes**: ✈️ volar (Saltar = subir, Ctrl o botón ⬇ = bajar, quieto = flotar), ❤️ vida infinita, 🔫 munición infinita, ⚡ súper velocidad, 💥 daño x50, 🩹 curar y recargar, 🎒 todas las armas, 💀 eliminar bots.
- ⚠️ Seguridad: el juego es HTML/JS en el dispositivo, así que el código no está en texto plano pero **no es seguridad real**: cualquiera con conocimientos puede leerlo o saltárselo. Sirve para uso del creador, no para impedir trampas en online.
