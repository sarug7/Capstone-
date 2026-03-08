### AI Marketing Banner Generation

**Author:** UC Berkely ML/AI Capstone Project 

#### Executive summary

This project applies the CRISP-DM framework to Apparel product catalog and decade-long campaign data (2015–2025) to address two interlinked objectives: predicting product engagement outcomes using regression models, and generating AI-driven marketing banner text for Apparel's recommended products using NLP techniques. Five regression models (Linear, Multiple, Ridge, Lasso, and Logistic) were trained on 216 rated products from a 2,000-record catalog. The best regression models achieved R² ≈ 0.26, while Logistic Regression reached 68.2% accuracy — 18 percentage points above baseline — in predicting high-engagement products. A template-based NLG system generated 23 product-specific marketing banners, which were evaluated against human-written Apparel campaign slogans. AI banners outperformed on specificity, CTA inclusion, and scalability; human banners retained superiority in brand voice and memorability. A hybrid strategy — AI-generated drafts refined by human editors — is recommended for production deployment.

#### Ratoinale

Marketing effectiveness at scale is a persistent challenge for large retail brands. Apparel operates a catalog of thousands of products across multiple segments and markets, running numerous campaigns each year. Manually crafting tailored marketing copy for every product is time-intensive and inconsistent. At the same time, predicting which products will achieve high customer engagement enables smarter resource allocation for campaign targeting and banner placement. This project addresses both challenges by combining predictive modeling with NLP-driven content generation, offering a data-driven foundation for Apparel's marketing operations.

#### Research Question

1. Which product attributes (price, department, brand/market channel) most influence customer engagement, and can regression models predict engagement outcomes from catalog data alone?
2. Can AI-generated marketing banner text match or exceed the quality of human-written Apparel campaign copy across measurable dimensions?

#### Data Sources

Three datasets were used:

- **product_data.xlsx** — 2,000 Apparel product records containing style ID, price, department, brand/market code (BMC), average rating, rating count, and review count.
- **campaigns_and_products_2015_2025.csv** — 37 records covering 14 Apparel campaigns and 23 associated product entries spanning 2015–2025, including campaign name, season/year, promotion type, theme, and target categories.
- **product_recommendations.csv** — 23 curated product recommendations with segment, category, product name, fit/style, occasion, and recommendation tag fields.

#### Methodology

The project follows the six-phase CRISP-DM process:

1. **Business Understanding** — Defined objectives, KPIs, and success criteria aligned to Apparel's marketing goals.
2. **Data Understanding** — Exploratory data analysis covering price distributions, department volumes, brand channel splits, and engagement metric profiling. Identified a critical data quality issue: 89% of products lack engagement data.
3. **Data Preparation** — Filtered to 216 rated products, removed price outliers (>$500), applied log1p transforms to price and rating count, label-encoded department and BMC, engineered a binary high-engagement target (rating_count > median), and applied StandardScaler before modeling. An 80/20 train/test split was used with random_state=42.
4. **Modeling** — Five models were trained and evaluated: Linear Regression (target: avg rating), Multiple Regression, Ridge Regression (α=1.0), and Lasso Regression (α=0.01) (all targeting log engagement), and Logistic Regression (binary high-engagement classification). Cross-validation was applied to all models.
5. **NLP & Banner Generation** — Product names and recommendation tags were scored using a custom sentiment lexicon and a keyword relevance system mapped to four campaign categories (denim, basics, outerwear, sustainability). A template-based NLG system generated 23 unique banners keyed to each product's recommendation tag and populated with product-specific attributes.
6. **Evaluation** — Models were assessed against business success criteria. AI and human banners were compared quantitatively (word count, CTA rate, specificity) and qualitatively (brand voice, emotional resonance, memorability).

#### Results

**Regression Models:**
- All engagement models (Multiple, Ridge, Lasso) achieved R² ≈ 0.263 on the test set — explaining approximately 26% of engagement variance with three features.
- Department encoding was the strongest predictor (coefficient: −1.158), followed by brand/market channel (+0.605). Price had minimal effect (−0.064).
- Ridge and Lasso coefficients converged to near-identical values as OLS, expected with only three features.
- Logistic Regression achieved 68.2% accuracy, a +18 percentage point improvement over the 50% random baseline, providing a practical tool for campaign targeting decisions.

**NLP & Banner Generation:**
- AI-generated banners averaged 14.3 words vs. 4.5 for human banners; AI included CTAs in 17.4% of banners vs. 0% for human banners; AI specificity score (5.6 long words/banner) far exceeded human (1.1).

- A hybrid approach (AI generates product-specific drafts; human editors refine tone and brand alignment) is recommended for production.

#### Next Steps
The primary production path for this project is to integrate the AI banner generation pipeline with a headless CMS — ex: **Amplience** — so that model outputs flow directly into content publishing infrastructure.

1. **Enrich the dataset** — Collect engagement data for the 89% of unrated products; this is the single highest-impact improvement available to model performance.

2. **Map AI banner outputs to Amplience content schemas** — Define a custom Amplience content type (e.g., `ai-product-banner`) that captures the fields generated by this pipeline: `bannerText`, `productName`, `segment`, `recommendationTag`, `sentimentScore`, `campaignCategory`, and `hasCtA`. AI-generated banners are pushed to Amplience as draft content items via the [Amplience Dynamic Content Management API](https://amplience.com/developers/docs/api/), ready for editorial review before publishing.

3. **Build a real-time banner generation microservice** — Wrap the NLG pipeline in a lightweight REST API (e.g., FastAPI or Flask) that accepts a product SKU or recommendation tag and returns a banner payload formatted for Amplience ingestion. This service acts as the bridge between the ML model and the CMS, enabling on-demand content generation for new products without re-running the full notebook.
4. **A/B test banners** — Run live A/B tests comparing AI-generated vs. human-written banners on click-through rate across Apparel's digital channels.
5. **Fine-tune a generative model** — Fine-tune a GPT or T5 model on Apparel's historical campaign copy for production-quality NLG that captures brand voice.
6. **Integrate campaign context** — Route NLG template selection dynamically based on active campaign themes (e.g., "Better in Denim" → denim-category templates).

#### Outline of Project

- [generate_marketing banner_capstone](Ggenerate_marketing banner_capstone.ipynb)

**Project structure:**
```
Apparel_crisp_dm_project/
├── generate_marketing banner_capstone.ipynb   ← Main notebook — run this
├── README.md                      ← This file
└── data/
    ├── product_data.xlsx
    ├── campaigns_and_products_2015_2025.csv
    └── recommendations.csv
```

**To run:**
```bash
pip install pandas numpy scikit-learn matplotlib seaborn openpyxl jupyter
jupyter notebook
# Open generate_marketing banner_capstone.ipynb → Kernel → Restart & Run All
```

##### Contact and Further Information

For questions about this project, methodology, or dataset, please contact the project author. The notebook is fully self-contained and reproducible using the three data files in the `data/` directory and standard Python data science libraries (pandas, numpy, scikit-learn, matplotlib, seaborn, openpyxl).
