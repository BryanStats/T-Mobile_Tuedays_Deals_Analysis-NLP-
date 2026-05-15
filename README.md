📱 T-Mobile Tuesdays Deal Analysis — NLP & Topic Modeling

Is T-Mobile Tuesday actually worth it? This project uses a full NLP pipeline to analyze crowd-sourced discount codes from Reddit and categorize them into spending themes — so you can finally answer that question for your own lifestyle.


🧠 Overview
Every Tuesday, T-Mobile subscribers get access to 3–7 discounts spanning everything from fast food to luxury sneakers. But with a pricier plan, the value proposition depends entirely on which deals show up and whether you'd actually use them.
To find out, we scraped years of community-shared discount codes from r/TMobileTuesdays and ran them through a custom NLP pipeline to:

Extract brand names from raw Reddit comments
Deduplicate and normalize hundreds of brand aliases
Categorize discounts into human-readable spending themes using LDA topic modeling
Visualize the breakdown so users can estimate their personal value


🔧 Pipeline Breakdown
1. Data Collection
Discount codes were scraped from r/TMobileTuesdays using the Arctic Shift Reddit archival API. Each weekly thread contains hundreds of top-level comments where users share unused codes in Brand: CODE format.
2. Comment Cleaning
Raw comments are normalized using regex:

URLs, emails, and punctuation removed
Known noise words and stopwords stripped
Brand alias substitutions applied (e.g. lc → little caesars, jib → jack in the box)

3. Brand Extraction
A regex pattern matcher identifies lines in Brand: CODE or Brand CODE format and extracts the brand name. Only short, well-formed lines (≤4 words) are considered to reduce false positives.
4. Fuzzy + Semantic Deduplication
Brand names are deduplicated using a hybrid approach:

Fuzzy matching via thefuzz (token sort ratio, threshold: 75)
Semantic similarity via sentence-transformers (all-MiniLM-L6-v2) + cosine similarity (threshold: 0.6)

Brands are processed in frequency order so the most common variant becomes the canonical form.
5. LDA Topic Modeling
Each brand is enriched with a short descriptive phrase (e.g. "Cajun fried chicken spicy sandwich biscuit" for Popeyes) to give the model enough vocabulary to work with. These descriptions are then:

Cleaned and tokenized
Lemmatized using spaCy
Vectorized into a bag-of-words corpus with gensim
Modeled with Latent Dirichlet Allocation (LDA) — 7 topics, 10 passes, alpha='auto'

6. Evaluation
Model quality is assessed using the c_v coherence score via gensim's CoherenceModel.

📊 Discovered Topics
Topic IDLabel0Fried Chicken (Popeyes / KFC)1Pharmacy & Health2Gas & Fuel3Magazines & Subscriptions4Pizza (Little Caesars)5Fast Food (Burgers / Tacos)6Movies & Entertainment

🛠 Tech Stack
ToolPurposerequests + Arctic Shift APIReddit data scrapingpandasData manipulationre (regex)Comment parsing & brand extractionthefuzzFuzzy string matchingsentence-transformersSemantic brand deduplicationgensimLDA topic modeling + dictionary/corpusspaCyLemmatizationnltkStopword removalmatplotlibVisualizations

📁 Project Structure
├── Final_Project_tmobile_tuesdays_lda.ipynb   # Main notebook
├── tmobiletuesdays_raw.xlsx                    # Raw scraped data
├── tmobiletuesdays.xlsx                        # Cleaned, exploded brand dataset
└── README.md

✍️ Authors
Jennifer Mac & Bryan Ramirez
Final Project — STAT 653

⚠️ Limitations

The Arctic Shift scraping API went offline mid-collection; the dataset reflects a partial scrape
Brand descriptions used for LDA were manually authored, which partially decouples the model from raw Reddit text
Code-sharing frequency may not directly reflect actual deal popularity or redemption rates
