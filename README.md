# Explainable AI metody wyjaśnialności w analizie kompleksów ligand-receptor na przykładzie antagonistów receptora D2 i 5-HT2A


Projekt wykorzystuje metody wyjaśnialności do analizy odcisków palców interakcji białko-ligand oraz identyfikacji kluczowych reszt aminokwasowych odpowiedzialnych za aktywność biologiczną w receptorach GPCR (DRD2 i 5HT2A).


---

##  Przegląd projektu

### Cele

1. **Zbudowanie modeli ML** do predykcji aktywności ligandów na podstawie odcisków interakcji (IFP)
2. **Zastosowanie metod wyjaśnialności** (SHAP, LIME, PDP) do identyfikacji krytycznych interakcji
3. **Identyfikacja kluczowych aminokwasów** w miejscu wiązania
4. **Porównanie wyników ML** z analizą statystyczną (chi-kwadrat, Mann-Whitney)
5. **Wykrycie synergicznych interakcji** między parami aminokwasów (interakcje SHAP)
6. **Dostarczenie praktycznych wskazówek** dla projektowania leków opartego na strukturze

### Kluczowe wyniki

#### 
**Wydajność modelu:**
- Random Forest: AUC 0.72, Accuracy 68%

Pliki explainable_ml_comprehensive_*.ipynb zawierają szczegółowe wyniki analizy wyjaśnialności dla obu receptorów, 
podczas której są wytypowane cechy kontekstowe, kluczowe aminokwasy oraz analizy decyzji modelu.



### Zależności

Projekt wykorzystuje następujące kluczowe biblioteki (zarządzane przez `pixi.toml`):

rdkit = ">=2026.3.1,<2027"
polars = ">=1.40.0,<2"
pyarrow = ">=24.0.0,<25"
seaborn = ">=0.13.2,<0.14"
pandas = ">=3.0.2,<4"
matplotlib = ">=3.10.9,<4"
scikit-learn = ">=1.8.0,<2"
ipykernel = ">=7.2.0,<8"
shap = ">=0.51.0,<0.52"
xgboost = ">=3.2.0,<4"
Wszystkie zależności są automatycznie instalowane podczas aktywacji środowiska Pixi.

### Uruchamianie analizy

Aby przeprowadzić analizę fingerprintów należy:
1: stworzyć i wytrenować model (ml_model.ipynb)
2: uruchomić pliki explainable_ml_comprehensive_*.ipynb dla obu receptorów
3:uruchomić pliki biological_analysis*.ipynb dla obu receptorów
---

##  Struktura projektu

```
.
├── data/                               # pozy
├── Fingerprints/                       # Odciski palców interakcji
│   ├── DRD2/
│   │   ├── ifp_DRD2.csv               # Surowy IFP
│   │   └── ifp_DRD2_BW.csv            # IFP z numeracją BW
│   └── 5HT2A/
│       ├── ifp_5HT2A.csv
│       └── ifp_5HT2A_BW.csv
├── data_preprocessing.ipynb            # Pipeline danych (ChEMBL → IFP)
├── stats.ipynb                         # Analiza statystyczna
├── ml_model.ipynb                      # Bazowe modele ML
│
├── explainable_ml_comprehensive_DRD2.ipynb    # Kompletna wyjaśnialność DRD2
├── explainable_ml_comprehensive_5HT2A.ipynb   # Kompletna wyjaśnialność 5HT2A
├── biological_analysis_DRD2.ipynb      # Interpretacja biologiczna DRD2
├── biological_analysis_5HT2A.ipynb     # Interpretacja biologiczna 5HT2A
├── pixi.toml                           # Konfiguracja środowiska
├── residue_table_DRD2.csv             # Mapowanie numeracji BW
├── residue_table_5HT2A.csv
└── README.md                           
```

---

## Pipeline analizy
Pliki należy urochamiać kolejno

### 1. Przetwarzanie danych (`data_preprocessing.ipynb`)

**Wejście**: Dane bioaktywności ChEMBL (wartości Ki)

**Kroki**:
1. **Filtrowanie danych**: Usuwanie duplikatów, obsługa pomiarów cenzurowanych
2. **Etykietowanie aktywności**: Aktywne (Ki ≤ 100 nM), Nieaktywne (Ki ≥ 10 000 nM)
3. **Dobór różnorodny**: MaxMin picking na odciskach Morgan (ECFP4)
4. **Przygotowanie do dokowania**: Generowanie póz 3D (Glide SP)
5. **Wybór najlepszej pozy**: Najniższy wynik dokowania na ligand
6. **Generowanie IFP**: Odciski palców interakcji ProLIF
7. **Mapowanie notacji BW**: Numeracja Ballesteros-Weinstein dla GPCR

**Wyjście**:
- `ifp_DRD2_BW.csv`: 1583 ligandy × 116 cech IFP
- `ifp_5HT2A_BW.csv`: Podobna struktura dla 5HT2A

### 3. Modele uczenia maszynowego (`ml_model.ipynb`)

**Modele**:
1. Regresja Logistyczna (regularyzacja L2)
2. Random Forest (n=500 drzew, głębokość=20)

- Random Forest: AUC 0.72


### 2. Analiza statystyczna (`stats.ipynb`)

**Metody**:
- Testy chi-kwadrat / Fisher exact (kategorialne)
- Test Mann-Whitney U (ciągłe)
- Korekcja Bonferroniego dla testów wielokrotnych
- Wykresy wulkaniczne
- Analiza wzbogacenia

**Wyjście**: Bazowa ważność cech z klasycznej statystyki

### 4. Analiza wyjaśnialności (`explainable_ml_comprehensive_*.ipynb`)


