# Target Users Taxonomy
**Document Version:** 1.0  
**Date:** February 2026  
**Purpose:** Define canonical user personas for NeuraGuide AI tools platform

---

## Executive Summary

**Problem:** Current dataset has catastrophic user targeting issues:
- **51.5% marked "General"** (28,253 tools - completely unusable for matching)
- **100% "General" in critical categories:** Health, Data Analysis, Audio Generation
- **218 fragmented values** with 203 having <10 tools each
- **Case sensitivity issues:** "Content Creators" vs "content creators"
- **Compound values:** "Researchers and students", "Designers and marketers"

**Solution:** Consolidate into **12 canonical user personas** with clear definitions

**Impact:**
- Chatbot can match users to appropriate tools
- "General" eliminated entirely (FORBIDDEN value)
- Each persona has 1,500-3,000+ tools
- Clear targeting for recommendations

**CRITICAL:** This fixes the #1 blocker for your live chatbot (51.5% unusable data)

---

## Design Principles

### 1. **Ban "General" Forever**
"General" is NEVER an acceptable value. Every tool must target a specific audience.

**Rationale:** "General" provides zero value for matching. It's the same as NULL.

### 2. **User-Centric Personas**
Personas represent how users self-identify, not job titles or org charts.

**Examples:**
- ✅ "Content Creators" (how users think)
- ❌ "Digital Content Production Specialists" (HR speak)

### 3. **Mutually Exclusive Primary Persona**
Each tool has ONE primary target user. Tools may serve multiple personas, but we categorize by the PRIMARY user.

**Decision Rule:** Who gets 80% of the value from this tool?

### 4. **Balanced Distribution**
Each persona should have 1,000-3,000 tools. Avoid:
- Mega personas (>5,000 tools - too broad)
- Tiny personas (<500 tools - not viable for filtering)

### 5. **Action-Oriented Matching**
Personas should enable clear user queries:
- "tools for developers" ✅
- "tools for general users" ❌

---

## Canonical User Personas (12)

### **PERSONA 1: Developers**
**Definition:** Software developers, programmers, and engineers who write code professionally.

**Includes:**
- Frontend/backend developers
- Full-stack developers
- Mobile app developers
- DevOps engineers
- Software engineers

**Excludes:**
- Data scientists (use Data Scientists & Analysts)
- AI/ML engineers building models (use Data Scientists & Analysts)
- Low-code/no-code users (use Business Users)

**Key Characteristics:**
- Primary activity: Writing and maintaining code
- Technical skill level: High
- Tools needed: IDEs, debugging, version control, testing

**User Query Match:**
- "tools for developers"
- "coding tools"
- "programming assistants"
- "developer productivity"

**Examples of Tools:**
- GitHub Copilot (code completion)
- Cursor (AI code editor)
- Postman (API testing)

**Consolidates from:**
- Developers (3,125)
- Software developers (2)
- Game developers (2)
- Developers and enterprises (2)
- Developers and researchers (1)
- Individual developers (1)
- AWS developers (1)
- Professional developers (1)
- Enterprise developers (1)
- Web developers (implied)
- Mobile developers (implied)

**Total:** ~3,154 tools (5.7%)

---

### **PERSONA 2: Data Scientists & Analysts**
**Definition:** Professionals who work with data for analysis, insights, machine learning, and business intelligence.

**Includes:**
- Data scientists
- Data analysts
- Business analysts
- ML engineers (model building)
- Data engineers
- BI professionals

**Excludes:**
- Software developers (use Developers)
- Researchers in academia (use Researchers & Academics)
- Business users who just view dashboards (use Business Users)

**Key Characteristics:**
- Primary activity: Data analysis, modeling, visualization
- Technical skill level: Medium to High
- Tools needed: Data platforms, ML frameworks, visualization, statistics

**User Query Match:**
- "data science tools"
- "analytics platforms"
- "ML tools"
- "data analysis software"

**Examples of Tools:**
- Tableau (BI and visualization)
- DataRobot (automated ML)
- Jupyter notebooks (data science)

