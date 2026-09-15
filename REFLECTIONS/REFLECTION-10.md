# Daily Reflection Journal | CSC 360: Computer Graphics

| Field | Details |
|---|---|
| **Entry Date** | 15th September 2026 |
| **Session Date** | 8th September 2026 |
| **Session Topic** | Lecture 10 – Course Structure, Java Error Handling, Generics, Graphical Programming, UI Components and Dialogue Boxes |

---

## 1. Remembering

The lecture opened with a discussion on grading components and the activities tied to each one, followed by an overview of the five books prescribed for this course and what each covers, ranging from Java fundamentals to graphics theory and practice.

The bulk of the session worked through three chapters from Core Java Volume 1. Chapter 7 covered exceptions, assertions, and logging: the three main mechanisms Java provides for dealing with errors and tracking program behavior. Chapter 8 covered generic programming, which is about writing code that works with type parameters rather than fixed types. Chapter 10, the one most directly relevant to this course, covered graphical programming in Java.

Beyond the textbook discussion, several UI-specific topics came up. The connection between event handling and graphics was revisited. Grid layout (as a layout system) was discussed. The concept of components in graphics was introduced. Radio buttons and checkboxes were compared. And dialogue boxes were discussed in terms of their importance, with real-world examples, focusing on two key ideas: not losing user content and forcing attention.

---

## 2. Understanding

**Exceptions, Assertions, and Logging**

These three are Java's layered approach to dealing with things going wrong. They operate at different points in the development and production lifecycle, which is why they are grouped together.

Exceptions are for runtime errors: things that go wrong while the program is actually running. Java has checked exceptions (the compiler forces you to handle them) and unchecked exceptions (subclasses of RuntimeException, which the compiler does not enforce). The key distinction is that checked exceptions represent conditions you should reasonably anticipate, like a file not being found. Unchecked exceptions represent programming mistakes, like a null pointer dereference or an index out of bounds. The hierarchy runs from Throwable down to Exception and Error, with RuntimeException sitting under Exception for the unchecked branch.

Assertions are for development-time sanity checks. You write assert condition : "message" to verify assumptions your code makes about its own state. If the condition fails, an AssertionError is thrown. Assertions are disabled by default in production (you have to explicitly enable them with the -ea JVM flag), which is the point: they are a debugging tool, not a production error handler. Using assertions is a way of documenting and enforcing the invariants you expect to hold at certain points in your code.

Logging is for production observability. Instead of printing debug output with System.out.println(), you use a logging framework (java.util.logging, or more commonly SLF4J with Logback) that lets you set severity levels: SEVERE, WARNING, INFO, FINE, FINER, FINEST. In production you might only log WARNING and above. During development you turn on FINE to see detailed trace output. The program does not change. Only the logging configuration does. This is a much more maintainable approach than scattering print statements everywhere.

**Generic Programming**

Generics let you write a class or method once and have it work with any type, with the specific type checked at compile time rather than at runtime. The syntax is a type parameter in angle brackets: class Box<T> means Box parameterized by some type T. When you create a Box<String>, T becomes String throughout. The compiler enforces this so you get a type error at compile time, not a ClassCastException at runtime.

The practical motivation is safety without repetition. Before generics, Java collections stored Object references, and every retrieval required an explicit cast that could fail at runtime. With generics, a List<String> guarantees at compile time that everything in the list is a String. No cast needed, no surprise at runtime.

For my project specifically, the tree data structure is a natural candidate for a generic type. A Tree<T> that stores any type of object in its nodes is more useful than a tree hardcoded to store one specific type.

**Graphical Programming and Event Handling**

Chapter 10 being the "main chapter" of the course is not a surprise at this point, but it ties together everything covered so far. Graphical programming in Java Swing centers on the Graphics2D object inside paintComponent(), which is where all the drawing primitives from the early sessions live. The connection between event handling and graphics is that user input (mouse clicks, key presses) drives state changes, and state changes trigger repaints. The flow is: event fires, listener updates state, repaint() is called, paintComponent() runs with the new state, and the screen reflects it. This is the same event listener model from Session 1, now understood as the engine behind interactive graphics.

