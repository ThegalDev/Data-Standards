# NeuraGuide Column-Level Constraints Specification
**Version:** 1.0  
**Date:** February 9, 2026  
**Owner:** ML Engineering Team  
**Purpose:** Define validation rules and constraints for each column

---

## Purpose

This document specifies the **exact constraints** that must be enforced for each column in the NeuraGuide AI Tools dataset. These constraints serve as:

1. **Validation rules** for data entry and imports
2. **Quality gates** for production deployment
3. **Contract** between data producers and consumers
4. **Documentation** for developers building validation systems

**All data entering the system must pass these constraints or be rejected.**

---

## Constraint Types Reference

### Data Type Constraints
- **String:** Text data, variable length
- **Integer:** Whole numbers only
- **Float:** Decimal numbers
- **Enum:** Must match one value from a predefined list
- **URL:** Must be valid web address
- **JSON:** Must be valid JSON format
- **Timestamp:** Date/time in ISO 8601 format

### Validation Constraints
- **Required:** Cannot be NULL/empty
- **Nullable:** Can be NULL
- **Unique:** No duplicates allowed
- **Range:** Numeric values must fall within min/max
- **Length:** String length restrictions
- **Pattern:** Must match regex pattern
- **Foreign Key:** Must reference existing value in another table/list

---

## Column Constraints

### 1. Tool Name

**Data Type:** String  
**Required:** Yes (cannot be NULL or empty)  
**Nullable:** No  
**Unique:** Yes (must be unique across dataset)  

**Length Constraints:**
- Minimum: 2 characters
- Maximum: 200 characters

**Format Constraints:**
- Cannot be only whitespace
- No leading or trailing whitespace
- No special characters except: `-`, `_`, `.`, `&`, `+`, `(`, `)`
- Must contain at least one letter or number

**Business Rules:**
- Must be the official product name as used in marketing
- Avoid generic names like "Tool", "Software", "App" alone
- If duplicate exists, append differentiator: "ToolName (by CompanyName)"

**Validation Examples:**
- Valid: "ChatGPT", "Midjourney", "Stable Diffusion XL", "Claude 3.5"
- Invalid: "", "  ", "Tool", "123456789" (no letters), "A" (too short)
- Warning: "Tool Name!!!" (excessive punctuation, should be cleaned)

**Current Issues to Fix:**
- 115 duplicate tool names must be resolved
- Standardize naming conventions (e.g., version numbers)

---

### 2. ID

**Data Type:** Integer  
**Required:** Yes  
**Nullable:** No  
**Unique:** Yes (primary key)  

**Range Constraints:**
- Minimum: 1
- Maximum: 9,999,999,999 (10 digits)

**Business Rules:**
- Auto-increment for new records
- Never reuse deleted IDs (maintain referential integrity)
- Sequential assignment preferred but not required

**Validation Examples:**
- Valid: 1, 100, 54910, 82730
- Invalid: 0, -1, NULL, 99999999999 (exceeds max)

---

### 3. category_rank

**Data Type:** Integer  
**Required:** Yes  
**Nullable:** No  

**Range Constraints:**
- Minimum: 1
- Maximum: Unbounded (depends on category size)

**Business Rules:**
- Rank within category (1 = highest rank)
- Lower number = higher priority/popularity
- Must be recalculated when category changes
- Ranking methodology must be documented separately

**Validation Examples:**
- Valid: 1, 50, 1745
- Invalid: 0, -5, NULL

**Documentation Required:**
- How ranks are calculated (algorithm/formula)
- Update frequency (daily, weekly, monthly)
- Criteria (popularity, quality, recency, etc.)

---

### 4. Category_code

**Data Type:** Integer  
**Required:** Yes  
**Nullable:** No  

**Range Constraints:**
- Minimum: 0
- Maximum: N-1 (where N = total number of categories)

**Business Rules:**
- Must have 1:1 mapping to Category text value
- Mapping table must be maintained separately
- Zero-indexed (0 is valid first code)

**Validation Examples:**
- Valid: 0, 50, 207
- Invalid: -1, 999 (exceeds category count), NULL

**Required Reference:**
- Must maintain `category_code_mapping.csv`:
  ```
  category_code,category_name
  0,3D
  1,Education
  ...
  ```

---

### 5. Category

