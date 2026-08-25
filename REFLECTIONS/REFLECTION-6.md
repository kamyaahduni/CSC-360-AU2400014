# Daily Reflection Journal | CSC 360: Computer Graphics

| Field | Details |
|---|---|
| **Entry Date** | 25th August 2026 |
| **Session Date** | 20th August 2026 |
| **Session Topic** | Session 5 – Peer Review, Markdown, Java Graphics Pipeline, OOP in Java and Drawing with Points |

---

## 1. Remembering

This was a broad session that covered both meta-level skills (how to write and format reflections, how to review peers' work) and core Java graphics concepts. On the reflection side, the class reviewed each other's markdown files, discussed what makes a good reflection and what does not, and covered how markdown formatting actually works. One practical tip was how to view markdown files locally without an IDE: using a browser with a markdown rendering extension, or a tool like Grip which renders markdown exactly the way GitHub does.

On the Java side, four interconnected ideas came up. First, the graphics creation pipeline: create a JFrame, set it up, create a JPanel subclass that overrides painting, add the panel to the frame, and use colors inside the drawing logic. Second, Java OOP: classes, subclasses using the extends keyword, and anonymous classes. Third, drawing shapes using coordinate points: a rectangle from two diagonal corner points, and a triangle from three points where two can be chosen freely and the third needs more care. Fourth, the essential Java syntax present in every graphics file: import statements, class declarations, JFrame setup, super.paintComponent(), and how arguments are passed to drawing methods.

---

## 2. Understanding

The peer review exercise is useful for a reason that is easy to miss. Reading someone else's reflection surfaces assumptions you did not know you were making in your own. When you write your own entry, you know what you meant. When you read someone else's, you only have what they actually wrote. That gap reveals what is clear versus what is just implicit. It is the same principle that makes code review valuable: the author is too close to the code to see what is missing.

The markdown discussion matters practically. Markdown is plain text with lightweight syntax that renders into formatted HTML. Headers use # symbols. Bold uses double asterisks. Tables use pipes and dashes. The reason it is used for reflections in this course is the same reason developers use it for documentation: it is human-readable in raw form, renders cleanly in any markdown viewer, and works naturally with git version control since it is plain text. Viewing markdown locally without an IDE is straightforward. Opening the file in a browser with an extension like Markdown Preview Plus, or running Grip from the terminal (which serves a GitHub-style render at localhost), are both clean options.

The Java graphics pipeline is where I need to correct something I noted down. I had written "create frame, pass arguments, put frame in panel" but that has the relationship backwards. The panel does not contain the frame. The frame contains the panel. The correct order is:

1. Create a JFrame (this is the window itself)
2. Create a JPanel subclass that overrides paintComponent(Graphics g) (this is the canvas where drawing happens)
3. Inside paintComponent, call super.paintComponent(g) first, then set colors and draw shapes
4. Add the panel to the frame using frame.add(panel)
5. Set the frame size and make it visible with frame.setVisible(true)

The JFrame is the outer container, a window in the operating system. The JPanel is the drawing surface that lives inside it. Thinking of it like a picture frame and the canvas inside is helpful: the frame holds the canvas, not the other way around.

super.paintComponent(g) is important because it tells the parent class to clear the background before you draw. Skipping it means old drawings accumulate on the panel across repaints, which produces visual artifacts.

For classes and subclasses: the extends keyword means "is a more specific type of." So if the example in class was class Apple extends SweetApple, that would mean Apple is a more specific kind of SweetApple, which reads oddly. The more natural direction is class SweetApple extends Apple, meaning SweetApple is a specific kind of Apple that inherits all of Apple's properties and can add its own. The child class (subclass) always extends the parent class (superclass), not the other way around.

Anonymous classes are classes with no name, defined and instantiated in the same expression. They are used when you need a one-off implementation of an interface or abstract class and it is not worth creating a full named class for it. The most common case in Swing is event listeners: new ActionListener() { public void actionPerformed(ActionEvent e) { doSomething(); } }. Java 8 lambdas replaced most of these with shorter syntax, but anonymous classes are still the underlying mechanism.

The two-point rectangle and three-point triangle are about defining shapes by their geometric constraints. A rectangle is fully defined by two diagonal corner points because you can derive the width and height from them: width = x2 - x1, height = y2 - y1, then call drawRect(x1, y1, width, height). A triangle needs three non-collinear points. Two can be chosen freely anywhere within a given range. The third has to be chosen carefully to ensure the three points are not all on the same line, because three collinear points produce a line segment, not a triangle. The carefulness is really about guaranteeing a valid, visible, non-degenerate triangle.

---

## 3. Applying

The peer review process in practice means asking: does the Remembering section actually recall the session content, or does it interpret and analyze already? Does Understanding explain the "why" behind the concepts, or does it just restate what was in Remembering? Each Bloom's level has a specific job and slippage between them is the most common problem in reflection writing. Reviewing a peer's markdown through that lens, checking whether each section is doing what it is supposed to do, is more useful than just checking for grammar.

For the graphics pipeline in code, the minimal working Swing graphics program looks like this:

```java
import javax.swing.*;
import java.awt.*;

public class MyFrame extends JFrame {
    public MyFrame() {
        MyPanel panel = new MyPanel();
        add(panel);
        setSize(500, 500);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setVisible(true);
    }

    public static void main(String[] args) {
        new MyFrame();
    }
}

class MyPanel extends JPanel {
    @Override
    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        g.setColor(Color.BLUE);
        g.drawRect(100, 100, 200, 200);
    }
}
```

The import statements at the top bring in javax.swing for Swing components and java.awt for the Graphics and Color classes. public class declares a named class. The static main method is the entry point. JFrame is the window. JPanel is the drawing surface. The extends keyword makes MyPanel a subclass of JPanel so it can override paintComponent.

For the rectangle from two points: if the user clicks two corners, call drawRect(Math.min(x1, x2), Math.min(y1, y2), Math.abs(x2 - x1), Math.abs(y2 - y1)). Using Math.min and Math.abs handles the case where the second click is above or to the left of the first, which would give a negative width or height without the guards.

For the triangle from three points: int[] xPoints = {x1, x2, x3} and int[] yPoints = {y1, y2, y3}, then drawPolygon(xPoints, yPoints, 3). Choosing the third point carefully means making sure it has a different y-coordinate from the line connecting the first two points, so the triangle has visible area.

For the question from class about putting a button in a frame using the Session 2 code: a JButton is added to the JFrame the same way a JPanel is. Create a button with new JButton("Click me"), add an ActionListener to it, and call frame.add(button). The complication is that JFrame's default layout is BorderLayout, so if you add both a panel and a button without specifying layout positions, they will stack unpredictably. A cleaner approach is to set the frame's layout to BorderLayout explicitly, add the panel to the CENTER, and add the button to the SOUTH. That places the drawing canvas in the main area and the button at the bottom.

---

## 4. Analyzing

The extends relationship in Java creates a hierarchy where the child inherits everything from the parent and can add or override behavior. This is why MyPanel extends JPanel works for graphics: JPanel already knows how to be a UI component, handle repaints, manage layout, and so on. MyPanel inherits all of that and just overrides the one method (paintComponent) that controls what gets drawn. Without extends, you would have to reimplement all of JPanel's functionality from scratch.

Anonymous classes versus named classes is a trade-off between brevity and reuse. An anonymous class is defined once, inline, and thrown away after that one use. A named class can be instantiated multiple times, tested independently, and extended further. For event listeners in small programs, anonymous classes (or lambdas) make sense because each button usually has a unique action and creating a named class for each one is overkill. For more complex shared behavior, a named class is the right choice.

The two-point rectangle is more user-friendly than the four-argument version when handling interactive input because it maps to how people naturally think about drawing: click one corner, drag to the opposite corner. Under the hood it is the same drawRect() call, but the input model is different. The Math.min and Math.abs handling is the entire difference between an interface that works in all drag directions and one that only works top-left to bottom-right.

The three-point triangle's carefulness about the third point is essentially a constraint satisfaction problem. The first two points define a line. The third point must not lie on that line. For a visually interesting triangle the third point should also not be too close to the line, otherwise the triangle is very flat and thin. Placing the third point at a y-coordinate that is at least some minimum distance from the line connecting the other two is a reasonable heuristic.

---

## 5. Evaluating

The peer review format is a genuinely good way to calibrate reflection quality. Self-assessment is harder than it looks because you always know what you meant to write. Reading someone else's entry from the outside, checking whether each Bloom's level actually does its job, builds the same analytical muscle that makes a good reflection possible in the first place. It is metacognitive practice disguised as peer feedback.

The Swing graphics pipeline, while a bit boilerplate-heavy, is actually a clean design once you understand the frame-contains-panel relationship. The JFrame handles the OS window. The JPanel handles the drawing. They have one job each and do not bleed into each other. The verbosity of setting up a Swing app is the cost of that clarity. For a learning environment it is worth it because each piece of the setup is explicit and traceable.

The two-point and three-point shape definitions are the right way to think about interactive drawing in a graphics course. Hardcoded coordinates are fine for demonstrating rendering but they tell you nothing about how shapes are parameterized. Defining a rectangle by two user-specified points and a triangle by three introduces the idea that shapes are constraints on points, which is the foundation for anything more complex like Bezier curves, polygon clipping, or collision detection.

---

## 6. Creating

One extension of the three-point triangle exercise: add a constraint visualizer. When the first two points are placed, draw the line connecting them and shade the region that would produce a "flat" triangle (points too close to the line). The third point can only be placed outside the shaded zone. This makes the non-collinearity constraint visible rather than just a rule someone told you. It is also a good exercise in using the Graphics2D fill methods for the shaded region.

On the reflection side, I want to try building a simple markdown linting script. It reads a reflection markdown file and checks: are all six Bloom's levels present? Does each section have at least a certain number of words? Does the Key Takeaways section have between three and five bullets? This would automate the peer review checklist and could be run as part of the Maven build. Small tool, but it connects the course tooling from Lecture 3 with the reflection process from this session.

---

## Key Takeaways

- The panel goes inside the frame, not the frame inside the panel. JFrame is the window; JPanel is the drawing surface that lives in it. super.paintComponent(g) clears the canvas before each redraw and must be called first.
- In Java, the subclass extends the superclass, not the other way. SweetApple extends Apple because SweetApple is the more specific type. The child inherits from the parent.
- A rectangle is fully defined by two diagonal corner points. A triangle needs three non-collinear points. The carefulness around the third point is just ensuring the triangle has visible area and is not degenerate.
- Anonymous classes are single-use, inline class definitions. They are the mechanism behind event listener syntax in Swing, even when lambdas replace the verbosity in modern Java.
- Peer review works because it forces you to read a reflection as a reader, not as the author. That shift in perspective surfaces gaps that self-review misses every time.