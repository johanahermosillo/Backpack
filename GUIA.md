# Cómo publicar Backpack con Firebase y GitHub

Vas a necesitar unos 20 minutos, tu cuenta de Google (johanahermo2023@gmail.com) y una cuenta de GitHub. Todo es gratis.

Al final vas a tener un link como `https://TU-USUARIO.github.io/backpack/` que tú y Aneth abren desde cualquier computadora o celular, entrando con su Gmail.

---

## Parte 1: Firebase (donde se guardan los datos)

### Paso 1. Crea el proyecto

1. Entra a **https://console.firebase.google.com** con tu cuenta de Google.
2. Haz clic en **Create a project** (Crear un proyecto).
3. Nombre: `backpack`. Acepta los términos y dale **Continue**.
4. Cuando pregunte por **Google Analytics**, apágalo (no lo necesitas) y dale **Create project**.
5. Espera a que termine y dale **Continue**.

### Paso 2. Activa el inicio de sesión con Google

1. En el menú de la izquierda, abre **Build** (Compilación) y luego **Authentication**.
2. Dale **Get started** (Comenzar).
3. En la pestaña **Sign-in method**, elige **Google**.
4. Activa el interruptor **Enable**, elige tu correo en **Project support email** y dale **Save**.

### Paso 3. Crea la base de datos

1. En el menú de la izquierda: **Build** y luego **Firestore Database**.
2. Dale **Create database**.
3. Ubicación: elige una de Estados Unidos (por ejemplo `nam5 (United States)`). Dale **Next**.
4. Elige **Start in production mode** y dale **Create**.

### Paso 4. Pon las reglas de seguridad

Estas reglas hacen que **solo tú y Aneth** puedan ver y cambiar los datos. Nadie más, aunque tenga el link.

1. En Firestore Database, abre la pestaña **Rules** (Reglas).
2. Borra todo lo que hay y pega esto:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function isAllowed() {
      return request.auth != null
        && request.auth.token.email_verified == true
        && request.auth.token.email in [
          'johanahermo2023@gmail.com',
          'anethermosillo77@gmail.com'
        ];
    }
    match /{document=**} {
      allow read, write: if isAllowed();
    }
  }
}
```

3. Dale **Publish**.

### Paso 5. Copia la configuración de tu app

1. Arriba a la izquierda, haz clic en el engrane ⚙️ junto a **Project Overview** y elige **Project settings**.
2. Baja hasta **Your apps** y haz clic en el ícono de web: **`</>`**.
3. Nickname: `backpack`. **No** marques Firebase Hosting. Dale **Register app**.
4. Te va a mostrar un bloque de código con algo así:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "backpack-xxxx.firebaseapp.com",
  projectId: "backpack-xxxx",
  storageBucket: "backpack-xxxx.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abc123"
};
```

5. Deja esa pestaña abierta: lo vas a pegar en el Paso 7.

> Es normal que estos datos queden visibles en GitHub. No son contraseñas: lo que protege tus datos son las reglas del Paso 4.

---

## Parte 2: GitHub (donde vive la página)

### Paso 6. Sube la app

1. Entra a **https://github.com** (si no tienes cuenta, créala gratis).
2. Arriba a la derecha, dale **+** y luego **New repository**.
3. Repository name: `backpack`. Déjalo en **Public** (GitHub Pages gratis lo necesita; tus datos siguen protegidos en Firebase).
4. Dale **Create repository**.
5. En la página del repositorio, haz clic en **uploading an existing file** (o **Add file** y luego **Upload files**).
6. Arrastra `index.html` (y si quieres también `GUIA.md`).
7. Dale **Commit changes**.

### Paso 7. Pega tu configuración de Firebase

1. En tu repositorio, haz clic en `index.html` y luego en el lápiz ✏️ (**Edit this file**).
2. Busca esta parte (está cerca del inicio del código, abajo de los `import`):

```js
const firebaseConfig = {
  apiKey: "PASTE_HERE",
  authDomain: "PASTE_HERE",
  ...
};
```

