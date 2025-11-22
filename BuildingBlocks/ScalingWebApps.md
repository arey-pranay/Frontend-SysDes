## 1. Scaling Web Applications: Architectures 

Exploring the three main architectural approaches: **Monolithic**, **Micro Frontends**, and **Modular Monolith**.

### 1.1 Defining Frontend Scaling

Unlike backend scaling (vertical or horizontal), which manages concurrent user traffic, frontend scaling is primarily an **organizational tool**.

* **Frontend Scaling Definition:** The ability of an application to allow multiple developers or teams to work simultaneously on a large and complex product, facilitating the addition of changes and deployment of apps.
* **Architecture vs. Design:**
    * **Architecture** includes high-level concerns related to the overall structure, communication, and coordination of multiple applications or systems.
    * **Design** deals with the internal structure and organization of code within a single application (e.g., MVC patterns).

### 1.2 Monolithic Architecture

A monolithic architecture builds an application as a **single, self-contained unit** that is tested and deployed as a whole.

| Pros | Cons |
| :--- | :--- |
| **Easy Deployment** (single unit) | **Slower Development Speed** as the application and team grow |
| **Easy Development** in early stages/MVP | **Poor Scalability** (cannot separate the single unit) |
| **Simplified Testing** (testing the application as a whole) | **Reliability** (error in one module can affect the entire application) |
| **Easy Debugging** (full stack trace is visible) | **Barriers to Technology Adoption** (changes affect all parts) |
| **Use Cases:** Small projects, prototyping/MVP, projects with a small team | **Anti-Use Cases:** Large-scale enterprise applications, projects requiring different technologies/scaling, projects with many developers |

### 1.3 Micro Frontends (MFE)

Micro Frontends are an architectural style where **independently deliverable** frontend applications are composed into a greater whole. The core idea is to apply the microservices concept to the frontend.

* **Key Concept: Clear Boundaries** MFE's separation of responsibilities among team members facilitates a more flexible deployment process and faster feature delivery. * **Division Approaches:**
    * **Vertical Slices (by Domains/Pages):** Most common for web applications (e.g., calendar app).
    * **Horizontal Slices (by Components):** Better for applications with many identical pages (e.g., e-commerce sites like Amazon).

| Pros | Cons |
| :--- | :--- |
| **Incremental Upgrades** (rewrite only one part) | **Potential Performance Issues** (increased bundle size with multiple technologies) |
| **Independent Deployment** (reduces risk and time) | **Lack of Communication** (risk of code duplication) |
| **Simpler Decoupled Codebase** | **More Complex Architecture** and deployment process |
| **Autonomous Teams** and **Reduced Cognitive Load** | **UX/UI Inconsistency** risk |
| **Use Cases:** Big projects with multiple teams, need for independent development/release cycles, integrating new technologies/features, cross-functional teams | **Anti-Use Cases:** Small projects, small/tightly integrated teams, limited resources/budget, lack of automation |

#### MFE Implementation Approaches (Composition)

The core challenge is collecting these independent modules to work as a whole on the user device:

* **Server-Side Template Composition:** Builds the application on the server using templates and URL-based HTML fragment files (used when the site already uses Server-Side Rendering).
* **Client-Side/Runtime Integration:**
    * **Iframe:** Provides **good isolation**, but leads to difficult communication, performance degradation, and accessibility issues.
    * **JavaScript:** Offers **better performance and easier communication**, but lacks good module isolation.
    * **Web Components:** Uses custom elements to provide good **style isolation** via the Shadow DOM, but has worse browser support.
    * **Webpack Module Federation:** Allows synchronous/asynchronous loading of JavaScript chunks and, crucially, permits **library sharing** across MFEs.
* **Build-Time Integration:** Rejected as it requires recompiling the entire application when any micro frontend changes, violating the idea of independence.

### 1.4 Modular Monolith

This architecture combines the benefits of modular design with the simplicity of a monolithic architecture.

* **Structure:** A single application built and deployed as a single unit (like a monolith), but the code is divided into **loosely coupled, independent modules** with clear boundaries.
* **Role:** Can be a **transitional stage** to micro frontends or a permanent architectural style.
* **Internal Working (Application Shell):** A framework that provides the overall layout, navigation, and basic functionality (header, footer, common models) and controls how the features/modules integrate into the page.
* **Key Rules:**
    1.  Each module must have **well-defined input and output**.
    2.  Modules must have **minimum side effects**.

| Advantages | Challenges/Anti-Use Cases |
| :--- | :--- |
| Features are encapsulated; allows for **reusable code** and easy refactoring | Cannot easily use different technologies or languages across modules |
| Better organized dependencies | Cannot deploy modules independently (still a single unit) |
| Less complex than Micro Frontends (single deployment process) | **Anti-Use Cases:** Highly distributed teams, need for independent deployment/scaling, technology diversity is required |
| **Use Cases:** Cross-functional teams, small-to-medium-sized applications, when **time to market is crucial** and operational simplicity is desired | **Anti-Use Cases:** Simple applications or MVP (a simple monolith is better) |

