Below is a complete, end‑to‑end **Instructor Guide** and **Student Guide** for the lab workshop:

> **AI‑Compliant Deep Research Process**
> (aka **Your first App, Your first AI Agent, Your first AI Search, Your first AI‑powered Portal** on ServiceNow **Zurich**)

---

## 0) Learning outcomes (both tracks)

By the end of this workshop, participants will be able to:

1. **Stand up a Zurich instance & plugins** required for App Engine, AI Agents, AI Search, Playbooks, Virtual Agent, and Workspaces. ([ServiceNow][1])
2. **Create a scoped app** two ways (choose‑one): **App Engine Studio** or the **new ServiceNow Studio** experience. ([ServiceNow][2])
3. **Link the app to GitHub** and work with branches/commits from Studio/AES. ([ServiceNow][3])
4. **Model data** by extending **Task** into a new table *Compliance Research Request* with additional fields and status/stage. ([ServiceNow][4])
5. **Publish intake via a Record Producer** in Service Catalog (with category), and understand how this makes intake available in portals and Virtual Agent. ([ServiceNow][5])
6. **Design a Playbook** (staged lifecycle) and surface it in a **Workspace** with lists, filters, and a custom dashboard—triggered on record creation. ([ServiceNow][6])
7. **Build and test an AI Agent** in **AI Agent Studio** that collects intake details via chat and **creates records** (one tool: *save record*). ([ServiceNow][7])
8. **Stand up AI Search**: create a Knowledge Base + 3 articles, configure an **AI Search Profile**, and **extend** search with a **Web Crawler** for external sources. ([ServiceNow][8])
9. **Build a Now Assist Skill Kit “Deep Research” skill** that uses a **Retriever** tied to your AI Search Profile to synthesize an answer and (via deployment to a UI Action) **creates a Knowledge Article** linked back to the originating request and advances the Playbook stage. ([ServiceNow][9])
10. **Enable AI Search in a Service Portal and Virtual Agent** so newly created articles are discoverable (including Q&A/Genius Result cards). ([ServiceNow][10])

> **Governance & safety framing (Instructor note)**
> Remind learners that all AI features must adhere to ServiceNow’s AI Acceptable Use Policy and enterprise policies; point them to **AI Control Tower** for central oversight of AI assets, guardrails, approvals, and reporting. ([ServiceNow][11])

---

# INSTRUCTOR GUIDE

### Audience & roles

* Audience: Developers/Platform Engineers, Architects, Product Owners, and selected Business SMEs.
* Roles in instance: **admin** for instructors; students need **app_engine_admin** (or **admin**) plus roles required by Playbooks, AI Search, AI Agents, and Skill Kit (varies by subscription). ([ServiceNow][12])

### Lab structure at a glance

1. **Setup**: instance + plugins + Git (branch created).
2. **Your first app** (choose one path):

   * 2A) App Engine Studio (AES), or
   * 2B) ServiceNow Studio (new Studio)
     Both converge at the same data model, record producer, Playbook, and Workspace.
3. **Your first AI Agent** (AI Agent Studio).
4. **Your first AI Search** (+ external Web Crawler).
5. **Now Assist Skill Kit** deep research skill → UI Action → KB → links back → Playbook stage update.
6. **Portal & Virtual Agent**: enable AI Search and verify.

> **Timing suggestion (full‑day workshop)**
> Setup (45m), App (2.5h), Lunch (45m), AI Agent (75m), AI Search (60m), Skill Kit (60m), Portal/VA & wrap (45m). (Adjust to your cohort experience.)

### Environment prerequisites

* **Zurich** instance (DemoHub if available to your org; otherwise a Developer PDI). DemoHub is partner‑focused; many learners will use PDIs instead. ([ServiceNow][13])
* **Plugins** (ensure entitlements):

  * **App Engine Studio** (AES). ([ServiceNow][14])
  * **ServiceNow Studio** (core app dev—available OOTB). ([ServiceNow][15])
  * **AI Agent Studio / Now Assist AI Agents**. ([ServiceNow][16])
  * **AI Search for Next Experience** (+ **External Content** if using Web Crawler). ([ServiceNow][17])
  * **Playbooks / Process Automation Designer (Workflow Studio)**. ([ServiceNow][6])
  * **Virtual Agent** (plus “Enable generative AI capability in Virtual Agent Designer” if available). ([ServiceNow][18])
  * **Configurable Workspace / UI Builder**. ([ServiceNow][19])
  * (Optional) **Now Assist Skill Kit** (often delivered via Store; ensure Zurich‑compatible version). ([ServiceNow][9])
    Check **List of plugins (Zurich)** for names, store packages, and dependent plugins. ([ServiceNow][20])