**Layout, Components, Radio Buttons, Checkboxes, Dialogues**

In Java Swing, every visible element is a Component: JButton, JLabel, JPanel, JFrame. Containers hold other components, forming a hierarchy. A Grid layout divides a container into a fixed grid of rows and columns where each component occupies one cell. This is analogous to CSS Grid on the web: rows and columns, with components slotting into cells. The layout manager determines how components are sized and positioned inside their container.

Radio buttons and checkboxes look similar but represent fundamentally different selection models. Radio buttons are mutually exclusive: only one in a group can be selected at a time. In Swing this is enforced by a ButtonGroup that wraps multiple JRadioButton instances. Checkboxes are independent: any combination can be selected simultaneously, with each JCheckBox holding its own boolean state. The rule of thumb is radio buttons for "choose exactly one" and checkboxes for "choose any combination."

Dialogue boxes are modal interruptions. A modal dialog forces attention: the user cannot interact with anything else in the application until they dismiss the dialog. This is the "forcing attention" concept from the session. The "not losing content" concept is the reason for unsaved-changes dialogs specifically. When a user tries to close an application with unsaved work, a dialog intercepts the close action and asks: save, discard, or cancel. That interruption exists because the cost of accidentally discarding work is high and unrecoverable. The forced attention is proportional to the stakes.

---

## 3. Applying

For exception handling in the context of this course, the most immediate application is wrapping file I/O operations in try-catch blocks. Reading or writing the save file in my project can fail for many reasons: file not found, permission denied, disk full, corrupted data. A checked exception like IOException must be handled explicitly. The pattern is:

```java
try {
    // file read or write operation
} catch (IOException e) {
    // log the error and show the user an informative dialog
    logger.log(Level.SEVERE, "Failed to save file", e);
    JOptionPane.showMessageDialog(frame, "Save failed: " + e.getMessage());
}
```

This connects exception handling directly to dialogue boxes: the catch block is where you tell the user something went wrong, and a JOptionPane is the standard Swing way to do that.

For generics, implementing my tree project with a generic node looks like:

```java
class TreeNode<T> {
    T data;
    List<TreeNode<T>> children;

    TreeNode(T data) {
        this.data = data;
        this.children = new ArrayList<>();
    }
}
```

Now the tree can hold any type. A TreeNode<String> for a text-based tree, a TreeNode<FileObject> for a file system view, whatever the application needs.

For radio buttons vs checkboxes, a practical application in my project is the properties panel. If a node in the tree can have one alignment (left, center, right), that is three JRadioButtons in a ButtonGroup. If a node can have multiple visual flags (bold, italic, highlighted), those are three independent JCheckBoxes. Getting this wrong (using checkboxes for mutually exclusive options) is a UI bug that confuses users immediately.

For the unsaved-changes dialogue, the right moment to show it is in the windowClosing event of the JFrame. The listener checks whether the current state differs from the last saved state. If it does, a confirmation dialog gives the user the choice to save, discard, or stay. This is JOptionPane.showConfirmDialog() with YES, NO, and CANCEL options. Choosing CANCEL means the window close is vetoed and nothing changes.

---

## 4. Analyzing

Exceptions vs assertions vs logging is a cleaner separation than it first appears. Exceptions handle conditions that can happen in a correctly written program: a file might not exist, a network might be unavailable, a user might type unexpected input. Assertions catch conditions that should never happen if the code is correct: a variable that should never be null, a list that should always be non-empty at a certain point. Logging records what happened for later analysis without changing the program's behavior. These three tools do not overlap in purpose even though they all involve "dealing with problems."

