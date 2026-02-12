# NeuraGuide Data Schema Specification
**Version:** 1.0  
**Date:** February 9, 2026  
**Owner:** ML Engineering Team  
**Status:** Draft for Review

---

## Purpose

This document defines the structure, meaning, constraints, and quality standards for the NeuraGuide AI Tools dataset. It serves as the authoritative reference for:

- Data engineers implementing ETL pipelines
- ML engineers building recommendation systems
- Product managers defining feature requirements
- QA teams validating data quality

**All dataset modifications must comply with this specification.**

---

## Dataset Overview

- **Total Records:** 54,910 AI tools
- **Total Columns:** 15
- **Primary Key:** `ID`
- **Data Quality Score:** 73.6/100 (weighted)
- **Critical Issues:** 4 (Target Users, Taxonomy Clarity, Key Features, Launch Year)

---

## Current System Usage

**CRITICAL CONTEXT:** This dataset is **currently in production**, powering:

1. **AI Chatbot (LLM-based)** - Recommends tools based on natural language queries
   - Example query: "What AI tools can I use to do a PPT presentation?"
   - LLM uses this data to match user needs to appropriate tools

2. **LLM Training/RAG** - Language models are trained on or retrieve from this dataset
   - Quality issues in data = quality issues in recommendations
   - Generic/placeholder values break recommendation logic

**Production Impact of Data Quality Issues:**
- 51.5% "General" Target Users → LLM cannot properly segment recommendations by user type
- 88.2% generic Key Features → LLM cannot differentiate tools by capabilities  
- 200 categories → LLM struggles with overly granular classification
- Category/Function overlap → LLM gets confused about tool categorization

**USER IMPACT:** Poor data quality directly causes:
- Irrelevant tool recommendations
- Generic responses instead of specific matches
- User frustration and reduced trust in platform

---

## Column Inventory

### 1. Tool Name
**Current State:**
- **Data Type:** Text (object)
- **Nullable:** No (0 nulls found)
- **Unique Values:** 54,795 (out of 54,910 rows)
- **Duplicates:** 115 duplicate names detected

**Semantic Meaning:**  
The official product name of the AI tool as used in marketing and documentation.

**Current System Usage:**
- **Chatbot:** Displays tool name in recommendations ("I recommend [Tool Name]...")
- **LLM:** Uses tool name for entity recognition and matching user queries
- **Frontend:** Displayed as primary identifier in search results
- **Impact of Issues:** 115 duplicates could cause LLM to recommend same tool multiple times or create confusion

**Issues Identified:**
- **CRITICAL:** 115 duplicate tool names exist (e.g., "Kaedim" appears multiple times)
- This violates uniqueness assumptions for tool identification

**Constraints (Recommended):**
- Type: String
- Max Length: 200 characters
- Required: Yes (cannot be null)
- Uniqueness: Must be unique OR combined with Company to form composite key
- Validation: No empty strings, no leading/trailing whitespace

**Recommendation:**  
**DECISION NEEDED:** Either enforce uniqueness OR create composite key (Tool Name + Company). Current duplicates must be investigated - are they legitimate (same tool listed twice) or data entry errors?

---

### 2. ID
**Current State:**
- **Data Type:** Integer (int64)
- **Nullable:** No (0 nulls)
- **Unique Values:** 54,910 (100% unique)
- **Range:** 1 to 82,730
- **Sequential:** No (gaps exist)

**Semantic Meaning:**  
Unique identifier for each tool record. Primary key for the dataset.

**Issues Identified:**
- **MODERATE:** Non-sequential IDs suggest deletions or sparse allocation
- ID range (1-82,730) suggests ~28,000 IDs are unused/deleted

**Constraints (Current & Recommended):**
- Type: Integer
- Required: Yes
- Uniqueness: Yes (enforced)
- Auto-increment: Recommended for new records

**Recommendation:**  
**KEEP AS-IS.** This column is functioning correctly as a primary key. Non-sequential IDs are acceptable for historical datasets.

---

