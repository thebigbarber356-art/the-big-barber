# The Big Barber · guía de puesta en marcha

App web instalable con tres pestañas para clientes (**Turnos**, **Promos** y **Cursos**) y un **Panel** para Ema.

## Archivos

| Archivo | Para qué |
|---|---|
| `index.html` | La app completa |
| `manifest.json`, `sw.js`, `icon-*.png` | Para instalarla como app en el celu |
| `hero.webp` | El sticker recortado |
| `firestore.rules` | Reglas de seguridad de la base (paso 3) |

## ¿Hace falta Firebase?

Para usarla en serio, sí. Los turnos que sacan los clientes, el abierto/cerrado y las promos o precios que cambia Ema tienen que guardarse en un lugar compartido. Si no, cada celu ve solo lo suyo.
Firebase es gratis para este tamaño (plan Spark), alcanza con un Gmail y no pide tarjeta. Conviene crear un Gmail de la barbería y armar todo ahí.
Sin Firebase la app anda en **modo demo**, que sirve para mostrarla.

## 1. Probar ya (modo demo)

Subí la carpeta a un repo de GitHub y activá **Pages** (Settings → Pages → rama `main`, carpeta raíz).
En modo demo todo se guarda solo en el celu que la abre. PIN del panel: `1234` (se cambia en `CONFIG.demoPin`).

## 2. Firebase

1. Entrá a console.firebase.google.com → **Crear proyecto** (sin Analytics).
2. **Firestore Database** → Crear base → modo producción → ubicación `southamerica-east1` (São Paulo).
3. **Authentication** → Comenzar → **Correo electrónico/contraseña** → Habilitar.
   - Pestaña **Usuarios** → Agregar usuario → mail y contraseña de Ema.
   - **Configuración → Acciones del usuario** → desmarcá "Habilitar creación (registro)", así nadie más puede crearse una cuenta.
   - **Configuración → Dominios autorizados** → agregá `TU-USUARIO.github.io`.
4. Engranaje → **Configuración del proyecto** → Tus apps → ícono `</>` (Web) → registrá la app → copiá el objeto `firebaseConfig`.
5. En `index.html`, buscá `const CONFIG` y dejalo así:

```js
const CONFIG = {
  firebase: { apiKey:"...", authDomain:"...", projectId:"...", storageBucket:"...", messagingSenderId:"...", appId:"..." },
  adminEmail: "mail-de-ema@gmail.com",
  demoPin: "1234"
};
```

## 3. Reglas de seguridad

Firestore → **Reglas** → pegá el contenido de `firestore.rules`, reemplazá `MAIL_DE_EMA` por el mismo mail del paso anterior → **Publicar**.

Con estas reglas:
- Cualquiera ve los horarios ocupados (sin nombres), las promos y los cursos.
- Los clientes solo pueden **crear** turnos e inscripciones. Si dos piden el mismo horario, el segundo rebota.
- Nombres y teléfonos de los clientes solo los ve Ema.
- Solo Ema cambia precios, horarios, promos, cursos y el estado abierto/cerrado.

## 4. Primer uso de Ema

1. Instalar la app (ver **Instalar en iPhone y Android** más abajo).
2. Abajo de todo → **Acceso Ema** → entrar.
3. **Ajustes**: WhatsApp (formato `5492604123456`), dirección, horarios, intervalo de la agenda y servicios.
   Cada servicio lleva **categoría (Hombre / Mujer)**, **precio** y **duración**. Un servicio largo, como mechas de 150 min, ocupa varios turnos seguidos y la app solo ofrece horarios donde entra completo.
4. Parado **adentro del local** → **Marcar el local donde estoy** → aceptar el permiso de ubicación.
5. Pestaña **Promos** → **Nueva promo**: precio promo y precio normal (se muestra tachado con el % de descuento), para quién, días que vale, fecha de vencimiento y si se puede sacar turno con ella.
6. Pestaña **Cursos** → editar o agregar cursos (Barbería o Peluquería mujer) con inscripción, valor y cupo.