Generic programming vs using Object references is the comparison Java programmers had to live with before Java 5. The Object approach technically works but it trades compile-time safety for runtime danger. Every time you pull something out of a pre-generic collection you are making an assumption about its type that the compiler cannot verify. Generics push that verification to compile time, which is always cheaper. The downside is that Java generics use type erasure: at runtime, a List<String> and a List<Integer> are both just List. This means some things that should be possible (creating a generic array, using instanceof with a parameterized type) are not, because the type information is gone. It is a design compromise made for backwards compatibility with pre-generics Java code.

Radio buttons vs checkboxes is also a deeper design decision than it looks. Using radio buttons constrains the user to one choice, which simplifies the application logic (you always know exactly what is selected) but limits expressiveness. Checkboxes allow complex combinations, which is more expressive but means the application has to handle every possible combination, including ones that might be contradictory or nonsensical. The design question is whether the domain actually allows multiple selections or not. Getting this wrong does not just confuse users; it produces application state that the code was not written to handle.

---

## 5. Evaluating

The three-layer approach to error handling (exceptions for runtime, assertions for invariants, logging for observability) is the right architecture for any non-trivial Java application. The mistake most beginners make is using only one of these. Using only exceptions means your debug builds look the same as your production builds. Using only print statements means you cannot adjust verbosity without recompiling. Using all three gives you a graduated, configurable, maintainable system for dealing with the full lifecycle of an application.

Generic programming is unambiguously worth the extra syntax once you understand it. The angle bracket syntax is unfamiliar at first but the compile-time safety it provides is not optional in a serious project. My tree data structure being generic is not a nice-to-have. It is the correct design because the tree should not care what it stores. The storage mechanism and the data type should be independent concerns.

The dialogue box discussion gave me a useful mental model: modal dialogs are a UX tool for high-stakes decisions where the cost of the wrong action is unrecoverable. Use them sparingly, because every modal dialog is an interruption. But when the stakes are high (deleting data, closing with unsaved work, performing an irreversible action), the interruption is worth it. Overusing modal dialogs trains users to dismiss them without reading, which defeats the purpose entirely.

---

## 6. Creating

The generics and exceptions discussion together suggest a design for the tree project's persistence layer. A generic Serializer<T> class that takes any tree node type and handles the file I/O with proper exception handling and logging would separate persistence concerns from data structure concerns cleanly:

```java
class TreeSerializer<T> {
    private static final Logger logger = Logger.getLogger(TreeSerializer.class.getName());

    void save(TreeNode<T> root, String path) throws IOException {
        logger.info("Saving tree to " + path);
        // serialization logic here
    }

    TreeNode<T> load(String path) throws IOException {
        logger.info("Loading tree from " + path);
        // deserialization logic here
    }
}
```

This makes the serializer testable in isolation (you can test TreeSerializer<String> without the full UI) and the logging gives you visibility into when saves and loads happen in production.

The unsaved-changes dialogue also leads to a useful extension: a dirty flag. A boolean isDirty field on the application state that starts as false, flips to true whenever the user makes any change, and resets to false after a successful save. The windowClosing listener checks isDirty before deciding whether to show the dialog. If it is false, close immediately. If it is true, ask. This is a small but complete implementation of the "do not lose content" principle from the session.

---

## Key Takeaways

- Exceptions handle runtime conditions, assertions enforce invariants during development, logging provides production observability. These three tools cover different phases of the application lifecycle and are not interchangeable.
- Generics push type checking to compile time. A List<String> is a compile-time guarantee, not a runtime assumption. The syntax is unfamiliar but the safety is not optional in serious code. My tree node should be generic from day one.
- Event handling is the engine behind interactive graphics: event fires, state updates, repaint() triggers, paintComponent() runs. Every interactive graphics program is this loop, repeated forever.
- Radio buttons are for mutually exclusive choices; checkboxes are for independent multi-select. Using the wrong one is not just a UI mistake, it is a data model bug waiting to happen.
- Modal dialogue boxes exist for high-stakes, unrecoverable decisions. Use them only when an interruption is genuinely justified. The unsaved-changes dialog before close is the canonical example of a justified interruption.
