# Interactive Fractals

This repository hosts a web application that allows users to interactively explore various fractal patterns, including the Koch Snowflake, Sierpinski Triangle, Hilbert Curve, and Dragon Curve. The application is built using HTML, CSS (with Tailwind CSS for styling), and JavaScript for the fractal generation and user interaction.

## Live Demo

[FractalExplorer]([url](https://mimossaaa.github.io/Fractalexplorer/))

## Features

- **Interactive Fractal Generation:** Dynamically generates different fractal types on a canvas element.
- **Parameter Control:** Allows users to adjust the number of generations (iterations) for each fractal, influencing its complexity.
- **Navigation:** Provides a clear navigation menu to switch between different fractal types.
- **Zoom and Pan:** Implements basic zoom in/out and pan functionality to explore the details of the fractals.
- **Animated Drawing (for Hilbert and Dragon Curves):** Features an animation mode to visualize the step-by-step construction of the Hilbert and Dragon curves.
- **Responsive Design:** Utilizes Tailwind CSS to ensure a responsive layout across different screen sizes.
- **Social Media Links:** Includes links to the developer's GitHub and LinkedIn profiles.

## Technical Explanation: The Math Behind the Fractals

The beauty of fractals lies in their self-similarity and the mathematical rules that govern their generation. Here's a breakdown of the mathematical concepts behind each fractal implemented in this application:

### 1. Koch Snowflake

The Koch Snowflake is constructed iteratively by starting with an equilateral triangle and recursively modifying each line segment.

**Mathematical Basis:**

- **Iteration:** The process is repeated a specific number of times (generations).
- **Line Subdivision:** In each iteration, every straight line segment is divided into three equal parts.
- **Equilateral Triangle Construction:** On the middle segment, an equilateral triangle is constructed that points outwards.
- **Removal of the Base:** The base of the newly constructed triangle (the middle segment of the original line) is then removed.

**Geometric Transformation:**

If a line segment is defined by two points $\mathbf{P}_1 = (x_1, y_1)$ and $\mathbf{P}_2 = (x_2, y_2)$, the transformation to create four new segments involves finding three intermediate points:

1.  **Point A:** Divides the segment into a 1:3 ratio:
    $$\mathbf{A} = \mathbf{P}_1 + \frac{1}{3}(\mathbf{P}_2 - \mathbf{P}_1)$$
2.  **Point C:** Divides the segment into a 2:3 ratio:
    $$\mathbf{C} = \mathbf{P}_1 + \frac{2}{3}(\mathbf{P}_2 - \mathbf{P}_1)$$
3.  **Point B:** The apex of the equilateral triangle. To find this point, we can consider the vector $\mathbf{v} = \mathbf{P}_2 - \mathbf{P}_1$. We rotate this vector by $60^\circ$ (or $\pi/3$ radians) and scale it by $1/3$, then add it to $\mathbf{A}$. The rotation matrix for a $2D$ vector $(x, y)$ by an angle $\theta$ is:
    $$
    \begin{pmatrix} x' \\ y' \end{pmatrix} = \begin{pmatrix} \cos \theta & -\sin \theta \\ \sin \theta & \cos \theta \end{pmatrix} \begin{pmatrix} x \\ y \end{pmatrix}
    $$
    For $\theta = -\pi/3$:
    $$
    \begin{pmatrix} x'_v \\ y'_v \end{pmatrix} = \begin{pmatrix} 1/2 & \sqrt{3}/2 \\ -\sqrt{3}/2 & 1/2 \end{pmatrix} \begin{pmatrix} x_v \\ y_v \end{pmatrix}
    $$
    So, the vector from $\mathbf{A}$ to $\mathbf{B}$ is $\frac{1}{3} \mathbf{v}'$, and:
    $$\mathbf{B} = \mathbf{A} + \frac{1}{3} \begin{pmatrix} 1/2 & \sqrt{3}/2 \\ -\sqrt{3}/2 & 1/2 \end{pmatrix} (\mathbf{P}_2 - \mathbf{P}_1)$$

The `drawKochLine` function in the JavaScript code recursively applies this subdivision and point calculation.

### 2. Sierpinski Triangle (Line Method)

This implementation of the Sierpinski Triangle uses a recursive line drawing approach, where an initial triangle is subdivided into smaller triangles by connecting the midpoints of its sides.

**Mathematical Basis:**

- **Iteration:** The process is repeated for a given number of generations.
- **Midpoint Subdivision:** In each iteration, a triangle with vertices $\mathbf{P}_1, \mathbf{P}_2, \mathbf{P}_3$ is subdivided into four smaller triangles by finding the midpoints of its sides:
    $$\mathbf{M}_{12} = \frac{\mathbf{P}_1 + \mathbf{P}_2}{2}$$
    $$\mathbf{M}_{23} = \frac{\mathbf{P}_2 + \mathbf{P}_3}{2}$$
    $$\mathbf{M}_{31} = \frac{\mathbf{P}_3 + \mathbf{P}_1}{2}$$
- **Recursive Drawing:** The three outer triangles ($\mathbf{P}_1\mathbf{M}_{12}\mathbf{M}_{31}$, $\mathbf{M}_{12}\mathbf{P}_2\mathbf{M}_{23}$, $\mathbf{M}_{31}\mathbf{M}_{23}\mathbf{P}_3$) are then recursively processed. The central triangle ($\mathbf{M}_{12}\mathbf{M}_{23}\mathbf{M}_{31}$) is implicitly "removed" by not drawing it further.

The `drawSierpinskiRecursive` function in the JavaScript code implements this recursive midpoint subdivision and drawing.

### 3. Hilbert Curve

The Hilbert Curve is a space-filling fractal curve that visits every point in a square. It's generated using an L-system (Lindenmayer system), which defines a set of rules for string rewriting.

**Mathematical Basis (L-System):**

- **Axiom:** The starting string: "A"
- **Production Rules:** Rules that define how symbols are replaced in each iteration:
    - A → +BF-AFA-FB+
    - B → -AF+BFB+FA-
- **Angle:** A fixed angle of $90^\circ$.
- **Interpretation of Symbols:**
    - F: Move forward and draw a line.
    - \+: Turn right by the specified angle.
    - -: Turn left by the specified angle.
    - A, B: Symbols that are rewritten according to the production rules but do not directly correspond to drawing actions.

**String Generation:**

The `generateLSystemString('hilbert')` function iteratively applies these rules to the axiom for a given number of generations, resulting in a long string of drawing commands.

**Drawing the Curve:**

The `renderLSystem('hilbert', stepsToDraw)` function interprets this string. It maintains a current position $(x, y)$ and an angle. For each symbol in the string:

- 'F': A line is drawn from the current position to a new position calculated using trigonometry:
    $$x_{new} = x_{current} + \text{stepLength} \times \cos(\text{angle})$$
    $$y_{new} = y_{current} + \text{stepLength} \times \sin(\text{angle})$$
- '+': The angle is increased by $90^\circ$.
- '-': The angle is decreased by $90^\circ$.

The `animateLSystem('hilbert')` function draws the curve step-by-step to visualize its construction.

### 4. Dragon Curve

The Dragon Curve is another fractal generated using an L-system, known for its intricate folding pattern.

**Mathematical Basis (L-System):**

- **Axiom:** The starting string: "FX"
- **Production Rules:**
    - X → X+YF+
    - Y → -FX-Y
- **Angle:** A fixed angle of $90^\circ$.
- **Interpretation of Symbols:**
    - F: Move forward and draw a line.
    - \+: Turn right by the specified angle.
    - -: Turn left by the specified angle.
    - X, Y: Symbols that are rewritten according to the production rules but do not directly correspond to drawing actions.

**String Generation and Drawing:**

Similar to the Hilbert Curve, the `generateLSystemString('dragon')` function generates a string based on the axiom and production rules. The `renderLSystem('dragon', stepsToDraw)` function then interprets this string to draw the curve, using the same trigonometric principles for movement and angle manipulation as described for the Hilbert Curve. The `animateLSystem('dragon')` function provides a step-by-step visualization.

### Pan and Zoom

The basic pan and zoom functionality implemented here involves adjusting the starting points and scaling factors when drawing the fractals on the canvas.

- **Panning:** When the user drags the canvas, the `translateX` and `translateY` variables are updated, effectively shifting the origin of the drawing. This offset is then applied to the initial coordinates of the fractal.
- **Zooming:** The `globalScale` variable is adjusted when the user clicks the zoom in or zoom out buttons. This scale factor multiplies the dimensions and positions of the fractal elements, making them appear larger or smaller.

For the line-based fractals (Koch and Sierpinski in this implementation), the initial size and positions of the base shapes are multiplied by the `globalScale`, and the `translateX` and `translateY` are added to their coordinates. For the L-system based fractals (Hilbert and Dragon), the `stepLength` is influenced by the `globalScale`, affecting the overall size of the curve.

## Getting Started

To run this application locally:

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/your-username/your-repo-name.git](https://github.com/your-username/your-repo-name.git)
    ```
2.  **Navigate to the project directory:**
    ```bash
    cd your-repo-name
    ```
3.  **Open the `index.html` file in your web browser.**

## Contributing

Contributions to this project are welcome. If you have ideas for new fractals, improved interactivity, or bug fixes, please feel free to open an issue or submit a pull request.

## Author

[Majid Rebouh](https://github.com/mimossaaa)


