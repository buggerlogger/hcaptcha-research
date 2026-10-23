# Screw

The screw goes in the empty joint. A joint is where planks cross. On the picture that place is thick, because two boards overlap, and the edges arrive from more than one direction. The middle of a single plank is also thick, which is why thickness alone picks the wrong spot.

![steps](img/screw_steps.gif)

*Wood, the thick spots, then the re-rank.*

## Wood

Wood is a colour range, not a texture. Hue under about 48 degrees or over about 330, so the reds and oranges, saturation over 0.34, value over 0.18. Grey and black fail the saturation or the value. The right-hand 22% of the board is the piece strip, where the screw itself sits, and it is cut off before the colour test so the screw is not part of the cluster.

The largest connected piece of that mask is the plank cluster. Its centre is the average of its pixels.

## Depth times centrality

For every wood pixel, depth is the distance to the nearest non-wood pixel. A point in the middle of an overlap is deep. A point on the edge of a plank is shallow.

Centrality is `1 / (1 + distance to the cluster centre / 45)`. At the centre this is 1. At 45 pixels out it is 0.5. The 45 is the scale of the cluster, not a score cutoff. It stops a deep spot on a plank far from the middle from tying with a shallower spot in the middle.

The geometric score is depth times centrality. Non-wood is -1 and cannot be chosen.

Peaks are taken one at a time. The maximum is moved to the score-weighted centre of the pixels within 22 pixels that are at least 85% of that peak, so the candidate sits in the middle of the thick region rather than on one pixel. A 22 pixel square around it is then blanked, and the search repeats, up to 8 times. 22 pixels is about the width of a joint. Without the blank, the next seven candidates are the same joint.

![scoremap](img/screw-scoremap.png)

*Red is depth times centrality. The numbers are these peaks, before the re-rank.*

## Why the geometric winner is often wrong

A thick place in the middle of one plank is deep and central. It wins the product. A joint can be less deep and still be the empty hole, because the hole itself removes some of the thickness.

The top 5 peaks are re-ranked by a small boosted tree model. Each training row is one candidate. The label is whether that candidate is the joint a person marked. The model sees 12 numbers. The one it leans on is the mix of edge directions in a 12 pixel window.

Strong edges in that window, gradient magnitude over 18, have their direction folded into 8 bins around half a turn. The score is the entropy of that histogram. Edges that all share a direction put almost all the mass in one bin, and the entropy is low. Edges from several plank corners fill several bins, and the entropy is high. That number carries about 0.40 of the model's weight. Brightness carries about 0.13. The geometric score divided by the best geometric score carries about 0.12, and so does the amount of edge nearby. The model is not asked to rediscover the planks. It is asked which of the thick spots is a crossing.

The output is a probability. The drop is the candidate with the highest one.

![pick](img/screw-pick.png)

*Candidate 4, where the edges cross, at 0.91.*

On the board in the pictures the wood is about 35 thousand pixels, centre (142, 139).

Geometric candidate 1 is (125, 131). Depth 33, only 19 pixels from the centre, geometric score 23.6. Its edge entropy is low. The model gives it 0.035.

Candidate 4 is (134, 81). Geometric score only 15.8, so the product of depth and centrality ranks it fourth. Its edge entropy is 2.07, the highest of the five, and it is brighter, about 127 against about 96. The model gives it 0.907.

0.907 against 0.035 is the re-rank doing the only job it has: the thick central spot is not the crossing. The drop is (134, 81). The click on this board was (129, 69).
