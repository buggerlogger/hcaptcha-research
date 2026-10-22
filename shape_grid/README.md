# Shape grid

Put the right shape in the empty cell.

The board is a grid of bright coloured shapes. One cell is empty. Each column is one kind of shape, so the piece that belongs in the hole is the one that looks like the other shapes in that column.

![steps](img/shape_grid_steps.gif)

*The grid, the empty cell, the piece.*

I find the shapes as the saturated pixels that aren't almost white. Their x positions cluster into columns and their y positions into rows. If a gap is several steps wide I fill the missing lines back in, otherwise the empty cell would delete a row or a column and the grid would be short.

Then I look at the middle of each cell and ask how far its colour is from the background of the whole board. The empty cell is close to the background. The cells with a shape in them are far from it. I take the lowest, and I want it at or under about two thirds of the typical cell, so a slightly dim shape doesn't get called empty.

The drop is that cell's place on the lattice, the column's x and the row's y.

The piece comes from the column. I turn each piece, and each other shape in the empty column, into a small silhouette. The score is how well the silhouettes overlap, minus a penalty if one of them has a hole and the other doesn't. A ring and a disk can overlap a lot in the wrong way. The highest score is the piece.

![grid](img/grid-hole.png)

*The number in each cell is how far it is from the background. The empty one is 17. The rest are 136 to 232. That column is spheres, so the sphere piece wins, 0.96 against 0.79.*

On that board the columns sit near x = 166, 250, 336, 417, about 82 pixels apart, and the rows near y = 57, 157, 257, about 99 apart. The background is a grey green, around (165, 191, 171). The empty cell's fill is 17. The median of the others is about 213, and two thirds of that is about 138, so 17 is comfortably empty. The other shapes in the column are solid. One piece overlaps them at 0.87 but has a small hole, so it drops to 0.79. The sphere overlaps at 0.96 and has no hole. Drop at (166, 257). The click on this board was (165, 252).
