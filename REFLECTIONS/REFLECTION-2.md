# Daily Reflection Journal | CSC 360: Computer Graphics

| Field | Details |
|---|---|
| **Entry Date** | 19th August 2026 |
| **Session Date** | 6th July 2026 |
| **Session Topic** | Lecture 2 – SSH, Key Pairs, Git Protocols and Vector vs Raster Graphics |

---

## 1. Remembering

SSH stands for Secure Shell. It is a network protocol for securely accessing remote systems. The two protocols for accessing git repositories are SSH and HTTPS. The tools used for SSH operations covered in this lecture are ssh-keygen and Microsoft PowerShell.

SSH KeyGen is the command used to generate a key pair: a private key (which stays on your machine) and a public key (which you share with the server). Public keys are what you upload to platforms like GitHub. Private keys never leave your machine.

On the graphics side, two important distinctions were introduced. Static graphics are pre-rendered images that do not respond to user input. Interactive graphics update in real time based on what the user does. The second distinction was Vector vs Raster graphics. Vector graphics store images as mathematical descriptions (lines and curves). Raster graphics store images as a fixed grid of pixels. The lecture also included a live demo of a triangle acting as a cursor moving across a campus coordinate space, and a separate demo of that same triangle zooming in and zooming out.

---

## 2. Understanding

The SSH vs HTTPS difference is best understood by thinking about what each protocol actually sends over the wire. HTTPS sends credentials, even if they are token-based, over a TLS-encrypted channel. SSH never sends a secret at all. Here is how it works: you generate a key pair with ssh-keygen. The public key goes on GitHub. The private key stays on your machine. When you try to connect, the server sends your client a challenge. Your client uses the private key to respond to that challenge in a way that proves you hold the matching key, without ever revealing the private key itself. The server checks the response against your public key and lets you in. No password, no token, nothing secret transmitted.

A good analogy for this is a padlock and key. The public key is a padlock you give to everyone. Anyone can click it shut (the server "locks" a challenge using your public key). But only you have the key that opens it (your private key solves the challenge). This is asymmetric cryptography in practice, and it is more elegant for a developer workflow than typing passwords or managing token expiry.

PowerShell shows up here because it is the modern cross-platform shell that ships on Windows and is available on macOS and Linux too. Running ssh-keygen through PowerShell makes the setup reproducible across any operating system a developer might be on.

The static vs interactive graphics distinction connects directly to what a graphics program is actually doing under the hood. Static graphics is a single paint operation. You call your draw functions once and the image is done. Interactive graphics needs to keep listening. It has a loop (or an event-driven model) that says: did the user do something? If yes, update the state and repaint. The triangle demo made this concrete. The triangle does not just sit there. It responds to input, which means the program is continuously checking for events and redrawing.

Vector vs Raster is the distinction that ties Lecture 2 back to Lecture 1 directly. Vector graphics store shapes as mathematical descriptions, which is exactly the lines and curves we identified as geometric primitives last session. Because the shape is stored as a formula, you can render it at any resolution without losing quality. Raster graphics store a fixed grid of pixels. Scale them up and you are just making each pixel bigger, which is why photos get blurry when you zoom in. In Computer Graphics we almost always work in vector space and only convert to pixels at the very last step (rasterization), which is actually where the word "raster" comes from.

---

## 3. Applying

The SSH setup process follows a clear sequence. Run ssh-keygen in PowerShell or terminal. It generates two files: id_rsa (the private key) and id_rsa.pub (the public key). Open the .pub file, copy the contents, and paste them into GitHub under Settings > SSH and GPG keys. From that point on, git clone, push, and pull over SSH all work without any credential prompts. Set it up once, and it is done.

The practical difference between cloning over HTTPS and SSH is visible right in the URL. HTTPS looks like https://github.com/user/repo.git. SSH looks like git@github.com:user/repo.git. Switching an existing repo from HTTPS to SSH is just: git remote set-url origin git@github.com:user/repo.git.

