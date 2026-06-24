# World Cup Discourse Classifier (r/worldcup)

## Project Overview
This project builds and evaluates a text classification pipeline to categorize organic user discourse in the **r/worldcup** community during the live 2026 World Cup tournament. The classifier organizes public posts into three distinct categories matching community formatting: structured sports analytics (`analysis`), infrastructure/travel coordination (`logistics`), and emotional fan discourse (`reaction`).

---

## Evaluation Report

### Model Performance Summary
The table below displays the performance comparison between the zero-shot Llama-3.3-70b baseline and our fine-tuned DistilBERT model evaluated on a locked test set of 32 perfectly stratified examples.

| Metric | Zero-Shot Baseline (Llama 3.3) | Fine-Tuned Model (DistilBERT) |
| :--- | :---: | :---: |
| **Overall Accuracy** | **96.9%** | **71.9%** |
| **Analysis F1-Score** | 1.00 | 0.82 |
| **Logistics F1-Score** | 0.95 | 0.67 |
| **Reaction F1-Score** | 0.96 | 0.64 |

### Confusion Matrix (Fine-Tuned Model)
| True \ Predicted | analysis | logistics | reaction |
| :--- | :---: | :---: | :---: |
| **analysis** | 9 | 0 | 1 |
| **logistics** | 4 | 6 | 1 |
| **reaction** | 2 | 1 | 8 |

---

## Error Analysis: 3 Organic Failure Modes

### 1. Misclassification #1: Keyword Leakage in Mixed-Context Posts
* **Text:** `"Late-night rail service is a lifesaver after extra time."`
* **True Label:** `logistics` | **Predicted Label:** `analysis` (Confidence: 0.38)
* **Analysis:** This post demonstrates the difficulty small models face with organic forum data. The user is discussing transit logistics, but uses the football idiom "extra time." Because our dataset size is relatively small ($210$ total rows), the fine-tuned model over-indexed on "extra time" as a hard feature for `analysis`, failing to grasp the primary logistical intent of the sentence. The $70\text{B}$ parameter baseline easily resolved this due to its superior contextual window.

### 2. Misclassification #2: Failure to Detect Implicit Fan Sentiment
* **Text:** `"Football wins when matches are this entertaining."`
* **True Label:** `reaction` | **Predicted Label:** `analysis` (Confidence: 0.37)
* **Analysis:** Organic fan reactions are not always accompanied by high-variance punctuation like exclamation points or emojis. Here, a user is expressing pure emotional satisfaction, but writes it as a declarative statement. DistilBERT misread the formal sentence structure as a structural insight and incorrectly guessed `analysis`. 

### 3. Misclassification #3: Low Confidence Under-Fitting from Limited Sample Size
* **Text:** `"The reaction from the bench said it all."`
* **True Label:** `reaction` | **Predicted Label:** `logistics` (Confidence: 0.35)
* **Analysis:** The fine-tuned model predicted `logistics` with an incredibly weak confidence score of $0.35$, essentially guessing. This indicates severe under-fitting due to sample size limitations. While $210$ examples are enough to establish basic parameters, the immense diversity of conversational English on a public subreddit requires a much higher density of training examples for a lightweight model ($110\text{M}$ parameters) to solidify its boundaries.

---

## Sample Classifications

Below are examples run through the fine-tuned model demonstrating its standard operational thresholds:

1. **Text:** *"Argentina's 3-2-5 buildup keeps creating overloads between the lines."*
   * **Predicted Label:** `analysis` (Confidence: 0.78)
   * **Verdict:** Correct. Highly explicit technical jargon ("3-2-5 buildup") maps cleanly to clear-cut examples in the training set.
2. **Text:** *"NJ Transit is packed heading toward MetLife three hours before kickoff."*
   * **Predicted Label:** `logistics` (Confidence: 0.74)
   * **Verdict:** Correct. Strong spatial and transit-specific vocabulary ("NJ Transit", "MetLife") triggers high confidence.
3. **Text:** *"Ride-share surge pricing is out of control."*
   * **Predicted Label:** `analysis` (Confidence: 0.37)
   * **Verdict:** Incorrect. Lacking distinct geographic stadium markers, the model fails to categorize the transit complaint correctly and defaults to a low-confidence guess.

---

## Spec Reflection & AI Usage

* **Spec Guidance Success:** The taxonomy boundaries designed in the initial spec successfully kept the baseline model running flawlessly without any overlapping label assignment conflicts.
* **Implementation Divergence:** Our final implementation exposed a massive performance gap between the zero-shot baseline ($96.9\%$) and the fine-tuned model ($71.9\%$). While our spec anticipated that fine-tuning would optimize performance for community-specific jargon, the reality of real-world text diversity meant that the small fine-tuned model suffered from keyword leakage given our strict data scale constraints.
* **AI Usage Disclosure:** LLM tools were utilized as an evaluation assistant to process the list of wrong predictions and help categorize linguistic trends during failure analysis.