### Pre‑flight checks (before learners arrive)

* Validate the instance is on **Zurich** and all plugins above are active. ([ServiceNow][1])
* Create a **sample GitHub org** and an empty repo per team (or let teams create their own). Test **Studio → Link to Source Control** with a PAT/credential. ([ServiceNow][3])
* Prepare a **Service Portal** (e.g., `sp`) and **search profile** stub (empty is fine; students will complete). ([ServiceNow][10])
* Ensure **AI Control Tower** is accessible in case participants ask about governance (show the dashboard). ([ServiceNow][21])

### Assessment & rubrics

* **Bronze**: App created (either path), table & fields ok, record producer works, Playbook stages visible, Workspace loads.
* **Silver**: AI Agent creates records; Git commits at each checkpoint; AI Search profile returns KB results; Web Crawler indexes external site.
* **Gold**: Now Assist Skill Kit skill retrieves from AI Search, creates a KB article via UI Action, links to the task, and advances the Playbook stage; AI Search Q&A/Genius Results active in portal & VA.

### Risk/Safety callouts for regulated enterprises

* Apply **least‑privilege roles**, data separation, and **no PII** in demo content.
* For **AI data flow**, show learners where **data sharing** and **model provider** settings are governed (AI Control Tower & Generative AI Controller). ([ServiceNow][22])

---

# STUDENT GUIDE

> **Important**: When the instructions tell you to **commit to Git**, do it immediately. Keep your **branch** small and **commit message** precise. We’ll provide suggested messages.

---

## Step 1 — Get your instance & install required plugins

1. **Get an instance**

   * If your org uses **DemoHub**, request a Zurich demo instance there (partner‑only in many cases). Otherwise, use a **Developer PDI** from the ServiceNow developer program. (Your instructor will guide you.) ([ServiceNow][13])

2. **Install/verify plugins** (admin):

   * **App Engine Studio** (AES). ([ServiceNow][14])

   * **ServiceNow Studio** (core app dev). ([ServiceNow][15])

   * **AI Agent Studio / Now Assist AI Agents**. ([ServiceNow][7])

   * **AI Search for Next Experience** (+ **External Content** if using Web Crawler). ([ServiceNow][17])

   * **Playbooks / Process Automation Designer (Workflow Studio)**. ([ServiceNow][12])

   * **Virtual Agent** (+ enable generative AI Designer feature). ([ServiceNow][18])

   * **Configurable Workspace / UI Builder**. ([ServiceNow][19])

   > How to discover exact plugin names & dependencies in Zurich: open **List of plugins (Zurich)**. ([ServiceNow][20])

**Checkpoint commit** (create repo first in Step 3):
`docs: add lab prerequisites and plugin checklist (Zurich)`

---

## Step 2 — Choose your build path (do one; both converge later)

### Option 2A — **Build with App Engine Studio (AES)**

1. **Create the app** → *All > App Engine > App Engine Studio* → **Create app**. Name: **AI Compliant Deep Research**; scope suggestion: `x_<yourorg>_deep_research`. ([ServiceNow][2])
2. **Confirm application scope**: your changes should be in your app’s scope (watch the scope badge). Review app scope basics if needed. ([ServiceNow][23])

### Option 2B — **Build with the new ServiceNow Studio**

1. *All > App Engine > ServiceNow Studio* → **Create Application** → **App**. Provide name and description; keep scope consistent (`x_<yourorg>_deep_research`). ([ServiceNow][24])
2. (FYI) Zurich features you’ll notice: smoother Studio navigation and AI‑aware files/flows. ([ServiceNow][25])

**Checkpoint commit**:
`feat(app): scaffold “AI Compliant Deep Research” app (scope x_<org>_deep_research)`