### 3. category_rank
**Current State:**
- **Data Type:** Integer (int64)
- **Nullable:** No (0 nulls)
- **Range:** 1 to 1,745
- **Unique Values:** Not analyzed (multiple tools can have same rank in different categories)

**Semantic Meaning:**  
Ranking position of a tool within its category. Lower numbers = higher rank/popularity.

**Issues Identified:**
- **MODERATE:** Purpose is unclear - is this manual curation or algorithmic?
- No documentation on ranking methodology
- Ranking criteria unknown (popularity? quality? recency?)

**Constraints (Recommended):**
- Type: Integer
- Required: Yes
- Min Value: 1
- Max Value: Unbounded (depends on category size)

**Recommendation:**  
**DOCUMENT RANKING LOGIC.** Create separate documentation explaining:
- How ranks are assigned
- How often they're updated
- Whether they're category-specific or global

---

### 4. Category_code
**Current State:**
- **Data Type:** Integer (int64)
- **Nullable:** No (0 nulls)
- **Unique Values:** 208
- **Range:** 0 to 207

**Semantic Meaning:**  
Numeric encoding of the Category field. Maps category names to integers for ML purposes.

**Issues Identified:**
- **MODERATE:** 208 unique codes but only 200 unique categories (8-code discrepancy)
- No mapping table provided (code → category name)
- Unclear if 0-indexed or 1-indexed

**Constraints (Recommended):**
- Type: Integer
- Required: Yes
- Range: 0 to N-1 (where N = number of categories)
- Must have 1:1 mapping to Category values

**Recommendation:**  
**CREATE MAPPING TABLE.** Publish `category_code_mapping.csv` with columns:
- `category_code` (integer)
- `category_name` (text)
- `category_description` (text)

Investigate 8-code discrepancy and resolve.

---

### 5. Category
**Current State:**
- **Data Type:** Text (object)
- **Nullable:** No (0 nulls)
- **Unique Values:** 200
- **Top Values:** Education (2,901), Marketing (2,865), Finance (2,842), Health (1,843)

**Semantic Meaning:**  
**UNCLEAR - REQUIRES DEFINITION.** Appears to represent broad classification of tools, but overlaps 51.7% with Primary Function.

**Current System Usage:**
- **Chatbot:** Primary field for categorizing tools in responses
- **LLM:** Used to filter/match tools to user queries (e.g., "marketing tools" → Category=Marketing)
- **Frontend:** Category filter dropdown, browse by category pages
- **Impact of Issues:** 
  - 200 categories overwhelms users and LLM decision-making
  - 51.7% overlap with Primary Function creates ambiguity in LLM prompts
  - LLM cannot distinguish when to use Category vs Primary Function for matching

**Issues Identified:**
- **CRITICAL:** 200 categories is excessive for user navigation
- **CRITICAL:** 81 categories have fewer than 10 tools (not viable for recommendations)
- **CRITICAL:** Overlaps with Primary Function in 51.7% of cases
- **MODERATE:** Inconsistent relationship with Primary Function (sometimes same, sometimes hierarchical, sometimes unrelated)

**Constraints (Current):**
- Type: String
- Required: Yes
- Allowed Values: Any text (no controlled vocabulary)

**Constraints (Recommended):**
- Type: String (Enum from controlled taxonomy)
- Required: Yes
- Allowed Values: 30-50 canonical categories (see Taxonomy Definitions document)
- Validation: Must match approved category list exactly

**Recommendation:**  
**MAJOR REDESIGN REQUIRED.** See Task 3 (Ambiguous Columns) and Task 5 (Category Taxonomy) for detailed analysis. Options:
1. Keep Category as parent, make Primary Function always more specific
2. Merge into single "Primary Category" field
3. Create hierarchical taxonomy (Category → Subcategory)

**DECISION NEEDED by product team.**

---

### 6. Primary Function
**Current State:**
- **Data Type:** Text (object)
- **Nullable:** No (0 nulls)
- **Unique Values:** 313
- **Top Values:** Data Analysis (3,231), Text Generation (3,180), Image Generation (3,149)

