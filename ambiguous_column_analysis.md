# NeuraGuide Ambiguous Columns Analysis
**Version:** 1.0  
**Date:** February 9, 2026  
**Owner:** ML Engineering Team  
**Status:** Requires Product Decision

---

## Executive Summary

**CRITICAL ISSUE IDENTIFIED:** The `Category` and `Primary Function` columns have an **inconsistent, ambiguous relationship** that is actively degrading the quality of AI recommendations in the live chatbot.

**Impact Severity:** 🔴 **P0 - CRITICAL**

**Quantified Impact:**
- Same user query returns **40-87% different results** depending on which column the LLM uses
- 48.3% of tools (26,522 tools) have different values in these columns
- No consistent pattern: sometimes redundant, sometimes hierarchical, sometimes cross-dimensional
- LLM cannot reliably determine which column to use for filtering/matching

**Action Required:** Product and engineering leadership must choose one of three options (detailed below) before AI recommendations can be reliable.

---

## Problem Statement

### The Ambiguity

**Question:** What is the difference between `Category` and `Primary Function`?

**Current Answer:** It depends.

| Pattern | Frequency | Example | Interpretation |
|---------|-----------|---------|----------------|
| **Identical** | 51.7% (28,388 tools) | Category=Health, Function=Health | Completely redundant |
| **Different** | 48.3% (26,522 tools) | Category=Healthcare, Function=Data Analysis | Unclear relationship |

**This inconsistency makes it impossible for the LLM to use these fields reliably.**

---

## Detailed Analysis

### Finding 1: Overlap Analysis

**When Category == Primary Function (51.7% of data):**

| Category | Tools with Identical Function | Total in Category | % Identical |
|----------|-------------------------------|-------------------|-------------|
| Health | 1,843 | 1,843 | 100% |
| Data Analysis | 1,724 | 1,724 | 100% |
| Audio Generation | 1,695 | 1,695 | 100% |
| Translation | 1,667 | 1,672 | 99.7% |
| Productivity | 1,696 | 1,705 | 99.5% |
| Code Assistant | 1,761 | 1,771 | 99.4% |
| Marketing | 1,902 | 2,865 | **66.4%** |
| Education | 1,839 | 2,901 | **63.4%** |
| Finance | 1,790 | 2,842 | **63.0%** |

**Observation:**
- Some categories (Health, Data Analysis) are **always identical** to function
- Others (Marketing, Education, Finance) are identical only **~65% of the time**
- No consistent rule applies across all categories

---

### Finding 2: When They Differ - Three Distinct Patterns

**Pattern A: Category More Specific, Function Generic (BACKWARDS!)**

| Category | Primary Function | Count | Problem |
|----------|------------------|-------|---------|
| Python | General | 381 | "Python" is MORE specific than "General"! |
| R | General | 83 | "R" is MORE specific than "General"! |
| .NET | General | 18 | ".NET" is MORE specific than "General"! |

**This makes no sense.** If there's a hierarchy, the specific value should be in Primary Function, not Category.

---

**Pattern B: Cross-Dimensional (Industry → Capability)**

| Category (Domain/Industry) | Primary Function (Capability) | Count | Interpretation |
|----------------------------|-------------------------------|-------|----------------|
| Healthcare | Data Analysis | 81 | Industry → Task |
| Mobile Apps | Text Generation | 82 | Platform → Feature |
| Gaming | Data Analysis | 78 | Industry → Task |
| Gaming | Code Generation | 78 | Industry → Task |
| Business | Recommendation | 80 | Domain → Capability |

**Possible interpretation:** Category = vertical/industry, Function = horizontal/capability

---

**Pattern C: Hierarchical (Parent → Child)**

| Category (Broader) | Primary Function (More Specific) | Interpretation |
|-------------------|----------------------------------|----------------|
| 3D/AR | 3D Capture | Parent → Specific type |
| 3D/AR | 3D Scanning | Parent → Specific type |
| 3D/AR | Photogrammetry | Parent → Specific type |
| AI Engineering | Summarization | Broad field → Specific task |
| AI Platforms | Automation | Broad category → Specific function |

**Possible interpretation:** Category = parent, Function = subcategory

---

### Finding 3: Impact on Filtering (Quantified)

**Scenario 1: User asks "Show me marketing tools"**

