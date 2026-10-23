# Area select

A click on a still picture, 480 by 320. The words pick which measurement runs. Letters behind ribs. Arrows around a circle. A broken pattern. Rings that can be lifted off. Turning, the bee, and odd one out are specified here too, but the still boards for those three are other puzzles filed under the question. Those three show up as clips, and the clip measurements are in the video note.

## Letters

The letters sit behind ribs about 16 pixels apart. The ribs are a stripe, so they are one sharp line in the horizontal frequencies of the picture, plus the repeats of that line. Notching those frequencies out removes the glass and leaves the letters.

What remains is turned into ink. A wide median, about 100 pixels, estimates a background that ignores a letter, because a letter is narrower than the median window. Ink is the colour distance from that background. A last horizontal smear, one rib wide, clears what is left of the stripe.

Places are the peaks of a smoothed ink, away from the border, merged if they fall within 30 pixels of each other. Each peak is slid to the centre of mass of the ink around it. The crop of that ink, 48 by 48, is what gets read.

The reader is a small net with 27 outputs, A to Z and "not a letter". It is taught on drawn boards only. The drawing places the letters, runs the same rib and the same ink, and the correct letter of each crop is the letter that was drawn. About thirty thousand crops. A real board is not in that set.

The score of a place is the sum of the probabilities of the letters named in the group. Sum, not max. The group is several letters, and a place is good if its probability sits on any of them. The top N places are clicked. N is the count in the question, and it is 2 when the question does not give one.

A click is called high when every chosen place has a group probability of at least 0.5. That is the same half cut as elsewhere. Under 0.5 the mass of the softmax is on letters outside the group, or on "not a letter".

![letters steps](img/letters_steps.gif)

![letters](img/letters.png)

*The last frame is the click.*

On that board the rib period comes out at 16.6 pixels. The group is ABC. The places read as K 0.00, Z 0.01, A 1.00, B 0.92, V 0.00, and two spots whose probability of "not a letter" is 0.62 and 0.99.

A at 1.00 and B at 0.92 are the group mass. K, Z and V have essentially none of it, so they are not in the top two. The V crop is a Y. The net's guess there is wrong, and the group probability is still about 0, so the wrong guess does not become a click. The clicks are near (101, 90) and (380, 156). The clicks on this board were (111, 110) and (393, 171).

## Arrows

About fifteen faint arrows run around a circle, all but two pointing the same way. A reversed arrow is not a different colour. It is the same colour with the head facing the other way. Colour cannot separate them. The head can.

The arrow-coloured pixels are the ones where red and blue both exceed green, after a wide blur has been subtracted so a slow stain is not an arrow. A circle is fit through those pixels. The centre is then nudged until the band around the circle is mirror-symmetric. An arrow is symmetric about its own axis, so a wrong centre makes the unwrapped band fail to fold onto itself. The nudge is a few pixels. It is not a second circle fit.

The ring is cut into a straight strip, one pixel of arc per column. A head pointing one way has its outer wing on one diagonal and its inner wing on the other. The product of the horizontal edge and the vertical edge is negative on one diagonal and positive on the other. Sum that product over the outer half of the strip, minus the sum over the inner half, and smooth along the strip. A normal head becomes a bump. A reversed head becomes a dip. A shaft is flat. Its edges do not form two opposing diagonals, so it adds nothing.

The spacing of the bumps is the lag where the strip correlates with itself. The search for that lag only accepts a spacing that would mean between 9 and 22 arrows. Fewer or more than that is not this puzzle, and the lag would be locking onto noise.

The sign whose peaks add up to more is the flow. The clicks are the strongest peaks of the other sign. A peak has to be at least 0.6 of a spacing away from the previous peak of the same sign, or the same head is counted twice.

The answer is called high when the weakest click is at least twice the next peak of that sign, and at least half the median normal peak. Twice is the gap between a real reversed head and the ripple beside it. Half the normal median means the reversed head is a head, not a dent in the shaft.

An earlier measurement cut the ring into slices and took the long axis of each slice as the arrow. That axis points outward from the centre of the circle for every slice, reversed or not. The with-or-against bit was noise. The wing product is the measurement that actually changes sign.

![arrows steps](img/arrows_steps.gif)

