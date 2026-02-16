# Pricing Model Taxonomy
**Document Version:** 1.0  
**Date:** February 2026  
**Purpose:** Define canonical pricing models for NeuraGuide AI tools platform

---

## Executive Summary

**Problem:** Current dataset has 30 pricing model variations with inconsistencies:
- Concatenated values: "FreeFreemiumPaid", "Free TrialPaid"
- Non-pricing values: "Active deal", "Service", "Preview"
- Vague values: "Paid" (799 tools - paid how?)
- Unknown: 1,422 tools (2.6%)

**Solution:** Consolidate into **6 canonical pricing models** with clear definitions

**Impact:**
- Users can filter by budget accurately
- LLM can understand pricing structure
- Consistent language across platform
- Clear comparison between tools

---

## Design Principles

### 1. **User-Centric Language**
Use terms users understand and search for:
- ✅ "Free" (clear to everyone)
- ❌ "Open Source" (technical users only)

### 2. **Mutually Exclusive Categories**
Each tool has exactly ONE pricing model. No overlap or concatenation.

### 3. **Decision-Making Clarity**
Pricing model should help users answer:
- "Can I try this without paying?"
- "What's my budget commitment?"
- "How will I be charged?"

### 4. **LLM-Friendly Definitions**
Clear rules for categorization that an LLM can apply consistently.

---

## Canonical Pricing Models (6)

### **1. FREE**
**Definition:** Tool is completely free to use with no payment required for core functionality.

**Includes:**
- Fully free tools (no paid tiers)
- Open source software (free to use)
- Community editions (free version is full-featured)
- Academic/non-profit free offerings

**Excludes:**
- Tools with free trials but paid core features (use Freemium)
- Tools requiring payment after trial period (use Freemium or Paid Subscription)

**Key Characteristic:** User gets full value without ever paying.

**User Query Match:**
- "free AI tools"
- "no cost tools"
- "open source alternatives"

**Examples:**
- Stable Diffusion (open source image generation)
- Llama models (open source LLMs)
- GIMP (free image editing)

**Consolidates from:**
- Free (9,006)
- Open Source (2,223)
- Free/Premium (2 - if truly free, else Freemium)
- Free/Paid (1 - if truly free, else Freemium)

**Total:** ~11,229 tools (20.5%)

---

### **2. FREEMIUM**
**Definition:** Tool has a free tier with limited features, and paid tiers with additional functionality.

**Includes:**
- Free tier with usage limits (e.g., 10 queries/month)
- Free tier with feature restrictions (e.g., watermarks, lower resolution)
- Free trials with time limits (converts to paid)
- "Forever free" tier with paid upgrades

**Excludes:**
- Completely free tools (use Free)
- Tools with no free tier at all (use Paid Subscription)

**Key Characteristic:** User can start for free, but needs to pay for full value.

**Pricing Structure Decision Rules:**
- Has free tier + paid tier → **Freemium**
- Free trial only (no free tier after trial) → **Paid Subscription**
- Free tier is just a demo → **Freemium**

**User Query Match:**
- "freemium AI tools"
- "free tier available"
- "try before you buy"

**Examples:**
- ChatGPT (free tier, Plus tier)
- Grammarly (free with limits, premium features)
- Canva (free tier with paid templates)

**Consolidates from:**
- Freemium (9,397)
- Free Trial (3,016 - if followed by paid tier)
- FreemiumFree Trial (5)
- FreeFreemium (1)
- Free TrialFreemium (5)

**Total:** ~12,413 tools (22.6%)

---

### **3. PAID SUBSCRIPTION**
**Definition:** Recurring payment model (monthly, annually) for ongoing access to the tool.

**Includes:**
- Monthly subscriptions
- Annual subscriptions
- Tiered subscription plans (Basic, Pro, Enterprise with different features)
- Subscriptions with usage limits per tier

**Excludes:**
- Pay-as-you-go with no recurring fee (use Usage-Based)
- One-time lifetime payment (use One-Time Purchase)
- Enterprise-only pricing (use Enterprise)