**Data Type:** String (Enum - controlled vocabulary)  
**Required:** Yes  
**Nullable:** No  

**PENDING DECISION:** See `ambiguous_columns_analysis.md`

**Temporary Constraints (until decision made):**
- Must match one value from approved taxonomy (see Task 5)
- Length: 3-50 characters
- No special characters except spaces, `/`, `&`, `-`

**Post-Decision Constraints:**

**If Option 1 (Merge) chosen:**
- Field renamed to `primary_category`
- Must match approved taxonomy (30-50 values)
- Enum constraint enforced

**If Option 2 (Hierarchy) chosen:**
- Must match parent category list
- Each category must have defined child functions

**If Option 3 (Cross-Dimensional) chosen:**
- Renamed to `industry` or `domain`
- Must match industry taxonomy

**Validation Examples:**
- Valid: "Marketing", "3D/AR", "Code Assistant"
- Invalid: "", "General", "marketing" (wrong case), "Misc", "Other"

**Forbidden Values:**
- "General", "Various", "Multiple", "Other", "Misc", "Unknown"

---

### 6. Primary Function

**Data Type:** String (Enum - controlled vocabulary)  
**Required:** Yes  
**Nullable:** No  

**PENDING DECISION:** See `ambiguous_columns_analysis.md`

**Current Issues:**
- 1,225 values are "General" (forbidden)
- 313 unique values (too many)

**Post-Decision Constraints:**

**If Option 1 (Merge) chosen:**
- This column will be removed (merged into `primary_category`)

**If Option 2 (Hierarchy) chosen:**
- Must match allowed child functions for parent category
- Length: 3-50 characters
- Must be more specific than Category

**If Option 3 (Cross-Dimensional) chosen:**
- Renamed to `capability` or `function`
- Must match capability taxonomy (40-60 values)

**Validation Examples:**
- Valid: "Data Analysis", "Text Generation", "3D Modeling"
- Invalid: "General", "", "general" (wrong case)

**Forbidden Values:**
- "General", "Various", "Multiple", "Other", "See website"

---

### 7. Pricing Model

**Data Type:** String (Enum - controlled vocabulary)  
**Required:** Yes  
**Nullable:** No  

**Allowed Values (EXACTLY 8 - see Task 6 for definitions):**
1. `Free`
2. `Freemium`
3. `Free Trial`
4. `Paid - Subscription`
5. `Paid - One-Time`
6. `Paid - Usage-Based`
7. `Enterprise`
8. `Open Source`

**Format Constraints:**
- Must match exactly (case-sensitive)
- No variations allowed
- No concatenated values

**Business Rules:**
- If pricing is unclear, use "Enterprise" (indicates custom/contact sales)
- Open Source can also be "Free" if no paid tier exists
- If multiple models apply, choose primary revenue model

**Validation Examples:**
- Valid: "Free", "Freemium", "Paid - Subscription"
- Invalid: "free" (wrong case), "Free Trial Paid" (concatenated), "Subscription" (incomplete), "Unknown", "Contact for Pricing"

**Current Issues to Fix:**
- 30 variations must be mapped to these 8 values (see Task 8)
- Concatenated values must be split and primary chosen

---

### 8. Target Users

**Data Type:** String (Enum - controlled vocabulary)  
**Required:** Yes  
**Nullable:** No  

**Allowed Values (10-15 personas - see Task 7 for full definitions):**

Preliminary list (to be finalized in Task 7):
1. `Developers`
2. `Data Scientists`
3. `Business Analysts`
4. `Marketing Teams`
5. `Content Creators`
6. `Designers`
7. `Product Managers`
8. `Small Business Owners`
9. `Enterprise Teams`
10. `Researchers`
11. `Students & Educators`
12. `Freelancers`

**Format Constraints:**
- Must match exactly (case-sensitive)
- Singular or plural as defined in taxonomy
- No abbreviations

**Business Rules:**
- Choose the PRIMARY user persona (if multiple, pick most important)
- "General" is FORBIDDEN - every tool has a target audience
- If truly multi-audience, choose broadest relevant persona

**Validation Examples:**
- Valid: "Developers", "Content Creators", "Enterprise Teams"
- Invalid: "General", "Everyone", "Users", "All", "N/A", "Various"

**Current Critical Issue:**
- 28,253 tools (51.5%) are "General" and must be reclassified