## Promos

- Con "Se puede sacar turno": el cliente toca **Sacar turno con esta promo** y solo puede elegir los días en que vale.
- Sin turno: aparece **Pedirla por WhatsApp**.
- Las vencidas desaparecen solas para los clientes. Ema las sigue viendo marcadas como "Vencida" para reactivarlas.

## Instalar en iPhone y Android

Es la misma app para los dos. No pasa por App Store ni Play Store: se instala desde el link y queda con ícono propio en la pantalla de inicio.

- **iPhone:** abrir el link en **Safari** → botón **Compartir** (cuadrado con flecha hacia arriba) → **Agregar a inicio** → Agregar. La app muestra un aviso con estos pasos la primera vez.
- **Android:** abrir el link en Chrome → aparece el aviso **Instalar** en la app (o menú ⋮ → **Instalar app**).
- Ya instalada, el aviso no aparece más. Para actualizarla no hay que hacer nada: toma la versión nueva sola al abrirla con internet.

**iPhone de Ema:** la primera vez que active el automático, iOS pregunta por la ubicación. Tiene que elegir **Permitir mientras se usa la app**. Si lo rechazó: Ajustes → Privacidad y seguridad → Localización → Big Barber (o Safari) → Mientras se usa la app.

## Cómo funciona el abierto/cerrado

- En **Automático**, el celu de Ema compara su ubicación con el punto marcado. Dentro del radio (60 m por defecto) y en su horario → **Abierto**. Al alejarse → **Cerrado**. Necesita dos lecturas seguidas para cambiar, así no parpadea.
- **Importante (sobre todo en iPhone):** una app web no puede seguir la ubicación con la app cerrada. Ema tiene que abrir la app cuando llega, y con eso se pone en Abierto.
- Para que no quede "Abierto" de más si se va sin abrirla: en automático, fuera de su horario (con 30 min de margen) se muestra **Cerrado** solo. Así se cierra solo al mediodía y a la noche.
- **Abierto** / **Cerrado** manual pisan al automático: sirven para quedarse fuera de horario o para días libres.

## Avisos de turnos nuevos

- Con la app abierta, cuando alguien saca un turno o se inscribe a un curso, a Ema le aparece un **cartel arriba con sonido**: nombre, servicio, día y hora, con un botón **Ver**.
- La pestaña **Panel** muestra un número con lo que no vio, y arriba del Panel está la lista de **Novedades**. **Marcar todo como visto** la limpia.
- Si abre la app después de un rato, le aparece "Tenés N novedades desde la última vez".
- **Activar avisos del celu** (en Novedades): si la app quedó en segundo plano hace poco, el aviso también sale como notificación del sistema. En iPhone requiere la app instalada en la pantalla de inicio.
- Con la app **cerrada del todo** no llegan avisos. Para eso hace falta una pieza aparte (mail automático o notificaciones push).

## Compartir y difundir

- **Imágenes con la marca:** en Promos y Cursos, cada tarjeta tiene el botón **Imagen** (solo lo ve Ema). En el Panel → **Difundir** está la imagen "Sacá tu turno" con QR y los horarios.
  - Formato **Historia / estado** (vertical, para estados de WhatsApp e historias de Instagram) o **Post** (para el feed de Instagram).
  - **Compartir imagen** abre el menú del celu: elegir WhatsApp → Mi estado, o Instagram → Historia / Publicación. En la compu aparece **Guardar imagen**.
  - Todas llevan un QR que abre la app. La de "Sacá tu turno" sirve también para imprimir y pegar en el local.
- **Compartir link:** todos (clientes también) tienen **Compartir** en cada promo y curso, y **Compartir la app** abajo de todo: WhatsApp directo, Instagram/otras apps o copiar link.

## Pasarle el link a los clientes

- Bio de Instagram, estado de WhatsApp, QR impreso en el local.
- `…/#promos` y `…/#cursos` abren directo esas pestañas.