**Consolidates from:**
- Data Scientists (1,690)
- Analysts (1,657)
- Business analysts (implied)
- Data analysts (implied)

**Total:** ~3,347 tools (6.1%)

---

### **PERSONA 3: Content Creators**
**Definition:** Individuals who create digital content including videos, podcasts, blogs, social media, and multimedia.

**Includes:**
- YouTubers and video creators
- Podcasters
- Bloggers and writers
- Social media influencers
- Photographers (content focus)
- Musicians and audio creators

**Excludes:**
- Graphic designers (use Designers)
- Professional writers for business (use Marketing & Sales Professionals)
- Game content creators (use Developers or Designers based on role)

**Key Characteristics:**
- Primary activity: Creating and publishing content
- Technical skill level: Low to Medium
- Tools needed: Video/audio editing, content generation, scheduling

**User Query Match:**
- "content creation tools"
- "tools for creators"
- "YouTuber tools"
- "podcast tools"

**Examples of Tools:**
- Descript (video/audio editing)
- Canva (graphics for content)
- Buffer (social media scheduling)

**Consolidates from:**
- Content Creators (1,656)
- Content creators (4) - case variation
- Content creators and marketers (2)
- Photographers and content creators (1)
- Podcasters and content creators (1)
- Digital creators (implied)
- Influencers (implied)

**Total:** ~1,675 tools (3.1%)

---

### **PERSONA 4: Designers**
**Definition:** Creative professionals who design visual elements, user interfaces, graphics, and digital experiences.

**Includes:**
- Graphic designers
- UI/UX designers
- Product designers
- Web designers
- Brand designers
- 3D designers and artists

**Excludes:**
- Content creators making social posts (use Content Creators)
- Marketing professionals using design tools (use Marketing & Sales Professionals)

**Key Characteristics:**
- Primary activity: Visual design and creative work
- Technical skill level: Medium to High (in design tools)
- Tools needed: Design software, prototyping, collaboration, asset management

**User Query Match:**
- "design tools"
- "UI/UX tools"
- "graphic design software"
- "tools for designers"

**Examples of Tools:**
- Figma (UI/UX design)
- Adobe Creative Suite (design)
- Canva (graphic design)

**Consolidates from:**
- Designers (1,659)
- Designers and marketers (2)
- Designers and e-commerce (1)
- Artists and designers (1)
- Web designers (1)
- Digital artists (2)
- Developers and artists (1)
- Creative professionals (2)

**Total:** ~1,667 tools (3.0%)

---

### **PERSONA 5: Marketing & Sales Professionals**
**Definition:** Professionals who drive customer acquisition, engagement, and revenue through marketing and sales activities.

**Includes:**
- Digital marketers
- Content marketers
- SEO specialists
- Email marketers
- Sales representatives
- Sales managers
- Account executives
- Marketing managers

**Excludes:**
- Business owners doing their own marketing (use Business Owners & Entrepreneurs)
- Social media influencers (use Content Creators)

**Key Characteristics:**
- Primary activity: Marketing campaigns, lead generation, sales processes
- Technical skill level: Low to Medium
- Tools needed: Marketing automation, CRM, analytics, content creation

**User Query Match:**
- "marketing tools"
- "sales tools"
- "CRM software"
- "lead generation tools"

**Examples of Tools:**
- HubSpot (marketing automation)
- Salesforce (CRM)
- SEMrush (SEO)

**Consolidates from:**
- Marketers (1,693)
- Sales Teams (1,622)
- Sales teams and managers (1)
- B2B sales and marketing (1)
- Sales and marketing teams (1)
- Marketing and training (1)
- Content marketers (2)
- Marketers and agencies (2)
- Marketers and small businesses (1)

**Total:** ~3,322 tools (6.1%)

---

### **PERSONA 6: Business Owners & Entrepreneurs**
**Definition:** Founders, business owners, and entrepreneurs running small to medium businesses.

