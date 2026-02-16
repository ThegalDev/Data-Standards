# Data Cleaning & Mapping Rules
**Document Version:** 1.0  
**Date:** February 2026  
**Purpose:** Implementation playbook for cleaning NeuraGuide AI tools dataset

---

## Executive Summary

**Cleanup Scope:**
- **Category:** 200 → 30 (170 to consolidate)
- **Pricing Model:** 30 → 6 (24 to consolidate)
- **Target Users:** 219 → 12 (207 to consolidate)
- **Data Quality Fixes:** ~13,834 tools with placeholders/unknowns

**Current Validation Failure Rate:**
- Category: 100% need remapping (all 200 invalid)
- Pricing: 62.3% invalid (34,233 tools)
- Target Users: 88.3% invalid (48,470 tools)
- Launch Year format: 88.2% invalid (48,460 tools with decimals)

**Impact:** Transform 54,910 tools from messy → production-ready data

---

## Implementation Priorities

### **Priority 0: Critical (Week 1) - BLOCKS CHATBOT**
Must fix before chatbot can make recommendations:
1. Target Users: Eliminate "General" (28,253 tools - 51.5%)
2. Category: Map all 200 → 30 (enables category filtering)
3. Pricing: Map all 30 → 6 (enables budget filtering)

### **Priority 1: High (Week 2) - IMPROVES QUALITY**
Significantly improves user experience:
4. Launch Year: Fix format (remove decimals, validate range)
5. Case normalization (Content Creators vs content creators)
6. Compound values (split "Researchers and students")

### **Priority 2: Medium (Week 3-4) - ENRICHMENT**
Enhances data but not blocking:
7. Key Features: Replace "See website" (6,207 tools)
8. Company: Research "Unknown" (6,207 tools)
9. Description: Fix templates (1,420 tools)

### **Priority 3: Low (Ongoing) - MAINTENANCE**
10. Duplicate tool names (115 duplicates)
11. Multi-category tools refinement (26,522 tools)
12. Rating system implementation

---

## SECTION 1: Category Mapping Rules

### **Mapping Table: 200 → 30 Categories**

#### **1.1 Content Creation Group (Consolidate 25 → 4)**

| Old Categories | New Category | Logic |
|----------------|--------------|-------|
| Image Generation, Image Generator, Image Editing, Text To Image, Portrait Generators | **Image Creation & Editing** | All image-related output |
| Video Generation, Video & Media, Video Editing, Video Generators, Video Enhancer, Text To Video, Video, Audio/Video Editing | **Video Creation & Editing** | All video-related output |
| Audio Generation, Music & Audio, Audio Editing, Music, Music AI, Audio/Music AI, Audio AI | **Audio & Music Creation** | All audio/music output |
| Text Generation, Content Creation, Other text generators | **Writing & Text Generation** | All text/writing output |

**Tools Affected:** ~8,000

---

#### **1.2 Business & Productivity Group (Consolidate 15 → 6)**

| Old Categories | New Category | Logic |
|----------------|--------------|-------|
| Marketing, Sales | **Marketing & Advertising** | Marketing function |
| Finance, Finance AI | **Finance & Accounting** | Financial function |
| Productivity, Automation | **Productivity & Workflow** | General productivity |
| Customer Service, CRM | **Customer Service & Support** | Customer-facing |
| E-commerce, E-commerce AI | **E-commerce & Retail** | Online selling |
| Business (partial), Sales (partial) | **Sales & CRM** | Sales-specific tools |

**Tools Affected:** ~11,000

---

#### **1.3 Development & Engineering Group (Consolidate 40 → 3)**

| Old Categories | New Category | Logic |
|----------------|--------------|-------|
| Code Assistant, Code/LLM, ‍ Code & Developer Tools | **Code Assistance & Generation** | Code writing/review |
| AI Engineering, 🏗 AI Engineering, AI Platforms, AI Agents, Machine Learning, Deep Learning, General-Purpose Machine Learning | **AI Development & Engineering** | Building AI/ML systems |
| Development Tools, ☁️ Cloud Services, DevOps, Low-code/no-code, Programming languages (Python, Java, R, etc.) | **Developer Tools & Infrastructure** | Dev environment/tools |

