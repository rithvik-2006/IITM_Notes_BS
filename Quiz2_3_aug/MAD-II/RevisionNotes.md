Here is a detailed week-wise revision note of the concepts, drawing on the information in the provided sources:

### Week 1: Foundations – API Interaction and Asynchronous JavaScript

This week focuses on the fundamental mechanisms for connecting frontend and backend systems, and how to manage operations that don't complete immediately.

*   **Using APIs and Separation of Concerns**: A core principle is the **separation of concerns** between the backend and frontend. The **backend** is responsible for managing data models, while the **frontend** manages the user interface (UI). There must be a **clean interaction mechanism** to separate the two.
    *   **System-level design** requires a separate backend and frontend.
    *   The **backend should never know what the UI looks like** and should avoid direct calls to HTML template rendering.
    *   Data output from the backend should be in **neutral formats**, with **JSON preferred**. Data input is typically through form data or URLs.
*   **Fetch Mechanism**: This describes how to retrieve data from a backend, primarily through **URL-based APIs**.
*   **Rendering Mechanism**: The frontend can either be **rendered on the server and pushed** to the client, or **implemented in the browser and pull data**.
*   **Asynchronous Operations**: Fetching data is an asynchronous operation because it depends on factors outside server control, such as **latency to the backend, network load, and disruptions**.
    *   These operations **should not make the browser hang**.
    *   An asynchronous operation starts in the background, waits for results, and then updates.
    *   Common ways to achieve asynchronous behaviour include **Events and Callbacks**, **Promises**, the **fetch API**, and **Axios**.
*   **Callbacks**: When a function takes a long time to execute, direct execution (`let result = doSomething()`) blocks the entire JavaScript interpreter, causing the browser to hang as JS is single-threaded. Instead, you **start the function and tell it to call you back when done**.
    *   Callbacks can also be **higher-order functions** that call other functions based on conditions.
*   **Events**: Events, like a button's `onclick` handler, are functions that are not explicitly "called" but are invoked when a particular event occurs, as specified to the DOM.
*   **JavaScript Event Loop and Call Stack**: The **call stack executes all operations in the present scope sequentially**. After that, it continuously checks a "callback queue" for new functions to execute. Events (e.g., from timeouts) can be pushed to this queue later.
*   **Promises**: Offers an alternative syntax to callbacks (e.g., `doSomething().then(successCB, failureCB)`). Promises provide **additional guarantees on behaviour** and make **chaining easier**. The `fetch()` API is implemented using Promises.
*   **Fetch API**: A JavaScript API available since ES6 for asynchronously fetching URLs. It is built into most browsers, with "polyfills" for backward compatibility.
*   **Axios**: A custom API library with functionality similar to `fetch`. It offers good backward compatibility across browsers and works on Node.js.
*   **Concurrency vs. Parallelism**:
    *   **Concurrent** means multiple operations can be in process simultaneously, but might execute in a time-multiplexed manner (not literally at the same instant).
    *   **Parallel** means multiple concurrent operations are actually physically executing at the same time.
    *   Parallelism requires concurrency, but not vice versa. Asynchronous operations introduce the notion of concurrency. Web workers and timers can enable parallel execution.

### Week 2: Frontend Development – Storage, Validation, and Components

This week delves into managing data on the client-side, ensuring data quality, and structuring frontend applications effectively using components.

*   **Persistent Storage**: Vue data is lost or reset on page reload, so **persistent state without the need for a server** is crucial.
    *   **Why?**: It enables **offline work** without a server connection, supports **simple apps** that don't need server requests, and allows for **local configuration**.
    *   **How?**:
        *   **Cookies**: Can be used for simple data, but have **limited storage** and are usually session temporary.
        *   **localStorage**: An API for saving **simple key-value entries**. It persists across browser restarts. Complex objects should be stringified using JSON.
        *   **sessionStorage**: Similar to localStorage, but **storage ends with the session** (browser restart) and offers more storage than cookies (~5MB).
        *   **IndexedDB**: A **transactional, object-oriented, JS-based database system** for storing and retrieving objects with a key.
*   **Form Validation**: Checking whether data entered in a form meets certain criteria.
    *   **Simple checks can be done in the browser** (e.g., text field content, empty fields).
    *   **Server-side checks are essential for security** in many cases, though they are more costly and increase server load.
    *   **Vue and Validation**: Vue's **data binding and reactivity** facilitate easy updates of the DOM and selective display of error messages using `v-if` or `v-show`. `v-model` connects form fields to JavaScript variables. `preventDefault()` can stop normal form processing unless checks are successful, connected as a submit event handler.
    *   **Custom Validation**: Examples include custom email checks or verifying overall conditions (e.g., numbers adding up). To use custom validation, you often need to **prevent regular form validation** using `novalidate=true` in the form definition.
