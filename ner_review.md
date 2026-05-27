This audit identifies systematic extraction errors in the 1921 Tulsa City Directory dataset.

### 1. Alphabetical Range/Context Drift
Several entries are assigned to the wrong `alphabetical_range` or represent a failure to detect transitions between sections. This is most evident when names starting with "A" appear deep inside sections that should be "B" or "C," or when the range remains static despite the names changing.

*   **Examples:**
    *   `ALL,CRANDALL & MURTA` (Should be in "CRA")
    *   `ATC,Abercrombie & Rush` (Should be in "ABE" or "AAR")
    *   `ALL,ABBOTT & WELCH` (Should be in "ABB")
*   **Estimated Rows Affected:** 500–1,000 rows.
*   **Likely Cause:** The NER model is relying on the `prior_context` provided in the prompt rather than correctly identifying the centered single-letter headings (e.g., "B") or the running headers on new pages.
*   **Suggested Fix:** Improve the pipeline's "Heading Transition" logic to prioritize centered uppercase letters as section breaks.

### 2. Ghost Duplicates (OCR Double-Reads)
There are numerous instances where a single physical entry was extracted twice—once correctly and once with a slight OCR error—resulting in near-duplicate rows.

*   **Examples:**
    *   `AKI,Aeguntos George` vs. `AKI,Aeguntos orge` (Same address/role)
    *   `ACM,Acre Albert B` vs. `ACM,Acre Albert` (Same address/role)
    *   `BEN,Brenner Louis M` vs. `BEN,Brenner Louie M` (Same address/role)
*   **Estimated Rows Affected:** 2–3% of the dataset.
*   **Likely Cause:** The OCR pipeline is likely processing overlapping segments of the page or re-reading lines during the "Identify Entries" phase.
*   **Suggested Fix:** Implement a fuzzy-matching deduplication step that flags rows with identical addresses and high name similarity.

### 3. Systematic OCR Character Substitution
Specific surnames and common terms are consistently misread across dozens of rows, suggesting a pattern of character confusion.

*   **Examples:**
    *   **Agnew/Agnen:** `AKI,Agnen Herbert I`, `AKI,Agnen James R` (Consistently misreading 'w' as 'n').
    *   **Tool/Tcol:** `AUS,Austin E Fulton` (Occupation: `tcol dresser` instead of `tool dresser`).
    *   **Building/Bldg:** Consistently inconsistent capitalization and period usage (e.g., `bldg` vs `Bldg`).
*   **Estimated Rows Affected:** 1,500+ rows.
*   **Likely Cause:** Standard OCR limitations with 1920s serif typefaces where 'w' and 'n' or 'oo' and 'co' are tightly spaced.
*   **Suggested Fix:** Post-process the `occupation_role` and `employer` fields with a dictionary-based autocorrect for common 1920s directory terms.

### 4. Field Overlap: Occupation vs. Employer
The NER model frequently fails to distinguish between a job title and the company name, often concatenating them into the `occupation_role` or duplicating the company name in both `occupation_role` and `employer`.

*   **Examples:**
    *   `AAR,ABBOTT C KENNETH`: Occupation: `Mngr Independent Torpedo Co`, Employer: `Independent Torpedo Co`.
    *   `ACM,Acme Brick Co`: Occupation: `W T Johnson dist mngr`. (Officer name merged into occupation).
    *   `AKI,ADKISON JAMES M`: Occupation: `Commr of Police and Fire`.
*   **Estimated Rows Affected:** 10–15% of individual entries.
*   **Likely Cause:** The directory format often places the title and employer in a single comma-separated string, confusing the NER's boundary detection.
*   **Suggested Fix:** Refine the NER prompt to explicitly state that if a company name appears in the title string, it should be moved to the `employer` field.

### 5. Address Field Concatenation
The `address` field often contains both the business location and the residence, or multiple room numbers, without clear delimiters.

*   **Examples:**
    *   `ALE,Alch Mathilde`: Address: `713 First Natl Bank bldg, r "D" r Cynthia Court`
    *   `ALL,Allen Eugene B`: Address: `315 E 2d, r 32 N Gillette av`
    *   `ALL,Allen John J`: Address: `214 S Frisco av, r same`
*   **Estimated Rows Affected:** 5% of rows.
*   **Likely Cause:** The model is capturing the entire end-of-entry string as the address rather than splitting the business address from the residential address (prefixed by 'r').
*   **Suggested Fix:** Update the schema to include `business_address` and `residential_address` to force the model to separate these distinct entities.

### Overall Quality Assessment
The extraction is **generally good** for identifying individual names and basic roles, but it is **unreliable for structured geographical analysis** due to address merging and section-range drift. The presence of "ghost" duplicates suggests the pipeline needs a more robust line-segmentation or deduplication logic.