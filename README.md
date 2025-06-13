
# Khipu Inside Web – Demo de Pago Integrado

Este repositorio contiene un ejemplo de integración **front-end puro** de la pasarela de pagos chilena [Khipu](https://khipu.com) utilizando su modalidad **Inside Web** (SDK JavaScript embebido en modal/iframe). Permite crear intenciones de pago desde un formulario HTML y lanzar el flujo de autorización directamente en la misma página.

> ⚠️ **Este ejemplo usa credenciales de *pruebas* y está pensado para desarrollos y prototipos.**

---

## ¿Qué hace este ejemplo?

- Despliega un formulario donde el usuario ingresa:
  - **Monto** a pagar (en pesos chilenos, CLP)
  - **Motivo** o asunto del pago (obligatorio)
  - **Descripción adicional** (opcional)
- Al enviar, solicita la creación de una intención de pago a la API REST de Khipu.
- Al obtener un `payment_id`, muestra el modal/iframe de Khipu para autorizar el pago dentro del sitio.
- Muestra mensajes de error si ocurre algún problema.

---

## Estructura de archivos

- `index.html` — Código fuente del demo (todo en un solo archivo).
- No requiere frameworks ni dependencias extra.

---

## Requisitos

- Cualquier **servidor estático** para servir archivos HTML (puedes usar [VSCode Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer), [http-server](https://www.npmjs.com/package/http-server), o simplemente abrir el archivo en un navegador).
- Acceso a internet para cargar el SDK de Khipu.

---

## Uso y pruebas

1. **Clona o descarga** el archivo `index.html`.
2. Abre el archivo en tu navegador, o móntalo en un servidor local.
3. Ingresa los datos requeridos en el formulario.
4. Al hacer clic en "Iniciar pago", se abrirá el modal de Khipu. El flujo es de prueba, no transacciona dinero real.

---

## Detalle del código y flujo

### 1. Inclusión del SDK

```html
<script src="https://js.khipu.com/v1/kws.js"></script>
```

Carga la librería necesaria para lanzar el modal de pagos dentro de tu sitio web.

---

### 2. Formulario

```html
<form id="pay-form">
  <!-- Campos: monto, motivo, descripción -->
</form>
```

Validación en el front para evitar montos inválidos y motivos vacíos.

---

### 3. Lógica de integración (JS)

#### a. **Configuración**

```js
const API_KEY = '...';      // API KEY de pruebas (reemplaza en producción)
const RECEIVER_ID = ...;    // ID de comercio en Khipu
```

#### b. **Envío y creación de pago**

- Envía un POST a `https://payment-api.khipu.com/v3/payments` con los datos requeridos.
- Si la respuesta es correcta, recibe un `payment_id`.

```js
const resp = await fetch('https://payment-api.khipu.com/v3/payments', {
  method : 'POST',
  headers: {
    'Content-Type': 'application/json',
    'x-api-key'    : API_KEY
  },
  body: JSON.stringify({
    receiver_id, amount, currency: 'CLP', subject, body, return_url, cancel_url
  })
});
```

#### c. **Despliegue del modal de pago**

```js
new Khipu().startOperation(
  payment_id,
  callback,
  {
    mountElement: mountEl,
    modal       : true,
    ...
  }
);
```

Esto abre el flujo de pago **dentro de la misma página**, sin redirigir al usuario.

---

## Personalización

- **Colores y tipografía:** puedes modificar `primaryColor` y `fontFamily` en las `modalOptions`.
- **Retorno de usuario:** puedes definir las URL de éxito o cancelación (`return_url`, `cancel_url`) según tu flujo.

---

## Seguridad y consideraciones

- **Nunca publiques tu API KEY de producción en front-end.** Este ejemplo usa claves de prueba y es sólo para demos. Para entornos reales, realiza la creación del pago desde tu backend y sólo expón el `payment_id` al front.
- Puedes consultar la [documentación oficial de Khipu](https://khipu.com/page/api) para más detalles sobre flujos productivos, pruebas y parámetros avanzados.

---

## Recursos

- [Khipu API v3 – Docs oficiales](https://khipu.com/page/api)
- [Khipu Inside Web (modal/iframe)](https://khipu.com/page/khipu-inside-web)
- [Ejemplo oficial en GitHub](https://github.com/khipu/khipu-inside-web-example) *(si está disponible)*

---

## Preguntas frecuentes

- **¿Puedo usar esto en producción?**  
  No, debes crear el pago desde el backend y nunca exponer claves reales en el navegador.

- **¿Por qué usar el modal/iframe y no redirección?**  
  Mejora la experiencia de usuario, no lo saca del flujo de tu web y es más fácil de integrar en SPAs.

---

## Licencia

Uso libre para fines de desarrollo y pruebas. Para producción, consulta los [Términos de Khipu](https://khipu.com/page/terminos).

---

¿Dudas o aportes?  
Contáctanos o revisa la [comunidad de Khipu en GitHub](https://github.com/khipu).

---

**Referencias bibliográficas:**

- Khipu. (2024). *Documentación oficial*. Recuperado de https://khipu.com/page/api  
- Khipu Inside Web JS SDK. (2024). *Manual de integración*. https://khipu.com/page/khipu-inside-web  
- OWASP Foundation. (2023). *Security Recommendations for API Keys*. https://owasp.org/www-project-api-security/
