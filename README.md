# Modelling Spatial Heterogeneity in Real Estate Markets

A comprehensive analysis of spatial variations in house feature impacts on property prices across Melbourne's council areas using hierarchical Bayesian mixed-effects modeling.

## 🏠 Overview

Real estate markets exhibit significant spatial heterogeneity, where the same property features can have vastly different impacts on prices depending on location. This project investigates **which house features have the most variable effect on price across Melbourne's council areas**, using advanced statistical modeling to quantify spatial differences.

### Key Research Question
*How does the importance of house features vary geographically across Melbourne's real estate market?*

**Hypothesis**: The impact of house features on property prices is heterogeneous across space. For example, parking spaces are more valuable in the city center due to space constraints compared to suburbs where space is abundant.

## 📊 Dataset

- **13,580 property records** with 21 features
- **Coverage**: Melbourne council areas
- **Key Variables**: Price, Building Area, Distance from CBD, Bedrooms, Bathrooms, Car spaces, Council Area, Month of sale
- **Missing Data**: Handled via Bayesian imputation to preserve 45.63% of observations that would otherwise be lost

### Data Processing Pipeline
1. **Bayesian Imputation** for missing values in Car, Building Area, Year Built, and Council Area
2. **Outlier Treatment** using Interquartile Range (IQR) method
3. **Standardization** (z-score transformation) for unbiased coefficient comparison
4. **Council Filtering** to focus on active areas (>200 properties sold)

## 🧠 Methodology

### Hierarchical Mixed-Effects Model

We employ a **Bayesian hierarchical mixed-effects framework** that distinguishes between:
- **Fixed Effects**: Features with constant impact across all council areas
- **Random Effects**: Features with spatially varying impacts

#### Model Specification
```
yi ~ N(tm(i) + uj(i) + Xi^T β + Zi vj(i), σ²ε)
```
Where:
- `yi`: Sale price of house i
- `tm(i)`: Month-specific random intercept
- `uj(i)`: Council-specific random intercept (with CAR structure)
- `β`: Fixed effect coefficients
- `vj(i)`: Council-specific random slope for feature Zi
- `σ²ε`: Error variance

### Spatial Dependencies
**Conditional Autoregressive (CAR) Component** captures spatial spillover effects between neighboring council areas using adjacency relationships.

### Inference Strategy
To address computational complexity, we fit **5 separate models** (one per house feature as random slope) and compare the variance of random slope coefficients across council areas.

#### Technical Implementation
- **PyMC Library** with No-U-Turn Sampler (NUTS)
- **3,000 posterior draws** with 3,000 tuning steps
- **4 independent chains** for convergence diagnostics
- **Non-centered parametrization** for improved sampling efficiency

## 🔍 Key Findings

### Spatial Heterogeneity Rankings
Features ranked by spatial variability (standard deviation of random slopes):

| Rank | Feature | Std Dev | Interpretation |
|------|---------|---------|----------------|
| 1 | **Distance** | 0.3484 | Highest spatial variation - location premium varies dramatically |
| 2 | **Bedroom2** | 0.2532 | Bedroom preferences differ significantly across areas |
| 3 | **Bathroom** | 0.1651 | Moderate spatial differences in bathroom value |
| 4 | **Car** | 0.1508 | Parking value varies moderately by location |
| 5 | **Building Area** | 0.0489 | Most uniform impact across all areas |

### Key Insights
- **Distance from CBD** shows the most spatial heterogeneity, confirming location-dependent premiums
- **Building Area** has remarkably consistent value across all council areas
- **Bedroom count** preferences vary significantly, suggesting demographic differences across regions
- Random effects show **opposite signs** across council areas, supporting our spatial heterogeneity hypothesis

## 🛠️ Installation & Usage

### Prerequisites
```bash
pip install pymc numpy pandas matplotlib seaborn scipy
```

### Repository Structure
```
├── spatial-heterogeneity-report.pdf  # Final project report (PDF)
├── Modelling Notes.docx         # Project notes and documentation
├── housing.csv                  # Main dataset
├── LICENSE                      # License file
├── .gitignore                   # Git ignore rules
└── README.md                    # Project overview and instructions
```

### Quick Start
```python
# Load and preprocess data
from src.data_preprocessing import preprocess_data
df_clean = preprocess_data('data/raw/melbourne_housing.csv')

# Fit spatial heterogeneity models
from src.model_fitting import fit_spatial_models
models = fit_spatial_models(df_clean, features=['Distance', 'Bedroom2', 'Bathroom', 'Car', 'BuildingArea'])

# Analyze spatial heterogeneity
from src.results_analysis import analyze_spatial_heterogeneity
heterogeneity_results = analyze_spatial_heterogeneity(models)
```

## 📈 Model Validation

### Convergence Diagnostics
- **R̂ ≈ 1** for all parameters (excellent convergence)
- **Effective sample sizes > 5,000** (sufficient for inference)
- **No divergent transitions** (reliable sampling)

### Assumption Validation
✅ **Fixed effects stability** across models  
✅ **Homoscedastic errors** across model variants  
✅ **Spatial independence** of month effects  
✅ **CAR structure robustness** across features

## 🎯 Applications & Impact

### For Stakeholders

**Policy Makers**
- Identify areas where specific amenities create disproportionate value
- Target infrastructure investments based on spatial heterogeneity patterns

**Real Estate Professionals**
- Optimize pricing strategies based on location-specific feature premiums
- Better understand neighborhood-specific buyer preferences

**Property Developers**
- Focus on features that add most value in specific council areas
- Make informed decisions about development priorities

**Researchers**
- Framework applicable to other cities and real estate markets
- Foundation for spatial economics and urban planning research

## 🔮 Future Research Directions

### Model Extensions
- **Multiple random slopes** per council area
- **Learnable precision matrix** in CAR component
- **Temporal dynamics** in spatial relationships

### Dataset Enrichment
- **Additional hierarchy levels** (suburb, postcode)
- **Socioeconomic factors** (income, demographics)
- **Infrastructure variables** (transport, schools)

### Methodological Improvements
- **Dynamic spatial weights** based on similarity measures
- **Non-parametric spatial effects** using Gaussian processes
- **Cross-validation frameworks** for spatial models

## 📚 Main References

1. Besag, J. (1974). Spatial interaction and the statistical analysis of lattice systems. *Journal of the Royal Statistical Society*, 36, 192-236.
2. Betancourt, M. (2013). Hamiltonian Monte Carlo for hierarchical models. *Department of Statistical Science, University College London*.
3. Ciarleglio, M. M., & Makuch, R. W. (2007). Hierarchical linear modeling: An overview. *Child Abuse & Neglect*, 31(2).

## 👥 Contributors
* [Letizia Dimonopoli](https://www.linkedin.com/in/letiziadimonopoli/)
* [Marco Lomele](https://www.linkedin.com/in/marco-lomele) 
* [Giovanni Mantovani](https://www.linkedin.com/in/giovanni-mantovani)
* [Sofia Villa](https://www.linkedin.com/in/sofiavilla/)

## 🔑 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.