# Daily Reflection Journal | CSC 360: Computer Graphics

| Field | Details |
|---|---|
| **Entry Date** | 17th September 2026 |
| **Session Date** | 10th September 2026 |
| **Session Topic** | Session 11 – Streaming in Graphics and Data Processing, XML and SVG as Vector Description |

---

## 1. Remembering

This session covered two connected ideas that look unrelated at first but tie together cleanly by the end.

The first idea is streaming. In graphics and digital processing, streaming means sending or processing data continuously, bit by bit or chunk by chunk, instead of waiting for the whole file to arrive before doing anything with it. The contrast is downloading: you wait for the complete file, then use it. Streaming means you use it as it arrives.

The streaming concept then extended into large-scale data computation. The example given was variance calculation across 10 trillion numbers. The variance formula is: σ² = E[X²] - (E[X])². Instead of loading all 10 trillion numbers into memory (which is impossible), you stream through them once, maintaining two running values: a sum of every number and a sum of every number squared. After the full stream, divide both by n to get E[X] and E[X²], then plug into the formula.

The second idea is XML and its relationship to graphics. XML (Extensible Markup Language) is a text-based format for storing and transporting data in a way that is both human-readable and machine-readable. Unlike HTML, which has a fixed set of tags, XML lets you define your own tags to describe whatever data you need. The biggest graphics connection is SVG (Scalable Vector Graphics): SVG is literally XML. It describes shapes, paths, lines, and colors using tags, making graphic data readable as plain text and editable with code.

---

## 2. Understanding

The word "streaming" does two different jobs and it is worth being precise about which one is in play. Consumer streaming (Netflix, Spotify) means receiving media data continuously so playback starts immediately instead of waiting for a full download. Computational streaming means processing data in sequential chunks so you never have to hold the full dataset in memory. Both are the same underlying idea: do not wait for everything, act on each piece as it arrives. The difference is what you are doing with each piece. One is playing it back. The other is computing something from it.

The variance-through-streaming example is a beautiful illustration of why the expectation formula matters. The naive approach to calculating variance is: load all the numbers, compute the mean, subtract the mean from each number, square each result, average them. That requires the full dataset in memory twice. But the expectation-based formula (σ² = E[X²] - (E[X])²) only requires two running totals regardless of how many numbers there are. You never go back. You never store more than two values plus a count. A 10 trillion-number dataset requires the same memory as a 10-number dataset. The formula is what unlocks the streaming approach: if your computation can be expressed in terms of running aggregates, you can stream it.

This is the kind of insight that bridges theory and engineering. Expectation and variance look like abstract statistics when you first see them. But the moment you need to process real data at scale, those formulas become the difference between a program that runs in constant memory and one that crashes.

XML's design philosophy is the opposite of HTML's. HTML has a fixed vocabulary: div, span, p, img, these are defined and the browser knows what to do with them. XML has no fixed vocabulary at all. You define tags that describe your data, and whatever reads the file is responsible for understanding what those tags mean. The structure of the document is the message. This makes XML incredibly flexible but also puts the burden of interpretation on the consumer.

SVG being XML is not a coincidence. Vector graphics are descriptions of shapes, not grids of pixels. Descriptions naturally map to text and tags. A circle is just a center point and a radius. A path is a sequence of move, line, and curve commands. All of that fits cleanly into XML's tag-based structure. And because it is text, SVG files are version-controllable, grep-able, and editable without any specialized software. A 100x100 circle in SVG is literally: `<circle cx="50" cy="50" r="50"/>`. Three numbers and a tag.

---

## 3. Applying

The streaming variance computation in code follows a simple loop:

```java
long n = 0;
double sumX = 0;
double sumX2 = 0;

while (dataStream.hasNext()) {
    double x = dataStream.next();
    n++;
    sumX += x;
    sumX2 += x * x;
}

double meanX = sumX / n;
double meanX2 = sumX2 / n;
double variance = meanX2 - (meanX * meanX);
```

The dataset could be 10 numbers or 10 trillion numbers. The code looks identical. The memory usage is three variables. This is what makes the expectation-based formula practically important: it directly enables a constant-memory streaming implementation.

For SVG applied to graphics, the connection to Lecture 1 is direct. The geometric primitives we identified as the foundation of all graphics (lines and curves) are exactly what SVG tags describe. A `<line>` tag takes two coordinate pairs. A `<path>` tag takes a sequence of move and draw commands that describe any curve. Drawing a square in SVG means four `<line>` tags or one `<rect>` tag with x, y, width, and height. The same shape, the same coordinates, just expressed in XML instead of Java method calls.

For practical use in this course: an SVG file produced by a graphics program (or hand-written) can be opened in any browser without any IDE or graphics library. This is actually the "view without an IDE" idea from Session 5, applied to vector graphics. An SVG is human-readable, browser-renderable, and git-trackable. These are the same properties markdown has for text, applied to graphics.