**Special Rule:** Programming language categories (Python, R, Java, Perl, etc.) → Map based on TOOL PURPOSE:
- If tool is code assistant → **Code Assistance & Generation**
- If tool is ML library/framework → **AI Development & Engineering**
- If tool is general dev tool → **Developer Tools & Infrastructure**

**Tools Affected:** ~6,000

---

#### **1.4 Data & Analytics Group (Consolidate 10 → 2)**

| Old Categories | New Category | Logic |
|----------------|--------------|-------|
| Data Analysis, Data Analytics, Data Analysis / Data Visualization, Business Intelligence, Analytics, Data Science | **Data Analysis & Visualization** | Data insights/BI |
| Research, Research & Analysis | **Research & Knowledge Management** | Research/knowledge work |

**Tools Affected:** ~4,000

---

#### **1.5 AI Assistants Group (Consolidate 15 → 2)**

| Old Categories | New Category | Logic |
|----------------|--------------|-------|
| AI Assistants, AI Chatbots, Conversational AI, Chat & Communication (AI-focused portion) | **AI Chatbots & Conversational AI** | General AI assistants |
| Email Assistants, Meeting AI, Meeting assistants | **AI Productivity Assistants** | Task-specific AI helpers |

**Tools Affected:** ~2,000

---

#### **1.6 Industry Solutions Group (Consolidate 20 → 5)**

| Old Categories | New Category | Logic |
|----------------|--------------|-------|
| Health, Healthcare, Healthcare AI, Biotech | **Healthcare & Medical** | Healthcare industry |
| Education, Education AI | **Education & Learning** | Education industry |
| Legal, Legal AI | **Legal & Compliance** | Legal industry |
| Real Estate | **Real Estate & Property** | Real estate industry |
| Agriculture AI | **Agriculture & Environmental** | Agriculture/environment |

**Tools Affected:** ~9,000

---

#### **1.7 Communication & Media Group (Consolidate 5 → 2)**

| Old Categories | New Category | Logic |
|----------------|--------------|-------|
| Chat & Communication (non-AI portion), Collaboration, Email | **Communication & Collaboration** | Team communication |
| Translation | **Language & Translation** | Translation/localization |

**Tools Affected:** ~3,000

---

#### **1.8 Specialized Tools Group (Consolidate 50 → 6)**

| Old Categories | New Category | Logic |
|----------------|--------------|-------|
| Design, Graphic Design, Creative Tools | **Design & Graphics** | Visual design |
| 3D, 3D/AR, Animation | **3D & Visualization** | 3D/AR/VR |
| Gaming, Gaming AI, Entertainment AI | **Gaming & Entertainment** | Games/interactive |
| Security, Cybersecurity AI, AI Detection | **Security & Privacy** | Security/privacy |
| Automotive AI, Transportation AI, Navigation AI, Delivery AI, Logistics AI | **Automotive & Transportation** | Transport/logistics |
| Text To Speech, Text-to-speech, Speech-to-text, Voice AI | **Voice & Speech** | Voice/speech tech |

**Tools Affected:** ~8,000

---

### **Category Decision Tree**

```
1. What is the PRIMARY output?
   
   Image/photo → Image Creation & Editing
   Video → Video Creation & Editing
   Audio/music → Audio & Music Creation
   Text/writing → Writing & Text Generation

2. If not content creation, what is the PRIMARY function?
   
   Marketing/sales → Marketing & Advertising OR Sales & CRM
   Money management → Finance & Accounting
   General business tasks → Productivity & Workflow
   Customer support → Customer Service & Support
   Online selling → E-commerce & Retail

3. If not business, is it development?
   
   Writing code → Code Assistance & Generation
   Building AI models → AI Development & Engineering
   Dev environment → Developer Tools & Infrastructure

4. If not development, is it data work?
   
   Analyzing data → Data Analysis & Visualization
   Research/knowledge → Research & Knowledge Management

5. If not data, is it AI assistant?
   
   General chatbot → AI Chatbots & Conversational AI
   Task-specific assistant → AI Productivity Assistants

6. If not AI assistant, is it industry-specific?
   
   Healthcare → Healthcare & Medical
   Education → Education & Learning
   Legal → Legal & Compliance
   Real estate → Real Estate & Property
   Agriculture → Agriculture & Environmental

7. If not industry, what specialty?
   
   Visual design → Design & Graphics
   3D/AR/VR → 3D & Visualization
   Gaming → Gaming & Entertainment
   Security → Security & Privacy
   Transportation → Automotive & Transportation
   Voice/speech → Voice & Speech
   Team communication → Communication & Collaboration
   Translation → Language & Translation
```