**Semantic Meaning:**  
**UNCLEAR - REQUIRES DEFINITION.** Appears to represent tool's main capability, but overlaps with Category in unclear ways.

**Issues Identified:**
- **CRITICAL:** 313 unique values is even more excessive than Category (200)
- **CRITICAL:** 1,225 tools have "General" as Primary Function (meaningless)
- **CRITICAL:** Overlaps with Category in 51.7% of rows
- **MODERATE:** When different from Category, relationship is inconsistent

**Constraints (Current):**
- Type: String
- Required: Yes
- Allowed Values: Any text (no controlled vocabulary)

**Constraints (Recommended):**
- Type: String (Enum from controlled taxonomy)
- Required: Yes
- Allowed Values: Controlled list (40-60 functions)
- Validation: Must NOT be "General" or other placeholder

**Recommendation:**  
**CLARIFY RELATIONSHIP WITH CATEGORY.** Must decide:
- If keeping both: Define clear hierarchy (e.g., Category = Industry, Function = Capability)
- If merging: Consolidate into single field
- Ban "General" as a value

See Task 3 for detailed analysis.

---

### 7. Pricing Model
**Current State:**
- **Data Type:** Text (object)
- **Nullable:** No (0 nulls)
- **Unique Values:** 30
- **Top Values:** Freemium (9,397), Free (9,006), Subscription (8,227), Pay-per-use (8,108)

