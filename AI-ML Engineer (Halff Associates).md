# AI/ML Engineer | Halff Associates 
**March 2023 - July 2024**


**Focus Areas:** Natural Language Processing (NLP), Predictive Analytics, Computer Vision & Deep Learning, Operations Automation  

---

### Project Portfolio

#### 1. Material Cost: Classification and Database (Natural Language Processing)
*Orchestrated an automated pipeline to extract, clean, classify, and standardize unstructured text data from over a decade of infrastructure material and utility cost documentation.*

##### ⚙️ The Challenge & Business Context
All civil engineering consulting and design services delivered by Halff require contractors to submit competitive bids to execute field construction work. Contractors quote prices based on the specific materials, processes, and quantities outlined by Halff. Halff engineers then aggregate these disparate estimates into a formalized bid sheet matrix. Historically, this aggregation was performed manually by individual engineers with zero structural oversight, resulting in highly inconsistent document layouts, fractured naming conventions, and unstandardized material descriptions

##### 🛠️ Architectural Implementation
1. **Data Ingestion & Optical Character Recognition (OCR):** Ingested historical bid sheets trapped in highly inconsistent PDF, CSV, and Excel formats. Deployed a suite of specialized Python OCR tools to extract raw tabular layouts from static PDFs.
2. **Schema Alignment:** Normalized the parsed data arrays, aligning varying schemas, column structures, and broken headers into a unified, clean tabular CSV format.
3. **Zero-Shot Semantic Classification:** Designed and trained a zero-shot text classification model using Hugging Face transformers. This mapped arbitrary, unstructured contractor descriptions and names to Halff’s newly established master data dictionary of standard utility/resource categories.
4. **Confidence-Gated Fallback Loop:** Engineered a safety fallback mechanism that flags low-confidence predictions, routing ambiguous items to human-in-the-loop validation to continually preserve database integrity.

##### 📈 Impact & Business Value
* **3x Efficiency Multiplier:** Empowered civil engineers to construct highly accurate, context-aware new cost estimates three times faster using instant access to normalized historical pricing trends.
* **Centralized Intelligence:** Transformed isolated, unstructured document silos into an authoritative institutional repository of material costs and contractor behaviors.

##### 💻 Tech Stack
`Python` • `Hugging Face Transformers` • `PyTorch` • `SpaCy` • `Pandas` • `OCR Tools` • `Regex`

---

#### 2. Utility Pipe Failure Prediction (Predictive Analytics & Survival Analysis)
*Designed and deployed an end-to-end Machine Learning system combining physical asset telemetry, environmental variables, and historical fault data to forecast structural failures across sub-surface utility networks.*

##### ⚙️ The Challenge & Business Context
Subsurface municipal utility infrastructure is expensive to survey and maintain. Traditional maintenance strategy relies on reactive fixes or arbitrary schedule-based testing, which misallocates finite engineering budgets. The goal was to build an intelligent diagnostic model capable of prioritizing high-risk pipe segments, allowing municipalities to preemptively address structural degradation and mitigate catastrophic failures.

##### 🛠️ Architectural Implementation
1. **Multi-Modal Feature Engineering:** Constructed a robust feature matrix capturing both quantitative and qualitative structural health indicators, including pipe material composition, surrounding soil types, historical maintenance logs, absolute pipe age, volumetric flow rates, spatial failure density, and elapsed time since the last localized structural event.
2. **Data Back-Dating & Target Labeling:** Implemented a "back-dated" data reconstruction window where historical failure timeframes were treated as the "next failure" target variable, creating a structured temporal dataset for supervised learning.
3. **Censored Data Handling (Survival Analysis):** Implemented survival analysis via `scikit-survival` to accurately model pipes that had *not* yet failed. This introduced projected time-to-failure bounds for right-censored data points, preventing the training set from introducing critical survival biases by only looking at historical failures.
4. **Survival Analysis Integration:** Used **Random Survival Forest (RSF)** to generate survival curves for each pipe. These curves (representing the probability of survival at specific time stamps) were concatenated with standard features to provide the regression model with temporal context.
5. **Gradient Boosting Regression:** Trained and tuned a `LightGBM` regression framework optimized to output continuous temporal windows indicating precisely when a pipe segment's next structural integrity breach would occur.

##### 📈 Impact & Business Value
* **Financial Savings:** Saved approximately **$436,000** in localized utility maintenance and diagnostic costs by optimizing targeted excavation schedules.
* **High-Precision Foresight:** Achieved **86% predictive accuracy** within a 20-month variance window, and bumped accuracy to **93%** when predicting within a 30-month variance window.