**Forbidden Values:**
- "General", "Everyone", "All", "Various", "Multiple", "Users", "People"

---

### 9. Description

**Data Type:** String (Text)  
**Required:** Yes  
**Nullable:** No  

**Length Constraints:**
- Minimum: 50 characters (ensure meaningful content)
- Maximum: 500 characters (keep concise)
- Target: 100-200 characters (optimal for LLM context)

**Format Constraints:**
- Must be complete sentences with proper grammar
- Must end with punctuation
- Must contain at least 5 words
- No excessive capitalization (max 20% uppercase)
- No special characters except standard punctuation

**Content Constraints:**
- Must describe what the tool DOES (capabilities)
- Should mention primary use case or benefit
- Must be unique (no copy-paste from other tools)
- Cannot be template: "AI tool from [Category]"

**Quality Rules:**
- No marketing fluff ("revolutionary", "game-changing" without substance)
- Specific over generic ("Converts 2D to 3D models" not "Helps with 3D")
- Action-oriented (what users can DO with it)

**Validation Examples:**
- Valid: "Converts 2D images into high-quality 3D models using neural networks. Supports OBJ, FBX, and STL export formats."
- Invalid: "AI tool" (too short), "AI tool from 3D" (template), "THE BEST AI TOOL EVER!!!" (marketing fluff), "Tool." (not descriptive)

**Current Issues to Fix:**
- 1,420 template descriptions ("AI tool from X")
- Average length only 76 characters (below minimum)

---

### 10. Key Features

**Data Type:** String (Comma-separated list)  
**Required:** Yes  
**Nullable:** No  

**Length Constraints:**
- Minimum: 3 features
- Maximum: 10 features
- Each feature: 3-50 characters

**Format Constraints:**
- Comma-separated format: "Feature1, Feature2, Feature3"
- Consistent capitalization (Title Case recommended)
- No trailing comma
- Spaces after commas

**Content Constraints:**
- Features must be SPECIFIC to this tool
- Cannot be "See website"
- Cannot be generic templates:
  - "AI integration, Machine learning, Real-time processing, Cloud-based, API access"
  - "AI-powered, Machine Learning, Automation"
- Must be verifiable from tool website/documentation
- Should differentiate this tool from competitors

**Quality Rules:**
- Specific over generic:  "Converts JPEG to 3D meshes in 2 minutes" vs  "Image processing"
- Technical features preferred:  "REST API", "SSO integration", "Webhook support"
- Quantifiable when possible:  "50+ templates", "Supports 12 languages"

**Validation Examples:**
-  Valid: "Real-time collaboration, 256-bit encryption, SSO support, Version control, 50+ integrations"
-  Invalid: "See website", "AI-powered, Machine Learning, Automation", "Good features"

**Current Critical Issue:**
- 88.2% are generic templates or "See website"
- Must be researched and replaced with actual features

---

### 11. Website

**Data Type:** String (URL)  
**Required:** Yes  
**Nullable:** No  

**Format Constraints:**
- Must be valid URL
- Must start with `http://` or `https://` (https preferred)
- Must include domain name
- Maximum length: 500 characters

**Pattern Validation:**
```
^https?://[a-zA-Z0-9-.]+(.[a-zA-Z]{2,})(:[0-9]+)?(/.*)?$
```

**Business Rules:**
- Must be the official product website (not social media, docs, blog)
- Prefer marketing/landing page over login page
- Must be accessible (200 HTTP response)
- Should be stable (not beta/staging URLs)

**Quality Rules:**
- Remove tracking parameters (UTM codes, etc.)
- Use canonical URL (avoid redirects when possible)
- No URL shorteners (bit.ly, etc.)

**Validation Examples:**
- Valid: "https://www.openai.com", "https://midjourney.com", "https://claude.ai"
- Invalid: "openai.com" (missing protocol), "http:/openai.com" (malformed), "", "www.site"

**Current State:**
- 100% populated (good)
- Should validate all are still live (404 check)

---

### 12. Company

**Data Type:** String  
**Required:** No  
**Nullable:** Yes  

**Length Constraints:**
- Minimum: 2 characters (if provided)
- Maximum: 100 characters

**Format Constraints:**
- Proper capitalization
- No leading/trailing whitespace
- Legal entity suffixes optional: "Inc.", "LLC", "Ltd.", "Corp."