**Includes:**
- Startup founders
- Small business owners
- Solopreneurs
- Freelancers (self-employed)
- Independent contractors

**Excludes:**
- Corporate executives (use Enterprise Decision Makers)
- Freelancers who are specialists (use their specialty: Designers, Developers, etc.)

**Key Characteristics:**
- Primary activity: Running and growing a business
- Technical skill level: Low to Medium
- Tools needed: All-in-one platforms, automation, productivity, business management

**User Query Match:**
- "tools for small business"
- "startup tools"
- "entrepreneur software"
- "freelancer tools"

**Examples of Tools:**
- QuickBooks (accounting)
- Shopify (e-commerce)
- Notion (business management)

**Consolidates from:**
- Business Owners (1,725)
- Startups (1,756)
- Freelancers (1,668)
- Small businesses (1)
- Marketers and small businesses (1)
- Small businesses and freelancers (1)
- Teams and startups (1)
- E-commerce businesses (2)

**Total:** ~5,149 tools (9.4%)

---

### **PERSONA 7: Enterprise Teams & Decision Makers**
**Definition:** Large organization teams, department heads, and enterprise decision makers.

**Includes:**
- Enterprise IT teams
- Enterprise security teams
- Department heads at large companies
- CTOs, CIOs, VPs at enterprises
- Corporate teams (>500 employees)

**Excludes:**
- Small business owners (use Business Owners & Entrepreneurs)
- Project managers at any size company (use Project & Product Managers)

**Key Characteristics:**
- Primary activity: Enterprise-scale operations and decisions
- Technical skill level: Varies (High for IT, Medium for business)
- Tools needed: Enterprise-grade platforms, security, compliance, scalability

**User Query Match:**
- "enterprise tools"
- "tools for large companies"
- "corporate software"
- "enterprise-grade platforms"

**Examples of Tools:**
- Salesforce Enterprise (CRM)
- Snowflake (data warehouse)
- Databricks (analytics platform)

**Consolidates from:**
- Enterprise Teams (1,607)
- Developers and enterprises (2)
- Enterprise security (2)
- Enterprise developers (1)
- Enterprise content teams (1)
- Businesses and consumers (1) - if B2B focus

**Total:** ~1,623 tools (3.0%)

---

### **PERSONA 8: Researchers & Academics**
**Definition:** Academic researchers, scientists, and scholars conducting research and education.

**Includes:**
- University researchers
- Academic professors
- PhD students (research focus)
- Research scientists
- Lab researchers

**Excludes:**
- Students learning (use Students & Learners)
- Business researchers/analysts (use Data Scientists & Analysts)

**Key Characteristics:**
- Primary activity: Academic research, publication, teaching
- Technical skill level: High (domain-specific)
- Tools needed: Research platforms, citation management, data analysis, collaboration

**User Query Match:**
- "academic research tools"
- "tools for researchers"
- "scientific research software"
- "research collaboration"

**Examples of Tools:**
- Elicit (research AI)
- Zotero (citation management)
- Overleaf (LaTeX editor)

**Consolidates from:**
- Researchers (1,731)
- Researchers and students (3)
- Researchers and academics (2)
- Academic researchers (2)
- Developers and researchers (1)
- Researchers and writers (1)

**Total:** ~1,753 tools (3.2%)

---

### **PERSONA 9: Students & Learners**
**Definition:** Students, trainees, and individuals in formal or self-directed learning.

**Includes:**
- University/college students
- High school students
- Online course learners
- Self-taught learners
- Bootcamp participants

**Excludes:**
- PhD students doing research (use Researchers & Academics)
- Professional development learners (use their profession)

**Key Characteristics:**
- Primary activity: Learning and skill development
- Technical skill level: Low to Medium (learning)
- Tools needed: Learning platforms, note-taking, study aids, collaboration

**User Query Match:**
- "student tools"
- "learning tools"
- "study aids"
- "educational software"

**Examples of Tools:**
- Coursera (online learning)
- Notion (note-taking)
- Grammarly (writing improvement)