---

## Step 3 — Link your app to **GitHub** (source control) & branching

> You will use **Studio**’s Source Control integration even if you created the app in AES. (AES and Studio share source control patterns in Zurich.) ([ServiceNow][26])

1. Create a new **GitHub repository** (empty; no README/license).
2. In your instance, open your app in **Studio** → **Source Control** → **Link to Source Control**. Provide repo URL (HTTPS or SSH), branch `main`, and credential (PAT or SSH). ([ServiceNow][3])
3. After linking, **Create Branch** for each lab segment:

   * `feature/data-model`
   * `feature/record-producer`
   * `feature/playbook`
   * `feature/workspace`
   * `feature/ai-agent-intake`
   * `feature/ai-search`
   * `feature/skillkit-deep-research` ([ServiceNow][27])

> **Best practice**: Use Git for **application metadata**; use **Update Sets** only for *global* configuration not tied to a scoped app (e.g., portal settings), and for legacy use cases. Know the difference. ([ServiceNow][28])

**Checkpoint commit**:
`chore(git): link app to GitHub; add branch naming convention`

---

## Step 4 — Update Set & Scope sanity check

1. Ensure your app scope is active before creating tables/objects. ([ServiceNow][23])
2. Create a **Global** Update Set (e.g., `Global_Config_Lab`) for portal/AI Search toggles that live outside your app. (Update Sets capture instance‑level config; your app metadata will remain in Git.) ([ServiceNow][28])

**Checkpoint commit**:
`chore(config): note update set for global changes; verify scope selection`

---

## Step 5 — **Data model**: extend **Task** for “Compliance Research Request”

1. *Studio/AES* → **Create Table** → **Extend table**: choose **Task [task]**. Table label: **Compliance Research Request**; name: `x_<org>_deep_research_request`. ([ServiceNow][4])
2. Add fields (examples; adapt to your org):

   * **Executive sponsor** (Reference → `sys_user`)
   * **Business unit** (Reference → `cmn_department`)
   * **Regulatory driver** (Choice: GDPR, HIPAA, SOX, PCI, Other)
   * **Risk level** (Choice: Low/Medium/High)
   * **Due date** (Date/Time)
   * **Summary** (String, 160)
   * **Details** (HTML)
   * **Stage** (Choice: *Intake*, *Research*, *Review*, *Complete*)
   * **Linked knowledge** (Reference → `kb_knowledge`)
3. Save.

**Checkpoint commit**:
`feat(data): new table extending task + core fields + Stage choices`

---

## Step 6 — **Record Producer** for request intake (Service Catalog)

1. *All > Service Catalog > Catalog Definition > Record Producers* → **New**. Target table: your new table. Title: “**Compliance Research Request**”. Add variables that map to your fields (sponsor, driver, risk, due date, summary, details). ([ServiceNow][5])
2. Place it into a **Catalog** and **Category** (e.g., Catalog “Employee Services”, Category “Compliance”). This organizes the experience in portals and helps discovery. ([ServiceNow][29])
3. (AES path) You can also add a Record Producer from AES’s **Experience** section. ([ServiceNow][30])

> **Why this matters for chat & portals**
> Catalog and Record Producers surface in **Service Portals** and can be discovered via **AI Search** and **Virtual Agent** (VA). VA can call AI Search during a conversation (Run AI Search block) to show KB or relevant items. ([ServiceNow][31])

**Checkpoint commit**:
`feat(intake): record producer + catalog/category for portal/VA discoverability`

---

## Step 7 — **Playbook** for staged lifecycle (Intake → Research → Review → Complete)

> In Zurich, Playbooks are authored in **Workflow Studio / Process Automation Designer**. Ensure entitlement is active. ([ServiceNow][6])

1. *All > Workflow Studio (Process Automation Designer)* → **Create Playbook** for your table. Stages:

   * **Intake**: “Validate fields”, “Initial triage”, “Assign handler”.
   * **Research**: “Collect sources”, “SME questions”, “Draft findings”.
   * **Review**: “Peer/legal review”, “Exec sponsor review”.
   * **Complete**: “Publish KB link”, “Close request”.
2. For each stage, add **Activities/Tasks** and (optionally) rules to set the **Stage** field when entering/exiting.
3. Publish.