**Semantic Meaning:**  
How the tool is monetized and what users pay (or don't pay) to access it.

**Current System Usage:**
- **Chatbot:** Filters tools by budget/pricing preference
- **LLM:** Matches pricing model to user constraints
  - Query: "free tools for image editing" → Should match Pricing Model=Free
  - Query: "tools with free trial" → Should match Pricing Model=Free Trial
- **Frontend:** Pricing filter, displays pricing information
- **Impact of Issues:**
  - 30 variations confuse LLM ("Free Trial" vs "Free TrialPaid" vs "FreemiumFree Trial")
  - LLM cannot reliably filter by budget
  - Concatenated values break exact matching logic

**Issues Identified:**
- **CRITICAL:** 30 variations is excessive (should be 5-8 clear models)
- **CRITICAL:** Concatenated values without delimiters: "FreeFreemiumPaid", "Free TrialPaid"
- **MODERATE:** Wrong concepts mixed in: "Service", "Active deal", "Preview" (not pricing models)
- **MODERATE:** "Paid" is too vague (subscription? one-time? usage-based?)
- **MODERATE:** 1,422 rows marked "Unknown"

**Constraints (Current):**
- Type: String
- Required: Yes
- Allowed Values: Any text (no controlled vocabulary)

**Constraints (Recommended):**
- Type: String (Enum from controlled taxonomy)
- Required: Yes
- Allowed Values: 
  - `Free` (no payment required)
  - `Freemium` (free tier + paid tiers)
  - `Free Trial` (limited-time free access, then paid)
  - `Paid - Subscription` (recurring payment)
  - `Paid - One-Time` (single purchase)
  - `Paid - Usage-Based` (pay per use/API call)
  - `Enterprise` (custom pricing, contact sales)
  - `Open Source` (free, source code available)
- Validation: Must match approved list exactly (case-sensitive)

**Recommendation:**  
**IMPLEMENT CONTROLLED VOCABULARY.** See Task 6 (Pricing Model Taxonomy) for mapping rules from messy values to clean values. Requires data cleanup script.

---

### 8. Target Users
**Current State:**
- **Data Type:** Text (object)
- **Nullable:** No (0 nulls)
- **Unique Values:** 219
- **Top Values:** General (28,253!), Developers (3,125), Startups (1,756)

**Semantic Meaning:**  
The intended audience or user persona for whom the tool is designed.

**Current System Usage:**
- **Chatbot:** **CRITICAL FIELD** - Used to match tools to user's profile/role
- **LLM:** Filters recommendations based on implied user type from query
  - Query: "tools for developers" → Should match Target Users=Developers
  - Query: "small business tools" → Should match Target Users=Business Owners
- **Frontend:** Filter by user type, personalized recommendations
- **Impact of Issues:** 
  - **CATASTROPHIC:** 51.5% are "General" → LLM cannot personalize recommendations
  - User asks "tools for data scientists" → LLM includes 28,253 "General" tools (irrelevant)
  - This is the **#1 reason for poor recommendation quality**
  - Users get generic, unhelpful results instead of targeted matches

**Issues Identified:**
- **CRITICAL:** 51.5% (28,253 tools) are labeled "General" - completely unusable for user segmentation
- **CRITICAL:** Without specific user targeting, AI cannot provide personalized recommendations
- This is the **#1 data quality blocker** for the product

**Constraints (Current):**
- Type: String
- Required: Yes
- Allowed Values: Any text (no controlled vocabulary)

**Constraints (Recommended):**
- Type: String (Enum from controlled taxonomy)
- Required: Yes
- Allowed Values: 10-15 specific personas (see Task 7: Target Users Taxonomy)
- Validation: "General" must be BANNED as a value
- Default: NULL (better to admit we don't know than claim it's for "everyone")

**Recommendation:**  
**EMERGENCY CLEANUP REQUIRED.** This field blocks the entire recommendation engine. Options:
1. Research and classify the 28,253 "General" tools manually
2. Mark them as incomplete and exclude from AI recommendations
3. Use tool descriptions/features to infer likely users algorithmically

**DECISION NEEDED: Product cannot launch with 51.5% unusable segmentation.**

---

### 9. Description
**Current State:**
- **Data Type:** Text (object)
- **Nullable:** No (0 nulls)
- **Average Length:** 76 characters
- **Template Descriptions:** 1,420 (2.6%) follow pattern "AI tool from [Category]"

**Semantic Meaning:**  
Human-readable summary of what the tool does and its key value proposition.

**Current System Usage:**
- **Chatbot:** Primary source for explaining what tool does in recommendations
- **LLM:** Main text field for semantic matching to user queries
  - RAG/embedding search likely uses descriptions to find relevant tools
- **Frontend:** Displayed in search results, tool cards
- **Impact of Issues:**
  - 2.6% template descriptions ("AI tool from [Category]") provide zero semantic meaning
  - Short average length (76 chars) may lack detail for good LLM matching
  - Template descriptions hurt embedding quality and search relevance

**Issues Identified:**
- **MODERATE:** 1,420 template-generated descriptions provide no real information
- **MODERATE:** Average length (76 chars) is quite short - may lack detail
- **MINOR:** Generally in acceptable state

**Constraints (Current):**
- Type: String
- Required: Yes
- Min Length: No enforced minimum
- Max Length: No enforced maximum

**Constraints (Recommended):**
- Type: String
- Required: Yes
- Min Length: 50 characters (ensure meaningful content)
- Max Length: 500 characters (keep concise)
- Validation: 
  - Cannot match template pattern "AI tool from *"
  - Must contain at least 5 words
  - Should mention tool capabilities or use cases

**Recommendation:**  
**ENRICH TEMPLATE DESCRIPTIONS.** The 1,420 template descriptions should be flagged for manual review and rewriting. Consider web scraping or manual research to replace them.

---

### 10. Key Features
**Current State:**
- **Data Type:** Text (object)
- **Nullable:** No (0 nulls)
- **Unique Values:** 246
- **Template Features:** 88.2% are one of two generic templates

**Semantic Meaning:**  
Distinctive capabilities, functionalities, or characteristics that differentiate this tool.

**Current System Usage:**
- **Chatbot:** Used to match tool capabilities to user needs
- **LLM:** Parses features to answer "what can this tool do?" questions
  - Query: "tools with real-time collaboration" → Should match Key Features containing "real-time collaboration"
- **Frontend:** Displayed in tool detail pages, used in feature-based search
- **Impact of Issues:**
  - **CATASTROPHIC:** 88.2% are generic templates → LLM cannot differentiate tools
  - Query: "tools with OCR" → LLM finds 24,994 tools all claiming "AI integration, Machine learning, Real-time processing..." (useless)
  - 11.3% "See website" → LLM has no feature information at all
  - **Result:** All tools look identical to LLM, recommendations are random

**Issues Identified:**
- **CRITICAL:** 11.3% (6,207 tools) are "See website" - placeholder with zero value
- **CRITICAL:** 45.5% (24,994 tools) use generic template #1: "AI integration, Machine learning, Real-time processing, Cloud-based, API access"
- **CRITICAL:** 42.7% (23,466 tools) use generic template #2: "AI-powered, Machine Learning, Automation"
- Only 0.5% have actually researched, tool-specific features

**Constraints (Current):**
- Type: String
- Required: Yes
- Format: Comma-separated list
- Allowed Values: Any text

**Constraints (Recommended):**
- Type: String (comma-separated list)
- Required: Yes
- Min Features: 3 specific features
- Validation:
  - Cannot be "See website"
  - Cannot exactly match generic templates
  - Should include at least one tool-specific feature
  - Features should be verifiable from tool website/documentation

**Recommendation:**  
**MAJOR DATA ENRICHMENT REQUIRED.** 88.7% of this field is generic/useless. Options:
1. Web scraping to extract real features from tool websites
2. Manual research for high-priority tools
3. Mark as incomplete and exclude from feature-based search
4. Use AI to extract features from descriptions/websites

**This blocks feature-based comparison and search.**

---

### 11. Website
**Current State:**
- **Data Type:** Text (object)
- **Nullable:** No (0 nulls)
- **Unique Values:** 54,892 (nearly all unique)

**Semantic Meaning:**  
Official website URL where users can learn more about or access the tool.

**Issues Identified:**
- **NONE:** This field is in excellent shape
- All tools have URLs
- Nearly all are unique (18 duplicates - likely tools sharing landing pages)

**Constraints (Current):**
- Type: String
- Required: Yes
- Format: URL

**Constraints (Recommended):**
- Type: String (URL)
- Required: Yes
- Format: Must be valid URL starting with http:// or https://
- Validation: URL should be accessible (200 response code)
- Uniqueness: Should be unique per tool

**Recommendation:**  
**KEEP AS-IS.** This is one of the best-quality fields. Consider adding periodic validation to ensure URLs are still live (detect dead links).

---

### 12. Company
**Current State:**
- **Data Type:** Text (object)
- **Nullable:** No (0 nulls technically, but 6,207 are "Unknown")
- **Unique Values:** 48,677

**Semantic Meaning:**  
Name of the organization or company that develops/maintains the tool.

**Issues Identified:**
- **MODERATE:** 11.3% (6,207 tools) are "Unknown"
- These appear to be the same 6,207 rows with other missing data
- High number of unique values (48,677) suggests mostly single-tool companies

**Constraints (Current):**
- Type: String
- Required: Yes (but semantically nullable via "Unknown")
- Allowed Values: Any text

**Constraints (Recommended):**
- Type: String (nullable)
- Required: No (can be NULL if genuinely unknown)
- Validation: If provided, should be verifiable company name
- Standardization: Need rules for variants (e.g., "OpenAI" vs "Open AI" vs "OpenAI Inc.")

**Recommendation:**  
**CONVERT "Unknown" to NULL.** Better to represent missing data as NULL than as a string value. Consider:
1. Company name standardization (merge variants)
2. Link to company database for enrichment
3. Extract company from website domain when missing

---

### 13. Launch Year
**Current State:**
- **Data Type:** Text (object) **← WRONG**
- **Nullable:** Yes (1,420 nulls)
- **"Unknown" Values:** 4,787
- **Total Missing:** 6,207 (11.3%)
- **Format Issues:** Mixed "2023" and "2023.0"

**Semantic Meaning:**  
The year the tool was first released or launched to the public.

**Issues Identified:**
- **CRITICAL:** Stored as TEXT instead of INTEGER (breaks chronological operations)
- **CRITICAL:** Mixed formats: "2023" vs "2023.0" (inconsistent)
- **CRITICAL:** Contains string "Unknown" instead of NULL
- **MODERATE:** Suspicious historical values: "1907", "1971", "1982" (predates AI tools era)
- **MODERATE:** 11.3% missing data

**Constraints (Current):**
- Type: String (incorrect)
- Required: No (1,420 nulls exist)
- Validation: None

**Constraints (Recommended):**
- Type: Integer
- Required: No (can be NULL if unknown)
- Range: 2000-2026 (reasonable bounds for AI tools)
- Validation: 
  - Must be 4-digit year
  - Cannot be future year beyond current + 1
  - Flag suspicious years (<2000 for manual review)

**Recommendation:**  
**URGENT DATA TYPE CONVERSION REQUIRED.** Steps:
1. Convert "Unknown" → NULL
2. Remove ".0" suffix (2023.0 → 2023)
3. Convert to integer type
4. Validate range (flag 1907, 1971, etc. for review)
5. Investigate and correct suspicious values

**This blocks trend analysis and "new/trending" features.**

---

### 14. average_rating
**Current State:**
- **Data Type:** Float (float64)
- **Nullable:** No (0 nulls)
- **Min/Max/Mean:** All 0.0
- **Non-Zero Values:** 0

**Semantic Meaning:**  
Average user rating for the tool on a 5-star scale, aggregated from multiple external sources.

**Current System Usage:**
- **Chatbot:** Intended to be used for ranking/sorting recommendations by quality
- **LLM:** Should filter out low-rated tools or prioritize highly-rated ones
- **Frontend:** Display star ratings, enable "sort by rating" functionality
- **Impact of Issues:** 
  - **CRITICAL:** All zeros prevents any quality-based filtering or sorting
  - LLM cannot distinguish between excellent and poor tools
  - Users have no quality signals for decision-making

**Issues Identified:**
- **CRITICAL:** All 54,910 values are 0.0 - completely unusable
- No internal review system exists
- LLM needs this for quality-based recommendations

**APPROVED STRATEGY: External Rating Aggregation**

**Implementation Plan:**
1. **Scrape ratings from multiple sources:**
   - G2 (5-star, B2B/Enterprise focus)
   - Capterra (5-star, SaaS focus)
   - ProductHunt (upvotes → normalized to 5-star)
   - Trustpilot (5-star, consumer focus)
   - GitHub Stars (for dev tools → normalized to 5-star)

2. **Normalize all sources to 5-star scale:**
   - ProductHunt: 100+ upvotes = 5★, 50-99 = 4.5★, etc.
   - GitHub: 10K+ stars = 5★, 5K-10K = 4.5★, etc.
   - G2/Capterra/Trustpilot: Already 5-star (validate range)

3. **Calculate weighted composite rating:**
   - G2: 30% weight (verified reviews, enterprise)
   - Capterra: 25% weight (verified reviews, SaaS)
   - ProductHunt: 20% weight (early adopter signal)
   - Trustpilot: 15% weight (consumer)
   - GitHub: 10% weight (developer community)

4. **Store with attribution and metadata**

**Constraints (Recommended):**
- Type: Float (nullable)
- Required: No (NULL when no external ratings found)
- Range: 1.0 to 5.0 (5-star scale)
- Precision: 1 decimal place (e.g., 4.3)
- Update Frequency: Weekly scraping to keep fresh
- Attribution: Must track which sources contributed (see rating_sources column)

**New Columns to Add:**
```
average_rating          FLOAT(2,1)   # Composite rating (1.0-5.0)
review_count           INT          # Total reviews across all sources
rating_sources         JSON         # {"g2": 4.5, "producthunt": 4.2, ...}
rating_last_updated    TIMESTAMP    # When ratings were last scraped
rating_confidence      ENUM         # Low/Medium/High based on source count
```

**Validation Rules:**
- If rating exists, rating_sources JSON must not be empty
- rating_confidence = High (3+ sources), Medium (2 sources), Low (1 source)
- Ratings older than 30 days should be flagged for re-scraping
- Must display attribution in UI: "4.5/5 based on G2, ProductHunt"

**Legal/Ethical Requirements:**
- Must attribute source platforms ("4.5★ on G2")
- Must respect platform ToS for scraping
- Must update regularly (weekly) to avoid stale data
- Must handle missing data gracefully (not all tools on all platforms)
- NEVER generate synthetic/fake ratings

**Recommendation:**  
**APPROVED: Populate via external scraping with proper attribution and regular updates.**

Timeline:
- Phase 1 (Week 1-2): Build scraping infrastructure
- Phase 2 (Week 3-4): Initial scrape of all 54,910 tools
- Phase 3 (Ongoing): Weekly updates + add new tools as discovered

---

### 15. review_count
**Current State:**
- **Data Type:** Integer (int64)
- **Nullable:** No (0 nulls)
- **Min/Max/Sum:** All 0
- **Non-Zero Values:** 0

**Semantic Meaning:**  
Total number of user reviews/ratings submitted across all external sources.

**Current System Usage:**
- **Chatbot:** Intended to show "social proof" (e.g., "Rated 4.5★ by 1,234 users")
- **LLM:** Should use review count to assess rating reliability (100 reviews vs 5 reviews)
- **Frontend:** Display review count alongside rating
- **Impact of Issues:**
  - All zeros prevents showing social proof
  - Cannot distinguish between "5★ from 3 reviews" vs "5★ from 3,000 reviews"

**Issues Identified:**
- **CRITICAL:** All 54,910 values are 0
- No reviews exist for any tool
- Needed to assess rating confidence

**APPROVED STRATEGY: Aggregate Review Counts from External Sources**

**Implementation:**
- Sum total reviews across all scraped sources
- Example: G2 (234 reviews) + Capterra (156 reviews) + ProductHunt (89 upvotes) = 479 total
- Store as integer count

**Constraints (Recommended):**
- Type: Integer (nullable)
- Required: No (NULL or 0 when no reviews found)
- Range: 0 to unbounded
- Validation: Must be >= 0
- Must be consistent with rating_sources (if rating exists, review_count should be > 0)

**Display Rules:**
- If review_count < 10: Show "New - Limited Reviews"
- If review_count < 50: Show "★★★★☆ (23 reviews)"
- If review_count >= 50: Show "★★★★☆ (234 reviews)" with confidence

**Relationship to rating_confidence:**
```
review_count >= 100 + sources >= 3 → High confidence
review_count >= 25 + sources >= 2  → Medium confidence  
review_count < 25 or sources == 1   → Low confidence
```

**Recommendation:**  
**APPROVED: Populate via sum of external source review counts during weekly scraping.**

---

## Summary of Critical Issues

###  Blocking Issues (Must Fix Before AI Launch)

| Column | Issue | Impact | Priority | Status |
|--------|-------|--------|----------|--------|
| **Target Users** | 51.5% are "General" | Cannot segment users for recommendations | P0 - CRITICAL |  Pending |
| **Category vs Primary Function** | 51.7% overlap, unclear relationship | Cannot categorize tools properly | P0 - CRITICAL |  Pending |
| **Key Features** | 88.2% are generic templates | Cannot compare or search by features | P0 - CRITICAL |  Pending |
| **Launch Year** | Wrong data type (text not int) | Cannot sort/filter by date | P1 - HIGH |  Pending |
| **Pricing Model** | 30 variations, concatenated values | Cannot filter by pricing | P1 - HIGH |  Pending |
| **Ratings** | All zeros, no quality signal | Cannot rank by quality | P1 - HIGH |  Strategy Approved |

###  Important Issues (Should Fix Soon)

| Column | Issue | Impact | Priority | Status |
|--------|-------|--------|----------|--------|
| **Tool Name** | 115 duplicates | Breaks uniqueness assumption | P2 - MEDIUM |  Pending |
| **Company** | 11.3% "Unknown" | Missing attribution data | P2 - MEDIUM |  Pending |
| **Description** | 2.6% are templates | Low-quality content | P3 - LOW |  Pending |
| **Category** | 200 categories (81 have <10 tools) | Navigation overwhelming | P2 - MEDIUM |  Pending |

###  Rating Strategy Approved

| Decision | Status |
|----------|--------|
| **Approach** | External rating aggregation from G2, Capterra, ProductHunt, Trustpilot, GitHub |
| **Rating Scale** | 5-star (normalized from all sources) |
| **Update Frequency** | Weekly scraping |
| **Attribution** | Must display source ("4.5★ on G2, ProductHunt") |
| **New Columns** | rating_sources (JSON), rating_last_updated, rating_confidence |
| **Timeline** | Phase 1: Build scraper (Week 1-2), Phase 2: Initial scrape (Week 3-4), Phase 3: Ongoing updates |

---

## Data Quality Gates

Before any data enters production, it must pass these gates:

### Gate 1: Completeness (Critical Fields)
- Tool Name: 100% populated 
- Category: 100% populated (and NOT "General") 
- Primary Function: 100% populated (and NOT "General") 
- Target Users: 100% populated (and NOT "General") 
- Pricing Model: 100% populated (from approved list) 
- Website: 100% populated 
- **Ratings: Target 80% coverage within 4 weeks** 

### Gate 2: Data Type Correctness
- Launch Year: Must be INTEGER  (currently text)
- ID: Must be INTEGER 
- All categorical fields: Must be STRING 
- **average_rating: Must be FLOAT with 1 decimal precision** 
- **review_count: Must be INTEGER >= 0** 

### Gate 3: Taxonomy Compliance
- Category: Must match approved taxonomy  (no taxonomy exists)
- Primary Function: Must match approved taxonomy  (no taxonomy exists)
- Pricing Model: Must match approved list  (no list enforced)
- Target Users: Must match approved personas  (no personas defined)

### Gate 4: Business Logic
- Launch Year: Must be between 2000-2026  (has 1907, mixed formats)
- Tool Name: Must be unique OR compositely unique with Company  (115 duplicates)
- Key Features: Cannot be "See website" or generic template  (88.2% fail)
- **Ratings: If average_rating exists, must have rating_sources and review_count > 0** 
- **Ratings: Must have attribution metadata (source, timestamp, confidence)** 

### Gate 5: Rating Quality (NEW)
- **Rating sources must be attributed and traceable**
- **Ratings must be updated within 30 days**
- **Composite ratings must use approved weighting formula**
- **Tools with ratings must display source attribution in UI**
- **Low confidence ratings (<25 reviews, 1 source) must be flagged**

---

## Next Steps

This schema specification document should be used in conjunction with:

1. **`taxonomy_definitions.md`** (Task 5-7) - Defines allowed values for categorical fields
2. **`data_standards.md`** (Task 8-9) - Defines formatting rules, naming conventions, and enforcement procedures
3. **`rating_scraper.py`** (NEW) - Implementation of external rating aggregation system

**DECISION POINTS requiring product/engineering leadership:**
1. Category vs Primary Function: Keep both, merge, or redefine relationship? → **Task 3**
2. **APPROVED:** Ratings via external scraping (G2, Capterra, ProductHunt, etc.)
3. Target Users "General" values: Research/reclassify or mark incomplete? → **Task 7**
4. Key Features templates: Web scraping enrichment or manual research? → **Requires resources**

**IMPLEMENTATION PRIORITIES:**

**Immediate (Week 1-2):**
1. Build rating scraper infrastructure
2. Define Category/Function relationship (Task 3 analysis)
3. Create controlled vocabularies for Pricing Model (Task 6)

**Short-term (Week 3-4):**
1. Initial rating scrape for all 54,910 tools
2. Define Target Users taxonomy (Task 7)
3. Fix Launch Year data type (convert text → integer)

**Medium-term (Month 2-3):**
1. Enrich Key Features (replace templates with real data)
2. Reduce Category taxonomy from 200 → 30-50
3. Resolve 115 duplicate Tool Names

**Ongoing:**
1. Weekly rating updates
2. Monitor rating confidence levels
3. Validate new tool submissions against data quality gates

---

**Document End**