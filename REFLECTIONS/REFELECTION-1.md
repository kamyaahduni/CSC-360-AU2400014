# Daily Reflection Journal | CSC 360: Computer Graphics

| Field | Details |
|---|---|
| **Entry Date** | 19th August 2026 |
| **Session Date** | 4th August 2026 |
| **Session Topic** | Lecture 1 – Introduction to Computer Graphics, Geometric Primitives and Java Frameworks |

---

## 1. Remembering

The first lecture walked through the course outline and immediately set up two foundational questions: what is Computer Graphics, and how is it different from Image Processing? The answer was direct. Computer Graphics is about generating images from data or models (synthesis). Image Processing takes an existing image and analyzes or transforms it. Opposite directions.

The geometric primitives of drawing are lines and curves. Everything you render on screen reduces to these two. On the Java side, three graphical frameworks were covered: AWT (Abstract Window Toolkit), Swing, which was specifically flagged as being "part of the runtime," and JavaFX. The design pattern for handling user interactions across all of these frameworks is the Event Listener Model, which breaks down into event sources like buttons, event objects, and event listeners that respond when an event fires. The lecture also drew a connection between curves and calculus, pointing to parametric equations and derivatives as the math behind smooth curve rendering.

---

## 2. Understanding

The Graphics vs. Image Processing distinction is easy to mix up until you think about the direction of the data. A 3D game engine building a scene from scratch is Graphics. You start with nothing visible and synthesize an image. An Instagram filter applied to a photo you already took is Image Processing. You start with pixels and transform them. The output of both is an image, which is why people conflate them. But the process runs in completely opposite directions, and once you see it that way, it sticks.

Lines and curves being the only two geometric primitives makes complete sense from first principles. A circle is a curve. A rectangle is four lines. A font is a set of carefully parameterized curves. It is the same idea as how all of chemistry builds from a handful of elements. The complexity of what you see on screen is just an emergent property built entirely from these two mathematical objects. Starting here feels intentional.

The Java framework lineup is worth understanding as a design evolution, not just a list to memorize. AWT is the oldest and delegates rendering to native OS widgets. That means it looks native but behaves inconsistently across platforms. Swing came next and drew everything in Java, giving it cross-platform consistency. The "part of the runtime" detail matters practically because Swing ships with the JDK, so there is zero dependency setup. JavaFX is the modern answer with hardware acceleration and a scene graph, but it was decoupled from the JDK after Java 11, making it an external dependency now.

The Event Listener Model is basically the Observer design pattern applied to UIs. A button does not know or care what happens when it is clicked. It just fires an event. Any object registered as a listener hears that event and responds. The UI component stays dumb, and the business logic lives elsewhere. This decoupling is the whole point, and it makes everything easier to test and maintain.

The curves-calculus connection is the part that excited me the most. A curve in graphics is defined parametrically as x(t) and y(t) as functions of a parameter t. Calculus gives you the tangent vector (the first derivative) at any point on that curve. That matters for normals, blending between curve segments, and anything involving direction. This is not just theoretical flavor. It is the mathematical backbone that makes Bezier curves smooth rather than jagged. The course is signaling from day one that we are doing principled, math-grounded graphics work, not just calling library functions blindly.

---

## 3. Applying

The most direct application of today's session is writing a basic Swing window. The skeleton is: create a JFrame, add a JPanel subclass that overrides paintComponent(Graphics g), and call g.drawLine() and g.drawOval() inside that method. This is where the primitives concept stops being abstract. The Graphics object in Java literally exposes drawLine() as a first-class method because a line is a first-class primitive. The entire Swing rendering pipeline bottoms out at these two atomic elements.

For the Event Listener Model, the application is one line in modern Java: `button.addActionListener(e -> doSomething());`. The button is the source, the click is the event, and the lambda body is the listener. Before Java 8 this was a five-line anonymous class. Lambdas cleaned that up dramatically.

The curves-calculus link has a concrete rendering application too. Given x(t) = cos(t) and y(t) = sin(t), sampling t from 0 to 2pi at small increments and connecting consecutive sample points with drawLine() calls gives a circle approximation. This is how most graphics systems actually render curves under the hood. The math says curve, the hardware draws lines. That translation step is presumably what the rest of the semester is going to unpack.

