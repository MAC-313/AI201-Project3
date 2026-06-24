# Project Plan: World Cup Discourse Classifier

## 1. Community
I chose **r/worldcup** because it is highly active, text-heavy, and contains a massive spectrum of content quality. It is a perfect fit for a classification task because posts naturally divide into rigorous tactical breakdowns, logistical travel planning/hacks for host cities, and baseline emotional tournament hype/outrage.

## 2. Labels & Examples
* **analysis**: The post makes a structured argument backed by statistics, tournament formats, historical comparison, or tactical observation. Evidence is specific and verifiable.
    * *Example 1*: "Looking at the 20,000 Monte Carlo simulation runs, the full distribution shows Group C's runner-up has a 64% chance of facing Argentina in the round of 32."
    * *Example 2*: "Starting a shorter striker against Australia's tall center-backs failed because we completely lacked an outlet to make runs behind the big trees in the final third."
* **logistics**: A post providing or requesting objective travel, ticket, transit, broadcasting, or host-city planning information. 
    * *Example 1*: "Megathread: Match-Day Transit, Shuttles, and local hacks across the 16 host cities in North America."
    * *Example 2*: "What is the smartest way to navigate local transit if I am trying to commute directly between matches at MetLife and Lincoln Financial Field?"
* **reaction**: An immediate emotional response, meme, baseline prediction, or unbacked narrative about a team, refereeing, or ongoing controversy.
    * *Example 1*: "Cape Verde just took a point from Spain! Absolute cinema, the ball is round and anything can happen!"
    * *Example 2*: "Infantino is totally corrupt engineering the next cycle's hosting rights. Absolute clown show of a tournament."

## 3. Hard Edge Cases
* **The Edge Case**: A post complaining emotionally about tournament issues that pivots to citing explicit logistics/formatting rules (e.g., *"This 48-team format is total garbage. Having 8 third-placed teams go through with 4 points means half these final group games are going to be sluggish stalemates."*).
* **Decision Rule**: If the primary objective/intent of the text is to discuss how the structure or logistics function, label it `logistics` or `analysis` if it breaks down tactical metrics. If the text uses rules or logistics merely as window dressing to express frustration, anger, or hype, label it `reaction`.

## 4. Data Collection Plan
Data will be sourced directly from public threads on r/worldcup. Target is ~70 examples per class to hit 210 total rows. If one class is underrepresented, I will target the Matchday Megathreads for `reaction`, Stadium/Transit FAQs for `logistics`, and Simulation/Tactical post-match threads for `analysis`.

## 5. Evaluation Metrics
* **Primary Metric**: Macro F1-Score. Because tournament spikes can push data heavily into reactionary classes, Macro F1 ensures we evaluate performance on each class equally without letting a dominant class mask poor performance elsewhere.
* **Secondary Metric**: Confusion Matrix tracking to identify directional bleed (specifically to see if the model struggles to parse emotional logistical complaints vs true logistical queries).

## 6. Definition of Success
A Macro F1-score of **>= 0.75** across all three classes. This proves the model has successfully learned the difference between subjective hype, systemic logistics, and structural analysis, making it reliable enough to parse or filter a real-world community feed.

## 7. AI Tool Plan
* **Label Stress-Testing**: Used an LLM to generate borderline text to refine our decision boundaries.
* **Annotation Assistance**: An LLM will be used to generate/pre-label the starting pool of 200 data points based on these specific live 2026 World Cup nuances, followed by 100% human verification.
* **Failure Analysis**: Misclassified test set examples will be passed to an LLM to extract linguistic patterns of failure.
