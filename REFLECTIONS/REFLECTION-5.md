# Daily Reflection Journal | CSC 360: Computer Graphics

| Field | Details |
|---|---|
| **Entry Date** | 19th August 2026 |
| **Session Date** | 18th July 2026 |
| **Session Topic** | Lecture 4 – Square Drawing Algorithm, Midpoint Calculation and Color in Java Graphics |

---

## 1. Remembering

This session went back to the Java file from Lecture 3 and unpacked the algorithm behind it rather than just accepting that the code works. Three things were the focus: the algorithm for drawing a simple square using individual line segments, the midpoint calculation method for deriving where each line goes, and how to set colors on shapes in Java.

The square is constructed from four explicit line segments rather than a single drawRect() call. Each line connects two corners. The midpoint of the shape (or of the canvas) is used as the anchor point, and the four corners are calculated relative to that center by offsetting by half the side length in both x and y directions. Color setting in Java graphics uses the setColor() method on the Graphics object, which takes a Color value and applies it to everything drawn after that call until the color is changed again.

---

## 2. Understanding

The important thing about the midpoint approach is that it gives you a shape that is anchored to a center rather than a top-left corner. Java's drawRect() takes (x, y, width, height) where x and y are the top-left corner. That works, but if you want to place a square at the center of the canvas, you have to back-calculate the top-left corner yourself anyway. Defining the shape from its midpoint is more intuitive because you think "I want a square centered here" and the math follows directly from that. The four corners are just: center plus or minus half the side length in each direction.

So if the center is (cx, cy) and the side length is L, the corners are:

- Top-left: (cx - L/2, cy - L/2)
- Top-right: (cx + L/2, cy - L/2)
- Bottom-right: (cx + L/2, cy + L/2)
- Bottom-left: (cx - L/2, cy + L/2)

And then four drawLine() calls connect those corners in sequence. This is the same geometric primitive from Lecture 1, a line, doing the actual work. drawRect() just wraps this exact logic internally. Understanding the manual version first means the abstraction makes sense when you use it later.

The color setting part is about understanding that Java's Graphics object is stateful. It is like a paintbrush: whatever color you set on the brush is the color used for everything you draw until you change it. setColor(Color.RED) before drawLine() draws a red line. setColor(Color.BLUE) before the next call draws a blue shape. The color is not attached to the shape; it is a property of the drawing context at the moment of the call. This is easy to get wrong if you think of shapes as having their own intrinsic color properties.

The Color class in Java takes RGB values, either as named constants like Color.RED or as custom values like new Color(255, 128, 0) for orange. RGB (Red, Green, Blue) is an additive color model where each channel runs from 0 to 255. All three at 255 gives white. All three at 0 gives black. This is the same model used by screens, which makes sense because the output of this rendering pipeline is ultimately pixels on a display.

---

## 3. Applying

The algorithm for the square in code looks like this: define cx and cy as half of getWidth() and getHeight() respectively, so the center tracks the middle of the panel dynamically. Define L as the side length. Calculate the four corners as integer coordinates (since drawLine() takes int arguments). Then draw four lines: top edge from top-left to top-right, right edge from top-right to bottom-right, bottom edge from bottom-right to bottom-left, and left edge from bottom-left to top-left. That is it. The square is four lines and a bit of arithmetic.

For color, the workflow is: call g.setColor() with the desired color before each shape or group of shapes that should share a color. If the square should have a red border, set red before the drawLine() calls. If there is a filled region inside, call g.setColor() with a different color and then call fillRect() or fillPolygon() before the outline lines. The order matters because fill and stroke are separate operations and the last setColor() before each operation wins.

A useful practice is to save the original color at the start of paintComponent() using g.getColor() and restore it at the end. This keeps the graphics context clean in case other components are sharing it.

---

## 4. Analyzing

Building the square from four drawLine() calls versus just using drawRect() is a trade-off between transparency and convenience. drawRect() is one line and is self-documenting. But it hides the coordinate arithmetic that is actually happening. Building it from drawLine() manually forces you to handle the corner coordinates yourself, which means you understand what "a rectangle" actually is in terms of the graphics pipeline. For learning purposes, manual is better. For production code, drawRect() is the obvious choice.

The midpoint anchoring approach versus top-left anchoring is also worth analyzing. Top-left anchoring is how most screen-space APIs work because screens have a natural top-left origin. But midpoint anchoring maps better to how people think about placing objects in a scene. "Put the square in the center" is a natural instruction. "Put the top-left corner of the square at (canvas_width/2 - L/2, canvas_height/2 - L/2)" is not. This tension between screen-space coordinates and world-space thinking is something that will keep coming up as the course moves into transformations.

The stateful color model in Java Graphics2D has implications for rendering order too. If you draw shapes that overlap, the last shape drawn appears on top. And since color is tied to the drawing state, mixing fill and outline operations for multiple shapes requires careful sequencing. It is not a problem for a single square but it becomes one fast when the scene gets more complex.

---

## 5. Evaluating

The midpoint calculation approach is the right way to think about shape placement in a graphics context. It is more work upfront than using drawRect() with hardcoded coordinates, but it produces code that is actually readable and generalizable. When the canvas resizes, a midpoint-anchored square stays centered because cx and cy recalculate from getWidth() and getHeight(). A hardcoded top-left coordinate does not do that.

Starting with the manual four-line version of the square before introducing drawRect() is a good pedagogical sequence. It mirrors the philosophy the course has had from the start: understand the primitives before using the abstractions. drawRect() means nothing if you do not know it is just four lines with a bit of arithmetic.

The Java color model being RGB with 0 to 255 per channel is intuitive enough for now but it is worth knowing that it is not the only model. HSB (Hue, Saturation, Brightness) is often more useful for artists and for programmatic color manipulation. Java supports both through the Color class. For this course RGB is probably sufficient, but HSB becomes useful the moment you want to do things like "make this color 20% brighter."

---

## 6. Creating

One thing I want to try: make the square's side length and position driven by mouse input. Click to set the center, drag to set the size. The midpoint calculation is already there, cx and cy just come from the mouse click coordinates instead of the canvas center. The drag distance (from click to release) becomes L. This turns the algorithm from a static computation into a live interactive tool, which is basically the next step after the triangle demo from Lecture 2.

Another idea: render the same square three times with different color combinations to visualize how the fill and outline interact. One with just an outline, one with just a fill, one with both. This would also be a practical test of the setColor() sequencing and a good reference for understanding the rendering order. Small experiment, but the kind that makes the stateful color model stick.

---

## Key Takeaways

- The midpoint approach anchors shapes to a center point rather than a corner, which is more intuitive for placing objects in a scene and more robust when the canvas resizes.
- A square is four drawLine() calls. drawRect() wraps that logic for convenience but the primitives are identical underneath. Knowing the manual version is what makes the abstraction meaningful.
- Java's Graphics object is stateful: setColor() sets the brush, and everything drawn after that call uses that color until it changes. Color belongs to the context, not to the shape.
- RGB values run from 0 to 255 per channel. This matches the additive screen color model directly, which is why it is the default for on-screen rendering.
- Fill and outline are separate operations. Order matters: fill first, then outline, so the outline draws on top of the filled region and stays crisp.
