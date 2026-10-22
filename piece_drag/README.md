# Piece drag

Drag the right piece into the hole. The wording changes (finish the puzzle, finish the pattern, put the animal in its outline) but the board doesn't. Two pieces sit off to the side. The scene has one hole.

![steps](img/piece_drag_steps.gif)

*One board. Green is where the piece goes.*

I try the picture first, and the outline only if that fails.

On a puzzle the hole is the original picture, just darkened, down to about half brightness. I slide each piece over the scene and look for a spot that looks like the piece but dimmer. I compare brightness, with the piece's own shape as the mask, so the background of the piece image doesn't count. A real piece matches somewhere around 0.73 to 0.93. The other piece isn't in the scene, so it stays at or below about 0.46.

I trust that match when three things hold. The best score is at least 0.60. It beats the other piece by at least 0.20. And where it lands, the scene really is a dimmed copy of the piece, not a full-brightness copy. I fit scene = a times the piece, plus a constant. A dimmed hole has a near 0.5. If a is 0.90 or more I'm matching the piece against itself and I throw the match out.

On a pattern, or an animal outline, the hole is flat. A dark patch, or a grey silhouette. Sliding the picture around doesn't find anything, because nothing in the scene looks like the piece. So I lay the piece down and measure the colour jump just inside its outline and just outside it. I do that in 8 directions around the piece. The jump has to hold almost the whole way round, so I take the weak end of those 8, not the average. I also divide by how much the colour inside the piece already jumps around, otherwise a noisy piece looks like a strong edge. The shape whose outline sits on the hole wins.

Either way the drop is the middle of the piece, placed on the middle of the hole.

![puzzle](img/puzzle.png)

*The tiger matches the dimmed copy, 0.88 against 0.32. Green outline is the piece on the hole. Green cross is the drop.*

On that puzzle the winning match is 0.882 and the blend comes out at 0.544, so the brightness test is the one I use. The outline test agrees, same spot. The piece is 90 by 86, sitting with its top-left at (206, 65), so the middle is (251, 108). The click on this picture was (254, 83), up toward the top of the hole rather than the middle.

![pattern](img/pattern.png)

*The leaf's outline fits the flat hole, 1.68 against 0.80.*

On that pattern the brightness matches are 0.493 and 0.199, both under 0.60, so I don't use them. The outline scores are 1.677 and 0.799. The leaf wins. Its middle lands at (213, 140.5). The click on this picture was (217, 136).