**Business Rules:**
- Should be official registered company name
- If unknown, use NULL (not "Unknown" string)
- Solo developer: Use individual name or "Independent"

**Standardization Rules:**
- "OpenAI" (not "Open AI", "OpenAI Inc.")
- "Google" (not "Google Inc.", "Alphabet")
- Consistent naming across all tools from same company

**Validation Examples:**
- Valid: "OpenAI", "Anthropic", "Google", "Microsoft", NULL
- Invalid: "Unknown" (use NULL), "" (use NULL), "unknown company"

**Current Issues:**
- 6,207 are string "Unknown" (should be NULL)
- Need company name standardization (merge variants)

---

### 13. Launch Year

**Data Type:** Integer  
**Required:** No  
**Nullable:** Yes  

**Range Constraints:**
- Minimum: 2000 (reasonable earliest for AI tools)
- Maximum: Current year + 1 (allow upcoming launches)
- As of 2026-02-09: Range is 2000-2027

**Business Rules:**
- Year of initial public release (not beta, not founding year)
- If unknown, use NULL (not 0, not "Unknown")
- Future years only for officially announced upcoming releases

**Validation Examples:**
- Valid: 2023, 2024, 2025, 2026, NULL
- Invalid: 1907, 1999, 2030, 0, -1, "Unknown", "2023.0" (wrong type)

**Current Critical Issues:**
- Currently stored as STRING (must convert to INTEGER)
- Mixed formats: "2023" vs "2023.0" (must clean)
- String "Unknown" must convert to NULL
- Suspicious old values: 1907, 1971, 1982 (must investigate)

---

### 14. average_rating

**Data Type:** Float  
**Required:** No  
**Nullable:** Yes  

**Range Constraints:**
- Minimum: 1.0
- Maximum: 5.0
- Precision: 1 decimal place

**Format:**
- Format: X.X (e.g., 4.3, 3.7, 5.0)
- Not: 4.33333, 4, 5.00001

**Business Rules:**
- Composite of multiple external sources (see schema doc)
- Must be accompanied by `rating_sources` JSON
- Must have `review_count` > 0 if rating exists
- Must have `rating_last_updated` timestamp
- If no external ratings found, use NULL

**Validation Examples:**
- Valid: 4.5, 3.2, 5.0, NULL
- Invalid: 0.0, 6.0, 4.56 (too precise), -1, "4.5" (wrong type)

**Dependencies:**
- If `average_rating` is NOT NULL:
  - `rating_sources` must NOT be empty
  - `review_count` must be > 0
  - `rating_last_updated` must be within 30 days

---

### 15. review_count

**Data Type:** Integer  
**Required:** No  
**Nullable:** Yes  

**Range Constraints:**
- Minimum: 0
- Maximum: Unbounded

**Business Rules:**
- Sum of all reviews across external sources
- If no ratings exist, use NULL or 0
- Must be consistent with `average_rating`
- Updated when ratings are updated

**Validation Examples:**
- Valid: 0, 50, 1234, NULL
- Invalid: -5, 999999999999999 (unrealistic), "100" (wrong type)

**Dependencies:**
- If `average_rating` exists, `review_count` must be > 0
- If `review_count` > 0, `average_rating` should exist

---

### 16. rating_sources (NEW COLUMN)

**Data Type:** JSON  
**Required:** No  
**Nullable:** Yes  

**Format:**
```json
{
  "g2": 4.5,
  "capterra": 4.3,
  "producthunt": 4.8,
  "trustpilot": 4.1,
  "github": 4.0
}
```

**Constraints:**
- Valid JSON format
- Keys must be lowercase source names
- Values must be floats between 1.0-5.0
- At least 1 source if rating exists
- Maximum 5 sources

**Allowed Source Names:**
- `g2`
- `capterra`
- `producthunt`
- `trustpilot`
- `github`

**Validation Examples:**
- Valid: `{"g2": 4.5, "producthunt": 4.2}`, `{}` (if no rating), NULL
- Invalid: `{G2: 4.5}` (wrong case), `{"random_site": 5.0}` (invalid source), `"not json"`

---

### 17. rating_last_updated (NEW COLUMN)

**Data Type:** Timestamp (ISO 8601)  
**Required:** No  
**Nullable:** Yes  