**Checkpoint commit**:
`feat(process): playbook with 4 stages + activities for research lifecycle`

([ServiceNow][12])

---

## Step 8 — **Workspace** to manage these requests

1. Create a **Configurable Workspace** (or use **Workspace Builder** in AES). Add:

   * **Home dashboard** (KPIs: Open by Stage, SLA due soon).
   * **Lists**:

     * “My Team’s Research Requests” (filter by assignment group).
     * “High‑risk Requests” (Risk = High).
     * “Pending Review” (Stage = Review).
   * **Record page**: include the **Playbook** panel and related lists. ([ServiceNow][19])
2. Configure **Playbook to open on record creation** in the record page template (use the Playbook component for your table).
3. Save/Publish.

**Checkpoint commit**:
`feat(workspace): lists, dashboard, and embedded Playbook on record page`

---

## Step 9 — **AI Agent** handles chat intake & saves the record

1. *All > Now Assist AI Agents > AI Agent Studio* → **New agent**: “**Compliance Intake Agent**”. Define **Role/Persona** and guardrails (ask only necessary questions, avoid PII). ([ServiceNow][7])
2. **Add a Tool** → **Record Operation** (or **Save record**) mapped to your table to create a record with fields gathered during the chat. Keep it as **the single tool**. ([ServiceNow][32])
3. **Test** in **AI Agent Studio testing**: converse until a record is created. Verify the new record shows in your Workspace and **Playbook** starts. ([ServiceNow][33])

> **AI Agent vs Virtual Agent—what’s different?**
> **AI Agents** use agentic patterns to reason, call tools, and complete goals in fewer prescriptive steps; **Virtual Agent** provides guided, stateful conversations and topic‑based flows and can call **AI Search** inside dialogue. Use each where it fits best; both can coexist. ([ServiceNow][34])

**Checkpoint commit**:
`feat(ai-agent): chat intake agent + tool to create compliance request`

---

## Step 10 — **AI Search** foundation

1. **Create a Knowledge Base** (KB) “**Compliance Research**”. ([ServiceNow][8])
2. Create **three Knowledge Articles** (examples):

   * “How to request deep research for a new regulation”
   * “Approved data sources for regulatory research”
   * “KB authoring style guide for compliance research” ([ServiceNow][35])
3. **Create an AI Search Profile** and link sources (your KB; optionally Users, Catalog if useful). Publish the profile. ([ServiceNow][36])

> **Note on Git vs data**
> KB **articles are data** and don’t sync to Git. Your **KB configuration** and search **profile metadata** do. If you must version sample content, store a CSV/markdown in your repo and provide a **Load Data** or **Fix Script** to seed demo articles. ([ServiceNow][26])

**Checkpoint commit**:
`feat(knowledge): KB + search profile published (articles seeded via loader)`

---

## Step 11 — **Extend AI Search** with a **Web Crawler**

1. Activate **External Content for AI Search** if not already. ([ServiceNow][37])
2. Create a **Web Crawler external content connector**: specify allowed domains/paths for a public website (e.g., an industry regulator). Configure crawl settings and a schedule. ([ServiceNow][38])
3. Create a **Search Source** pointing to the connector’s indexed source and link it to your **AI Search Profile**. Re‑publish the profile. ([ServiceNow][39])

**Checkpoint commit**:
`feat(search): external web crawler source + linked to AI Search profile`

---

## Step 12 — **Now Assist Skill Kit**: build the **Deep Research** skill

Goal: A skill that uses **Retriever** to pull from your **AI Search Profile** (KB + crawled content), synthesizes an answer, then creates a **Knowledge Article** and links it to the originating request—exposed via a **UI Action** in your Workspace.