![arrows](img/arrows.png)

*Cyan with the flow. Orange against it. Under the picture, bumps and dips.*

On that board the circle settles near (293, 155), radius 116. Unwrapped, 729 pixels around, spacing 51 pixels, which is about 14 arrows, inside 9 to 22. Fifteen heads point clockwise. The two the other way have size 143774 and 95188. The next dip is 21350.

`143774 / 21350` is about 6.7, which is past 2. The normal median is 102828, and 95188 is more than half of it. Both reversed heads clear both cuts. The clicks are near (340, 49) and (258, 265). The clicks on this board were (342, 62) and (251, 265).

## A broken pattern

Tiles in a light grid. The repeating direction is whichever of rows or columns is more alike inside itself. A stranger is a tile far from the typical object of its own line, and closer to some other line.

The grid is the pale, unsaturated pixels. The extent of those pixels is divided by 63, which is the tile size these boards are drawn at, and rounded. The grid is kept only when every cell is at least 12% pale, the centre of every cell is at least 25% ink, and both sides have at least four cells. Those three cuts reject a pale band that is not a grid.

Each cell is embedded. Similarity is the cosine of two cells. For each line, the medoid is the cell most like the others. The prototype is the average of the medoid and the cells closest to it, the closest half of the line plus one, normalised. The stranger is left out of that average on purpose. If it were included it would pull the prototype toward itself and stop looking strange.

`fit = cosine with own prototype - 0.5 × max(0, best other prototype - own)`

The 0.5 is a weight, not a probability. A tile that is a bit closer to another line pays half that extra similarity. Without it, a tile that matches its own line poorly but matches every line poorly looks like the stranger. The clicks are the N lowest fits. N comes from the question, and it is 3 when the question does not say.

![pattern](img/pattern.png)

*The grid that was found. The red marks were placed by eye on this figure. The fit numbers for this board were not stored.*

The box runs from about (78, 0) to (393, 319). The least pale cell is still about 0.53, above 0.12. The least ink in a cell centre is about 0.34, above 0.25. Both cuts pass, so the extent is treated as a 5 by 5 grid rather than a pale stain.

## Rings

A ring that passes over the big circle is an unbroken green tube. A ring trapped under the circle is broken where the circle crosses it. The score is how much of the tube is visible.

Green tube pixels are the ones where green exceeds red and blue by the ratios written into the mask, and green itself is not bright. Those ratios separate the tube from the circle and from the background. A blob of at least 300 pixels, outer radius at least 12, filling at least 35% of its annulus, is a ring. 35% drops a green smear that happens to be round.

The tube fraction walks 72 angles, every 5 degrees, and asks whether a tube pixel sits within 3 pixels of the mid radius. The fraction is the share of angles that say yes. The top 3 fractions are the clicks, which is the count these questions ask for. The click is the average of the ring's pixels.

## Turning, as a still

No still board of this question has been captured. The filed stills are other puzzles. The measurement, if a still were real, would be: blades are the closed dark regions, ignoring the right-hand 20% where the piece sits. The hub is the interior point whose blade tips are most evenly spaced. Tip redness is the share of the outer shell of an arm, past its 80th percentile of radius, where red exceeds green by more than 60. The odd arm is the one with the lowest redness, and only if at least two arms are above 0.25 and the odd arm is under 0.85 of the median. 0.25 means a tip is actually red. 0.85 of the median means the odd arm is not just the least red of a set that is all red. The clip version measures motion instead of redness, because a clip has time and a still does not.

## The bee, as a still

No still board of this question either. A still reading would cut the picture into a 3 by 3 and embed each cell against "a photo of a bee on a flower". The lowest cosine would be the click. That measurement never sees a landing. The clip version counts frames where the bee sits inside a flower, which is the event the question names.

## Odd one out

No still board for this route either. The filed pictures are other puzzles. The measurement cuts the picture into a 3 by 3, shrinks each cell to 64 by 64 grey, and subtracts the mean and divides by the spread so brightness cannot decide it. The distance of a cell is the sum, over the other eight, of the mean squared pixel difference. That sum is divided by the average distance, so the number is a ratio to the typical cell. The click is the largest ratio. A mirrored copy of each cell is also compared. That second number does not change which cell wins.