| Filter Method | Results | Difference |
|--------------|---------|------------|
| Category='Marketing' | 2,865 tools | Baseline |
| Primary Function='Marketing' | 1,903 tools | **-962 tools (33% fewer!)** |
| Overlap (both match) | 1,902 tools | Only these are "safe" |

**Implication:** LLM misses 963 tools if it filters by Function instead of Category.

---

**Scenario 2: User asks "Show me data analysis tools"**

| Filter Method | Results | Difference |
|--------------|---------|------------|
| Category='Data Analysis' | 1,724 tools | Baseline |
| Primary Function='Data Analysis' | 3,231 tools | **+1,507 tools (87% more!)** |
| Only in Category | 960 tools | Missed if using Function |
| Only in Function | 1,442 tools | Missed if using Category |

**Implication:** 
- Using Category: Misses 1,442 relevant tools (44% of total)
- Using Function: Gets 1,507 extra tools, but may include 960 that shouldn't match

---

**Generalized Impact Across Top Categories:**

| Category | By Category | By Function | Difference | % Difference |
|----------|-------------|-------------|------------|--------------|
| Education | 2,901 | 1,894 | 1,007 | 35% |
| Marketing | 2,865 | 1,903 | 962 | 34% |
| Finance | 2,842 | 1,790 | 1,052 | 37% |
| Data Analysis | 1,724 | 3,231 | 1,507 | **87%** |
| Audio Generation | 1,695 | 3,140 | 1,445 | **85%** |
| Image Generation | 1,685 | 3,149 | 1,464 | **87%** |
| Translation | 1,672 | 3,084 | 1,412 | **84%** |

**Conclusion:** Users get radically different results depending on which column the LLM uses. This is unacceptable for a production system.

---

### Finding 4: Real User Query Simulation

**Query: "presentation tools"**

| Search Method | Results | Only Here | Verdict |
|--------------|---------|-----------|---------|
| Search Category | 1,939 | 13 | Misses 1,461 tools |
| Search Function | 3,387 | 1,461 | Misses 13 tools |
| Search Both (Union) | 3,400 | - | Most complete |
| Overlap | 1,926 | - | Only "safe" matches |

**Impact:** If LLM searches only Category, user **misses 43% of relevant presentation tools**.

---

**Query: "data analysis tools"**

| Search Method | Results | Only Here | Miss Rate |
|--------------|---------|-----------|-----------|
| Search Category | 2,752 | 960 | Misses 45% |
| Search Function | 3,234 | 1,442 | Misses 30% |
| Search Both (Union) | 4,194 | - | Most complete |

**Impact:** No matter which column LLM uses, it **misses 30-45% of relevant tools**.

---

**Query: "coding assistants"**

| Search Method | Results | Consistency |
|--------------|---------|-------------|
| Search Category | 1,787 | ✅ Good |
| Search Function | 1,776 | ✅ Good |
| Difference | 11 tools | 99.4% overlap |

**Impact:** This query works well - only 11 tools differ. This is what consistency looks like.

---

## Root Cause Analysis

### Why This Happened

The data was created **without a clear data dictionary** defining:
1. What should go in Category
2. What should go in Primary Function
3. The relationship between them

**Evidence:**
- 126 categories **always** have different functions (suggesting hierarchy)
- 74 categories **always** have identical functions (suggesting redundancy)
- Different data entry people interpreted the fields differently
- No validation rules were enforced

### Why It Persists

**Current LLM behavior (hypothesis):**
```python
# LLM prompt probably says something like:
"Search for tools where Category or Primary Function matches the user's query"

# This creates unpredictable results because:
# - Sometimes both match (overlap)
# - Sometimes only one matches (miss the other)
# - No clear priority or logic
```

---

## Impact on Live Chatbot

### Current State Assessment

**User Experience Issues:**
1. **Inconsistent Results:** Same query phrased differently returns different tools
2. **Missing Recommendations:** 30-87% of relevant tools never shown
3. **Irrelevant Results:** Tools appear that don't match intent
4. **User Confusion:** "Why didn't you show me [obvious tool]?"

**Technical Issues:**
1. **Prompt Engineering Nightmare:** Cannot write reliable filtering logic
2. **Embedding Confusion:** Category embeddings vs Function embeddings point in different semantic directions
3. **Metadata Ambiguity:** Which field should be indexed for search?
4. **Performance Degradation:** Must search both fields, then deduplicate

