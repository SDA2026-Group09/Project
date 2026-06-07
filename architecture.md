# Processing 4 Architecture Analysis Report

This repository contains a comprehensive architectural analysis of the Processing 4 software system, evaluating its structural patterns, component breakdowns, SOLID design principles, and alignment with Clean Architecture concepts.
---

## 1. Context Diagram
The context diagram presents Processing 4 as a single software system and shows how it interacts with external actors and systems. At this level, the goal is to understand the system boundary rather than its internal implementation.
The primary actor is the User, who interacts with Processing to create sketches, animations, visual prototypes, and educational projects. Processing was originally designed for artists, designers, students, and beginners who need a simple programming environment for creative coding. The user writes code through the graphical interface and executes sketches directly from the application.
Processing communicates with three external systems. The first is the Operating System, which provides file management, hardware access, window management, and graphics resources. Processing relies on the operating system whenever it needs to read sketch files, store preferences, create windows, or access rendering hardware.
The second external system is the Processing Server. This server hosts contributed libraries, tools, modes, examples, and software updates. Through HTTPS communication, Processing can retrieve catalogs of available packages and download them for local installation. This allows the system to be extended without modifying the core source code.
The third external system is an External IDE. Modern development environments such as Visual Studio Code or IntelliJ IDEA can interact with Processing through command-line tools and language services. This enables developers to use Processing outside its default development environment while still benefiting from its compilation and execution capabilities.
The context diagram shows that Processing acts as a bridge between users, operating-system resources, online repositories, and external development tools. Although users experience Processing as a simple educational environment, the system actually coordinates several external services behind the scenes.

![Context Diagram](./images/context-diagram.png)

---

## 2. Container Diagram
The container diagram decomposes Processing into four major containers: the PDE, Java Mode, Sketch Process, and Contribution Manager.
The PDE (Processing Development Environment) is the graphical application used by developers. It contains the code editor, menus, toolbar, console, and project management features. Users interact directly with the PDE when creating and running sketches. However, the PDE itself does not compile or execute code. Instead, it delegates these responsibilities to specialized containers.
The Java Mode container implements Processing's Java-based programming language. It is responsible for preprocessing source files, compiling code, launching sketches, debugging applications, and performing background error checking. Java Mode acts as the execution coordinator for Processing sketches and provides most of the language-specific functionality.
When a user runs a sketch, Java Mode launches the Sketch Process, which is a separate Java Virtual Machine responsible for executing the compiled application. This separation improves reliability because crashes occurring inside a sketch do not directly affect the PDE. The Sketch Process contains the runtime environment, including rendering engines, animation management, event handling, and resource loading.
The fourth container is the Contribution Manager. This component manages installation, updating, and removal of libraries, tools, examples, and programming modes. It communicates with the Processing Server through HTTPS and stores downloaded packages locally using operating-system services.
The relationships between these containers demonstrate a clear separation of responsibilities. The PDE focuses on user interaction, Java Mode handles language processing, the Sketch Process executes code, and the Contribution Manager handles extensions. This modular architecture improves maintainability because each container has a well-defined purpose.
Another important aspect of the design is process isolation. Instead of executing user sketches directly inside the PDE, Processing launches a dedicated runtime process. This reduces coupling between development tools and runtime execution while improving fault tolerance. If a sketch crashes, the IDE can continue operating normally.
Overall, the container architecture demonstrates a strong separation between interface concerns, execution concerns, runtime concerns, and package-management concerns.

![Container Diagram](./images/container-diagram.png)

## 3. Component Diagrams and SOLID Violations

### PDE Component Diagram
The PDE contains several components that together provide the user experience. The Application Core acts as the central coordinator and manages editor windows, user preferences, active sketches, and mode selection.
The UI Layer contains graphical components such as the editor, toolbar, and console. It handles all interactions performed by the user.
The Theme System manages fonts, icons, colors, and visual resources. By separating appearance from functionality, the architecture allows the interface to be customized more easily.
The Mode Host is responsible for communicating with language-specific modes such as Java Mode. It allows the PDE to support multiple programming languages without changing the editor itself.
The Tools Host provides support for plugins and external tools. This component improves extensibility because new functionality can be added independently from the core system.
Finally, the Platform Layer acts as an operating-system abstraction. It provides platform-specific services while shielding the rest of the application from implementation details.

![PDE Component Diagram](./images/pde-component-diagram.png)

### Java Mode Component Diagram
Java Mode contains the components responsible for transforming Processing code into executable applications.
The Java Mode Coordinator acts as the entry point and controls the execution workflow. It coordinates preprocessing, compilation, debugging, and execution.
The Preprocessor converts Processing syntax into valid Java source code. This allows users to write simplified sketches while still relying on Java as the execution platform.
The Compiler translates generated Java source code into bytecode and reports compilation errors.
The Runner launches the Sketch Process as an isolated Java Virtual Machine and manages its lifecycle.
The Error Checker performs background analysis while the user types and continuously reports syntax errors.
The Debugger provides debugging capabilities such as breakpoints and variable inspection.
The Tweak Mode allows runtime modification of certain values without requiring recompilation.
The LSP Server exposes code intelligence services to external editors through the Language Server Protocol.
Finally, the CLI enables Processing projects to be compiled and executed without opening the PDE.

