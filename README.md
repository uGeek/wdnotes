# wdnotes - Un Bloc de Notas Autoalojado basado en WebDAV

`wdnotes` es una aplicación web de una sola página (SPA) para tomar notas, inspirada en la simplicidad de Google Keep pero con un enfoque en la privacidad, el control de los datos y la portabilidad. No requiere bases de datos ni un backend complejo; simplemente necesita un servidor WebDAV para almacenar tus notas como archivos HTML individuales.

Esto significa que tus notas son archivos legibles y portátiles que puedes sincronizar, respaldar y gestionar con las herramientas que ya usas.

Versión Móvil

<img width="579" height="796" alt="image" src="https://github.com/user-attachments/assets/47d7eb28-2986-40f2-a0cf-ea27474e420d" />


Versión Escritorio


<img width="1318" height="777" alt="image" src="https://github.com/user-attachments/assets/48aad3d9-18d3-4544-b189-90b40d730d89" />


Múltiples opciones de edición

<img width="590" height="945" alt="image" src="https://github.com/user-attachments/assets/8d7d3a2c-0008-4a03-b55e-3d50dec9d3aa" />



---

## ✨ Características Principales

- **Autoalojado y Sin Dependencias:** Funciona con cualquier servidor WebDAV (Nextcloud, Synology, Apache, etc.). Tus datos, tus reglas.
- **Formato Abierto:** Cada nota es un archivo `.html` independiente. Fácil de leer, respaldar y migrar.
- **Editor de Texto Enriquecido:** Soporte para títulos, negrita, cursiva, listas, checklists, bloques de código, tablas y más.
- **Organización Flexible:**
    - **Etiquetas:** Añade etiquetas directamente en el título (`#etiqueta`) o a través de la interfaz.
    - **Fijar Notas:** Mantén las notas importantes siempre a la vista.
    - **Archivo y Papelera:** Mantén tu espacio de trabajo limpio sin perder nada.
    - **Colores:** Asigna colores a las notas para una mejor organización visual.
- **Búsqueda Universal:** Una única barra para buscar notas por título, contenido o etiqueta, o para crear una nueva nota al instante.
- **Funcionalidades Avanzadas:**
    - **Wikilinks:** Enlaza notas entre sí usando la sintaxis `[[Nombre de la Nota]]`.
    - **Adjuntar Archivos:** Sube archivos que se almacenan junto a tus notas.
    - **Historial de Versiones:** Guarda automáticamente copias de seguridad de tus notas al modificarlas.
    - **Cifrado de Notas:** Cifra el contenido de notas sensibles con una contraseña (cifrado AES).
    - **Integración con IA (Opcional):** Utiliza modelos de IA autoalojados o externos para dictar, corregir o mejorar tus notas.
- **Atajos de Teclado:** Optimizado para un uso rápido y eficiente sin necesidad del ratón.
- **Personalización:**
    - Tema claro y oscuro.
    - Vista de rejilla o de lista.
    - Modo simple o completo.

## 🚀 Cómo Funciona: Arquitectura de Archivos

La simplicidad de `wdnotes` reside en su estructura de archivos. Todo se gestiona en el directorio que configures en tu servidor WebDAV.

### Estructura de Directorios

```
/tu/directorio/de/notas/   (Ej: /dav/Notas/html/notas/)
│
├── NombreDeLaNota.html          # Una nota simple
│
├── Otra Nota#etiqueta1#c_f28b82.html # Nota con etiqueta y color
│
├── pin.txt                      # Archivo que almacena el orden de las notas fijadas
│
├── .versiones/                  # Directorio para el historial de versiones (backups)
│   └── 20250904_183000-NombreDeLaNota.html
│
└── .files/                      # Directorio para los archivos adjuntos
    └── documento_importante.pdf
```

### Formato del Nombre de Archivo

El nombre de cada archivo de nota contiene metadatos importantes, lo que elimina la necesidad de una base de datos. El formato es:

`Nombre visible de la nota` + `#etiqueta1` + `#etiqueta2` + `#c_CÓDIGO_HEX_COLOR` + `.html`

- **Separador:** Se utiliza el carácter `#`.
- **Etiquetas:** Cada palabra precedida por `#` se trata como una etiqueta.
- **Color:** Una etiqueta especial `#c_` seguida de un código de color hexadecimal (sin el `#` inicial) asigna el color a la nota (ej: `#c_fbbc04` para amarillo).
- **Estado (Archivada/Papelera):** Se añaden las etiquetas especiales `#archivada` o `#papelera` para gestionar su estado.

## 🛠️ Instalación y Configuración