**Format:**
- ISO 8601: `YYYY-MM-DDTHH:MM:SSZ`
- Example: `2026-02-09T14:30:00Z`

**Business Rules:**
- Timestamp of most recent rating scrape
- Must be updated whenever ratings are refreshed
- Flag if older than 30 days (stale data warning)

**Validation Examples:**
- Valid: `2026-02-09T14:30:00Z`, `2026-01-15T08:00:00Z`, NULL
- Invalid: `02/09/2026` (wrong format), `2026-13-01T00:00:00Z` (invalid month)

---

### 18. rating_confidence (NEW COLUMN)

**Data Type:** String (Enum)  
**Required:** No  
**Nullable:** Yes  

**Allowed Values:**
- `High` (3+ sources, 100+ reviews)
- `Medium` (2 sources, 25-99 reviews)
- `Low` (1 source, <25 reviews)

**Calculation Logic:**
```
IF source_count >= 3 AND review_count >= 100: "High"
ELSE IF source_count >= 2 AND review_count >= 25: "Medium"
ELSE IF source_count >= 1: "Low"
ELSE: NULL
```

**Validation Examples:**
- Valid: "High", "Medium", "Low", NULL
- Invalid: "high" (wrong case), "Very High", "Confident", ""

---

## Cross-Column Validation Rules

### Rule 1: Category-Function Consistency
**Status:** PENDING (awaiting decision on Option 1, 2, or 3)

**When decided:**
- If Option 1: Primary Function column removed
- If Option 2: Function must be allowed child of Category
- If Option 3: Both must match their respective taxonomies

---

### Rule 2: Rating Dependencies

**If `average_rating` IS NOT NULL:**
- `rating_sources` must contain at least 1 source
- `review_count` must be > 0
- `rating_last_updated` must exist
- `rating_confidence` must be one of: High, Medium, Low

**If `average_rating` IS NULL:**
- `rating_sources` should be NULL or `{}`
- `review_count` should be NULL or 0
- `rating_last_updated` should be NULL
- `rating_confidence` should be NULL

---

### Rule 3: Uniqueness Combinations

**Primary Key:**
- `ID` alone (unique)

**Alternate Key:**
- `Tool Name` + `Company` (composite uniqueness)
- Allows different companies to have tools with same name

---

### Rule 4: Launch Year vs Current Date

**Rule:**
```
IF Launch Year IS NOT NULL AND Launch Year > (Current Year + 1):
  REJECT (future launch too far out)
```

**Example (as of 2026):**
- Valid: 2027 (next year, allowed)
- Invalid: 2030 (too far in future)

---

## Constraint Enforcement Levels

### Level 1: CRITICAL (Must Fix Before Production)
- Tool Name: Required, unique, proper format
- Category: Must match approved taxonomy
- Pricing Model: Must match approved list
- Target Users: Cannot be "General"
- Description: Minimum 50 characters, no templates
- Website: Valid URL format

**Impact:** Blocks production deployment

---

### Level 2: HIGH (Should Fix Within 1 Month)
- Launch Year: Correct data type (integer)
- Key Features: No "See website", no generic templates
- Company: "Unknown" → NULL
- Rating columns: Proper dependencies

**Impact:** Degrades user experience

---

### Level 3: MEDIUM (Nice to Have)
- Description: Optimal length (100-200 chars)
- Company: Name standardization
- category_rank: Methodology documented

**Impact:** Minor quality improvement

---

## Validation Implementation Notes

**For Developers:**

1. **Validation Layers:**
   - Input validation (before entering database)
   - Storage validation (database constraints)
   - Output validation (before serving to LLM)

2. **Error Handling:**
   - Validation failures should return specific error messages
   - Include column name, value, constraint violated
   - Suggest corrective action when possible

3. **Batch Validation:**
   - Run full dataset validation weekly
   - Generate report of constraint violations
   - Track violations over time (trending)

4. **Constraint Testing:**
   - Unit tests for each constraint rule
   - Integration tests for cross-column rules
   - Test both valid and invalid examples

---

## Maintenance

**Review Schedule:**
- Quarterly review of constraints
- Update allowed values as product evolves
- Add constraints as new patterns emerge

**Change Management:**
- All constraint changes require approval
- Document reason for changes
- Version control this specification

---

**Document End**