---

## 4. Analyzing

The two meanings of streaming share a root but diverge in what "acting on the data" means. Media streaming tolerates lossy, approximate delivery because a dropped video frame is acceptable. Computational streaming requires exact processing of every value because a missed number changes the result. The constraint is different. Media streaming trades accuracy for continuity. Computational streaming must not trade either.

The expectation-based variance formula versus the definitional variance formula is a good example of algebraic equivalence that is not computational equivalence. Both give you the same number. But σ² = E[X²] - (E[X])² requires one pass through the data and two running totals. The definitional formula (average of squared deviations from the mean) requires knowing the mean first, which requires a full pass, then a second full pass to compute deviations. One is streamable. The other is not. This is why the choice of formula matters beyond a math class.

XML vs JSON is a comparison worth making because both are human-readable data formats competing for the same job in many contexts. XML is verbose (every opening tag needs a closing tag), supports attributes and nested structure, and has a rich ecosystem of tooling (XPath, XSLT, XML Schema). JSON is leaner, maps directly to objects and arrays in most programming languages, and is dominant in web APIs. SVG chose XML for good reason: it predates JSON's rise and the attribute-heavy structure of SVG (where a shape's properties are all attributes of a single tag) fits XML naturally. But if SVG were designed today it might look different.

---

## 5. Evaluating

The streaming variance example is the best kind of course content because it makes a purely theoretical formula immediately practical. You are not calculating variance because it is on a problem set. You are calculating it because it is the only algorithm that fits the memory constraints of the real problem. That is the right motivation for learning a formula. It should change what you do, not just what you know.

SVG is still the right format for vector graphics on the web and in interoperable tools despite XML's verbosity. The key advantage is that SVG is first-class HTML: you can embed it directly in a page, style it with CSS, animate it, and manipulate it with JavaScript like any other DOM element. No other vector format gets that treatment in a browser. The XML overhead is worth the ecosystem access.

The connection between streaming computation and streaming media is worth calling out explicitly because it is easy to think of these as two unrelated uses of the same word. But the underlying principle is the same and recognizing that matters. If you understand why variance can be computed in a stream (the formula only needs running aggregates), you can apply the same reasoning to any computation: can this be expressed as running aggregates? If yes, it can be streamed. That is a generalizable design principle, not just a statistics fact.

---

## 6. Creating

The streaming variance pattern generalizes to a class of algorithms worth thinking about as a category. A StreamingStatistics class that maintains running values for count, sum, and sum of squares and can report mean, variance, and standard deviation at any point in the stream is a clean reusable tool. The interface is simply: add a number, query whatever statistic you need. No dataset stored, no second pass required:

```java
class StreamingStats {
    private long n = 0;
    private double sumX = 0, sumX2 = 0;

    void add(double x) {
        n++;
        sumX += x;
        sumX2 += x * x;
    }

    double mean()     { return sumX / n; }
    double variance() { return (sumX2 / n) - Math.pow(mean(), 2); }
    double stdDev()   { return Math.sqrt(variance()); }
}
```

This would be directly useful in a graphics application that tracks rendering statistics (frame times, pixel counts, draw call frequencies) without accumulating a log of every value.

On the SVG side, since SVG is just XML and Java has built-in XML libraries (javax.xml), it is entirely possible to generate SVG files programmatically from a Java graphics application. The closed shapes and tree structures from this course could export to SVG: every drawLine() call becomes a `<line>` tag, every drawOval() becomes a `<circle>` or `<ellipse>`, and the result is an SVG file that any browser can render. This would connect the Java graphics pipeline directly to a universally viewable, version-controllable, scalable output format. For the tree project specifically, exporting the tree diagram as SVG would be a meaningful deliverable beyond the Swing application itself.

---

## Key Takeaways

- Streaming means acting on data as it arrives rather than waiting for all of it. This applies to media playback and to computation over large datasets: same principle, different action on each chunk.
- The variance formula σ² = E[X²] - (E[X])² is not just a statistics fact. It is the formula that makes streaming variance computation possible in constant memory. Algebraic equivalence does not mean computational equivalence.
- If a computation can be expressed as running aggregates over a stream, it never needs the full dataset in memory. The expectation formula is the canonical example of this, but the pattern generalizes.
- SVG is XML applied to vector graphics. Shapes are described as tagged text with coordinate attributes. The same lines-and-curves primitives from Lecture 1 show up here, just in a text-based format instead of Java method calls.
- XML defines its own tags and focuses on describing what data is. HTML defines fixed tags and focuses on displaying information. SVG chose XML because descriptions of shapes, not display instructions, is exactly what a vector graphics format needs.