*   **Single File Components (SFCs)**:
    *   **Need for SFCs**: Addresses issues like **global namespace conflicts**, difficulties with **string templates** in editors, lack of **CSS block scoping** (leading to only global CSS), and the absence of a **build step** for backward compatibility or tools like Babel.
    *   **SFC Structure**: Promotes **separation of concerns** by grouping semantic content (HTML), presentation (CSS), and logic (JS) within a single `.vue` file, without requiring separate files for each.
    *   **Extra Tooling**: A **compilation step is needed** as JavaScript cannot directly read `.vue` files. Tools like **Webpack, ESBuild, Vite** are used to convert `.vue` files into `.js`, `.css`, and `.html`. **npm (Node Package Manager)** is used for systematically managing JavaScript modules and importing new ones.
*   **Testing Vue Applications**:
    *   **Unit testing**: Components are good units to test, often by "mounting" them into a testing DOM.
    *   **End-to-End (E2E) testing**: Involves testing the full application, including the backend.
    *   **Cross-browser testing**: Checks compatibility with older browsers, though diminishing returns may apply.
    *   **Test Mechanisms**: Involve setting up **fixtures** (prepared data) and using **test suites** to collect tests. Tools like **Mocha, Chai, and Jest** provide supporting functions for testing element presence/absence.

### Week 3: Advanced Vue.js Concepts – State Management & Routing

This week explores how to manage complex application state across multiple components and handle navigation within a single-page application.

*   **State Management**:
    *   **Core Idea**: The UI is a function of its state (`UI = f(State)`).
    *   **State Management Pattern**:
        *   **State**: The "source of truth" about the app's internals.
        *   **View**: A declarative mapping that is a function of the State.
        *   **Actions**: Input from the view that causes the state to change.
    *   This forms a **one-way data flow**. This concept primarily addresses **UI state**, not necessarily system state, and can coexist with MVC on the server.
*   **Challenges with Multiple Components**:
    *   **Hierarchy (Parent-Child)**: Information passes **down through props** (parent to child) and **up through events** (child to parent). Directly invoking parent functions or modifying parent data from a child is not desirable as it breaks clean separation and makes debugging harder.
    *   **Sibling Components**: Multiple views may depend on the same state, and actions from different views might modify it. Information needs to **pass events up to a common parent** and then **pass props back down to the destination**.
    *   **Global Variables**: While directly accessible and modifiable by all components, they make it **difficult to track who modified what**, leading to debugging and maintenance issues.
*   **Vuex (Solution for Restricted Global Access)**: Vuex is a state management library for Vue.js that introduces a **globally accessible "store"**.
    *   **Concepts**: It has a **single shared state object** (often tree-structured for nesting). Components can still have local state not visible outside.
    *   **Getters**: Act as **computed properties on shared state objects**.
    *   **Access**: `this.$store` is available within all components.
    *   **Mutations**: The **only way to change state** is by "committing" a mutation. You **never directly update a variable**; always call a method that updates. Mutations **must be synchronous**.
    *   **Debugging Support**: Mutations are **recorded in devtools**, enabling "**time travel debugging**" by retracing steps or replaying mutations to reproduce system state at any point.
    *   **Actions**: Can contain **asynchronous functionality**. They **do not change state directly**; instead, they **commit mutations**. Actions allow for complex logic, including async calls, before committing the synchronous state changes via mutations.
*   **Similar State Management Ideas**:
    *   **Flux (Facebook/React)**: Features unidirectional data flow with a **store** (maintains state), a **dispatcher** (sends actions), and **views** (React components updating based on state).
    *   **Redux**: Based on three principles: a **single source of truth**, **state is read-only** (explicitly return a new state object for easier tracing), and **changes are made by "pure" functions** with no side effects.
    *   **Elm Architecture**: A functional language approach with a **Model** (app state), **View** (state to HTML), and **Update** (state update based on messages).
*   **Routing**: In Vue-like frameworks, parts of an application can correspond to components instead of full HTML pages, leading to an "application" rather than just a sequence of pages.
    *   **Mechanism**: Uses components like `<router-link>` for navigation and `<router-view>` as the outlet for the component matched by the route. Routes are defined as mappings from paths to components.
    *   **Advantages**: **Clickable links transition between components without needing actual HTML pages**. Clicks are handled by client-side JavaScript, avoiding server hits. It allows replacing parts of an existing page, limiting full refreshes.
    *   **Dynamic Routes**: Paths can include dynamic segments (e.g., `/user/:id`).
    *   **Impact on Reactivity**: Navigating between dynamic routes (e.g., `/user/one` to `/user/two`) may reuse the same component and not trigger reactive updates. This can be handled by **installing a watcher on the `$route` object**.
    *   **More Features**: Includes **nested routes**, **named routes** (for readability), **named views** (associating multiple components with different `router-view`s), and **HTML5 history mode** (pushing URLs into browser history for natural navigation).
    *   **Why a Router?**: Routes are handled in JS, associated with components, and enable navigation within a single app, with server updates only on demand. This is a key part of **Single Page Applications (SPAs)**.

