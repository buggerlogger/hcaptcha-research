# Vial

Each piece is a vial on a transparent background. The rack has a few holes. One hole has the same profile as one vial. The others do not.

The match is not a colour match. A cone and a cylinder can be the same grey. The thing that differs is the outline: how wide the shape is at each height, and whether the edges land on each other once the vial is scaled to the hole.

![steps](img/vial_steps.gif)

*Slots, silhouettes, the pair whose edges agree.*

## Finding a hole

Two looks, because some boards show the dark outline and some show the bright glass.

Outline: dark components, tried at three darkness cuts, because the stroke is not the same grey every time. A component is kept when it is 300 to 6000 pixels, 28 to 95 wide, 45 to 160 tall, at least 85% dark, and not in the bottom 15% of the board. Those bounds are the size of a slot on a 480 by 320 board. A shadow and the piece strip fail them. Two finds whose centres are within 25 pixels are the same slot seen at two cuts.

Interior: a bright region, taller than it is wide, with a dark cap in the rows just above it. If it lands on a slot the outline pass already has, it is skipped. The inside of the slot is the bright part. If the bright flood only fills part of the hole, each row is filled from the leftmost outline pixel to the rightmost. Otherwise a half-filled hole looks like a different shape.

## The nine numbers

The vial's opaque pixels are scaled to the slot's height. Then:

The edge distance is the mean distance from each outline to the other, tried at scales 0.9, 1.0 and 1.1 and at shifts of a few pixels. The best (smallest) distance is kept and then negated, so a closer fit is a larger number. A perfect lining-up is near 0. A cone in a cylinder hole is several pixels off and the number is largely negative.

The width profile is the width of each row, resampled to 32 rows, and the correlation of the vial's widths with the slot's widths. A cone gets wider toward the base. A cylinder does not. Correlation near 1 means the same rise and fall. Correlation near 0 means unrelated profiles. This does not care about a small shift. The edge distance does.

Interior overlap is the shared area of the insides over the union, again at a few scales and shifts.

Aspect is the absolute difference of height-over-width. A cosine compares the pair (aspect, 32-row profile) as two short vectors.

Fill is interior pixels divided by the slot's box area, and that number divided by the median fill of the rack. A slot whose interior detection failed is mostly empty. Its other numbers get small for a boring reason. The fill tells the model that.

Two of the nine are flags, 1 when the profile or the overlap could not be computed. A missing measurement must not look like a perfect 0.

## What the model is shown

Each training row is one vial placed on one slot. The label is whether that pair is the pair a person marked. The model is a small boosted tree. It is not shown the pictures again. It is shown the nine numbers.

The edge distance carries about 0.61 of the weight. Aspect and interior overlap carry about 0.13 each. The profile correlation matters, but less, because two different vials can share a vague taper. The probability is the model's `P(this pair is the marked one)`. The highest pair is the answer.

The drop is not the centre of the slot's box. The box includes the stroke and some margin. The drop is the average of the interior pixels.

![slots](img/vial-slots.png)

*The cone on the cone slot is 0.99. The other vial's best is 0.01.*

![fit](img/vial-fit.png)

*The cone silhouette scaled into each slot. Only one follows it.*

On the board in the pictures there are 3 slots. The cone on the matching slot:

edge score -0.46, so the mean edge gap is under half a pixel. Width correlation 0.88. Interior overlap 0.85. Aspect gap 0.00. The model reads that row as 0.992.

The same vial on the next slot: edge score -2.38, so the outlines are more than 2 pixels apart on average, and the width correlation falls to 0.41. The model reads 0.097. The other vial is 0.015 or less on every slot. Nothing in between. The probability is not a blend of the nine numbers by hand. The trees learned which combinations were the marked pair.

The slot box is centred near (224, 198). The interior pixels sit higher, average (224, 190). That average is the drop. The click on this board was (223, 195).
