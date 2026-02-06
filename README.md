
##  Quick Start (5 minutes)

### Step 1: Install Dependencies
```bash
pip install -r requirements.txt
```

### Step 2: Run Backend
```bash
python fastapi-backend.py
```

Output:
```
╔════════════════════════════════════════════════════════════════╗
║       Surya Sutra: Solar Microgrid Optimizer - FastAPI Backend             ║
║                Phase 3: Optimization Engine                    ║
╚════════════════════════════════════════════════════════════════╝

Starting server on http://localhost:8000
API Docs: http://localhost:8000/docs
```

### Step 3: Test Backend
Open browser: **http://localhost:8000/docs**

You'll see interactive Swagger UI with all endpoints ready to test!

### Step 4: Connect Frontend
- Dashboard already has API integration ready
- Go to "API Debug" tab
- Enter: `http://localhost:8000`
- Click "Test Connection"
- Should show ✅ Backend Connected!

---

## 📊 API Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/health` | GET | Test connection ✓ |
| `/analyze-solar` | POST | Solar suitability analysis |
| `/estimate-demand` | POST | Demand estimation |
| `/optimize-placement` | POST | Core optimization (main call) |
| `/validate-data` | POST | Check input validity |

---