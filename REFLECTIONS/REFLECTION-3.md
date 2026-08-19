# Daily Reflection Journal | CSC 360: Computer Graphics

| Field | Details |
|---|---|
| **Entry Date** | 19th August 2026 |
| **Session Date** | 13th July 2026 |
| **Session Topic** | Lecture 3 – Lab Setup: Apache Maven, TortoiseGit, Java Closed Shapes and Antigravity Configuration |

---

## 1. Remembering

This was a hands-on lab session. Four things happened: Apache Maven was installed, TortoiseGit was installed for git operations, a Java file was written to render basic closed shapes, and Java extensions were installed and configured inside Antigravity.

Apache Maven is a build automation and dependency management tool for Java projects. It manages project structure and handles compiling, packaging, and dependency resolution through a configuration file called pom.xml. TortoiseGit is a GUI client for git that integrates directly into the Windows file explorer through right-click context menus. The closed shapes exercise involved writing Java code to draw basic filled and outlined geometric shapes: rectangles, ovals, and polygons. Antigravity is the development environment used in this course, and setting it up required installing specific Java extensions to get the graphics workflow running.

---

## 2. Understanding

Maven exists because managing a Java project manually gets messy fast. Without it, you are manually downloading JAR files, putting them in the right folders, and hoping nothing breaks when you move the project to a different machine. Maven replaces all of that with a pom.xml file (Project Object Model) that declares what your project needs. Run the build and Maven fetches everything itself. It is the difference between giving someone a grocery list versus driving them to every store individually. The project becomes portable because the pom.xml travels with the code and the dependencies get pulled in automatically anywhere it runs.

TortoiseGit is a layer of abstraction on top of git. Command-line git is powerful but has a steep entry curve, especially for operations like resolving merge conflicts, visualizing branch history, or staging individual hunks of a file. TortoiseGit puts all of that into right-click menus in Windows Explorer. The git operations are exactly the same underneath. It is just a different interface to the same machinery we set up with SSH last session.

The closed shapes exercise is where Lectures 1 and 2 met code for the first time. Lines and curves being the atomic primitives means every closed shape is just a set of lines or a closed curve. A rectangle is four line segments. An oval is a closed parametric curve. A polygon is a sequence of connected line segments that closes back on itself. Writing the Java file forced that abstraction to become concrete.

Setting up the Java extensions in Antigravity is the kind of step that seems purely mechanical but actually matters for understanding how modern development environments work. An IDE does not know how to handle a Java graphics project by default. Extensions teach it the language, the build system, and the rendering pipeline. The setup step is also a signal that this course's dev stack is now locked in: Maven for builds, TortoiseGit for version control, Antigravity for development. Everything from here runs on this foundation.

---

## 3. Applying

Installing Maven and wiring it into the project meant creating (or verifying) the pom.xml structure. The minimum pom.xml needs a groupId (usually a reverse domain like com.au.csc360), an artifactId (the project name), and a version. From there, any external Java library needed later gets added as a dependency block and Maven handles the rest. Running mvn compile and mvn package in the terminal or through Antigravity's Maven plugin confirms the build is working.

TortoiseGit integration means every folder in the project shows a small overlay icon indicating its git status: green checkmark for committed, red exclamation for modified, blue plus for untracked. Committing is a right-click, then Git Commit, fill in the message, and push. It also gives a visual diff view before committing, which is useful for checking what changed before it goes to the remote. And because the SSH key from Lecture 2 is already configured, pushing does not prompt for credentials at all.

For the closed shapes Java file, the core methods come from the Graphics or Graphics2D object: drawRect(x, y, width, height) for an outlined rectangle, fillRect() for a filled one, drawOval() and fillOval() for circles and ellipses, and drawPolygon(xPoints, yPoints, nPoints) for arbitrary closed shapes. The coordinate system in Java's graphics context has (0, 0) at the top-left with y increasing downward, which is the opposite of the standard mathematical coordinate system. That flipped y-axis is something to internalize early because it affects every position calculation going forward.

---

## 4. Analyzing

Maven vs manually managing dependencies is not a close comparison for any project of real size. The manual approach breaks the moment you share the project with someone else or move it to a new machine, because the JAR files are not in the same paths. Maven's pom.xml makes the dependency graph explicit and reproducible. The trade-off is that the first time Maven runs it downloads everything from the internet, which can be slow on a new machine. But that is a one-time cost and the project becomes self-contained after that.

TortoiseGit vs command-line git is a more genuine trade-off. TortoiseGit is faster for common operations: stage, commit, push, pull. The visual diff and history views are better than what you get in a terminal. But it abstracts away the internals, which means if something goes wrong (a detached HEAD, a rebase conflict, a corrupted index) TortoiseGit's error messages are often less informative than what the CLI gives you. For this course's day-to-day workflow, TortoiseGit is the right default. But knowing the terminal commands behind what it is doing still matters.

The closed shapes exercise also revealed something worth noting: Java's Graphics2D coordinate system is not what you expect coming from mathematics. In math, y increases upward. In Java's graphics context, y increases downward. This matters immediately when positioning shapes and will matter even more when transformations come in. It is the kind of detail that causes hard-to-diagnose bugs if you do not know it upfront.

---

## 5. Evaluating

Maven is the right build tool for this course. It is the industry standard for Java projects, the setup cost is low, and it means our graphics projects will compile cleanly on any machine without manual dependency wrangling. Using it from the start builds a habit that will carry into any Java work after this course as well.

TortoiseGit is a good call for this environment. Most of the class is probably on Windows, and TortoiseGit's Explorer integration makes version control feel like a natural part of file management rather than a separate context switch to a terminal. The SSH key setup from Lecture 2 means TortoiseGit pushes and pulls without credential prompts, so the two sessions connect cleanly in practice.

The closed shapes exercise was the right first coding task. It is low-stakes enough that the focus can stay on the rendering pipeline itself rather than on algorithmic complexity. But it is not trivial: it forced me to think about coordinate systems, about how shapes are constructed from primitives, and about how the Java Graphics API translates the geometric abstractions from the first two lectures into actual method calls. Every shape drawn is a proof-of-concept for what Lecture 1 said about lines and curves.

---

## 6. Creating

The next thing I want to do with the closed shapes file is parameterize it. Right now the shapes probably have hardcoded positions and sizes. Making those values variables driven by the window dimensions, using getWidth() and getHeight() on the panel, means the shapes scale with the window. That is a small step but it connects closed shapes to the zoom and coordinate space ideas from the triangle demo in Lecture 2.

A bigger extension: combine the closed shapes with the Event Listener Model from Lecture 1. Add a mouse click listener that reads the click coordinates and draws a new shape at that position. Every click adds a shape to the canvas. Now the closed shapes file is a basic interactive paint program. Maven handles the build, TortoiseGit tracks the changes, and the whole stack from the last three lectures is working together as one thing.

---

## Key Takeaways

- Maven's pom.xml makes a Java project reproducible anywhere. Dependencies are declared, not manually managed. This is non-negotiable for any serious Java project.
- TortoiseGit is command-line git with a visual shell. The operations underneath are identical. The interface is just faster for common tasks. But the CLI knowledge still matters when things go sideways.
- Java's graphics coordinate system has (0, 0) at the top-left and y increasing downward. This is the opposite of standard math coordinates and it will cause bugs if you forget it mid-project.
- Every closed shape in Java is just the geometric primitives from Lecture 1 expressed as method calls. drawRect is four lines. drawOval is a closed curve. The abstraction holds all the way down.
- This session locked in the course's dev stack: Maven for builds, TortoiseGit for version control, Antigravity for development. Everything from here runs on this foundation.