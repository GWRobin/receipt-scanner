# 🧾 ReceiptScanner

En webbapp för att scanna kvitton med OCR, granska/justera de inlästa värdena och spara dem i en PostgreSQL-databas.

## Teknikstack

| Komponent | Teknologi |
|-----------|-----------|
| Frontend  | React 18 + Vite |
| Backend   | FastAPI (Python) |
| OCR       | Tesseract (swe + eng) |
| Databas   | PostgreSQL 16 |
| Deployment | Docker Compose |

## Kom igång

### Krav
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installerat och igång

### Starta appen

```bash
cd ReceiptScanner
docker compose up --build
```

Första gången tar det några minuter (Tesseract laddas ner). Sedan är appen tillgänglig på:

- **Frontend:** http://localhost:5173
- **Backend API:** http://localhost:8000
- **API-dokumentation:** http://localhost:8000/docs

### Stoppa appen

```bash
docker compose down
```

För att även ta bort databasen (all data raderas):

```bash
docker compose down -v
```

## Hur det fungerar

1. **Scanna** – Ladda upp en bild på ett kvitto (JPG, PNG, TIFF)
2. **OCR** – Tesseract extraherar text; backend letar efter belopp och datum med regex
3. **Granska** – Justera belopp och datum om OCR missade något, lägg till en kommentar
4. **Skicka** – Data + bild sparas i PostgreSQL

## Projektstruktur

```
ReceiptScanner/
├── docker-compose.yml
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── main.py          ← FastAPI-app + OCR-logik
│   └── database.py      ← SQLAlchemy-modeller
└── frontend/
    ├── package.json
    ├── vite.config.js
    └── src/
        ├── App.jsx
        └── components/
            ├── ReceiptUploader.jsx   ← Drag & drop + OCR-anrop
            ├── ReceiptForm.jsx       ← Granska/justera + spara
            └── ReceiptList.jsx       ← Historik
```

## API-endpoints

| Metod | Endpoint | Beskrivning |
|-------|----------|-------------|
| POST | `/api/ocr` | Ladda upp bild → kör OCR → returnera belopp + datum |
| POST | `/api/receipts` | Spara justerat kvitto till databasen |
| GET  | `/api/receipts` | Lista alla sparade kvitton |
| GET  | `/api/receipts/{id}/image` | Hämta kvittobilden |

## Tips för bättre OCR-träffsäkerhet

- Använd välupplysta foton utan skuggor
- Se till att texten är i fokus
- Håll kvittot rakt (inte vinklat)
- PNG ger generellt bättre resultat än JPEG för text