---

### **Category Edge Cases**

#### **Case 1: Multi-Category Tools (26,522 tools where Category ≠ Primary Function)**

**Example:** Tool with Category="Healthcare" and Primary Function="Data Analysis"

**Decision Rule:**
1. Check tool description for PRIMARY use case
2. If 80% healthcare-specific → **Healthcare & Medical**
3. If general data analysis that happens to be used in healthcare → **Data Analysis & Visualization**

**Test:** Would non-healthcare users find this tool useful?
- YES → Use capability category (Data Analysis)
- NO → Use industry category (Healthcare)

---

#### **Case 2: Programming Languages as Categories**

Current: Python (381), R (83), Java, Perl, etc. as separate categories

**Mapping Logic:**
```
Tool in "Python" category:
  └─ Read description
      ├─ Code completion/generation → Code Assistance & Generation
      ├─ ML library/framework → AI Development & Engineering
      └─ General dev tool → Developer Tools & Infrastructure
```

**Examples:**
- Python code formatter → **Code Assistance & Generation**
- PyTorch (ML library) → **AI Development & Engineering**
- Python package manager → **Developer Tools & Infrastructure**

---

#### **Case 3: Borderline Tools**

**Canva:** Design tool + AI image generation + templates

**Analysis:**
- Primary use: Graphic design (80%)
- Secondary: AI image generation (20%)

**Assignment:** **Design & Graphics** (primary use case)

**Descript:** Transcription + video editing + voice cloning

**Analysis:**
- Primary use: Video editing (60%)
- Secondary: Transcription (40%)

**Assignment:** **Video Creation & Editing** (primary use case)

---

## SECTION 2: Pricing Model Mapping Rules

### **Mapping Table: 30 → 6 Models**

| Old Value | New Model | Tools | Logic |
|-----------|-----------|-------|-------|
| Free | **Free** | 9,006 | Direct mapping |
| Open Source | **Free** | 2,223 | Open source = free to use |
| Freemium | **Freemium** | 9,397 | Direct mapping |
| Free Trial | **Freemium** | 3,016 | Trial → paid means freemium model |
| Subscription | **Paid Subscription** | 8,227 | Direct mapping |
| Pay-per-use | **Usage-Based** | 8,108 | Direct mapping |
| Usage-based | **Usage-Based** | 2,368 | Direct mapping |
| Tiered Pricing | **Paid Subscription** | 2,256 | Subscription tiers |
| Paid | **Paid Subscription** | 799 | Default to subscription |
| One-time Purchase | **One-Time Purchase** | 2,375 | Direct mapping |
| Enterprise | **Enterprise** | 2,274 | Direct mapping |
| Custom Pricing | **Enterprise** | 2,276 | Custom = enterprise |
| Contact for Pricing | **Enterprise** | 1,095 | Contact sales = enterprise |
| Unknown | **RESEARCH NEEDED** | 1,422 | Check website, use category default |
| Active deal | **RESEARCH NEEDED** | 26 | Not a pricing model |
| Service | **RESEARCH NEEDED** | 12 | Not a pricing model |
| Preview | **RESEARCH NEEDED** | 1 | Beta status, check pricing |
| Hardware Included | **One-Time Purchase** | 1 | Hardware bundle = one-time |
| Hardware/Software | **RESEARCH NEEDED** | 1 | Check actual pricing |

**Concatenated Values (Handle Specially):**