### Week 4: Web Application Architectures – SPAs, PWAs, and API Design

This week covers advanced web application paradigms that enhance user experience and explores the principles of designing effective APIs.

*   **Single Page Applications (SPAs)**:
    *   **Concept**: Dynamic websites that **rewrite the current page instead of re-rendering with a fresh load**.
    *   **Why SPAs?**: Provide a **faster user experience** with quicker transitions and page loads, feeling more like native apps (e.g., Gmail, Facebook, Google Maps).
    *   **How They Work**:
        *   Historically: Transferring all HTML in one request and using CSS to selectively display (large load time) or browser plugins (overhead, compatibility).
        *   **Modern Approach**: Primarily use **AJAX and fetch APIs** to asynchronously fetch and update parts of the DOM. This requires powerful rendering engines.
        *   **Async Transfer Models**: Websockets and server-sent events provide more interactivity but can be harder to implement.
    *   **Impact on Server**:
        *   **Thin server**: Only stateless API responses; all state and updates handled by JS on the browser.
        *   **Thick stateful server**: Server maintains complete state; client requests result in full async load but only partial page refresh.
        *   **Thick stateless server**: Client sends detailed info; server reconstructs state, generates response, and provides partial page refresh; scales more easily.
    *   **Running Locally**: SPAs can be executed from `file://` URIs after initial download. Updates require reloading from the server. WebStorage APIs are useful for local data.
    *   **Challenges**: **Search engine optimization (SEO)** (links often local or use `#`), **managing browser history** (can confuse users), and **analytics** (tracking popular pages not possible on local load).
    *   **SPA with Vue**: Complex application logic often involves a backend on the server, frontend state variables managed by **Vue + Vuex**, and navigation/page updates handled by **Vue Router** with a component-based approach.
*   **Progressive Web Apps (PWAs)**:
    *   **Relationship with SPAs**: Often confused; PWAs are **very often implemented as SPAs**, but **not all SPAs are PWAs** (may lack offline, web workers) and **not all PWAs are SPAs** (rendering might be server/web worker based).
    *   **Web Workers**: Scripts started by web content that **run in the background**. They can perform computations and fetch requests, sending messages (events) back to the main web content.
    *   **Characteristics**: **Installability** (Web Manifest metadata), **WebAssembly** (faster compiled operations), **Storage** (Web storage APIs), and **Service Workers** (for offline caching and network control).
*   **Web Apps vs. Native Apps**:
    *   **Native**: Compiled with SDKs (e.g., Flutter, Swift), best access to underlying OS, minimal restrictions with OS support, native look and feel (but not uniform across devices).
    *   **Web Apps**: "Write once, run anywhere," use simple technologies with low barrier to entry, and rely on evolving standards.
*   **Designing APIs**: The purpose of an API is **to be used to build applications**, so they should be designed with developers in mind.
    *   **Remote Procedure Call (RPC)**: Connects to a remote server to retrieve data based on function arguments.
    *   **Data-oriented approach**: Defines **Entities** (e.g., Students, Courses), **Actions** (Add, Edit, Delete), and **Summaries** (List, GPA).
    *   **Complexity**: Simple URL structures (`/getListOfStudents`, `/getStudent?id=xyz`) can become complex and hard to remember/document (`/getTopStudents`, `/createStudentAndAddToCourse`).
    *   **Use Conventions**:
        *   **URL Conventions**: **Nouns in URLs are good, verbs are bad** (e.g., `/students`, `/student/123` instead of `/create/student`). Verbs should be in the HTTP method. APIs should have **well-known URLs** for discoverability and **permalinks** (unique IDs). Structured URLs (e.g., `/course/123/students`) are generally preferred over flatter ones (`/course-123-students`) for developers.
        *   **HTTP Verbs**:
            *   **GET**: Read data/lists; cacheable as data is in URL.
            *   **POST**: Create new objects/data; generally not cacheable. Can also be used for reading.
            *   **PUT / PATCH**: Update existing data; **PATCH is preferred for incremental updates**.
        *   **Output Formats**: **JSON + extensions is the preferred format** currently due to its simplicity and human readability, despite limited data types compared to XML.
        *   **Included Links**: API responses can **include additional links** pointing to related useful information, making discovery more flexible than just documentation.
        *   **Authentication**: **Token-based authentication** is common, with **OAuth2** being a good standard and JWT (JSON Web Token) as another variant. Use standard techniques where possible.
    *   **Summary**: Good API design requires experience and is **mostly based on conventions**, not rigid rules, but conventions are important. APIs are **designed for developers, not end users**.