**Consolidates from:**
- Students (1,641)
- Researchers and students (3)
- Professionals and students (1)
- Students and professionals (1)
- Students and writers (1)
- Students and developers (1)
- Students and educators (1)
- Language learners (2)

**Total:** ~1,653 tools (3.0%)

---

### **PERSONA 10: Project & Product Managers**
**Definition:** Professionals who manage projects, products, teams, and workflows.

**Includes:**
- Project managers
- Product managers
- Program managers
- Scrum masters
- Agile coaches
- Team leads

**Excludes:**
- Business owners managing their own business (use Business Owners & Entrepreneurs)
- Sales managers (use Marketing & Sales Professionals)

**Key Characteristics:**
- Primary activity: Planning, coordination, team management
- Technical skill level: Low to Medium
- Tools needed: Project management, collaboration, roadmapping, tracking

**User Query Match:**
- "project management tools"
- "product management software"
- "team collaboration tools"
- "agile tools"

**Examples of Tools:**
- Asana (project management)
- Jira (agile management)
- Miro (collaboration)

**Consolidates from:**
- Project Managers (1,630)
- Sales teams and managers (1)

**Total:** ~1,631 tools (3.0%)

---

### **PERSONA 11: Healthcare & Medical Professionals**
**Definition:** Medical professionals, healthcare workers, and health industry practitioners.

**Includes:**
- Doctors and physicians
- Nurses
- Healthcare administrators
- Medical researchers (applied)
- Health tech professionals
- Therapists and counselors

**Excludes:**
- Academic medical researchers (use Researchers & Academics)
- Health/fitness enthusiasts (use General Consumers - see Persona 12)

**Key Characteristics:**
- Primary activity: Patient care, medical practice, health services
- Technical skill level: Medium to High (domain-specific)
- Tools needed: Diagnostics, patient management, health records, telemedicine

**User Query Match:**
- "healthcare tools"
- "medical software"
- "tools for doctors"
- "health professionals tools"

**Examples of Tools:**
- Epic (EHR)
- PathAI (diagnostics)
- Teladoc (telemedicine)

**Consolidates from:**
- Health category tools (1,843) - currently all "General", need reassignment
- Medical professionals (implied)
- Healthcare workers (implied)

**Total:** ~1,843 tools (3.4%) *requires reassignment from "General"*

---

### **PERSONA 12: General Consumers & Individuals**
**Definition:** Individual consumers using tools for personal, non-professional purposes.

**Includes:**
- Personal productivity users
- Hobbyists
- Individual consumers
- Personal finance users
- Home users

**Excludes:**
- Anyone using tools professionally (use their profession)
- Students (use Students & Learners)
- Content creators (even hobbyists - use Content Creators)

**Key Characteristics:**
- Primary activity: Personal use, hobbies, life management
- Technical skill level: Low
- Tools needed: Simple interfaces, personal productivity, entertainment

**User Query Match:**
- "personal use tools"
- "tools for individuals"
- "consumer AI tools"
- "everyday tools"

**Examples of Tools:**
- ChatGPT Free (general assistant)
- Mint (personal finance)
- Grammarly Free (personal writing)

**Use Sparingly:** This is the catch-all for truly general-use tools. When in doubt, choose a more specific persona.

**Consolidates from:**
- Tools truly for general public
- Global users (1)
- Individual users (implied)

**Total:** ~500-1,000 tools (1-2%) *intentionally small*

---

## Special Cases & Edge Cases

### **Multi-Persona Tools**
**Problem:** Tool serves multiple distinct user types (e.g., Figma for Designers AND Developers).

**Solution:** Assign to PRIMARY persona (80% rule). If truly 50/50, choose the persona with fewer tools in that category.

**Example:**
- **Figma:** Serves designers (80%) and developers (20%) → **Designers**
- **Notion:** Serves many personas equally → **Business Owners & Entrepreneurs** (most general business use)

---

### **Technical vs Non-Technical Split**
**Problem:** Tool like "data analysis" could be for Data Scientists OR Business Users.