1. *All > Now Assist Skill Kit > Home* → **New skill**: “**Deep Research Synthesis**”. ([ServiceNow][9])
2. **Tool editor** → **Add Retriever**. Point it at your **AI Search Profile**; configure query inputs (e.g., request summary + details). Choose parallel or series if you add multiple tools. ([ServiceNow][40])
3. **Prompt**: In the final prompt, instruct the LLM to produce a **well‑sourced research brief** (bulleted insights + references list), suitable for publication, with a concise abstract.
4. **Test** and iterate until output is acceptable; **Finalize prompt**. ([ServiceNow][41])
5. **Deploy** the skill as a **UI Action** on your table (table: `x_<org>_deep_research_request`). This generates a UI Action you can attach to Workspace. ([YouTube][42])
6. In **Now Assist Admin**, **activate** the published skill (ensure **Display trigger** is enabled). ([ServiceNow][41])
7. **Create a Flow or enhance the UI Action script** to:

   * Create a **kb_knowledge** record (target KB: “Compliance Research”).
   * Set **short_description** = request summary; **text** (HTML) = skill’s output; set **valid_to** if your policy requires review cadence. ([ServiceNow][35])
   * Update the request’s **Linked knowledge** reference to the new article and set **Stage = Review** (or Complete, per your policy) using **Flow Designer: Create/Update Record**. ([ServiceNow][43])

> Skill Kit tools and deployment patterns differ by version; consult the Zurich tool/deployment options overview if needed. ([ServiceNow][44])

**Checkpoint commit**:
`feat(skill): Now Assist “Deep Research” skill + UI Action + flow to create KB & link back`

---

## Step 13 — **Surface AI Search** in Portals and Virtual Agent

1. **Service Portal**: open your portal record (*Service Portal > Portals*), enable **AI Search**, and assign your **Search Application/Profile**. Test queries; you should see your articles and, if enabled, **Q&A/Genius Result** cards. ([ServiceNow][10])
2. **Virtual Agent**: in the Conversational Interfaces admin console, **Activate AI Search** so topics can call it (Run AI Search block). Test by asking a question that matches your new KB. ([ServiceNow][45])
3. (Optional) Enable **Now Assist in AI Search** so users get Q&A **Genius Results** summaries pulled from your KB. ([ServiceNow][46])

**Checkpoint commit**:
`feat(portal-va): enable AI Search in portal & VA; verify results and Q&A cards`

---

# Wrap‑up: What you built

* A compliant **intake‑to‑insight** flow: Portal/VA intake → Task record → Playbook‑guided research → **AI Agent** chat intake path → **AI Search** across KB + external site → **Now Assist Skill Kit** synthesis → auto‑**KB creation** → **link back** → **stage progression** in Playbook.
* Everything version‑controlled (app metadata), with **Update Sets** only for global toggles. ([ServiceNow][26])
* Search and assistance now visible to users in **Workspace**, **Portal**, and **Virtual Agent**. ([ServiceNow][19])

---

## Troubleshooting & tips

* **Plugin missing / access denied**: Verify entitlement and that the plugin is available in **Zurich** (check plugin catalog). ([ServiceNow][20])
* **Studio ↔ Git link fails**: Use HTTPS with a PAT for simplicity; confirm credentials record and branch exist. ([ServiceNow][3])
* **Articles not appearing in search**: Ensure the **Search Profile** includes your KB source; re‑publish profile; check indexing status. ([ServiceNow][36])
* **Web Crawler yields nothing**: Confirm allowed roots/paths and schedule, then run an on‑demand crawl. ([ServiceNow][38])
* **Virtual Agent can’t find content**: Ensure **AI Search** is activated for VA and your profile is assigned. ([ServiceNow][45])
* **Q&A/Genius Results missing**: Verify Now Assist in AI Search is enabled and a Genius Results config is linked and active for your profile. ([ServiceNow][46])
* **Playbook doesn’t show**: Confirm the table is enabled for Playbooks and the component is present in the record page; check stage mapping. ([ServiceNow][6])
* **Skill Kit output not saved**: Use a **UI Action** deployment and either script the record creation or invoke a **Flow** action on completion. ([YouTube][42])

---

## Appendix A — Suggested commit messages (per checkpoint)

* `feat(app): scaffold app (scope x_<org>_deep_research)`
* `chore(git): link to GitHub; add branching plan`
* `feat(data): table extending task + fields (Sponsor, BU, Risk, Stage, KB link)`
* `feat(intake): record producer + catalog/category`
* `feat(process): playbook with Intake/Research/Review/Complete`
* `feat(workspace): lists, dashboard, Playbook panel`
* `feat(ai-agent): intake agent + save record tool`
* `feat(knowledge): KB + search profile`
* `feat(search): web crawler source + linked to profile`
* `feat(skill): Now Assist deep research skill + UI Action + flow`
* `feat(portal-va): enable AI Search in portal & VA`