##### 💻 Tech Stack
`Python` • `LightGBM` • `Scikit-Survival` • `Scikit-Learn` • `Pandas`

---

#### 3. Property Type Attribute Prediction (Computer Vision & Deep Learning)
*Developed a custom Multi-Task Sequential Deep Learning network designed to extract quantitative geometric features and qualitative architectural metadata from physical properties using remote sensing images.*

##### ⚙️ The Challenge & Business Context
Municipalities and urban planning divisions require detailed spatial property attributes (such as foundation types and structural heights) to conduct precise land-use profiling and assess localized flood risks. Traditional visual surveying methods require physical boots-on-the-ground inspection, which is immensely slow, dangerous, and expensive to execute at scale across vast municipal districts.

##### 🛠️ Architectural Implementation
1. **Sensor-to-Image Data Pipeline:** Programmatically queried the Google Street View API utilizing precise latitude and longitude coordinates derived from Halff's terrestrial and LiDAR scanning to capture building imagery. Used **Roboflow** for bounding box extraction to crop and scale images to a standard resolution.
2. **Ground Truth Labeling:** Engineered precise target labels by manually mapping building classifications (residential vs. commercial) and extracting absolute finished floor heights directly within point-cloud LiDAR software environments.
3. **Sequential Multi-Task Network Architecture:** Designed a progressive, cascaded PyTorch neural network built on the theory of hierarchical feature reuse:
   * **Stage 1 (Metadata Concat):** Combined base numerical metadata (e.g., physical distance of the LiDAR sensor from the asset facade) with raw imagery tensor representations (`Image Vector + Meta Features`).
   * **Stage 2 (Task A - Classification):** Predicted the easiest structural attribute: *Number of Stories*. The resulting output vector was concatenated directly back onto the original feature space.
   * **Stage 3 (Task B - Classification):** Utilized the expanded feature space (original vector + story predictions) to predict structural *Foundation Type*.
   * **Stage 4 (Task C - Regression):** Concatenated the predicted foundation and story vectors back with the base features to output the ultimate, high-value target: *Finished Floor Height*. 
4. **Information Gain Loop:** This iterative chain allowed downstream task heads to benefit from the semantic abstractions mastered by the upstream classifiers, optimizing predictive capabilities for complex geometrical outputs.

##### 📈 Impact & Business Value
* **60% Surveying Cost Reduction:** Slashed municipal field surveying expenditures by 35% through automated, cloud-scale inferences injected directly into active municipal databases.
* **Risk Intelligence Automation:** Enabled automated flood risk mapping and rapid real estate categorization at scale.

##### 💻 Tech Stack
`PyTorch` • `Google Maps API` • `Python` • `NumPy` • `LiDAR Processing Systems`

---

#### 4. Route Planning (Reinforcement Learning)
*Developed a proof-of-concept route optimization tool for utility pipe placement using reinforcement learning.*

##### ⚙️ The Challenge & Business Context
Engineers must navigate complex constraints (buildings, wells, roads, subsusrface infrastructure, environmental hazards) when planning oil,gas,water and other utility pipe routes. This tool aimed to automate pathfinding to find optimal routes that avoid these obstacles and minimze total costs.

##### 🛠️ Architectural Implementation
1. **State Space Generation:** Created a grid-based state space from GIS rasters (simulated for development using randomly generated obstacles).
2. **Q-Learning Agent:** Implemented a **Multi-Q Table learning** approach where an agent learns to navigate from a start point to multiple waypoints while incurring penalties for approaching obstacles or restricted zones.
3. **Cost Mapping:** Developed a heatmap-based cost function where the agent receives negative rewards for proximity to obstacles.

##### 💻 Tech Stack
`Python` • `NumPy` • `Matplotlib`

---

## 🛠️ Summary Technical Proficiency Map

| Category | Technologies & Tooling |
| :--- | :--- |
| **Machine Learning / Deep Learning** | PyTorch, LightGBM, Scikit-Learn, Scikit-Survival, SMOTE |
| **Natural Language Processing (NLP)**| Word2Vec, Text Embeddings, Regex, Tokenization |
| **Data Engineering / Analytics** | Pandas, NumPy, Tabular Normalization, SMOTE |
| **Data Ingestion & APIs** | Google Street View API, OCR Engines, Geospatial LiDAR |
| **Reinforcement Learning** | Q-Learning, Multi-Q Table, State Space Modeling |
