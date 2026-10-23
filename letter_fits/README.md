# Letter fits

The piece in hand is one letter, A to E. The board has several green striped copies and one pale ghost. The ghost is a decoy. The drop is the green copy of the letter in hand.

Two different readings. The piece is a silhouette, so it can be matched against drawn letters. The board is a photograph of striped letters on a swirl, so a silhouette match against the whole board fails, and a net is taught the look of those stripes instead.

![steps](img/letter_fits_steps.gif)

*The letter is read, then marked on the board. Green is the drop.*

## Reading the piece

The piece is cropped to its opaque pixels and scaled into a 48 by 48 mask. A to E are drawn the same way, in 15 bold fonts, each turned through -28, -20, -12, -5, 0, 5, 12, 20 and 28 degrees. The piece arrives tilted. Those nine angles cover the tilt without a search over every degree.

The score against one drawing is soft overlap:

`sum of min(piece, template) / sum of max(piece, template)`

Where both masks are 1, the numerator and the denominator both count the pixel. Where only one is 1, only the denominator counts it. It is the same idea as shared area over the union, but the edges are soft.

Each letter keeps its best font and angle. The winner is the letter. The margin is that score minus the second letter. A margin near 0 means two letters fit the silhouette almost equally, and the read is weak. The read is still used. There is no second cutoff on the margin. The margin is reported so a close call is visible.

## What the net is shown

The board is 480 by 320. The net looks at every 4 by 4 block and must name it as one of eight things: background, green A, B, C, D, E, some other green letter, or the ghost.

It is not shown real boards. Each training board is drawn. The ground, the swirls, the letters, and the ghost are placed by the drawing, so the correct class of every 4 pixel cell is known exactly. Training pushes the net's eight probabilities toward that class. The weights in use were drawn with swirls that cover a letter or bleach it, and with at least 30% of every letter left visible. A net taught only on clean letters has nothing to say about a letter under a white swirl. The last picture is that case.

![why v3](img/why_v3.png)

*The B that fits is half under a white swirl. A net that never saw a cover puts the point somewhere else.*

## Where 0.60 comes from

The map for the letter that was read is a grid of probabilities. The drop is not the hottest cell. The hot cell is often on the edge of the letter. The blob is every cell connected to the peak whose probability is at least 0.60 times the peak. The drop is the average of those cells, weighted by probability.

0.60 was not chosen by looking at clicks. On generated boards that were held out of training, the same decode was run at 0.30, 0.40, 0.50, 0.60, 0.70 and 0.80. A threshold counts as hitting when the weighted centre lands within 25 pixels of the letter that was drawn. 0.60 is the value that hit most often. Lower keeps swirl noise in the blob and pulls the centre off the letter. Higher keeps only the peak and the centre sticks to one edge.

![steps](img/steps.png)

*The piece reads as B. Red is the green B.*

![answer](img/answer.png)

*The map for B. The cross is the weighted centre.*

On the board in the pictures the piece reads as B, and the soft-overlap margin over the next letter is 0.116. The B map peaks at 0.9974 in the cell whose board pixel is (286, 94).

The cutoff on this map is `0.60 × 0.9974 = 0.598`. 72 cells connected to the peak are at or above that. Another patch also clears 0.598 but does not contain the peak, so it is not the blob. The weighted centre of the 72 cells is (289.4, 90.9). The click on this board was (272, 95).
