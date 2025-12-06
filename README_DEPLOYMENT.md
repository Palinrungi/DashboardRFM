# 🚀 Panduan Deploy Dashboard RFM ke Streamlit Community Cloud

Panduan lengkap untuk mendeploy dashboard Streamlit RFM Analysis Anda ke production.

## 📋 Prasyarat

Sebelum melakukan deployment, pastikan Anda memiliki:
- Akun GitHub (gratis)
- Akun Streamlit Community Cloud (gratis - gunakan GitHub untuk sign in)
- File dashboard yang sudah siap: `dashboard_rfm.py`, `requirements.txt`, `E-Commerce Data.csv`

---

## 🎯 Opsi 1: Deploy ke Streamlit Community Cloud (Recommended - GRATIS!)

Ini adalah cara termudah dan gratis untuk mendeploy dashboard Streamlit Anda.

### Langkah 1: Persiapan File

Pastikan struktur folder Anda seperti ini:
```
Cpastone Fix/
├── dashboard_rfm.py          # File utama dashboard
├── E-Commerce Data.csv        # Data yang diperlukan
├── requirements.txt           # Dependencies
└── .streamlit/
    └── config.toml            # Konfigurasi Streamlit
```

**PENTING**: Update path data di `dashboard_rfm.py`:
- Ubah `'../E-Commerce Data.csv'` menjadi `'E-Commerce Data.csv'`
- Atau upload data ke cloud storage (Google Drive, S3) untuk file besar

### Langkah 2: Upload ke GitHub

1. **Buat Repository Baru di GitHub:**
   - Kunjungi https://github.com/new
   - Nama repository: `rfm-dashboard` (atau nama lain yang Anda inginkan)
   - Set sebagai **Public** (required untuk free tier)
   - Jangan centang "Initialize with README"
   - Klik "Create repository"

2. **Upload File via GitHub Web (Cara Mudah):**
   - Di halaman repository baru, klik "uploading an existing file"
   - Drag & drop semua file:
     - `dashboard_rfm.py`
     - `E-Commerce Data.csv`
     - `requirements.txt`
     - Folder `.streamlit/config.toml`
   - Tulis commit message: "Initial commit - RFM Dashboard"
   - Klik "Commit changes"

3. **Atau Upload via Git Command (Cara Terminal):**
   ```powershell
   cd "d:\Project Bintang\ASAH\Capstone\Cpastone Fix"
   git init
   git add .
   git commit -m "Initial commit - RFM Dashboard"
   git branch -M main
   git remote add origin https://github.com/USERNAME/rfm-dashboard.git
   git push -u origin main
   ```
   Ganti `USERNAME` dengan username GitHub Anda.

### Langkah 3: Deploy ke Streamlit Community Cloud

1. **Login ke Streamlit Community Cloud:**
   - Kunjungi https://share.streamlit.io/
   - Klik "Sign in with GitHub"
   - Authorize Streamlit untuk akses GitHub Anda

2. **Deploy App:**
   - Klik "New app" atau "Create app"
   - Pilih repository: `rfm-dashboard`
   - Branch: `main`
   - Main file path: `dashboard_rfm.py`
   - App URL: pilih custom URL (contoh: `rfm-analysis-yourname`)
   - Klik "Deploy!"

3. **Tunggu Deployment:**
   - Proses biasanya 2-5 menit
   - Anda bisa melihat log deployment
   - Setelah selesai, dashboard akan otomatis terbuka

4. **URL Dashboard Anda:**
   ```
   https://rfm-analysis-yourname.streamlit.app
   ```

### Langkah 4: Update Dashboard (Jika Ada Perubahan)

Untuk update dashboard setelah deployment:
1. Edit file di GitHub atau push perubahan dari local
2. Streamlit akan **otomatis rebuild dan redeploy** dalam beberapa menit
3. Atau klik "Reboot app" di dashboard Streamlit Cloud

---

## 🎯 Opsi 2: Deploy ke Heroku (Berbayar sekarang)

**CATATAN**: Heroku sudah tidak gratis sejak November 2022. Minimal $5/bulan.

### Langkah-langkah:

1. **Buat Procfile:**
   ```
   web: sh setup.sh && streamlit run dashboard_rfm.py
   ```

2. **Buat setup.sh:**
   ```bash
   mkdir -p ~/.streamlit/
   echo "\
   [server]\n\
   headless = true\n\
   port = $PORT\n\
   enableCORS = false\n\
   \n\
   " > ~/.streamlit/config.toml
   ```

3. **Deploy:**
   ```powershell
   heroku login
   heroku create rfm-dashboard-app
   git push heroku main
   ```

---

## 🎯 Opsi 3: Deploy ke AWS EC2 / Azure / GCP

Untuk deployment ke cloud provider:

1. **Setup Virtual Machine:**
   - Buat instance (Ubuntu 20.04 recommended)
   - Install Python 3.9+: `sudo apt install python3.9 python3-pip`

2. **Clone Repository:**
   ```bash
   git clone https://github.com/USERNAME/rfm-dashboard.git
   cd rfm-dashboard
   ```

3. **Install Dependencies:**
   ```bash
   pip3 install -r requirements.txt
   ```

4. **Run dengan Screen (Background Process):**
   ```bash
   screen -S dashboard
   streamlit run dashboard_rfm.py --server.port 8501 --server.address 0.0.0.0
   # Tekan Ctrl+A lalu D untuk detach
   ```