### Week 5: Modern API & Markup Alternatives, and Web Application Ecosystems

This week explores alternative API paradigms to REST, examines different markup approaches, and concludes with an overview of modern web application architectures like JAMstack.

*   **Problems with REST**:
    *   Many "RESTful" APIs violate some REST constraints. REST is an **architecture style, not an API design document**, allowing for flexibility.
    *   **"Chatty" APIs**: Often require **multiple requests to fetch data for a single view** (e.g., first student details, then courses, then course details, etc.).
    *   **Specific requests**: REST APIs permit specific types of requests, but are **not a general "Query language"**. You cannot specify "what is needed" declaratively; you must break it into "how to get the result" steps.
*   **GraphQL**:
    *   **Why GraphQL?**: REST-based APIs are endpoint-based, requiring complex data requests to be constructed with multiple GETs. Modern sites need inputs from multiple sources, requiring **simultaneous query and fusion of data**. GraphQL supports **declarative programming** (what to do, not how to do it), improving developer experience for data retrieval.
    *   **How it Works**: An **engine on the server side handles requests**, translating complex queries into (multiple) data requests to the server, collecting and filtering data, and **responding to the client only with the data needed**. It acts as a layer between the client and server.
    *   **Features**: It's a **query language** used over HTTP (usually POST) to send complex queries in the POST body. It has a **Type system** to specify query item types (String, Int, Collection) and relations, which helps catch errors. It supports **API versioning** by adding/deprecating functionality as needed, often without needing new API versions.
    *   **Mutation**: Supports Create/Update/Delete operations, generalized as any query that alters the underlying data store.
    *   **Tools**: **Apollo server** helps build GraphQL systems, connecting to multiple backends and defining resolvers. Explorers (e.g., Google, GitHub, graphql.org) allow dynamic construction and testing of queries.
    *   **Summary**: GraphQL is an **extension of core API concepts** that integrates with multiple data sources and uses a **complex query language** to filter data at the server and send only relevant data to the client. It **does not necessarily reduce server complexity**; the server may even become more complex.
*   **Markup Alternatives**:
    *   **Why HTML?**: It's a general markup for text, a "living standard" adaptable to new functional requirements, and extensible (WebComponents, JS). It focuses on "semantic" content, leaving styling to CSS.
    *   **Why Not HTML?**: It's not ideally designed for structured data communication (JSON is used but also limited, XML is better but overkill). It's relatively verbose for humans.
    *   **Text-based Markup**: Examples include **Markdown, reStructuredText (RST), AsciiDoc**. These use inline markers (e.g., `**strong**`, `# HEADING`).
        *   **Why Text?**: Uniform character representations (ASCII, Unicode) make it **write once, read anywhere** and guard against obsolescence. It's compact and easy for humans to read.
        *   **Why Not Text?**: Hard to encode complex "structure," ambiguity is possible in parsing, and it's more focused towards English/Roman alphabet.
    *   **Compile / Convert**: **Systematic conversion between markup formats** is possible, especially between structured languages. **Pandoc** is a versatile tool for converting between formats.
    *   **Mixed Functionality**: Programs can be mixed with documentation (Web/Weave, Doxygen). Frameworks like **JSX and Vue mix JS with templates and HTML structure**.
*   **JAMstack (JavaScript, APIs, Markup)**:
    *   **What an App Needs**: A **Data store** (accessed via APIs like SQL, NoSQL, GraphQL), a **User Interface** (Vanilla HTML + forms or JavaScript for interactivity), and **Business logic** (backend computation in Python, Go, NodeJS, or frontend in JS).
    *   **Content Management Systems (CMS)**: Manage data manipulation independently of the UI (e.g., CRUD for posts, user management). **WordPress** handles both backend data storage and frontend templating, and provides an API that can be used to build a CMS without its traditional frontend.
    *   **Static Site Generators (SSGs)**: Tools like **NextJS, NuxtJS, Gatsby** (JS-based, for interactive sites) and **Jekyll, Hugo** (text-oriented, for blogs/home pages).
        *   **Why SSGs?**: Servers can focus on delivering content. Static files are **faster to fetch**, and "compile time" optimization reduces file transfer. This improves "**First Contentful Paint**" as pure HTML transfers and displays quickly.
    *   **JS Hydration**: Static HTML is transferred from the server without interactivity. **JavaScript then "hydrates" the HTML with event handlers** to inject interactivity after initial rendering. This is delayed but still fast enough, combining speed and interactivity.
    *   **JAMstack as the Pinnacle**: This approach handles storage, logic, and presentation effectively. **APIs are flexible enough for any backend**, **markup is easy to change or compile**, and **JavaScript is powerful enough** to emulate other behaviours. It is general enough to extend with new APIs until performance issues arise.