| Concatenated Value | Decision Logic | Likely Mapping |
|--------------------|----------------|----------------|
| Free TrialPaid | Has trial → paid | **Freemium** |
| Free TrialFreemium | Has free tier | **Freemium** |
| FreemiumFree Trial | Has free tier | **Freemium** |
| Open Source/Commercial | Has free version | **Free** |
| Free/Premium | Has free tier | **Freemium** |
| Free/Paid | Check if free tier continues | **Freemium** or **Paid Subscription** |
| FreeFreemium | Has free tier | **Freemium** |
| FreeFreemiumPaid | Has free tier | **Freemium** |
| Open Source/Enterprise | Free with enterprise option | **Free** |
| PaidFree Trial | Trial then paid | **Paid Subscription** |
| FreemiumContact for Pricing | Has free tier + enterprise | **Freemium** |

---

### **Pricing Decision Tree**

```
1. Is there a completely free version (no time limit, full features)?
   YES → FREE

2. Is there a free tier with limited features/usage?
   YES → FREEMIUM

3. Is it pay-per-use (API calls, generations, compute time)?
   YES → USAGE-BASED

4. Is it recurring payment (monthly/annual subscription)?
   YES → PAID SUBSCRIPTION

5. Does it require contacting sales for pricing?
   YES → ENTERPRISE

6. Is it one-time payment for lifetime access?
   YES → ONE-TIME PURCHASE
```

---

### **Pricing Edge Cases**

#### **Case 1: Free Trial Only (No Free Tier After)**

**Example:** Adobe offers 7-day free trial, then requires subscription

**Decision:** **Paid Subscription** (not Freemium, as no free tier after trial)

---

#### **Case 2: Open Source + Commercial**

**Example:** GitLab - free self-hosted version + paid SaaS

**Decision:** 
- If free version has full features → **Free**
- If free version is limited → **Freemium**

---

#### **Case 3: Hybrid (Subscription + Overage)**

**Example:** Tool has $50/month subscription + $0.01 per extra API call

**Decision:** **Paid Subscription** (subscription is required)

---

#### **Case 4: Unknown Pricing**

**Strategy:** Research in this order:
1. Check tool website
2. Check Product Hunt, G2, Capterra
3. Use category default (from Task 6 analysis)
4. If still unknown → **Freemium** (most common model)

---

## SECTION 3: Target Users Mapping Rules

### **Mapping Table: 219 → 12 Personas**

#### **3.1 Direct Mappings (15 large values)**

| Old Value | New Persona | Tools | Notes |
|-----------|-------------|-------|-------|
| Developers | **Developers** | 3,125 | Direct mapping |
| Engineers | **Developers** | 1,558 | Engineers = developers |
| Data Scientists | **Data Scientists & Analysts** | 1,690 | Direct mapping |
| Analysts | **Data Scientists & Analysts** | 1,657 | Merge with data scientists |
| Content Creators | **Content Creators** | 1,656 | Normalize case |
| Content creators | **Content Creators** | 4 | Case normalization |
| Designers | **Designers** | 1,659 | Direct mapping |
| Marketers | **Marketing & Sales Professionals** | 1,693 | Merge marketers + sales |
| Sales Teams | **Marketing & Sales Professionals** | 1,622 | Merge marketers + sales |
| Business Owners | **Business Owners & Entrepreneurs** | 1,725 | Merge with startups + freelancers |
| Startups | **Business Owners & Entrepreneurs** | 1,756 | Merge with business owners |
| Freelancers | **Business Owners & Entrepreneurs** | 1,668 | Merge with business owners |
| Enterprise Teams | **Enterprise Teams & Decision Makers** | 1,607 | Direct mapping |
| Researchers | **Researchers & Academics** | 1,731 | Direct mapping |
| Students | **Students & Learners** | 1,641 | Direct mapping |
| Project Managers | **Project & Product Managers** | 1,630 | Direct mapping |

**Subtotal:** 26,657 tools (48.5%) - Automatic

---

#### **3.2 Case Normalization**

| Variations | Canonical Form | Total Tools |
|------------|----------------|-------------|
| Data Scientists, Data scientists | **Data Scientists & Analysts** | 1,691 |
| Content Creators, Content creators | **Content Creators** | 1,660 |

**Rule:** Convert all to proper case (First Letter Capitalized)

---

#### **3.3 Compound Values (110 tools)**

