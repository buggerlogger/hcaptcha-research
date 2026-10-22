# Scale

Drag the animal onto the side of the scale that still needs it.

![steps](img/scale_steps.gif)

*The two pans, then the side the animal goes to.*

I look at the pans hanging under the beam.

If one pan is clearly higher, that side is the light one, so the animal goes there. I call it clearly higher when the pan centres are 15 pixels or more apart in height, and only when I actually found both pans. If I had to guess where the pans are, I don't trust the tilt.

If the beam is level, or the pans are too covered to trace, I take the animal picture and see how well it sits on each pan. I try it at a few sizes. The pan where it does not match is the side that's missing it. I want the two matches to differ by at least 0.05 before I believe that.

If they're too close, I compare each whole pan with the animal as a picture embedding. A gap of 0.015 is enough to pick the pan that looks less like the animal. If that is also a tie, I go back to the higher pan.

The drop is the middle of the pan I picked.

A pan, when I can see it, is a light rounded box closed by a dark outline. It doesn't touch the border of the picture. It's a couple of thousand pixels, wider than about 40 and not a huge blob, and it sits in the lower part of the board, left of the piece strip. If the animal already in the pan splits the box into pieces, I glue those pieces back into one pan. Of the pairs I could keep, I want both pans large, and I don't want one of them much higher than the other just because I grouped the pixels badly.

When I can't get a clean pair I look for the dark rim near where the pans usually hang, around a quarter of the way across and around sixty percent of the way across. The lower rim on one side means that pan hangs lower. I build the two centres from those rims.

![tilt](img/scale-tilt.png)

*The beam is tilted. The right pan hangs higher, so the elephant goes there.*

On that board the left pan sits near (92, 207) and the right near (284, 162). The right one is about 45 pixels higher. Drop on the right. The click on this picture was (283, 149).

![species](img/scale-species.png)

*The elephant is already in the left pan. It matches the left at about 0.95 and the right at about 0.25, so it goes to the right.*

On that one I never found a clean pair of outlines, so I used the usual pan positions. The match gap is large, so the picture of the elephant decides it, not the height.
