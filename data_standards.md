# NeuraGuide Core Data Principles
**Version:** 1.0  
**Owner:** ML Engineering Team  
**Purpose:** Define what "correct data" means for AI-powered tool recommendations

---

## Mission Statement

NeuraGuide's AI platform can only be as good as the data it reasons over. This document establishes the non-negotiable standards for data quality, consistency, and AI-readiness.

**If data doesn't meet these standards, it cannot be used for AI reasoning.**

---

## The 5 Pillars of Correct Data

### Pillar 1: Truthfulness
**Definition:** Every data value must be factually accurate or explicitly marked as unknown/unavailable.

**Why it matters for AI:**  
AI systems assume data is truthful. False data creates hallucinations and erodes user trust.

**Standards:**
- Never invent or guess values
- Use `null` or explicit "Unknown" when information is unavailable
- All factual claims must be verifiable (e.g., launch year, pricing, company name)
- Marketing language is acceptable in descriptions but must not contradict facts

**Examples:**
- ✅ GOOD: `Launch Year: 2023` (verifiable from company website)
- ✅ GOOD: `Launch Year: null` (unknown, but honest)
- ❌ BAD: `Launch Year: 2020` (guess/estimate treated as fact)

---

### Pillar 2: Precision
**Definition:** Data must be specific enough to enable meaningful decision-making.

**Why it matters for AI:**  
Vague data forces AI to make arbitrary assumptions. "General" tells us nothing; "Small Business Owners" tells us who should use the tool.

**Standards:**
- Avoid placeholder values like "General", "Various", "Multiple", "See website"
- Use concrete, bounded terms (e.g., "0-50 employees" not "small teams")
- Provide enough detail for users to self-select (e.g., "No coding required" vs "Advanced Python knowledge")

**Examples:**
- ✅ GOOD: `Target Users: Marketing Teams (No Technical Background)`
- ✅ GOOD: `Key Features: Real-time collaboration, 256-bit encryption, SSO support`
- ❌ BAD: `Target Users: General`
- ❌ BAD: `Key Features: See website`

---

### Pillar 3: Standardization
**Definition:** The same concept must always be represented the same way across all records.

**Why it matters for AI:**  
AI cannot understand that "Free Trial", "Trial Available", and "Try for free" mean the same thing. Inconsistency breaks filtering, comparison, and recommendation logic.

**Standards:**
- Define controlled vocabularies for all categorical fields
- Enforce exact spelling, capitalization, and formatting
- Create explicit mapping rules for legacy/variant values
- Document all allowed values with definitions

**Examples:**
- ✅ GOOD: All pricing uses exactly: `Free`, `Freemium`, `Paid`, `Enterprise`, `Contact for Pricing`
- ❌ BAD: Mix of "free", "Free trial", "No cost", "Gratis", "Trial available"

---

### Pillar 4: Completeness
**Definition:** Each record must contain enough information to fulfill its purpose in the user journey.

**Why it matters for AI:**  
Incomplete data forces users to leave the platform. If 50% of tools say "See website" for features, our AI adds no value.

**Standards:**
- Critical fields must never be null/unknown (e.g., Category, Pricing Model)
- Important fields should have >80% coverage (e.g., Key Features, Target Users)
- Nice-to-have fields can be sparse (e.g., Review Count, Awards)
- Define what "complete enough" means for each field

**Examples:**
- ✅ GOOD: `Description: Converts 2D images to 3D models using neural networks. Supports OBJ, FBX, STL export. Processing time: 2-5 minutes.`
- ❌ BAD: `Description: AI tool for 3D.`

---

### Pillar 5: AI-Readiness
**Definition:** Data must be structured to enable logical reasoning without human interpretation.

**Why it matters for AI:**  
AI cannot "figure out" ambiguous categories or overlapping definitions. Unclear boundaries create nonsensical recommendations.

**Standards:**
- Categories must be mutually exclusive (tool belongs to ONE primary category)
- Taxonomies must have clear boundaries (what IS and what IS NOT in each bucket)
- Values must be machine-comparable (e.g., "Beginner" < "Intermediate" < "Advanced")
- Avoid human-only context (e.g., "As seen on ProductHunt" means nothing to AI)

**Examples:**
- ✅ GOOD: Clear category tree where "3D Modeling" and "3D Animation" don't overlap
- ✅ GOOD: `Skill Level: Beginner` (AI can filter and rank)
- ❌ BAD: Category "3D" where some tools do modeling, others do animation, others do rendering
- ❌ BAD: `Popularity: Well-known` (well-known to whom? in what context?)

---

## The Defensibility Test

For any data value, ask:

> **"If our AI makes a bad recommendation based on this data, can we defend our data quality in a review?"**

If the answer is "No" or "Uncertain", the data does not meet our standards.

### Examples:

| Data Value | Defensible? | Reasoning |
|------------|-------------|-----------|
| `Category: 3D` | ❌ No | Too vague - user wanted 3D printing tools, got animation software |
| `Category: 3D Animation & Motion Capture` | ✅ Yes | Specific boundary - we can defend the categorization |
| `Target Users: General` | ❌ No | Meaningless - cannot explain why AI recommended this |
| `Target Users: Enterprise DevOps Teams (500+ employees)` | ✅ Yes | Clear persona with context |
| `Pricing: Varies` | ❌ No | Cannot filter or compare |
| `Pricing: Freemium (Free tier: 3 projects, Paid: Unlimited)` | ✅ Yes | User knows exactly what to expect |

---

## Data That Fails These Principles

The following patterns indicate data that does NOT meet NeuraGuide standards:

| Anti-Pattern | Why It Fails | Fix |
|--------------|--------------|-----|
| `"Unknown"` as a value (not null) | Treated as a category instead of missing data | Use `null` or create "Data Unavailable" handling |
| `"See website"` | Placeholder that provides zero value | Extract actual features or mark as incomplete |
| `"General"` as a user segment | Meaningless precision loss | Define actual user personas |
| `"Various"` or `"Multiple"` | Vague handwaving | List actual values or create proper taxonomy |
| Duplicate column meanings | Wastes space, confuses AI | Merge or differentiate clearly |

---

## Non-Negotiable Requirements

Before ANY data enters production:

1. **All categorical fields must use controlled vocabularies** (defined in taxonomy documents)
2. **All critical fields must be ≥95% populated** (defined per field in schema spec)
3. **All values must pass the Defensibility Test**
4. **All schema changes must be reviewed against these 5 Pillars**

---

## Next Steps

This document provides the *conceptual foundation*. The following documents will operationalize it:

1. **Data Schema Specification** (defines each column, its type, constraints, and purpose)
2. **Taxonomy Definitions** (defines allowed values for categorical fields)
3. **Data Standards** (defines formatting rules, naming conventions, enforcement)

These principles are permanent. The implementation documents may evolve.

---

**End of Data Standards v1.0**