# Both shapes

Drag the X so it sits inside both shapes.

Two outlines overlap on a noisy background. The X is waiting off to the right, so I only look at the left three quarters of the board.

![steps](img/xboth_steps.gif)

*The overlap, then the drop.*

A small net looks at that left part and, every 4 pixels, says how likely the spot is inside both shapes. I don't drop on the single hottest cell. I take everything connected to the peak that is at least 70% of the peak, and I drop at the weighted middle of that blob. The middle of the overlap is what I want, and the peak is often over on one edge of it.

The net was trained on boards I generated: two random shapes on a swirl background, where I know the overlap because I drew it. The 70% cutoff was chosen on generated boards I didn't train on.

If I don't have the weights, or the net doesn't mark a blob, I fall back to the geometry. I fit a circle or a rectangle to each coloured stroke and drop at the middle of where those two fits overlap.

![steps](img/steps.png)

*A circle and a square. Red is the overlap. Green cross is the drop. The ring mark is the click.*

On that board the map is sure of a cell near (210, 158), and hundreds of cells around it are in the same blob. The weighted middle is (245, 148), which is the middle of the overlap, not the peak. The click on this board was (250, 148).

![example2](img/example2.png)

*The paint runs a little past the circle. The middle of the blob is still inside both.*