**Decision Logic:** Split compound values, choose PRIMARY user (80% rule)

| Compound Value | Split Into | Choose | Logic |
|----------------|------------|--------|-------|
| Researchers and students | Researchers / Students | Check tool context | Academic research tool → Researchers |
| Developers and enterprises | Developers / Enterprise | Usually Developers | Most dev tools → Developers |
| Designers and marketers | Designers / Marketers | Check PRIMARY function | Design tool → Designers |
| Writers and professionals | Writers / Professionals | **Content Creators** | Writing = content |
| 3D artists and developers | Artists / Developers | Usually Designers | 3D art → Designers |

**Process:**
1. Check tool description
2. Determine 80% use case
3. Assign primary persona
4. Document decision

---

#### **3.4 Tiny Values (<10 tools each) - 203 values**

**Examples and Mappings:**

| Tiny Value | Mapping | Logic |
|------------|---------|-------|
| Software developers | **Developers** | Developers |
| Game developers | **Developers** | Developers |
| AWS developers | **Developers** | Developers |
| Data analysts | **Data Scientists & Analysts** | Analysts |
| Business analysts | **Data Scientists & Analysts** | Analysts |
| Digital artists | **Designers** | Designers |
| Creative professionals | **Designers** | Designers |
| Game studios | **Developers** or **Designers** | Check if code or art focus |
| Legal professionals | **Business Owners & Entrepreneurs** (small firms) or **Enterprise Teams** (large) | Check firm size |
| Security professionals | **Enterprise Teams** (corporate) or **Developers** (security dev) | Check role |
| Farmers | **Business Owners & Entrepreneurs** | Ag business owners |
| Language learners | **Students & Learners** | Learning context |

**Default Strategy for Unclear Cases:**
- Professional role → Map to closest functional persona
- Industry role → Map to **Business Owners** (small) or **Enterprise** (large)
- Learning context → **Students & Learners**

---

#### **3.5 "General" Reassignment (28,253 tools - 51.5%)**

**Category-Based Reassignment Table:**

| Category (100% General) | Assign To | Tools | Logic |
|-------------------------|-----------|-------|-------|
| Health | **Healthcare & Medical Professionals** | 1,843 | Healthcare tools → healthcare users |
| Data Analysis | **Data Scientists & Analysts** | 1,724 | Data analysis → analysts |
| Audio Generation | **Content Creators** | 1,695 | Audio content → creators |
| Code Assistant | **Developers** | 1,761 | Code tools → developers |
| Image Generation | **Content Creators** (70%) / **Designers** (30%) | 1,675 | Split based on tool purpose |
| Translation | **General Consumers & Individuals** | 1,667 | Personal translation use |
| Productivity | **Business Owners & Entrepreneurs** | 1,696 | General business productivity |

**For Partially General Categories:**

| Category | General % | Strategy |
|----------|-----------|----------|
| Education | 63.4% | Review tool → **Students** (learning) or **Researchers** (academic) |
| Marketing | 66.4% | Default → **Marketing & Sales Professionals** |
| Finance | 63.0% | Default → **Business Owners & Entrepreneurs** |

**Process:**
1. Apply category-based defaults (bulk assignment)
2. Manual review of 10% sample per category
3. Adjust based on tool descriptions
4. Validate with spot checks

---

### **Target Users Decision Tree**

```
1. Is user a developer/engineer who writes code?
   YES → Developers

2. Is user analyzing data, building models, or BI?
   YES → Data Scientists & Analysts

3. Is user creating digital content (video, audio, blog, social)?
   YES → Content Creators

4. Is user designing visual elements (UI, graphics, brands)?
   YES → Designers

5. Is user in marketing or sales?
   YES → Marketing & Sales Professionals

6. Is user running a business (owner, founder, freelancer)?
   YES → Business Owners & Entrepreneurs

7. Is user in large enterprise (IT, security, corporate)?
   YES → Enterprise Teams & Decision Makers

8. Is user managing projects/products?
   YES → Project & Product Managers

9. Is user in academia doing research?
   YES → Researchers & Academics

10. Is user learning/studying?
    YES → Students & Learners

11. Is user in healthcare/medical field?
    YES → Healthcare & Medical Professionals

12. Is user general public for personal use?
    YES → General Consumers & Individuals
```

