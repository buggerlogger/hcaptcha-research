# Outline match

Drag the shape onto the outline that matches it. Same idea when the question says "the place where it fits".

One filled piece, made of hexagons, sits on the left. Four thin outlines are on the right. One of them is the piece's silhouette.

![steps](img/outline_match_steps.gif)

*The strokes, then each outline filled, then the drop.*

I throw away the textured background and keep the thin bright strokes. Each outline gets filled in, holes included, so a C shape stays a C and doesn't become a disk. Then I lay the piece's silhouette on each fill, middle on middle, scaled so the areas match, and I turn it a little. The pieces are drawn upright, so I only search from 6 degrees one way to 6 the other, one degree at a time. The score is how much the two shapes overlap, the shared area divided by the area either of them covers.

The best outline is the one I want. The drop shifts the piece so its shape, not the corner of its image, sits on that outline. The piece's pixels aren't always centred in the little image, so I add that offset.

I also run the same search the long way round, a full turn in steps of 2 degrees. If that picks a different outline, or the best overlap is under 0.85, I don't trust the answer much. An outline is only kept as a candidate when its filled area is between about a third of the piece and three times the piece. Strokes are the bright thin lines. The background of these boards is much darker than the strokes.

![steps](img/steps.png)

*Each fill scored against the piece. The C is 0.91. The next is 0.46.*

![answer](img/answer.png)

*The piece, in green, on its outline. The cross is the drop.*

On the board in the pictures the piece covers 2144 pixels. Its image middle is (43, 41) and the middle of the actual shape is (39, 42), so the shape sits a few pixels left of the image centre. Four outlines are in play. Their overlaps are 0.911, 0.458, 0.422 and 0.412. The winner is barely turned, about one degree, and almost the same size as the piece. Checking the full turn picks the same outline, at 0.904. The outline's middle is (272, 103). After the little shift for the piece not being centred in its image, the drop is (276, 102). The click on this board was (263, 104).