For the graphics demos, the triangle-as-cursor is the most basic interactive graphics program possible. A shape has a position. User input changes that position. The canvas repaints with the shape at the new position. Zooming in and out is a geometric scaling transformation applied to the entire coordinate space. Zoom in doubles the scale factor. Zoom out halves it. The triangle's actual coordinates do not change; the mapping from world coordinates to screen coordinates does. This distinction between world space and screen space is going to matter a lot later in the course.

---

## 4. Analyzing

SSH vs HTTPS for git comes down to two trade-offs: setup friction vs long-term convenience, and network compatibility. SSH takes a few minutes to set up initially but after that it is completely frictionless. HTTPS is easier to start with but requires managing credentials or tokens that expire. On the other hand, SSH uses port 22, which some corporate firewalls and restrictive networks block. HTTPS uses port 443, which is almost never blocked anywhere. So in a locked-down environment, HTTPS is the only option. But for day-to-day development on a normal network, SSH wins.

Vector vs Raster also has a less obvious trade-off that is worth thinking about. Vector is resolution-independent and scales cleanly, which sounds like a pure win. But rendering a very complex vector scene, something with thousands of overlapping shapes and gradients, is computationally expensive because every shape has to be computed from its mathematical description on every frame. Raster is computationally cheap to display because the pixels are already computed. This is actually why photo editing software works in raster: real-world images are too complex to represent as mathematical shapes. In this course we work in vector space precisely because our shapes are geometric and well-defined.

The triangle demo also revealed something about static vs interactive graphics at an architectural level. Static graphics does not need a game loop. Interactive graphics does. Or, in the Event Listener Model from Lecture 1, it needs listeners wired up to repaint the canvas whenever the relevant state changes. This connects the two halves of Lecture 2 in an interesting way: the SSH and tooling setup is how we get code onto machines, and the graphics concepts are what that code produces. The plumbing and the output.

---

## 5. Evaluating

SSH is the better choice for this course's workflow. We are going to be pushing code frequently, and having git stop asking for passwords every time is worth the ten-minute setup cost upfront. HTTPS is fine for occasional use but gets annoying fast in an active development cycle.

Vector graphics is clearly the right paradigm for the kind of work CSC 360 is about. Geometric primitives, transformations, scaling, zooming: all of these operate naturally on mathematical descriptions of shapes. The triangle demo itself would not work well in a raster model because every time you zoom, you would need to re-sample the pixel grid. In vector space, zoom is just a change in the scale factor. The math handles it cleanly.

The triangle-as-cursor demo was a good pedagogical choice. It is the simplest possible interactive graphics program, just a shape with a position that responds to input. But it captures the entire architecture: world state, user input, transformation, repaint. Every more complex graphics program we will build is just this, scaled up.

---

## 6. Creating

One extension of the triangle demo I want to try: instead of one triangle as a cursor, have a small fleet of triangles representing different "entities" on the campus map, each with its own position and a velocity vector. They move independently. When you zoom in, you see more detail about each one. When you zoom out, you see the full campus with all of them at once. This would turn the demo into a basic simulation, which sits right at the intersection of interactive graphics and the parametric math from Lecture 1.

On the SSH side, I want to script the entire setup: a PowerShell script that runs ssh-keygen, reads the public key, and prints the exact text to paste into GitHub with the right formatting. Basically automating the "set it up once" part so that setting up a new machine for this workflow takes a single command instead of a sequence of manual steps. That feels like a practical tool that would outlive this course.

---

## Key Takeaways

- SSH (Secure Shell) uses asymmetric key pairs for authentication. Public key goes on the server; private key stays with you. Nothing secret ever travels over the wire, which is what makes it secure.
- SSH over HTTPS is the better git workflow choice: set up once with ssh-keygen, and version control stops asking for credentials forever.
- Vector graphics store mathematical descriptions; raster stores pixel grids. In this course we live in vector space and only hit raster at the final rasterization step.
- Static graphics paints once; interactive graphics listens and repaints. The triangle demo is the simplest possible proof of that distinction, and every complex graphics program is just this architecture scaled up.
- The triangle zooming demo is not trivial. Zoom is a transformation on the coordinate space, not on the shape itself. World coordinates stay the same; the mapping to screen coordinates changes. That distinction is going to matter a lot.