***

## 2. Scaling Web Applications: Repositories and Case Studies 

The organization of code in repositories and applies the architectural concepts to two case studies.

### 2.1 Monorepositories vs. Poly Repositories

This section discusses how multiple projects are managed within a Version Control system.

* **Monorepository:** A single repository that contains **multiple projects or components** (e.g., front end, backend, shared libraries), simplifying dependency management.
    * *Note:* A monorepo is a *repository management strategy* and is **not** the same as a monolithic application.
* **Poly Repositories (Multi-repos):** A collection of **separate repositories**, each containing a single project or component, giving flexibility and autonomy to individual teams.

#### Monorepos for Micro Frontends

While polyrepos are a logical option for micro frontends (due to separate deployment), a monorepo may be considered for specific benefits:

| Monorepo Advantages | Monorepo Problems |
| :--- | :--- |
| **High Visibility** (easy to view all code) | **Fragile Modules** (changing shared code affects many components) |
| **Code Sharing** (easy reuse of common libraries) | **Merge Conflicts** (frequent on large codebases) |
| **Standardization** (easier to enforce policies/best practices) | **Security Concerns** (harder to separate access rights by team) |
| **Discoverability** (single view of all branches/modifications) | **Large Size/Slow Indexing** (requires special tools to manage local code) |

**Monorepo Use Cases (for MFE):**
1.  **Tightly Integrated Frontends** that share dependencies.
2.  When **code sharing is very important**.
3.  If all modules **must be released together**.

### 2.2 Versioning in Monorepos

When a monorepo contains multiple applications, there are three versioning approaches:

1.  **Semantic Release Versioning:** A fully **automated** method that determines the next version number, creates release notes, and publishes the package.
2.  **Manual Versioning:** Developers and maintainers are responsible for manually or semi-manually choosing the next version.
3.  **Global Versioning:** All packages and services have the **same version number** (one change increases the version for all others).
    * *Compromise:* This approach is **very expensive for huge projects** because upgrading one service forces an upgrade for everyone else in the repository.

### 2.3 Case Study: Admin Dashboard

This example is for a large application with **multiple, logically separated services** (Users, Metrics, Budget, Groups, Settings), where services are maintained by separate teams and have separate deployment processes.

* **Architectural Decision:**
    * Monolith is rejected immediately due to the application's size and the requirement for separate, independent team management/deployment.
    * **Modular Monolith** is better, as it scales with team/app size and provides separation of responsibilities.
    * **Micro Frontends** are the final choice if high model independence and separate deployment are crucial.
* **Repository Decision (for Micro Frontends):**
    * **Poly Repositories** are chosen. The teams work independently, and the advantages of a monorepo (easy sharing/visibility) are **not crucial enough** to justify the added resources required for monorepo orchestration.

### 2.4 Case Study: To-Do Application

This example is a **simple web application** with one main feature, managed by a small team of 2-3 developers.

* **Architectural Decision:**
    * **Monolithic Architecture** is the recommended start, as it is cheap, easy to develop, test, and debug for a small application and team.
    * **Modular Monolith** is only recommended if there are clear plans, time, and resources to make the application bigger in the future.
    * Micro Frontends are rejected because they would over-complicate a simple application.
* **Repository Decision (Full-Stack Monorepo):**
    * **Monorepository** is recommended if a single team is working on both the front end and backend (a common setup for small full-stack teams using the same technology stack, like TypeScript).
    * This choice provides high code discoverability, faster development, and enables the use of **TRPC (TypeScript RPC)** for easier client-server communication.

***

## 3. Architecture Decision Framework

The conclusion provides a framework to determine the appropriate architecture based on project and team size. 
The framework suggests starting with a **Monolith** and a single repository. If control questions (related to size, complexity, and team structure) are answered **"Yes"**, one moves to the next level:

* **Level 1:** Monolith → **Modular Monolith**
* **Level 2:** Modular Monolith → **Micro Frontends**
* **Level 3 (for MFEs):** Micro Frontends → Decide between **Monorepository** or **Poly Repositories**

The best approach is the one that is most suitable for the specific project's current and future requirements, not simply the most popular technology.

* You can find these videos at:
    * [https://youtu.be/2qtgegNSUoE](http://www.youtube.com/watch?v=2qtgegNSUoE)
    * [https://youtu.be/RRcb3ZUOe_w](http://www.youtube.com/watch?v=RRcb3ZUOe_w)

 

http://googleusercontent.com/youtube_content/1