---

## SECTION 4: Other Data Quality Fixes

### **4.1 Launch Year Cleanup**

**Problem:** 48,460 tools (88.2%) have decimal format (2023.0)

**Fix:**
```python
# Remove decimals
df['Launch Year'] = df['Launch Year'].str.replace('.0', '', regex=False)

# Convert to integer
df['Launch Year'] = pd.to_numeric(df['Launch Year'], errors='coerce')

# Validate range
df.loc[(df['Launch Year'] < 2000) | (df['Launch Year'] > 2026), 'Launch Year'] = None
```

**Validation:**
- Must be integer (no decimals)
- Range: 2000-2026
- Values like "1907" → NULL (invalid)
- "Unknown" → NULL

---

### **4.2 Key Features Cleanup**

**Problem:** 6,207 tools have "See website"

**Strategy:**

**Phase 1: Template Replacement (Immediate)**
Replace "See website" with category-based template:

| Category | Template Features |
|----------|------------------|
| Image Creation | "AI-powered image generation, Multiple styles, High-resolution output, Editing tools" |
| Video Creation | "AI video generation, Scene editing, Effects library, Export options" |
| Audio Creation | "AI audio generation, Sound editing, Multiple formats, Quality enhancement" |
| Code Assistance | "Code completion, Bug detection, Multi-language support, Real-time suggestions" |
| Data Analysis | "Data visualization, Statistical analysis, Report generation, Dashboard creation" |

**Phase 2: Web Scraping (Ongoing)**
1. Scrape tool websites for actual features
2. Replace templates with real features
3. Human review of 10% sample

---

### **4.3 Company "Unknown" Cleanup**

**Problem:** 6,207 tools have "Unknown" company

**Strategy:**

**Priority 1: Large/Popular Tools**
- Manually research top 100 most-used tools
- Add company information

**Priority 2: Automated Research**
- Scrape websites for "About" or "Company" info
- Parse from domain (e.g., openai.com → OpenAI)
- Use web search APIs

**Priority 3: Keep as Unknown**
- Small/indie tools where company is truly unknown
- Mark as "Independent Developer" if solo project

---

### **4.4 Template Description Cleanup**

**Problem:** 1,420 tools have "AI tool from [Category]"

**Examples:**
- "AI tool from 📚 Books"
- "AI tool from 🏗 AI Engineering"

**Fix:**

**Phase 1: Identify Real Tool Names**
- Check if "Tool Name" field has real name
- Research tool online if name seems legit

**Phase 2: Generate Proper Descriptions**
```
Template: "[Tool Name] is a [Category] tool that provides [Primary Function] capabilities."

Example:
"LangGraph is an AI Development & Engineering tool that provides agent workflow orchestration capabilities."
```

**Phase 3: Manual Enhancement**
- Top 100 tools: Write custom descriptions
- Remaining: Use generated templates
- Flag for future improvement

---

## SECTION 5: Validation Rules

### **Pre-Deployment Validation Checklist**

All tools must pass these checks before going to production:

#### **Rule 1: Category Whitelist**
```
Allowed values (30):
- Image Creation & Editing
- Video Creation & Editing
- Audio & Music Creation
- Writing & Text Generation
- Marketing & Advertising
- Finance & Accounting
- Productivity & Workflow
- Customer Service & Support
- E-commerce & Retail
- Sales & CRM
- Code Assistance & Generation
- AI Development & Engineering
- Developer Tools & Infrastructure
- Data Analysis & Visualization
- Research & Knowledge Management
- AI Chatbots & Conversational AI
- AI Productivity Assistants
- Healthcare & Medical
- Education & Learning
- Legal & Compliance
- Real Estate & Property
- Agriculture & Environmental
- Communication & Collaboration
- Language & Translation
- Design & Graphics
- 3D & Visualization
- Gaming & Entertainment
- Security & Privacy
- Automotive & Transportation
- Voice & Speech

Test: category IN allowed_values
Fail: 0 allowed (100% must pass)
```

---