---

## 4. Analyzing

The AWT to Swing to JavaFX timeline is a classic pattern: each generation solves the previous generation's main failure while introducing a new constraint. AWT solved cross-platform GUI existence but had platform inconsistency. Swing solved inconsistency but lost GPU acceleration and HiDPI support. JavaFX solved those modern problems but lost the convenience of being bundled with the JDK. Each step is a trade-off, not a clean upgrade. There is no final winner here, just context-dependent choices.

The Event Listener Model's verbosity in older Java is worth noting because it shows how language evolution interacts with design patterns. Before Java 8, every listener needed a full anonymous class. That is five or six lines for what is conceptually a one-liner. Lambdas compressed that dramatically. The pattern never changed. The language just caught up. And this makes me think: how many design patterns that seemed "heavy" in old Java are now elegant for the same reason?

The curves-calculus connection also explains something fundamental about vector graphics. A curve defined by x(t) and y(t) is a mathematical object, not a fixed pixel grid. You can sample it as densely or sparsely as your output resolution demands. Raster images are finite grids of fixed samples. Scale them up and the grid shows. This distinction between mathematical representation and sampled representation is what separates Computer Graphics from Image Processing at a deeper level than just "one makes images, one analyzes them."

---

## 5. Evaluating

Of the three Java frameworks, Swing is the right tool for this course. It is part of the runtime, so there is zero setup friction. It has decades of documentation and community support. JavaFX is more powerful but overkill for a fundamentals course, and the external dependency overhead is not worth it at this stage. AWT is too inconsistent to be a productive learning environment. Swing sits in exactly the right spot for what we are doing.

The Event Listener Model is a genuinely good design choice for UIs. It has been proven at scale across decades of GUI programming in Java, Android, and browser JavaScript. The initial cognitive overhead of figuring out who is listening to whom is real. But that confusion is actually a sign of how meaningful the decoupling is. Once it clicks, you start seeing the Observer pattern everywhere and every reactive system starts to feel familiar.

The decision to introduce curves through calculus rather than just handing us some API calls is the pedagogical choice I respect most from this first lecture. It anchors the course in mathematics from the very first session. Anyone can learn to call drawBezierCurve(). Understanding why that curve is smooth and continuous requires the calculus foundation being laid today, and that foundation is what will let me reason about unfamiliar problems later rather than just replicate known solutions.

---

## 6. Creating

The idea I want to build from today's session is a parametric curve visualizer in Swing. Two text fields for x(t) and y(t) expressions, an input for t range and step size, and a drawing panel that re-renders live whenever any parameter changes. The Event Listener Model handles the form inputs: a listener fires on each text field update, parses the expression, and triggers a repaint. The drawing logic samples the parametric function at the specified step and connects points with drawLine() calls. This brings together every thread from today's lecture: Java frameworks for the shell, event handling for the interactivity, and the calculus connection for the underlying curve math.

More broadly, the lines-and-curves framing makes me want to trace the primitive landscape across different graphics systems. SVG's path element, HTML Canvas's lineTo() and bezierCurveTo(), WebGL's vertex buffer objects: all of them bottom out at the same two primitives, just at different abstraction levels. Swing's drawLine() and a WebGL vertex shader are different doors into the same mathematical room. That feels like a useful mental model to carry through the entire course and beyond.

---

## Key Takeaways

- Computer Graphics synthesizes images from data; Image Processing analyzes images that already exist. They flow in opposite directions, and conflating them means misunderstanding both.
- Lines and curves are not just two things you can draw. They are the atomic elements from which all visual complexity is constructed. Everything on screen traces back to them.
- AWT to Swing to JavaFX is a timeline of trade-offs, not upgrades. Each generation solved one problem and inherited another. For this course, Swing is the right fit: runtime-included, well-documented, no external dependencies.
- The Event Listener Model is the Observer pattern applied to UIs. Decouple the source from the handler and everything becomes maintainable. It looks like boilerplate. It is actually architecture.
- Curves are parametric mathematical objects and calculus is what makes them well-behaved and smooth. The API call is the surface. The derivative is what is underneath.