**Business Impact:**
- Lower user satisfaction (missing relevant tools)
- Reduced trust in AI recommendations
- Competitive disadvantage (other platforms might have clearer categorization)
- Higher support burden ("Why didn't you recommend X?")

---

## Decision Options

### Option 1: Merge Into Single "Primary Category" Field ⭐ RECOMMENDED

**Approach:**
- Eliminate redundancy by keeping only ONE field
- Call it `primary_category` (clear, singular purpose)
- For tools where Category ≠ Function, apply decision rules

**Decision Rules for Merging:**
```
IF Category == Function:
    primary_category = Category  (simple)

IF Category != Function:
    # Pattern A: Category more specific → Use Category
    IF Function == "General":
        primary_category = Category
    
    # Pattern B: Cross-dimensional → Create combined value
    IF category_is_industry(Category) AND function_is_capability(Function):
        primary_category = Function  (capability is more searchable)
        Add tag: industry = Category (preserve info)
    
    # Pattern C: Hierarchical → Use more specific
    IF Category contains "/" or is parent:
        primary_category = Function  (more specific)
    
    # Fallback: Manual review
    ELSE:
        Flag for human decision
```

**Implementation:**
1. Write classification script (1 week)
2. Manually review ambiguous cases (~2,000 tools)
3. Merge columns
4. Add `industry_tag` field to preserve cross-dimensional info
5. Update LLM prompts to use single field

**Pros:**
- ✅ Eliminates ambiguity completely
- ✅ Simpler schema (14 columns instead of 15)
- ✅ Clear prompt engineering (only one field to search)
- ✅ Better user experience (consistent results)
- ✅ Preserves all information (via tags/metadata)

**Cons:**
- ⚠️ Requires manual review of ~2,000 ambiguous cases
- ⚠️ One-time migration effort (1-2 weeks)
- ⚠️ May need to update existing documentation/APIs

**Estimated Effort:** 2-3 weeks  
**Risk:** Low (reversible if needed)

---

### Option 2: Enforce Strict Hierarchy (Category = Parent, Function = Child)

**Approach:**
- Keep both fields
- Redefine clearly: Category = broad parent, Primary Function = specific child
- Enforce 1:many relationship (one Category can have many Functions)

**Implementation:**
1. Create canonical hierarchy (Category → allowed Functions)
2. Reclassify 28,388 currently-identical tools
3. For "Python → General", decide: Should Python be a Category or Function?
4. Update data entry rules

**Example Hierarchy:**
```
Category: 3D/AR
  ├─ Primary Function: 3D Modeling
  ├─ Primary Function: 3D Scanning
  ├─ Primary Function: AR Development
  └─ Primary Function: Photogrammetry

Category: AI Platforms
  ├─ Primary Function: Model Training
  ├─ Primary Function: Deployment
  ├─ Primary Function: Monitoring
  └─ Primary Function: AutoML
```

**Pros:**
- ✅ Preserves both fields (no data loss)
- ✅ Enables browsing by category, then drilling into functions
- ✅ Clear hierarchical navigation UI
- ✅ Preserves existing data structure

**Cons:**
- ❌ Requires reclassifying 51.7% of tools (28,388 currently identical)
- ❌ More complex schema (must maintain hierarchy)
- ❌ LLM must understand parent-child relationships
- ❌ Doesn't solve cross-dimensional cases (Healthcare → Data Analysis)
- ❌ Ongoing maintenance (validate new tools fit hierarchy)

**Estimated Effort:** 4-6 weeks  
**Risk:** Medium (complex to enforce, easy to violate)

---

### Option 3: Redefine as Cross-Dimensional (Industry × Capability)

**Approach:**
- Keep both fields
- Redefine: Category = Industry/Domain, Primary Function = Capability/Task
- Every tool has BOTH dimensions

**Example:**
```
Tool: Tableau
  Category: Business Intelligence (industry/domain)
  Primary Function: Data Visualization (capability)

Tool: Unity ML-Agents
  Category: Gaming (industry)
  Primary Function: Machine Learning (capability)

Tool: Medvice
  Category: Healthcare (industry)
  Primary Function: Diagnosis (capability)
```

