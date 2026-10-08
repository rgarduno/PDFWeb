# PDFWeb — Interactive Surgical PDF Web Studio

PDFWeb is the official client-side web application for **PDF Engine**, built on **Next.js 16 (App Router)**, **React 19**, **TypeScript**, and **Tailwind CSS**. It delivers an enterprise-grade studio interface for document inspection, multi-page manipulation, and in-situ surgical text editing with millimeter-accurate typographic fidelity.

---

## PDFEngine Ecosystem

PDFWeb is the user-facing web layer within the decoupled **PDFEngine** multi-repository architecture:

| Repository | Role | Tech Stack | Status |
| :--- | :--- | :--- | :--- |
| [**PDFEngine**](https://github.com/rgarduno/PDFEngine) | High-performance core engine & Python extension module | Rust (ISO 32000-1) + PyO3 | Production-ready |
| [**PDFAPI**](https://github.com/rgarduno/PDFAPI) | Commercial multi-tenant REST & WebSocket service | Python 3.13 + FastAPI + Pydantic v2 | Production-ready |
| [**PDFWeb**](https://github.com/rgarduno/PDFWeb) *(This Repo)* | Interactive Dual-Canvas Web Studio | Next.js 16 (App Router) + React 19 + Tailwind CSS | Production-ready |

---

## Key Features

- **Pixel-Perfect Dual-Canvas Architecture**:
  - **Layer 0 (PDF.js Canvas Backdrop)**: High-density raster projection rendering original vector graphics, Bézier curves, shading patterns (`/Shading`), background fills, and complex images.
  - **Layer 1 (Interactive DOM Overlay)**: Paragraph blocks mapped to exact device points for selection, cursor tracking, and in-place editing with real-time typographic reflow via WebSockets.
  - **Layer 2 (Surgical Highlights & Diff Engine)**: Color-coded bounding boxes for discrepancies, form fields, extracted tables, and vector annotations.
- **Top Editing Toolbar (`Toolbar`)**:
  - Cursor modes: Selection, In-situ paragraph editing, Annotations, and Vector drawing.
  - Vector drawing tools: Freehand ink (`/Ink`), rectangles (`/Square`), circles (`/Circle`), open-arrow lines (`/Line`), and polygons (`/Polygon`).
  - Smooth zoom controls with fit-to-width and fit-to-page presets.
- **Multifunctional Sidebar (`Sidebar`)**:
  - **Pages & Thumbnails**: Visual page carousel, reordering, per-page rotation (-90°, +90°, 180°), and deletion.
  - **AcroForms Designer**: Interactive form builder (Text, Checkbox, Radio, Dropdown) with surgical in-place flattening.
  - **Table Extraction**: Vector lattice grid and borderless stream detection with one-click export to JSON, CSV, Markdown, and HTML.
  - **Redaction & Sanitization**: Surgical text excision, regex scanning (RFC, CURP, SSN, Credit Cards), opaque blackout masks, and metadata purging.
  - **Digital Signatures & Security**: Certificate inspection, detached PKCS#7 / CMS signing, and RFC 3161 TSA timestamping.
  - **OCR & PDF/A**: Invisible searchable text layer injection (`3 Tr`) and PDF/A-1b / PDF/A-2b compliance inspection and conversion.
  - **Metadata Editor**: Bidirectional synchronizer for document `/Info` and XMP XML packages.
  - **Diff Engine**: Side-by-side document comparison with quantitative discrepancy metrics, word-level highlights, and instant navigation.
  - **Audit Log**: Real-time viewer for the tamper-evident action history.

---

## Prerequisites

- **Node.js**: `>= 20.9.0`
- **npm**: `>= 10.0.0`
- **Backend PDFAPI**: Running instance (default: `http://localhost:8000`).

---

## Configuration & Environment Variables

Create `.env.local` based on `.env.example`:

```bash
cp .env.example .env.local
```

Configure variables for your environment:

```env
# Backend API base URL (FastAPI)
NEXT_PUBLIC_API_URL=http://localhost:8000

# Bearer tenant token for local development
NEXT_PUBLIC_PDFENGINE_API_KEY=local-dev-secret-key-12345
```

---

## Installation & Running Locally

```bash
# 1. Install dependencies
npm install

# 2. Start development server with hot reloading
npm run dev

# 3. Build optimized production bundle
npm run build

# 4. Start production server
npm run start

# 5. Run ESLint code checks
npm run lint
```

The Web Studio will be accessible at [http://localhost:3000](http://localhost:3000).

---

## Deployment

PDFWeb is ready for deployment as a standard Next.js application:
- **Vercel**: Import the repository and set `NEXT_PUBLIC_API_URL` pointing to your deployed **PDFAPI** endpoint.
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

## License

Distributed under the MIT License.