5. **Setup Nginx sebagai Reverse Proxy (Optional):**
   - Lebih professional dan aman
   - Tutorial: https://docs.streamlit.io/knowledge-base/tutorials/deploy/nginx

---

## 🎯 Opsi 4: Run Locally (Development/Demo)

Untuk development atau demo lokal:

```powershell
cd "d:\Project Bintang\ASAH\Capstone\Cpastone Fix"
streamlit run dashboard_rfm.py
```

Dashboard akan terbuka di: http://localhost:8501

---

## ⚙️ Konfigurasi Penting

### 1. Environment Variables (Untuk Production)

Jika Anda menggunakan API keys atau credentials:

**Streamlit Community Cloud:**
- Di dashboard app, masuk ke "Settings" > "Secrets"
- Tambahkan dalam format TOML:
  ```toml
  api_key = "your-secret-key"
  database_url = "your-db-url"
  ```
- Akses di code: `st.secrets["api_key"]`

### 2. Handling Large Data Files

Jika `E-Commerce Data.csv` terlalu besar (>100MB):

**Opsi A - Google Drive:**
```python
import gdown
url = 'https://drive.google.com/uc?id=FILE_ID'
output = 'E-Commerce Data.csv'
gdown.download(url, output, quiet=False)
```

**Opsi B - AWS S3:**
```python
import boto3
s3 = boto3.client('s3')
s3.download_file('bucket-name', 'E-Commerce Data.csv', 'E-Commerce Data.csv')
```

**Opsi C - GitHub LFS (Large File Storage):**
```powershell
git lfs install
git lfs track "*.csv"
git add .gitattributes
git add "E-Commerce Data.csv"
git commit -m "Add large file with LFS"
git push
```

### 3. Performance Optimization

Tambahkan caching di dashboard untuk performa lebih baik:

```python
@st.cache_data
def load_data():
    # ... existing code ...
    return df, df_clean, df_uk, df_nonuk

@st.cache_data
def calculate_rfm(df_market):
    # ... existing code ...
    return rfm
```

---

## 🐛 Troubleshooting

### Error: "ModuleNotFoundError"
- **Solusi**: Pastikan semua library ada di `requirements.txt`
- Run: `pip freeze > requirements.txt` untuk update

### Error: "File not found: E-Commerce Data.csv"
- **Solusi**: 
  - Pastikan file ada di root folder
  - Update path dari `'../E-Commerce Data.csv'` ke `'E-Commerce Data.csv'`
  - Atau gunakan absolute path: `os.path.join(os.path.dirname(__file__), 'E-Commerce Data.csv')`

### App Terlalu Lambat
- **Solusi**:
  - Gunakan `@st.cache_data` untuk function berat
  - Kurangi ukuran data atau gunakan sampling
  - Optimize query dan processing

### Memory Error di Streamlit Cloud
- **Free tier limit**: 1GB RAM
- **Solusi**:
  - Reduce data size
  - Use data sampling
  - Upgrade ke paid tier ($20/month untuk 4GB RAM)

---

## 📊 Monitoring & Maintenance

### Streamlit Community Cloud:
- **View Logs**: Dashboard > "Manage app" > "Logs"
- **Reboot App**: Jika ada issue, klik "Reboot app"
- **Analytics**: Lihat usage stats di dashboard

### Custom Deployment:
- Setup monitoring dengan tools seperti:
  - **Uptime monitoring**: UptimeRobot, Pingdom
  - **Error tracking**: Sentry
  - **Analytics**: Google Analytics

---

## 💰 Biaya

| Platform | Biaya | Limits |
|----------|-------|--------|
| **Streamlit Community Cloud** | **GRATIS** | 1GB RAM, 1 CPU, 3 apps |
| Streamlit Cloud (Paid) | $20/bulan | 4GB RAM, unlimited apps |
| Heroku | $5-25/bulan | Tergantung dyno type |
| AWS EC2 | $5-50/bulan | Tergantung instance type |
| Azure App Service | $10-50/bulan | Tergantung tier |

**Rekomendasi**: Mulai dengan Streamlit Community Cloud (GRATIS!) untuk testing dan small-medium traffic.

---

## ✅ Checklist Deployment

- [ ] Update path data di `dashboard_rfm.py` (dari `../` ke `./`)
- [ ] Pastikan `requirements.txt` lengkap dan terbaru
- [ ] Buat repository GitHub dan set sebagai Public
- [ ] Upload semua file ke GitHub
- [ ] Login ke Streamlit Community Cloud dengan GitHub
- [ ] Deploy app dan tunggu proses selesai
- [ ] Test dashboard di URL production
- [ ] Share URL dengan stakeholders!

---

## 🎉 Setelah Deployment

Dashboard Anda sekarang live! Share URL dengan:
- Team/stakeholder
- Portfolio/resume
- LinkedIn
- Dokumentasi project

**Custom Domain (Optional)**:
- Streamlit Cloud support custom domain di paid plan
- Atau gunakan CNAME redirect dari domain Anda

---

## 📞 Bantuan Lebih Lanjut

- **Streamlit Docs**: https://docs.streamlit.io/streamlit-community-cloud
- **Streamlit Forum**: https://discuss.streamlit.io/
- **GitHub Issues**: Untuk bug atau feature request

**Selamat! Dashboard RFM Analysis Anda siap untuk production! 🚀**