---

## Appendix B — Governance checklist (show in class)

* **AI Control Tower**: asset inventory, approvals, data‑sharing settings, monitoring, risk/compliance. ([ServiceNow][47])
* **AI Acceptable Use Policy**: human oversight, accuracy review, prohibited uses. ([ServiceNow][11])

---

## Appendix C — Reference links used in this lab (selected)

* **Zurich release notes overview** (confirm features/plugins in Zurich). ([ServiceNow][1])
* **App Engine Studio** (create app). ([ServiceNow][2])
* **ServiceNow Studio** (create app; Studio enhancements). ([ServiceNow][24])
* **Source control (Studio/AES)**. ([ServiceNow][3])
* **Scope & Update Sets** (differences, when to use). ([ServiceNow][23])
* **Extend Task / build table**. ([ServiceNow][4])
* **Record Producers / Catalog & Categories**. ([ServiceNow][5])
* **Playbooks (Process Automation Designer/Workflow Studio)**. ([ServiceNow][6])
* **Workspace Builder / Configurable Workspace**. ([ServiceNow][19])
* **AI Agent Studio / add Record tool / test**. ([ServiceNow][7])
* **Knowledge base & articles**. ([ServiceNow][8])
* **AI Search: profiles, status, publish**. ([ServiceNow][48])
* **External Content connectors / Web Crawler**. ([ServiceNow][38])
* **Now Assist Skill Kit: explore, add Retriever, deploy to UI Action**. ([ServiceNow][9])
* **Enable AI Search in portal & Virtual Agent; Q&A/Genius Results**. ([ServiceNow][10])
* **AI Control Tower** (governance) & **AI AUP**. ([ServiceNow][47])

---

### What to tell executives who will use this app

“Your deep‑research request is **triaged and governed**: intake through a compliant form or chat; staged execution under a **Playbook**; answers are **synthesized** using curated internal KB + approved web sources; results are published as a **Knowledge Article** with traceability back to your task; and all AI usage is **governed centrally**.”

---

If you want, I can also package this as a printable **two‑column lab handout** (Instructor vs Student) or a **slide deck** with the exact screenshots to take on each step.