**Decision Rule:**
- **Requires code/SQL/technical skills** → Data Scientists & Analysts
- **No-code, drag-and-drop** → Business Owners & Entrepreneurs or Project & Product Managers
- **Just viewing dashboards** → Business Owners or Project Managers

**Example:**
- **Tableau** (create dashboards, some SQL) → Data Scientists & Analysts
- **Google Looker Studio** (template-based) → Business Owners & Entrepreneurs

---

### **Freelancers: Profession or Business Owner?**
**Problem:** Freelance designer - is this Designers or Business Owners?

**Decision Rule:** What is the tool helping them DO?
- **Design work** → Designers
- **Run their freelance business** (invoicing, scheduling) → Business Owners & Entrepreneurs

**Example:**
- **Figma** (design) → Designers
- **FreshBooks** (invoicing) → Business Owners & Entrepreneurs

---

### **Students vs Researchers**
**Problem:** PhD student - Student or Researcher?

**Decision Rule:**
- **Learning focus** (coursework, studying) → Students & Learners
- **Research focus** (publishing, grant writing) → Researchers & Academics

---

### **Industry-Specific Tools**
**Problem:** "Legal practice management software" - who is the user?

**Solution:** Create industry-specific sub-personas when needed (future expansion), but for now:
- Legal professionals → **Business Owners & Entrepreneurs** (if solo/small firm) OR **Enterprise Teams** (if large firm)
- Medical professionals → **Healthcare & Medical Professionals**
- Farmers, logistics → **Business Owners & Entrepreneurs**

**Rationale:** Better to have a functional persona (business owner) than a tiny one (farmers: 2 tools).

---

### **"General" Category Tools That Need Assignment**
**Problem:** Categories like Health, Data Analysis, Audio Generation are 100% "General".

**Assignment Strategy by Category:**

| Category | Likely Primary Persona | Logic |
|----------|------------------------|-------|
| Health | Healthcare & Medical Professionals | Medical use cases |
| Data Analysis | Data Scientists & Analysts | Data analysis is their job |
| Audio Generation | Content Creators | Audio content creation |
| Image Generation | Content Creators OR Designers | 80% content, 20% design |
| Code Assistant | Developers | Code is their domain |
| Productivity | Business Owners & Entrepreneurs | General business productivity |
| Translation | General Consumers & Individuals | Personal/general use |
| Education | Students & Learners | Learning context |

**Process:**
1. Analyze tool description and features
2. Determine primary use case (80% rule)
3. Assign appropriate persona
4. Flag edge cases for manual review

---

## Forbidden Values

### **NEVER Accept These Values:**

**1. "General"** - The plague. Always assign specific persona.

**2. Vague Modifiers:**
- "Everyone"
- "All users"
- "Anyone"
- "Any professional"
- "General users"

**3. Compound Values (unless truly hybrid):**
- "Researchers and students" → Choose: Researchers OR Students
- "Designers and marketers" → Choose: Designers OR Marketing Professionals
- "Businesses and consumers" → Choose: Business Owners OR General Consumers

**Exception:** Keep compound ONLY if tool genuinely serves both equally and neither is >60%.

**4. Organization Size as User Type:**
- ❌ "SMBs" → Use **Business Owners & Entrepreneurs**
- ❌ "Large enterprises" → Use **Enterprise Teams & Decision Makers**

**5. Technology Stack as User:**
- ❌ "Python developers" → Use **Developers**
- ❌ "AWS users" → Use **Developers** (or appropriate persona)

---

## Validation Rules

### **Automated Checks**

**Whitelist check:** Value must be one of 12 canonical personas  
**No "General":** Reject any variation (General, general, GENERAL)  
**Case sensitivity:** Enforce exact capitalization  
**No compounds:** Flag values with "and", "or", "/" for review  
**Length check:** Value must be ≤50 characters

### **Business Logic Checks**

**If Category = Health:**
- Target Users should likely be "Healthcare & Medical Professionals"
- If not, requires justification