3. Reemplaza cada `PASTE_HERE` con el valor correspondiente del Paso 5. Deja las comillas.
4. Dale **Commit changes** y otra vez **Commit changes**.

### Paso 8. Activa GitHub Pages

1. En tu repositorio, abre **Settings** (arriba).
2. En el menú de la izquierda, elige **Pages**.
3. En **Source** elige **Deploy from a branch**.
4. En **Branch** elige `main` y la carpeta `/ (root)`. Dale **Save**.
5. Espera 1 o 2 minutos y recarga. Arriba aparece: **Your site is live at** `https://TU-USUARIO.github.io/backpack/`. Ese es el link de tu app.

### Paso 9. Autoriza tu página en Firebase

Sin esto, el botón de Google va a dar error.

1. Regresa a Firebase: **Authentication**, pestaña **Settings**, luego **Authorized domains**.
2. Dale **Add domain** y escribe `TU-USUARIO.github.io` (solo eso, sin `https://` ni `/backpack`).
3. Dale **Add**.

---

## Parte 3: Empezar a usarla

### Paso 10. Entra y crea los buckets

1. Abre tu link `https://TU-USUARIO.github.io/backpack/`.
2. Dale **Sign in with Google** y entra con johanahermo2023@gmail.com.
3. Dale **Add our buckets**. Se crean Johana, Aneth (con sus clases, Work, Research, Projects y Orgs) y Johana & Aneth (Lista de compras, Viajes, Pagos).
4. Mándale el link a Aneth. Ella entra con anethermosillo77@gmail.com y ve todo lo mismo que tú, en tiempo real.

---

## Opcional: subir archivos (en lugar de links)

Sin esto, los archivos se agregan como links de Google Drive (sube el archivo a Drive, dale **Compartir**, luego **Cualquier persona con el enlace**, copia el link y pégalo en **Links**; puedes escribir un nombre antes, por ejemplo `Syllabus https://drive.google.com/...`).

Si prefieres subir archivos directo a la app:

1. En Firebase: **Build** y luego **Storage**. Dale **Get started**.
2. Firebase te va a pedir cambiar al plan **Blaze** y registrar una tarjeta. Tiene un nivel gratuito amplio; para dos personas normalmente no cobra nada. Puedes poner una alerta de presupuesto de $1 para estar tranquila.
3. Cuando esté creado, abre la pestaña **Rules** de Storage, borra todo y pega:

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    function isAllowed() {
      return request.auth != null
        && request.auth.token.email_verified == true
        && request.auth.token.email in [
          'johanahermo2023@gmail.com',
          'anethermosillo77@gmail.com'
        ];
    }
    match /{allPaths=**} {
      allow read, delete: if isAllowed();
      allow create, update: if isAllowed()
        && request.resource.size < 20 * 1024 * 1024;
    }
  }
}
```

4. Dale **Publish**.
5. En GitHub, edita `index.html` otra vez y cambia `const USE_STORAGE = false;` por `const USE_STORAGE = true;`. Guarda con **Commit changes**.

---

## Agregar a otra persona más adelante

Hay que poner su correo en dos lugares:

1. **Firebase:** en las reglas de Firestore (y de Storage, si lo usas), agrega su correo a la lista entre corchetes y dale **Publish**. Ejemplo: `'johanahermo2023@gmail.com', 'anethermosillo77@gmail.com', 'otra@gmail.com'`.
2. **GitHub:** en `index.html`, agrégalo a `ALLOWED_EMAILS` y guarda.

---

## Si algo no funciona

- **"This website isn't allowed in Firebase yet"**: falta el Paso 9, o el dominio está mal escrito.
- **"Google sign-in isn't turned on"**: falta el Paso 2.
- **"Your account doesn't have access"** después de entrar: el correo no está en las reglas del Paso 4, o las reglas no se publicaron.
- **La página dice "Almost there"**: todavía hay algún `PASTE_HERE` en `index.html` (Paso 7).
- **La página no se ve después de cambiar algo en GitHub**: espera 1 o 2 minutos y recarga.