![Java Mode Component Diagram](./images/java-mode-component-diagram.png)

### Sketch Process Component Diagram
The Sketch Process contains the runtime environment used to execute sketches.
The central component is PApplet, which provides the programming API used by developers. Every sketch extends this class and gains access to drawing functions and event callbacks.
The Animation Loop repeatedly executes setup() and draw() methods while maintaining the target frame rate.
The Window Manager (PSurface) handles windows and user input while interacting directly with the operating system.
The Render Engine (PGraphics) performs all graphics operations and supports different rendering backends.
The Resource Management component loads and manages images, fonts, and other assets required by the sketch.
Together, these components provide the complete runtime environment for graphical applications.

![Sketch Process Component Diagram](./images/sketch-process-component-diagram.png)

---

### SOLID Violations Analysis
* **Single Responsibility Principle (SRP)**
The Application Core in the PDE also partially violates SRP because it manages editor windows, preferences, sketches, and mode initialization.
The Java Mode Coordinator represents another example. It coordinates preprocessing, compilation, execution, debugging, and tweak mode functionality. While practical, this concentration of responsibilities increases complexity.

* **Open/Closed Principle (OCP)**
Regarding the Open/Closed Principle (OCP), Processing generally performs well. The plugin architecture provided by the Tools Host and Contribution Manager allows new functionality to be added without modifying existing code. The rendering architecture is also extensible because different rendering implementations can be introduced through abstraction.

* **Dependency Inversion Principle (DIP)**
The Dependency Inversion Principle (DIP) is only partially respected. Components such as the Runner, Window Manager, and Render Engine depend directly on operating-system services. Although abstractions such as the Platform Layer reduce coupling, some dependencies remain tied to low-level implementations.

Overall, Processing prioritizes usability and simplicity over strict adherence to SOLID principles. The resulting architecture remains maintainable while supporting a broad range of users.

---

## 4. Clean Architecture Analysis
Clean Architecture organizes software into layers where dependencies point inward toward core business logic. Frameworks, user interfaces, and infrastructure should remain separated from the application's essential functionality.
Processing partially follows this approach. The outermost layer consists of infrastructure and external systems, including the Operating System, Processing Server, and External IDEs.
The PDE and Contribution Manager can be viewed as interface and infrastructure layers because they manage user interaction and communication with external services.
Java Mode functions as an application layer. It coordinates use cases such as preprocessing, compilation, debugging, and execution. Rather than performing rendering directly, it orchestrates workflows and delegates responsibilities to specialized components.
The Sketch Process represents the core runtime layer. Components such as PApplet, Animation Loop, Render Engine, and Resource Management implement the functionality required to execute sketches.
Several aspects of the architecture resemble Clean Architecture. The separation between the PDE and runtime execution demonstrates clear layering. The use of dedicated containers also improves modularity and reduces coupling.
However, Processing does not fully implement Clean Architecture. Components such as PApplet remain closely connected to rendering and event-management concerns. In a pure Clean Architecture design, these framework-specific details would be isolated from the core domain logic. Additionally, some components still depend directly on operating-system APIs.
Therefore, Processing should be viewed as a modular layered architecture that adopts several Clean Architecture concepts without strictly following all of its principles.

---

## 5. Architectural Characteristics
Several architectural characteristics are particularly important in Processing.
The first is simplicity. Processing was designed to make programming accessible to beginners, artists, and designers. The architecture supports this goal by hiding implementation complexity behind simple abstractions such as PApplet. Users can create visual applications with only a few lines of code while the system manages compilation, rendering, and execution internally.
The second characteristic is extensibility. Processing includes multiple mechanisms that allow functionality to be expanded. The Contribution Manager supports installation of new libraries and tools, while the Tools Host and Mode Host provide extension points for plugins and language integrations. This extensibility has enabled a large ecosystem of community-developed packages.
The third characteristic is portability. Processing is built on top of Java and runs on multiple operating systems. The Platform Layer further improves portability by isolating operating-system-specific functionality from the rest of the application. As a result, sketches can usually run on Windows, Linux, and macOS without modification.
Another important characteristic is modularity. The separation into PDE, Java Mode, Sketch Process, and Contribution Manager create clear architectural boundaries. Each container has a specific responsibility, reducing complexity and improving maintainability.
Finally, reliability is supported through process isolation. User sketches execute in a dedicated Sketch Process rather than inside the PDE. This means that runtime failures are less likely to crash the development environment itself. Such isolation improves stability and creates a better user experience.

---
In conclusion, Processing's architecture successfully balances simplicity, extensibility, portability, modularity, and reliability. These characteristics align closely with the project's educational goals and explain why Processing remains a popular platform for creative coding and programming education.