**If Category = Code Assistant:**
- Target Users should likely be "Developers"
- If not, requires justification

**If Category = Education:**
- Target Users should likely be "Students & Learners" OR "Researchers & Academics"

---

## User Query Handling (LLM Guidance)

### **Query: "Tools for developers"**
**Match to:** Developers  
**Include:** Code assistance, developer tools, APIs

### **Query: "I'm a data scientist, what tools can help me?"**
**Match to:** Data Scientists & Analysts  
**Include:** Data analysis, ML platforms, BI tools

### **Query: "Best AI tools for my startup"**
**Match to:** Business Owners & Entrepreneurs  
**Include:** All-in-one platforms, productivity, automation

### **Query: "Tools for content creators"**
**Match to:** Content Creators  
**Include:** Video editing, image generation, social media

### **Query: "Enterprise-grade solutions"**
**Match to:** Enterprise Teams & Decision Makers  
**Include:** Enterprise pricing, scalable platforms, compliance

### **Query: "Free tools for students"**
**Match to:** Students & Learners  
**Filter by:** Pricing = Free OR Freemium

### **Query: "Marketing automation tools"**
**Match to:** Marketing & Sales Professionals  
**Category:** Marketing & Advertising

### **Query: "Project management software"**
**Match to:** Project & Product Managers  
**Category:** Productivity & Workflow

---

## Migration Mapping

### **Automatic Mappings (15 personas with 500+ tools)**

| Old Value | New Persona | Tools | Notes |
|-----------|-------------|-------|-------|
| Developers | **Developers** | 3,125 | Direct mapping |
| Data Scientists | **Data Scientists & Analysts** | 1,690 | Direct mapping |
| Analysts | **Data Scientists & Analysts** | 1,657 | Merge with Data Scientists |
| Content Creators | **Content Creators** | 1,656 | Direct mapping (normalize case) |
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
| Engineers | **Developers** | 1,558 | Merge with Developers |

**Subtotal:** 26,657 tools (48.5%) - mostly automatic

---

### **Category-Based Reassignment (28,253 "General" tools)**

**Strategy:** Assign based on category + tool description analysis.

| Category | Default Persona Assignment | Tools Affected |
|----------|---------------------------|----------------|
| Health | Healthcare & Medical Professionals | 1,843 |
| Data Analysis | Data Scientists & Analysts | 1,724 |
| Audio Generation | Content Creators | 1,695 |
| Image Generation | Content Creators (70%) / Designers (30%) | 1,675 |
| Code Assistant | Developers | 1,761 |
| Productivity | Business Owners & Entrepreneurs | 1,696 |
| Translation | General Consumers & Individuals | 1,667 |
| Education | Students & Learners | 1,839 |
| Marketing | Marketing & Sales Professionals | 1,902 |
| Finance | Business Owners & Entrepreneurs | 1,790 |

**Process:**
1. Bulk assign based on category defaults
2. Manual review of 10% sample per category
3. Adjust assignments based on tool descriptions
4. Re-run validation

**Estimated breakdown:**
- Developers: 3,125 + 1,761 (Code) + 1,558 (Engineers) = **6,444 tools**
- Data Scientists & Analysts: 3,347 + 1,724 (Data Analysis) = **5,071 tools**
- Content Creators: 1,675 + 1,695 (Audio) + 1,172 (Image 70%) = **4,542 tools**
- Business Owners: 5,149 + 1,696 (Productivity) + 1,790 (Finance) = **8,635 tools**
- Marketing & Sales: 3,315 + 1,902 (Marketing) = **5,217 tools**
- Students & Learners: 1,641 + 1,839 (Education) = **3,480 tools**
- Designers: 1,667 + 503 (Image 30%) = **2,170 tools**
- Healthcare: 1,843 tools
- Researchers: 1,753 tools
- Enterprise: 1,623 tools
- Project Managers: 1,630 tools
- General Consumers: 1,667 (Translation) + others = **~2,000 tools**