#### **Rule 2: Pricing Model Whitelist**
```
Allowed values (6):
- Free
- Freemium
- Paid Subscription
- Usage-Based
- Enterprise
- One-Time Purchase

Test: pricing_model IN allowed_values
Fail: 0 allowed (100% must pass)
```

---

#### **Rule 3: Target Users Whitelist**
```
Allowed values (12):
- Developers
- Data Scientists & Analysts
- Content Creators
- Designers
- Marketing & Sales Professionals
- Business Owners & Entrepreneurs
- Enterprise Teams & Decision Makers
- Project & Product Managers
- Researchers & Academics
- Students & Learners
- Healthcare & Medical Professionals
- General Consumers & Individuals

Test: target_users IN allowed_values
Fail: 0 allowed (100% must pass)
```

---

#### **Rule 4: No "General" in Target Users**
```
Test: target_users != "General"
Fail: 0 allowed
```

---

#### **Rule 5: Launch Year Format & Range**
```
Test 1: Launch Year is integer (no decimals)
Test 2: 2000 <= Launch Year <= 2026
Test 3: Launch Year is not NULL (unless truly unknown)

Acceptable NULL rate: <15%
```

---

#### **Rule 6: No Placeholder Values**
```
Forbidden values:
- Key Features: "See website"
- Description: "AI tool from [X]"
- Company: "Unknown" (acceptable <15%)
- Pricing: "Unknown"
- Target Users: "General", "Everyone", "All"

Test: key_features != "See website"
Test: description NOT LIKE "AI tool from%"
Fail: 0 allowed (except Company Unknown <15%)
```

---

#### **Rule 7: No Concatenated Values**
```
Test: pricing_model NOT LIKE "%/%"
Test: target_users NOT LIKE "% and %"
Test: Length(pricing_model) <= 30
Fail: 0 allowed
```

---

#### **Rule 8: Case Consistency**
```
Test: Category matches exact case (e.g., "Image Creation & Editing")
Test: Target Users matches exact case (e.g., "Content Creators")
Fail: 0 allowed
```

---

#### **Rule 9: Required Fields**
```
Must not be NULL:
- Tool Name
- Category
- Primary Function (or remove column)
- Description
- Website
- Pricing Model
- Target Users

Nullable allowed:
- Launch Year
- Company
- Key Features (temporarily during cleanup)

Test: required_field IS NOT NULL
Fail: 0 allowed for required fields
```

---

#### **Rule 10: Data Type Validation**
```
Tool Name: String, 1-200 chars
Category: String, exact match to whitelist
Description: String, 10-5000 chars
Website: String, valid URL format
Pricing Model: String, exact match to whitelist
Key Features: String, 20-2000 chars
Target Users: String, exact match to whitelist
Launch Year: Integer, 2000-2026 or NULL
Company: String, 1-200 chars or NULL
ID: Integer, unique
Category_code: Integer
average_rating: Float, 0-5 or NULL
review_count: Integer, >=0
```

---

## SECTION 6: Implementation Plan

### **Week 1: Priority 0 - Critical Mappings**

**Day 1-2: Category Mapping**
- [ ] Create mapping table (200 → 30)
- [ ] Apply automatic mappings (90%)
- [ ] Manual review of 100 edge cases
- [ ] Validate: 100% pass whitelist check

**Day 3-4: Pricing Mapping**
- [ ] Create mapping table (30 → 6)
- [ ] Apply automatic mappings (97%)
- [ ] Research "Unknown" pricing (1,422 tools)
- [ ] Validate: 100% pass whitelist check

**Day 5: Target Users Mapping Part 1**
- [ ] Create mapping table (219 → 12)
- [ ] Apply direct mappings (26,657 tools)
- [ ] Case normalization
- [ ] Validate: All non-"General" tools pass

---

### **Week 2: Priority 1 - Quality Improvements**

**Day 1-3: Target Users "General" Elimination**
- [ ] Apply category-based defaults (28,253 tools)
- [ ] Manual review of 10% sample (2,825 tools)
- [ ] Adjust based on descriptions
- [ ] Validate: 0 "General" values remain

**Day 4: Launch Year Cleanup**
- [ ] Remove decimals (48,460 tools)
- [ ] Convert to integer
- [ ] Validate range (2000-2026)
- [ ] Flag outliers (1907, etc.)