**Implementation:**
1. Define allowed Industries (~30-50)
2. Define allowed Capabilities (~40-60)
3. Reclassify all tools into Industry × Capability matrix
4. Update LLM to search both dimensions appropriately

**LLM Search Logic:**
```python
User: "data analysis tools for healthcare"
→ Match: Category=Healthcare AND Function=Data Analysis

User: "marketing tools"
→ Match: Category=Marketing OR Function=Marketing
```

**Pros:**
- ✅ Richest metadata (two dimensions of information)
- ✅ Enables complex queries (industry + capability)
- ✅ Clearer semantics than current state
- ✅ Supports advanced filtering UI

**Cons:**
- ❌ Requires reclassifying 100% of tools (not all fit this model)
- ❌ Most complex option (maintain two taxonomies)
- ❌ Some tools don't have clear industry (general-purpose tools)
- ❌ Highest ongoing maintenance burden
- ❌ LLM prompt complexity (when to AND vs OR)

**Estimated Effort:** 6-8 weeks  
**Risk:** High (complex, may not fit all tools)

---

## Recommendation Matrix

| Criteria | Option 1: Merge | Option 2: Hierarchy | Option 3: Cross-Dimensional |
|----------|-----------------|---------------------|----------------------------|
| **Simplicity** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **Data Quality** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **LLM Clarity** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **User Experience** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Implementation Speed** | ⭐⭐⭐⭐⭐ (2-3 weeks) | ⭐⭐⭐ (4-6 weeks) | ⭐⭐ (6-8 weeks) |
| **Maintenance Burden** | ⭐⭐⭐⭐⭐ (Low) | ⭐⭐⭐ (Medium) | ⭐⭐ (High) |
| **Risk** | ⭐⭐⭐⭐⭐ (Low) | ⭐⭐⭐ (Medium) | ⭐⭐ (High) |
| **Metadata Richness** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

---

## Final Recommendation

### ⭐ **RECOMMENDED: Option 1 (Merge Into Single Field)**

**Rationale:**

1. **Fastest Time to Value:** 2-3 weeks vs 4-8 weeks for other options
2. **Lowest Risk:** Simple, reversible, proven approach
3. **Best for LLM:** Single field = clear, unambiguous filtering
4. **Solves Core Problem:** Eliminates inconsistency completely
5. **Preserves Information:** Can add `industry_tag` field for cross-dimensional cases

**Implementation Roadmap:**

**Week 1:**
- Day 1-2: Write classification script
- Day 3-5: Run automated classification (handles ~80% of cases)

**Week 2:**
- Day 1-3: Manual review of ~2,000 ambiguous cases
- Day 4-5: Implement `industry_tag` field for cross-dimensional tools

**Week 3:**
- Day 1-2: Update LLM prompts to use `primary_category`
- Day 3-4: Test recommendations quality
- Day 5: Deploy to production

**Success Metrics:**
- Query consistency: Same query → same results (100%)
- Recommendation completeness: ≥95% of relevant tools returned
- User satisfaction: Increased click-through on recommendations

---

## Alternative: Hybrid Approach (If More Time Available)

**If product team wants richer metadata but needs fast results:**

1. **Phase 1 (Weeks 1-3):** Implement Option 1 (merge) to fix immediate problem
2. **Phase 2 (Months 2-3):** Add `industry_tag` and `capability_tag` fields
3. **Phase 3 (Month 4+):** Gradually enrich with cross-dimensional metadata

**Benefits:**
- Get quick win (fix ambiguity fast)
- Build toward richer metadata over time
- Learn from Phase 1 before investing in Phase 2


## Decision Required

**Who Decides:** Product Leadership + Engineering Leadership  
**Timeline:** Decision needed by: **End of Week** (Feb 14, 2026)  
**Blocking:** Yes - AI recommendations cannot be reliable until this is resolved

**Questions for Decision-Makers:**

1. **Speed vs Richness:** Fast fix (Option 1) or richer metadata (Option 3)?
2. **Resources:** Do we have 2-3 weeks (Option 1) or 6-8 weeks (Option 3)?
3. **Product Vision:** Simple categorization or complex multi-dimensional filtering?
4. **User Needs:** Do users need industry filters separate from capability filters?

**Document End**

**Next Action:** Schedule decision meeting with stakeholders by Feb 14, 2026.