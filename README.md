# PDFWeb — Web Studio de Edición Quirúrgica de PDFs

PDFWeb es la aplicación web interactiva de **PDF Engine**, construida sobre **Next.js 16 (App Router)**, **React 19**, **TypeScript** y **Tailwind CSS**. Proporciona una interfaz visual tipo estudio para la inspección, navegación y edición in-situ de documentos PDF con fidelidad tipográfica milimétrica.

---

## Características Principales

- **Arquitectura Dual-Canvas Pixel-Perfect**:
  - **Capa 0 (Canvas Base PDF.js)**: Proyecta el renderizado vectorial de alta fidelidad original del documento (vectores, curvas Bézier, degradados `/Shading`, sombras e imágenes de fondo).
  - **Capa 1 (DOM Interactivo)**: Bloques de texto proyectados exactamente en coordenadas de página para selección, inspección y edición tipográfica con auto-reflow en tiempo real vía WebSockets.
  - **Capa 2 (Resaltado Quirúrgico & Diff Engine)**: Proyección de cuadros delimitadores coloreados para diferencias semánticas, campos de formulario, tablas y anotaciones vectoriales.
- **Herramientas de Edición & Barra Superior (`Toolbar`)**:
  - Selector de modo de cursor (Selección, Edición in-situ, Anotaciones, Dibujo vectorial).
  - Herramientas de dibujo: trazo libre (`/Ink`), rectángulos (`/Square`), círculos (`/Circle`), líneas con flecha (`/Line`) y polígonos (`/Polygon`).
  - Controles de zoom con ajuste al ancho y ajuste a página completa.
- **Barra Lateral Multifuncional (`Sidebar`)**:
  - **Páginas / Miniaturas**: Reordenamiento, rotación individual y eliminación de páginas.
  - **Formularios AcroForm**: Constructor y editor de campos interactivos (texto, casillas, radio buttons, menús desplegables).
  - **Tablas**: Detección estructural de tablas (Lattice & Stream) con exportación directa a JSON, CSV, Markdown y HTML.
  - **Censura & Sanitización**: Censura por coincidencia de texto, patrones regex (RFC, CURP, SSN, tarjetas de crédito) o regiones seleccionadas, con purga de metadatos.
  - **Firmas Digitales & Seguridad**: Inspección de certificados, firma digital PKCS#7/CMS y sellado de tiempo TSA RFC 3161.
  - **OCR & PDF/A**: Inyección de capa OCR invisible y validación/conversión archivística PDF/A-1b y PDF/A-2b.
  - **Metadatos (/Info & XMP)**: Editor sincronizado bidireccional de metadatos documentales.
  - **Diff Engine**: Comparador visual de documentos con reporte de discrepancias cuantitativo y saltos interactivos por página.
  - **Auditoría**: Visor en tiempo real del registro inmutable de acciones.

---

## Requisitos Previos

- **Node.js**: `>= 20.9.0`
- **npm**: `>= 10.0.0`
- **Backend PDFAPI**: En ejecución (por defecto en `http://localhost:8000`).

---

## Configuración y Variables de Entorno

Crea un archivo `.env.local` basado en `.env.example`:

```bash
cp .env.example .env.local
```

Configura las variables según tu entorno:

```env
# URL base de la API de backend (FastAPI)
NEXT_PUBLIC_API_URL=http://localhost:8000

# Token Bearer para autenticación de inquilino (desarrollo local)
NEXT_PUBLIC_PDFENGINE_API_KEY=local-dev-secret-key-12345
```

---

## Instalación y Ejecución

```bash
# Instalar dependencias
npm install

# Modo desarrollo con Hot Reloading
npm run dev

# Compilar para producción
npm run build

# Iniciar servidor de producción
npm run start

# Ejecutar linter
npm run lint
```

El estudio estará disponible en [http://localhost:3000](http://localhost:3000).

---

## Despliegue

La aplicación está lista para desplegarse como un proyecto Next.js estándar:
- **Vercel**: Conectar el repositorio y configurar la variable de entorno `NEXT_PUBLIC_API_URL` apuntando a tu instancia de **PDFAPI**.
- **Docker**:
  ```dockerfile
  FROM node:20-alpine AS builder
  WORKDIR /app
  COPY package*.json ./
  RUN npm ci
  COPY . .
  RUN npm run build

  FROM node:20-alpine AS runner
  WORKDIR /app
  ENV NODE_ENV=production
  COPY --from=builder /app/public ./public
  COPY --from=builder /app/.next/standalone ./
  COPY --from=builder /app/.next/static ./.next/static
  EXPOSE 3000
  CMD ["node", "server.js"]
  ```

---

## Licencia

Distribuido bajo la licencia MIT.