#### Requisitos
1.  Un **servidor WebDAV** accesible.
2.  Un servidor web (como Nginx, Caddy o Apache) para servir el archivo `wdnotes.html`.

#### Pasos

1.  **Descargar el archivo:** Descarga el archivo `wdnotes.html` de este repositorio.

2.  **Subir a tu servidor:** Coloca `wdnotes.html` en un directorio accesible a través de tu servidor web.

3.  **Configurar la URL de WebDAV:**
    Abre el archivo `wdnotes.html` y edita la siguiente variable de JavaScript para que apunte a la ruta de tu servidor WebDAV donde quieres guardar las notas:
    ```javascript
    let webdavNotesUrl = "/dav/Notas/html/notas/"; 
    ```
    *   **Importante:** La ruta debe terminar con una barra `/`.

4.  **(Opcional) Configurar Cuadernos Adicionales:**
    Puedes añadir accesos directos a otros directorios de notas (cuadernos) editando la constante `predefinedPaths`:
    ```javascript
    const predefinedPaths = [{
      name: 'Personal',
      path: '/dav/Notas/html/notas/'
      }, {
      name: 'Música',
      path: '/dav/Notas/html/musica/'
      }, {
      name: 'Trabajo',
      path: '/dav/Notas/html/trabajo/'
    }];
    ```

5.  **(Opcional) Configurar Servidor Web con Proxy (Recomendado):**
    Para evitar problemas de CORS, es recomendable configurar tu servidor web para que sirva el archivo `wdnotes.html` y actúe como un proxy inverso para las peticiones a tu servidor WebDAV.

    **Ejemplo de configuración para Nginx:**
    ```nginx
    location / {
        # Sirve el archivo de la aplicación de notas
        alias /ruta/a/tu/servidor/web/;
        try_files $uri /wdnotes.html;
    }

    location /dav/ {
        # Redirige las peticiones WebDAV a tu servidor (ej: Nextcloud)
        proxy_pass http://192.168.1.100/remote.php/dav/files/tu_usuario/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        # ... otras directivas de proxy necesarias
    }
    ```

## ⌨️ Uso y Atajos de Teclado

### Atajos Globales

| Acción | Atajo |
| :--- | :--- |
| Buscar / Crear Nota | `Ctrl` + `F` |
| Navegar entre notas | `Ctrl` + `Flechas` |
| Abrir nota seleccionada | `Ctrl` + `Enter` |
| Guardar Nota (en editor) | `Ctrl` + `S` |
| Cerrar Editor de Nota | `Ctrl` + `Q` o `Esc` |
| Seleccionar Modelo IA | `Ctrl` + `L` |
| Mostrar esta ayuda | `Ctrl` + `H` |

### Atajos en el Editor de Notas

| Acción | Atajo |
| :--- | :--- |
| Negrita | `Ctrl` + `B` |
| Cursiva | `Ctrl` + `I` |
| Subrayado | `Ctrl` + `U` |
| Título 1 / 2 / 3 / 4 | `Ctrl` + `1...4` |
| Bloque de Código | `Ctrl` + `K` |
| Salto de línea en código | `Shift` + `Enter` |
| Lista Desordenada | `Ctrl` + `Shift` + `7` |
| Lista Ordenada | `Ctrl` + `Shift` + `8` |
| Insertar Tabla | `Ctrl` + `T` |

---

## 🧠 Configuración de la Integración con IA

`wdnotes` puede conectarse a servicios de IA que sean compatibles con la API de OpenAI o la API de Google Gemini para las funciones de dictado y mejora de texto.

1.  **Crea el archivo de configuración:** En tu servidor WebDAV, crea el siguiente archivo:
    `/dav/apps/llm/config.txt`

2.  **Añade tus proveedores:** Edita `config.txt` y añade una línea por cada proveedor de IA con el siguiente formato:
    `NombreParaMostrar,URL_de_la_API,API_KEY (opcional)`

    **Ejemplos:**
    ```
    # Para un servicio compatible con OpenAI (ej. Ollama, LM Studio)
    Ollama Local,http://192.168.1.50:11434/v1/

    # Para Google Gemini
    Google Gemini,https://generativelanguage.googleapis.com,TU_API_KEY_DE_GEMINI
    ```
3.  **Selección en la App:** Dentro de las ventanas de "Dictado por Voz" o "Mejorar Nota", podrás seleccionar el proveedor y el modelo que desees utilizar. Tu selección por defecto se guardará en `/dav/apps/llm/llm-wdrss.txt`.


## Idea y Desarrollo
Desarrollado por Angel de uGeek con la mayor parte del codigo con IA.
wdnotes © uGeek. 2025
