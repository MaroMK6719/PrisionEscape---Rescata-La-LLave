# Rescata la llave · Minijuego 6.2 · PLATYBYTES

Minijuego **Rescata la llave** del proyecto *Prison Escape* (FP.455, Grupo 2). Se puede jugar por separado y está preparado para unirse a la partida completa con los otros cuatro minijuegos.

El jugador guía una bola con la llave por **5 laberintos** inclinando el móvil, esquiva a los guardias y llega a la cerradura antes de que pasen **60 segundos**. Incluye paredes fijas, de rebote, pegajosas y móviles, guardias quietos y que patrullan, estrella opcional, invulnerabilidad de 1 segundo tras un golpe, calibración de la inclinación y pantalla completa en horizontal. La puntuación va de **0 a 100**: +12 por laberinto, +4 por estrella, −3 por guardia y hasta +20 por el tiempo sobrante.

## Requisitos

- **Unity 6** (probado con 6000.6), plantilla **Universal 2D**.
- Paquete **Input System** (incluido en la plantilla).
- **Android Build Support** con OpenJDK y SDK & NDK para probar en el móvil.

## Cómo añadirlo a un proyecto

1. Copia la carpeta `Assets/Platybytes/RescataLaLlave` (con sus archivos `.meta`) dentro de `Assets` de vuestro proyecto.
2. Pulsa **Platybytes > Rescata la llave > Crear escena** en la barra de menús de Unity.
3. Se abre la escena `RescataLaLlave`. Pulsa **Play**.

La escena solo contiene un objeto con el componente `RescataLaLlave`. La cámara, la luz 2D, el laberinto, la bola y el HUD se crean por código. Los archivos `.meta` llevan identificadores fijos, así que el componente funciona igual en el ordenador de cualquier miembro del equipo. **No los borréis.**

## Cómo probar

- **En el editor:** flechas o WASD simulan la inclinación. Pon la vista **Game** en una resolución horizontal (por ejemplo, 1920x1080) y usa **Play Maximized** para verlo a pantalla completa.
- **En el móvil:** en **File > Build Profiles**, activa el perfil Android, desmarca **Export Project**, elige el móvil en **Run Device** y pulsa **Build And Run**. El móvil debe tener activada la depuración USB. También puedes pulsar **Build** y pasar el `.apk` al móvil.
- Al empezar, la postura con la que sujetas el móvil se toma como posición neutra. El botón **CALIBRAR** la vuelve a tomar.

## Cómo se une a la partida completa

Todo el código está en el espacio de nombres `Platybytes.Llave`, así que no choca con clases de otros minijuegos aunque tengan el mismo nombre. La única conexión es la clase `ConexionPartida`:

```csharp
using Platybytes.Llave;

// Antes de cargar la escena del laberinto
ConexionPartida.Vidas = vidasDelJugador;

// Una sola vez, por ejemplo en el Awake del GameManager común
ConexionPartida.AlTerminar += AlTerminarRescataLaLlave;

void AlTerminarRescataLaLlave(bool superado, int puntos)
{
    vidasDelJugador = ConexionPartida.Vidas;   // los guardias pueden haber quitado vidas
    puntosTotales += puntos;                    // de 0 a 100
    // cargar la siguiente prueba, repetir esta o ir a la pantalla final
}
```

Si nadie se suscribe a `AlTerminar`, el minijuego funciona en **modo de prueba** y al acabar muestra un botón **REINTENTAR**. Al salir de la escena, deja la gravedad 2D y la configuración del acelerómetro como estaban, para no afectar a los demás minijuegos. `Sonidos.Volumen` y `Sonidos.VibracionActivada` se pueden enlazar con el menú de opciones común.

## Ajustes de diseño

Los valores están al principio de `Scripts/RescataLaLlave.cs`, con comentarios: duración, puntos, fuerza y sensibilidad de la inclinación, ejes invertidos (`InvertirX`, `InvertirY`) y si agotar el tiempo quita todas las vidas, como dice el GDD, o solo una.

Los laberintos están en `Scripts/Laberintos.cs`, dibujados como texto de 21 columnas y 11 filas:

| Carácter | Significado | Carácter | Significado |
|---|---|---|---|
| `#` | Pared fija | `B` | Salida de la bola |
| `R` | Pared de rebote | `G` | Guardia quieto |
| `S` | Pared pegajosa | `P` | Guardia que patrulla |
| `M` | Pared móvil horizontal | `E` | Estrella |
| `V` | Pared móvil vertical | `C` | Cerradura (meta) |
| `.` | Suelo | | |

Se ha comprobado automáticamente que en los 5 laberintos hay camino hasta la cerradura y hasta la estrella.

## Problemas frecuentes

- **Error de compilación con `InputSystem`:** Edit > Project Settings > Player > Active Input Handling > **Input System Package (New)** o **Both**.
- **La bola va al revés en el móvil:** pon `InvertirX` o `InvertirY` a `true` en `RescataLaLlave.cs`.
- **El móvil no aparece en Run Device:** prueba otro cable USB (algunos solo cargan), pon el USB en modo **Transferencia de archivos** y acepta el aviso de depuración USB.

## Pendiente

El arte y los sonidos son **provisionales**: se dibujan y se generan por código con la paleta del GDD. Para usar los definitivos, basta con cambiar lo que devuelven las funciones de `Graficos.cs` (bola, guardia, estrella, cerradura, paredes) y de `Sonidos.cs`.

## Estructura

```
Assets/Platybytes/RescataLaLlave/
├── Editor/RescataLaLlaveSetup.cs   Menú que crea la escena y la añade a Build Settings
├── link.xml                        Conserva la luz 2D en las compilaciones de móvil
└── Scripts/
    ├── RescataLaLlave.cs           Controlador: flujo, HUD, puntos, construcción de niveles
    ├── Laberintos.cs               Los 5 laberintos
    ├── ConexionPartida.cs          Conexión con el GameManager común
    ├── Bola.cs                     Física de la bola y colisiones
    ├── Guardia.cs                  Guardias quietos y que patrullan
    ├── ParedMovil.cs               Paredes que se mueven con la inclinación
    ├── ParedRebote.cs · ParedPegajosa.cs · EstrellaNivel.cs · CerraduraNivel.cs
    ├── Graficos.cs                 Sprites provisionales, cámara, luz 2D y UI
    ├── Sonidos.cs                  Sonidos provisionales y vibración
    └── Paleta.cs                   Colores del GDD
```

**Autor:** Marouan Mnari Klai · PLATYBYTES · FP.455 · UOC