**Day 5: Compound Values**
- [ ] Split compound target users (110 tools)
- [ ] Manual review and assignment
- [ ] Validate: No "and" in values

---

### **Week 3-4: Priority 2 - Enrichment**

**Day 1-5: Key Features Replacement**
- [ ] Generate category-based templates
- [ ] Replace "See website" (6,207 tools)
- [ ] Begin web scraping for real features
- [ ] Manual review top 100 tools

**Day 6-10: Company Research**
- [ ] Research top 100 popular tools
- [ ] Web scraping for company info
- [ ] Manual data entry
- [ ] Target: <5% "Unknown" company

---

### **Ongoing: Priority 3 - Maintenance**

**Weekly:**
- [ ] Review new tool submissions
- [ ] Apply validation rules
- [ ] Flag edge cases for review
- [ ] Update mappings as needed

**Monthly:**
- [ ] Audit random 100 tools
- [ ] Check mapping accuracy
- [ ] Update templates
- [ ] Collect user feedback

---

## SECTION 7: Quality Gates

### **Gate 1: Pre-Alpha (Internal Testing)**

**Requirements:**
- ✅ Category: 100% valid (all 30 canonical)
- ✅ Pricing: 100% valid (all 6 canonical)
- ✅ Target Users: 100% valid (all 12 canonical)
- ✅ "General": 0% (eliminated)
- ⚠️ Key Features: "See website" <30% (improvement from 11.3%)

**Pass Criteria:** Category, Pricing, Target Users must be 100% clean

---

### **Gate 2: Alpha (Limited User Testing)**

**Requirements:**
- ✅ All Gate 1 requirements
- ✅ Launch Year: Format 100% correct (no decimals)
- ✅ Key Features: "See website" <10%
- ✅ Company: "Unknown" <10%
- ✅ Duplicates: Resolved or documented

**Pass Criteria:** <5% of tools have placeholder values

---

### **Gate 3: Beta (Public Testing)**

**Requirements:**
- ✅ All Gate 2 requirements
- ✅ Key Features: "See website" <5%
- ✅ Company: "Unknown" <5%
- ✅ Descriptions: No templates (<1%)
- ✅ LLM query testing: >90% accuracy
- ✅ User feedback: >80% satisfaction

**Pass Criteria:** Production-ready quality

---

### **Gate 4: Production**

**Requirements:**
- ✅ All Gate 3 requirements
- ✅ Key Features: Real features (not templates)
- ✅ Rating system: Implemented
- ✅ Monitoring: Dashboards active
- ✅ Feedback loop: User reporting functional

**Pass Criteria:** Full platform launch

---

## SECTION 8: Monitoring & Maintenance

### **Automated Monitoring**

**Daily Checks:**
- [ ] New tools: Apply validation rules
- [ ] Rejection rate: Flag if >10%
- [ ] User reports: Review flagged tools

**Weekly Reports:**
- [ ] Data quality score: Track trend
- [ ] Validation failures: Root cause analysis
- [ ] User feedback: Categorize issues

**Monthly Audits:**
- [ ] Random sample (100 tools): Manual review
- [ ] Mapping accuracy: Spot check
- [ ] Taxonomy evolution: New patterns?

---

### **Continuous Improvement**

**Quarterly Reviews:**
- [ ] Are 30 categories sufficient?
- [ ] Are 12 personas sufficient?
- [ ] New pricing models emerging?
- [ ] User behavior patterns changed?

**Annual Refresh:**
- [ ] Full taxonomy review
- [ ] Update mappings based on industry changes
- [ ] Incorporate user feedback
- [ ] Benchmark against competitors

---

## Appendix A: Complete Mapping Tables

### **Category Mapping (200 → 30)**
[Full table included in Category Taxonomy document]

### **Pricing Mapping (30 → 6)**
[Full table included in Pricing Taxonomy document]

### **Target Users Mapping (219 → 12)**
[Full table included in Target Users Taxonomy document]

---

## Document History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | Feb 2026 | Initial mapping rules | Data Audit Team |

---

**End of Document**