**Total:** 54,910 tools 

---

### **Manual Review Required (203 tiny values)**

**Examples needing review:**
- Game studios (3) → Developers OR Designers
- Legal professionals (3) → Business Owners OR Enterprise (depending on firm size)
- Farmers (2) → Business Owners & Entrepreneurs
- Security professionals (2) → Enterprise Teams (if corporate) OR Developers (if security dev)

**Process:** Research each tool individually and assign appropriate persona.

---

## Success Metrics

### **Quantitative**
- User personas: 219 → 12 (94.5% reduction) 
- "General": 28,253 → 0 (100% elimination) 
- Tiny values (<10 tools): 203 → 0 (100% fixed) 
- Average tools per persona: 122 → 4,576 (37× increase) 

### **Qualitative**
- Chatbot can match users to tools with >90% accuracy
- Users self-identify with at least one persona
- No user confusion about "who is this for?"
- Personas enable effective filtering

### **User Experience**
- "Show me tools for developers" returns 6,000+ relevant tools
- "I'm a content creator" surfaces video, audio, image tools
- "Small business owner" gets productivity, finance, marketing tools
- No more "this tool is for everyone" (useless)

---

## Governance

**Persona Owner:** Product Team  
**Review Frequency:** Quarterly  
**New Persona Criteria:**
- ≥1,500 tools would fit
- Cannot fit into existing 12 personas
- Represents distinct user need/behavior
- Users self-identify with this label

**Change Process:**
1. Proposal with user research
2. Validate via surveys ("Do you identify as X?")
3. Test LLM matching accuracy
4. Product approval
5. Deployment with monitoring

---

## Implementation Checklist

### **Phase 1: Data Cleanup (Week 1-2)**
- [ ] Normalize case variations (Content Creators vs content creators)
- [ ] Map 15 large personas automatically (26,657 tools)
- [ ] Category-based assignment for "General" tools (28,253 tools)
- [ ] Manual review of 203 tiny values

### **Phase 2: Validation (Week 3)**
- [ ] Run validation rules on all assignments
- [ ] Spot-check 500 random tools manually (1% sample)
- [ ] Test LLM query matching accuracy
- [ ] User testing with persona filters

### **Phase 3: Deployment (Week 4)**
- [ ] Update database schema
- [ ] Deploy to production
- [ ] Monitor user filter usage
- [ ] Track query → result relevance

### **Phase 4: Ongoing (Monthly)**
- [ ] Review newly added tools
- [ ] Collect user feedback on targeting
- [ ] Adjust personas if new patterns emerge
- [ ] Quarterly persona effectiveness review

---

## Appendix A: Complete Persona List (12)

**Technical & Creative (5 personas)**
1. Developers
2. Data Scientists & Analysts
3. Content Creators
4. Designers
5. Marketing & Sales Professionals

**Business & Management (3 personas)**
6. Business Owners & Entrepreneurs
7. Enterprise Teams & Decision Makers
8. Project & Product Managers

**Education & Research (2 personas)**
9. Researchers & Academics
10. Students & Learners

**Industry & General (2 personas)**
11. Healthcare & Medical Professionals
12. General Consumers & Individuals

---

## Appendix B: Rejected Persona Ideas

These were considered but rejected:

| Rejected Persona | Reason |
|------------------|--------|
| "Engineers" | Too broad - merge with Developers |
| "Analysts" | Ambiguous - merge with Data Scientists |
| "Professionals" | Vague - use specific profession |
| "Freelancers" | Job status, not role - use Business Owners |
| "Startups" | Org size, not role - use Business Owners |
| "SMBs" | Org size, not role - use Business Owners |
| "Individuals" | Too general - use General Consumers sparingly |
| "Everyone" | Anti-pattern - forbidden |
| "Creative professionals" | Vague - use Designers or Content Creators |
| "Knowledge workers" | Too broad - use specific role |

---

## Document History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | Feb 2026 | Initial user persona taxonomy | Data Audit Team |

---

**End of Document**