**Key Characteristic:** User pays regularly to maintain access.

**User Query Match:**
- "subscription-based AI tools"
- "monthly pricing"
- "SaaS tools"

**Examples:**
- Adobe Creative Cloud (monthly/annual)
- Jasper AI (monthly subscription)
- Notion (monthly/annual tiers)

**Consolidates from:**
- Subscription (8,227)
- Tiered Pricing (2,256 - when tiers are subscription-based)
- Paid (799 - when it's a subscription)
- Free TrialPaid (5 - maps to subscription after trial)
- PaidFree Trial (1)

**Total:** ~11,288 tools (20.6%)

---

### **4. USAGE-BASED**
**Definition:** Pay only for what you use, typically per API call, per minute, per generation, or per credit.

**Includes:**
- Pay-per-API-call pricing
- Credit-based systems (buy credits, use them)
- Pay-per-generation (e.g., per image, per video)
- Consumption-based pricing (compute time, storage)

**Excludes:**
- Subscriptions with usage limits (use Paid Subscription)
- One-time purchases (use One-Time Purchase)

**Key Characteristic:** Cost scales directly with usage. No usage = no cost (after potential base fee).

**User Query Match:**
- "pay as you go AI"
- "pay per use"
- "credit-based pricing"
- "API pricing"

**Examples:**
- OpenAI API (pay per token)
- Replicate (pay per second of compute)
- ElevenLabs (pay per character/minute)

**Consolidates from:**
- Pay-per-use (8,108)
- Usage-based (2,368)

**Total:** ~10,476 tools (19.1%)

---

### **5. ENTERPRISE**
**Definition:** Custom pricing negotiated based on organization needs, typically for large teams or high-volume usage.

**Includes:**
- "Contact sales" pricing
- Custom contracts negotiated per organization
- Volume-based enterprise deals
- White-label or on-premise deployments

**Excludes:**
- Standard tiered subscriptions (even if called "Enterprise tier" - use Paid Subscription)
- Self-service enterprise plans with listed pricing (use Paid Subscription)

**Key Characteristic:** No public pricing; requires sales conversation.

**Decision Rule:**
- If price is listed on website → **Paid Subscription** (even if expensive)
- If says "Contact sales" or "Custom pricing" → **Enterprise**

**User Query Match:**
- "enterprise AI tools"
- "custom pricing"
- "contact sales"

**Examples:**
- Salesforce Einstein (custom enterprise deals)
- Databricks (custom contracts)
- Custom GPT deployments

**Consolidates from:**
- Enterprise (2,274)
- Custom Pricing (2,276)
- Contact for Pricing (1,095)
- FreemiumContact for Pricing (1)
- Open Source/Enterprise (1 - if commercial version is enterprise)

**Total:** ~5,645 tools (10.3%)

---

### **6. ONE-TIME PURCHASE**
**Definition:** Single upfront payment for lifetime access or perpetual license.

**Includes:**
- Lifetime deals
- Perpetual software licenses
- One-time payment for lifetime access
- Pay-once for specific version (no ongoing updates)

**Excludes:**
- Subscriptions (use Paid Subscription)
- Hardware with included software (see Special Cases below)

**Key Characteristic:** Pay once, use forever (for that version).

**User Query Match:**
- "lifetime deal"
- "one-time payment"
- "buy once"
- "perpetual license"

**Examples:**
- AppSumo lifetime deals
- Affinity Designer (one-time purchase per version)
- Some indie AI tools with lifetime pricing

**Consolidates from:**
- One-time Purchase (2,375)
- Hardware Included (1 - if software is one-time)

**Total:** ~2,376 tools (4.3%)

---

## Special Cases & Edge Cases

### **Free Trial + Paid Subscription**
**Problem:** Tool offers 14-day free trial, then requires subscription.

**Solution:** Categorize as **Freemium** if free tier continues after trial, **Paid Subscription** if trial expires and no free tier remains.

**Examples:**
- ChatGPT Plus trial → **Freemium** (free tier exists)
- Adobe free trial → **Paid Subscription** (no free tier after trial)

---

### **Hybrid Models**
**Problem:** Tool has both subscription AND usage-based pricing (e.g., base subscription + overage fees).

**Solution:** Categorize by PRIMARY pricing structure:
- If base subscription is required → **Paid Subscription**
- If subscription is optional and can use pure usage → **Usage-Based**

**Example:**
- OpenAI API (can use without subscription) → **Usage-Based**
- Jasper (subscription required, overages possible) → **Paid Subscription**

---

### **Open Source with Commercial Version**
**Problem:** Tool is open source (free) but also has paid enterprise version.

**Solution:**
- If self-hostable free version has full features → **Free**
- If free version is limited, paid adds features → **Freemium**
- If paid version only for support/hosting → **Free**
- If commercial version is enterprise-only → **Free** (list enterprise as add-on)

**Examples:**
- Llama (fully open source) → **Free**
- GitLab (free tier + paid features) → **Freemium**
- Red Hat Enterprise Linux (free + paid support) → **Free**

---

### **Tiered Pricing**
**Problem:** "Tiered Pricing" could mean subscription tiers OR usage tiers.

**Solution:**
- If tiers are subscription-based (Basic/Pro/Enterprise) → **Paid Subscription**
- If tiers are usage-based (pay more per unit at higher volumes) → **Usage-Based**

---

### **Unknown Pricing**
**Problem:** 1,422 tools marked "Unknown" - how to categorize?

**Solution:** Research is required. Temporary handling:
1. Check tool website for current pricing
2. If website unavailable, check:
   - Category typical pricing (e.g., Code Assistants often Freemium)
   - Company size (startups often Freemium, enterprises often Enterprise)
3. If still unknown, mark as **Freemium** (most common model, ~22.6%)
4. Flag for manual review

**DO NOT leave as "Unknown" in production.**

---

### **Non-Pricing Values**
**Problem:** 40 tools have values like "Active deal", "Service", "Preview", "Hardware/Software"

**Solution - Mapping Rules:**

| Current Value | Meaning | Map To |
|---------------|---------|--------|
| Active deal | Temporary promotion | Check actual pricing model |
| Service | Product type, not pricing | Research actual pricing |
| Preview | Beta/early access status | Check if free beta or paid |
| Hardware Included | Bundling detail | **One-Time Purchase** or **Paid Subscription** |
| Hardware/Software | Product type | Research actual pricing |

**Action:** Manual review of all 40 tools to assign correct pricing model.

---

### **Concatenated Values**
**Problem:** "FreeFreemiumPaid", "Free TrialPaid", etc.

**Solution - Decision Tree:**
```
Is there a free tier that continues forever?
├─ YES → Freemium
└─ NO  → Is it subscription or usage-based?
         ├─ Subscription → Paid Subscription
         └─ Usage → Usage-Based
```

**Examples:**
- "FreeFreemiumPaid" → **Freemium** (has free tier)
- "Free TrialPaid" → **Paid Subscription** (trial expires, then paid)
- "FreemiumFree Trial" → **Freemium** (free tier exists)

---

## Validation Rules

### **Automated Checks**
Before accepting a pricing model assignment:

**Length check:** Value must be ≤30 characters (prevents concatenation)  
**Whitelist check:** Value must be one of 6 canonical models  
**No special characters:** No slashes (/), no spaces in middle of word  
**Case sensitivity:** Enforce exact capitalization (e.g., "Free" not "free")

### **Business Logic Checks**

**If Pricing Model = Free:**
- Cannot have "trial" in description
- Cannot have paid tiers mentioned

**If Pricing Model = Freemium:**
- Must mention free tier in description
- Must mention paid tier or upgrade

**If Pricing Model = Enterprise:**
- Website should say "Contact sales" or "Custom pricing"
- Should NOT have visible pricing on website

---

## User Query Handling (LLM Guidance)

### **Query: "Show me free AI tools"**
**Match to:** FREE only  
**Exclude:** Freemium (these aren't truly free)

### **Query: "What are freemium AI tools?"**
**Match to:** FREEMIUM only

### **Query: "AI tools I can try for free"**
**Match to:** FREE + FREEMIUM  
**Sort:** Free first, then Freemium

### **Query: "Cheapest AI tools" or "affordable AI"**
**Match to:** FREE, FREEMIUM, USAGE-BASED (usage can be cheap for low-volume)  
**Exclude:** Paid Subscription (ongoing commitment), Enterprise (expensive)

### **Query: "AI tools for enterprise"**
**Match to:** ENTERPRISE + PAID SUBSCRIPTION (enterprise tiers)  
**Sort:** Enterprise first

### **Query: "Pay as you go AI"**
**Match to:** USAGE-BASED only

### **Query: "No subscription AI tools"**
**Match to:** FREE, USAGE-BASED, ONE-TIME PURCHASE  
**Exclude:** Paid Subscription, Freemium (has subscription tiers)

---

## Migration Mapping

### **Automatic Mappings (97% of tools)**

| Old Value | New Value | Tools | Logic |
|-----------|-----------|-------|-------|
| Free | **FREE** | 9,006 | Direct mapping |
| Open Source | **FREE** | 2,223 | Open source = free to use |
| Freemium | **FREEMIUM** | 9,397 | Direct mapping |
| Free Trial | **FREEMIUM** | 3,016 | Trial implies paid conversion |
| Subscription | **PAID SUBSCRIPTION** | 8,227 | Direct mapping |
| Pay-per-use | **USAGE-BASED** | 8,108 | Direct mapping |
| Usage-based | **USAGE-BASED** | 2,368 | Direct mapping |
| Tiered Pricing | **PAID SUBSCRIPTION** | 2,256 | Usually subscription tiers |
| Paid | **PAID SUBSCRIPTION** | 799 | Default to subscription |
| One-time Purchase | **ONE-TIME PURCHASE** | 2,375 | Direct mapping |
| Enterprise | **ENTERPRISE** | 2,274 | Direct mapping |
| Custom Pricing | **ENTERPRISE** | 2,276 | Custom = enterprise deals |
| Contact for Pricing | **ENTERPRISE** | 1,095 | Contact sales = enterprise |

**Subtotal:** 53,424 tools (97.3%) - automatic

---

### **Manual Review Required (3% of tools)**

**Category 1: Concatenated Values (62 tools)**
- Free TrialPaid (5)
- Free TrialFreemium (5)
- FreemiumFree Trial (5)
- Open Source/Commercial (4)
- Free/Premium (2)
- Free/Paid (1)
- Open Source/Enterprise (1)
- FreeFreemium (1)
- Hardware/Software (1)
- PaidFree Trial (1)
- FreemiumContact for Pricing (1)
- FreeFreemiumPaid (1)
- Hardware Included (1)

**Action:** Manually research each tool and assign appropriate model.

---

**Category 2: Non-Pricing Values (40 tools)**
- Active deal (26)
- Service (12)
- Preview (1)
- Hardware Included (1)

**Action:** Research actual pricing model.

---

**Category 3: Unknown (1,422 tools)**
- Unknown (1,422)

**Action:** 
1. Scrape websites for current pricing
2. Use category-based defaults as fallback
3. Flag for user-reported corrections

---

## Category-Based Pricing Defaults

When pricing is unknown, use these category defaults (based on Cell 32 analysis):

| Category | Most Common Model | Second Most Common |
|----------|-------------------|-------------------|
| Education | Free (20.1%) | Freemium (19.2%) |
| Marketing | Freemium (19.5%) | Free (18.5%) |
| Finance | Usage-Based (18.9%) | Freemium (18.7%) |
| Health | Freemium (24.9%) | Free (24.6%) |
| Code Assistant | Free (26.3%) | Freemium (24.3%) |
| Data Analysis | Paid Subscription (26.4%) | Freemium (25.4%) |
| Productivity | Freemium (26.6%) | Free (24.8%) |
| Audio Generation | Free (26.0%) | Usage-Based (25.1%) |
| Image Generation | Usage-Based (26.3%) | Freemium (24.9%) |
| Translation | Free (25.4%) | Usage-Based (25.1%) |

**Default Strategy:**
If pricing unknown and category matches above → use category's most common model  
If category not listed → use **Freemium** (platform-wide most common at 22.6%)

---

## Success Metrics

### **Quantitative**
- Pricing models: 30 → 6 (80% reduction) 
- Unknown/invalid: 1,484 → 0 (100% resolution)
- Concatenated values: 62 → 0 (100% fixed)
- Non-pricing values: 40 → 0 (100% corrected)

### **Qualitative**
- Users can filter by budget with confidence
- LLM matches pricing queries with >95% accuracy
- No user confusion about pricing categories
- Comparison between tools is clear

### **User Experience**
- "Show me free tools" returns only truly free tools (not trials)
- "Affordable options" includes Free, Freemium, and low-usage Usage-Based
- "Enterprise solutions" surfaces Contact Sales tools

---

## Governance

**Pricing Model Owner:** Product Team  
**Review Frequency:** Quarterly (pricing models evolve)  
**Update Trigger:** 
- New pricing model appears in >100 tools
- User feedback indicates confusion
- Industry standard changes

**Change Process:**
1. Proposal with business case
2. User research (do users understand the model?)
3. LLM accuracy testing
4. Product approval
5. Deployment with monitoring

---

## Implementation Checklist

### **Phase 1: Data Cleanup (Week 1)**
- [ ] Map 53,424 tools using automatic rules
- [ ] Manually review 62 concatenated values
- [ ] Research 40 non-pricing values
- [ ] Flag 1,422 unknown for web scraping

### **Phase 2: Validation (Week 2)**
- [ ] Run validation rules on all assignments
- [ ] Spot-check 100 random tools manually
- [ ] Test LLM query matching accuracy
- [ ] User testing with pricing filters

### **Phase 3: Deployment (Week 3)**
- [ ] Update database schema
- [ ] Deploy to production
- [ ] Monitor user filter usage
- [ ] Track query → result relevance

### **Phase 4: Ongoing (Monthly)**
- [ ] Review newly added tools
- [ ] Update unknown pricing via research
- [ ] Collect user feedback
- [ ] Adjust mappings as needed

---

## Appendix A: Complete Pricing Model List

**Canonical 6 Pricing Models:**

1. **FREE** - Completely free to use, no payment required
2. **FREEMIUM** - Free tier + paid tier for more features/usage
3. **PAID SUBSCRIPTION** - Recurring monthly/annual payment
4. **USAGE-BASED** - Pay per use (API calls, generations, time)
5. **ENTERPRISE** - Custom pricing via sales team
6. **ONE-TIME PURCHASE** - Single payment for lifetime access

---

## Appendix B: Rejected Pricing Models

These were considered but rejected:

| Rejected Model | Reason |
|----------------|--------|
| "Tiered Pricing" | Ambiguous - tiers of subscription OR usage? Merge into Paid Subscription |
| "Pay-per-use" vs "Usage-based" | Same thing, use "Usage-Based" |
| "Contact for Pricing" | Same as Enterprise, use "Enterprise" |
| "Custom Pricing" | Same as Enterprise, use "Enterprise" |
| "Free Trial" | Not a pricing model, it's a feature of Freemium or Paid Subscription |
| "Open Source" | License type, not pricing - map to "Free" |
| "Active deal" | Temporary promotion, not a pricing model |
| "Unknown" | Not a model, requires research |

---

## Document History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | Feb 2026 | Initial pricing taxonomy | Data Audit Team |

---

**End of Document**