[1]: https://www.servicenow.com/docs/bundle/zurich-release-notes/page/release-notes/family-release-notes.html?utm_source=chatgpt.com "Zurich release notes"
[2]: https://www.servicenow.com/docs/bundle/zurich-application-development/page/build/app-engine-studio/task/create-app-scratch.html?utm_source=chatgpt.com "Create your application from scratch"
[3]: https://www.servicenow.com/docs/bundle/zurich-application-development/page/build/applications/task/t_LinkAnApplicationToSourceControl.html?utm_source=chatgpt.com "Link an application or application-customization to source ..."
[4]: https://www.servicenow.com/docs/bundle/zurich-platform-administration/page/administer/task-table/concept/c_PlannedTask.html?utm_source=chatgpt.com "Extending the Task table with Planned tasks"
[5]: https://www.servicenow.com/docs/bundle/zurich-servicenow-platform/page/product/service-catalog-management/task/t_DefRecProdInSCat.html?utm_source=chatgpt.com "Create a record producer"
[6]: https://www.servicenow.com/docs/bundle/zurich-build-workflows/page/administer/process-automation-designer/concept/exploring-process-automation-designer.html?utm_source=chatgpt.com "Playbooks"
[7]: https://www.servicenow.com/docs/bundle/zurich-intelligent-experiences/page/administer/now-assist-ai-agents/concept/ai-agent-studio.html?utm_source=chatgpt.com "AI Agent Studio"
[8]: https://www.servicenow.com/docs/bundle/zurich-servicenow-platform/page/product/knowledge-management/task/create-a-knowledgebase.html?utm_source=chatgpt.com "Create a knowledge base"
[9]: https://www.servicenow.com/docs/bundle/zurich-intelligent-experiences/page/administer/now-assist-skill-kit/concept/exploring-now-assist-skill-kit.html?utm_source=chatgpt.com "Exploring Now Assist Skill Kit"
[10]: https://www.servicenow.com/docs/bundle/zurich-platform-user-interface/page/build/service-portal/task/enable-ais-sp.html?utm_source=chatgpt.com "Enable and configure AI Search in Service Portal"
[11]: https://www.servicenow.com/ai-acceptable-use-policy.html?utm_source=chatgpt.com "ServiceNow AI Acceptable Use Policy"
[12]: https://www.servicenow.com/docs/bundle/zurich-build-workflows/page/administer/process-automation-designer/concept/activate-process-automation-designer.html?utm_source=chatgpt.com "Activate playbooks"
[13]: https://www.servicenow.com/community/in-other-news/minute-mondays-for-partners-unlock-your-demo-potential/ba-p/3290393?utm_source=chatgpt.com "Minute Mondays for Partners: Unlock your Demo Potential"
[14]: https://www.servicenow.com/docs/bundle/zurich-application-development/page/build/app-engine-studio/task/install-aes.html?utm_source=chatgpt.com "Installing App Engine Studio"
[15]: https://www.servicenow.com/docs/bundle/zurich-application-development/page/build/servicenow-studio/concept/servicenow-studio-landing.html?utm_source=chatgpt.com "ServiceNow Studio"
[16]: https://www.servicenow.com/docs/bundle/zurich-intelligent-experiences/page/administer/now-assist-ai-agents/task/install-ai-agents-plugins.html?utm_source=chatgpt.com "Install Now Assist AI agents"
[17]: https://www.servicenow.com/docs/bundle/zurich-platform-administration/page/administer/ai-search/task/install-ais-next-experience-app.html?utm_source=chatgpt.com "Install AI Search for Next Experience"
[18]: https://www.servicenow.com/docs/bundle/zurich-intelligent-experiences/page/administer/generative-ai-controller/task/enable-generative-ai-controller-for-virtual-agent.html?utm_source=chatgpt.com "Enable a generative AI capability in Virtual Agent Designer"
[19]: https://www.servicenow.com/docs/bundle/zurich-platform-user-interface/page/administer/configurable-workspace/concept/c_set-up-configurable-workspace.html?utm_source=chatgpt.com "Configurable Workspace"
[20]: https://www.servicenow.com/docs/bundle/zurich-platform-administration/page/administer/plugins/reference/list-of-plugins.html?utm_source=chatgpt.com "List of plugins (Zurich)"
[21]: https://www.servicenow.com/docs/bundle/zurich-intelligent-experiences/page/administer/ai-governance-workspace/concept/ai-governance.html?utm_source=chatgpt.com "AI Control Tower dashboard"
[22]: https://www.servicenow.com/docs/bundle/zurich-intelligent-experiences/page/administer/ai-governance-workspace/concept/configuring-ai-governance.html?utm_source=chatgpt.com "Configure AI Control Tower"
[23]: https://www.servicenow.com/community/it-service-management-forum/extending-tables-from-task/m-p/569653?utm_source=chatgpt.com "Extending tables from task"
[24]: https://www.servicenow.com/docs/bundle/zurich-application-development/page/build/servicenow-studio/task/create-an-application-in-servicenow-studio.html?utm_source=chatgpt.com "Create an application in ServiceNow Studio"
[25]: https://www.servicenow.com/community/developer-advocate-blog/focusing-on-servicenow-studio-enhancements-in-zurich/ba-p/3362918?utm_source=chatgpt.com "Focusing on ServiceNow Studio Enhancements in Zurich"
[26]: https://www.servicenow.com/docs/bundle/zurich-application-development/page/build/app-engine-studio/reference/source-control-operations.html?utm_source=chatgpt.com "Source control operations in App Engine Studio"
[27]: https://www.servicenow.com/community/developer-forum/learning-10-how-to-use-github-repository-create-branches-linkage/m-p/2589047?utm_source=chatgpt.com "Learning#10 : How to use GitHub Repository (Create..."
[28]: https://www.servicenow.com/docs/bundle/zurich-application-development/page/build/system-update-sets/reference/get-started-update-sets.html?utm_source=chatgpt.com "Get started with update sets"
[29]: https://www.servicenow.com/docs/bundle/zurich-servicenow-platform/page/product/service-catalog-management/concept/c_ServiceCatalogCategories.html?utm_source=chatgpt.com "Service catalog categories"
[30]: https://www.servicenow.com/docs/bundle/zurich-application-development/page/build/app-engine-studio/task/add-a-record-producer.html?utm_source=chatgpt.com "Add a record producer"
[31]: https://www.servicenow.com/docs/bundle/zurich-platform-user-interface/page/build/service-portal/concept/ai-search.html?utm_source=chatgpt.com "AI Search in Service Portal"
[32]: https://www.servicenow.com/docs/bundle/yokohama-intelligent-experiences/page/administer/now-assist-ai-agents/task/add-database-op-ai-agent.html?utm_source=chatgpt.com "Add a record operation to an AI agent"
[33]: https://www.servicenow.com/docs/bundle/zurich-intelligent-experiences/page/administer/now-assist-ai-agents/task/test-ai-agent.html?utm_source=chatgpt.com "Test an AI agent"
[34]: https://www.servicenow.com/ai/what-is-ai-agents-vs-chatbots.html?utm_source=chatgpt.com "AI Agents and Chatbots: What's the Difference?"
[35]: https://www.servicenow.com/docs/bundle/zurich-servicenow-platform/page/product/knowledge-management/task/create-knowledge-article.html?utm_source=chatgpt.com "Create a knowledge article"
[36]: https://www.servicenow.com/docs/bundle/zurich-platform-administration/page/administer/ai-search/task/create-search-profile-ais.html?utm_source=chatgpt.com "Create a search profile"
[37]: https://www.servicenow.com/docs/bundle/zurich-platform-administration/page/administer/ai-search/concept/external-content-ais.html?utm_source=chatgpt.com "Indexing and searching external content in AI Search"
[38]: https://www.servicenow.com/docs/bundle/yokohama-platform-administration/page/administer/ai-search/concept/webcrawler-external-content-connector.html?utm_source=chatgpt.com "Webcrawler external content connector"
[39]: https://www.servicenow.com/docs/bundle/zurich-platform-administration/page/administer/ai-search/concept/indexed-sources-ais.html?utm_source=chatgpt.com "Indexed sources in AI Search"
[40]: https://www.servicenow.com/docs/bundle/zurich-intelligent-experiences/page/administer/now-assist-skill-kit/task/add-retriever.html?utm_source=chatgpt.com "Add a retriever"
[41]: https://www.servicenow.com/community/now-assist-articles/now-assist-skill-kit-knowledge-article-coach/ta-p/3053575?utm_source=chatgpt.com "Now Assist Skill Kit - Knowledge Article Coach"
[42]: https://www.youtube.com/watch?v=_RDGeDyq9-8&utm_source=chatgpt.com "Now Assist Skill Kit: Deploy to UI Action"
[43]: https://www.servicenow.com/docs/bundle/zurich-build-workflows/page/administer/flow-designer/reference/create-update-record-designer.html?utm_source=chatgpt.com "Create or Update Record action"
[44]: https://www.servicenow.com/community/now-assist-articles/now-assist-skill-kit-tool-and-deployment-options/ta-p/3284803?utm_source=chatgpt.com "Now Assist Skill Kit – Tool and Deployment Options"
[45]: https://www.servicenow.com/docs/bundle/zurich-conversational-interfaces/page/administer/virtual-agent/task/activate-ai-search-va.html?utm_source=chatgpt.com "Activate AI Search for Virtual Agent"
[46]: https://www.servicenow.com/docs/bundle/zurich-platform-administration/page/administer/ai-search/reference/now-assist-ais.html?utm_source=chatgpt.com "Now Assist in AI Search"
[47]: https://www.servicenow.com/docs/en-US/bundle/zurich-intelligent-experiences/page/administer/ai-governance-workspace/concept/ai-control-tower-landing.html?utm_source=chatgpt.com "AI Control Tower"
[48]: https://www.servicenow.com/docs/bundle/zurich-platform-administration/page/administer/ai-search/concept/defining-search-profiles-ais.html?utm_source=chatgpt.com "Search profiles in AI Search"
