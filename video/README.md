# Video

A click in a short clip, a few seconds, 480 by 330. The point is in the video's own pixels. The words pick the measurement. Turning. A bee that does not land. Something related to a category. The odd one out. An animal that lays eggs.

The still versions of turning, the bee, and odd one out are in the area note. The clips are the boards that actually contain the event.

When the strict count is wrong, one too many or none at all, the same measurements still produce a best guess, and the guess is marked low. The mark is the strict rule failing. It is not a second algorithm.

## Turning

Objects are whatever differs from the background. The background of a pixel is its median colour over the clip, and the ground of the frame is the median of those. A pixel is foreground when its colour is more than 40 away from that ground. 40 is above compression noise and below the edge of a drawn object.

An object is a blob of pixels that are foreground in more than 2% of the frames, closed by 3 pixels, and larger than 400 pixels. 2% drops a one-frame glitch. 400 pixels drops specks.

Motion is optical flow inside the object, on frame pairs two apart, and only inside the interior of the object. Pairs whose mean flow is under 0.3 pixels are the still part of the clip and are skipped. 0.3 is twice 0.15. The 0.15 is the flow, per frame, below which the object is being held still on purpose.

Each object keeps the sum of its vertical flow and the sum of its horizontal flow. The axis is whichever sum is larger in the median across objects. The axis is refused when the larger median is under twice the smaller. Under twice, the clip is not committed to one direction, and a sign on the smaller axis is noise.

The odd object is the only one whose sum on that axis has the opposite sign, and only when at least two objects share the normal sign. If that count fails, the fallback is the object with the least motion in the normal direction, marked low.

![turning clip](img/turning_clip.gif)

![turning debug](img/turning_debug.gif)

*Three sums finish near +34 to +41. One finishes at -35.*

On that clip the vertical sums are about -35, +34, +34, +41. The horizontal sums are -2.8, 2.1, 1.0, 3.8. The median vertical size is about 35. The median horizontal size is about 2. The ratio is about 14, which is past 2, so the axis is vertical. One sum is negative and three are positive. The negative object is the click, at (345, 70). The click on this clip was (344, 70).

## The bee

The flowers stay put. The bee moves. The median of every frame is the scene with the bee erased, because the bee is not in one place long enough to enter the median.

Flowers are strong edges in that still scene, edge strength over 205, filled in, of at least 600 pixels. 205 is a hard edge. A soft shadow does not clear it.

The bee in a frame is the largest blob of at least 25 pixels where the frame differs from the median by more than 45. The track is kept only if the bee is found in at least a quarter of the frames. Under a quarter, the track is a flicker.

A landing is a frame where the bee's position is under 3 pixels from both the frame before and the frame after. 3 pixels is sitting. A fly-by moves more than that between frames. The landing counts for a flower when the bee lies inside that flower's box, padded by 8 pixels.

The answer is the flower-shaped sprite with a landing count of 0. Flower-shaped means at least 0.4 times the median area of the flowers that were visited, and width at most 1.4 times height. The 0.4 drops a speck the bee never touched. The 1.4 drops a horizontal streak, which is not a flower even if its count is 0.

If the count of zeros is not exactly one, the fallbacks are, in order, the largest sprite never touched, then the same search at edge strength 140, then whichever flower was visited least. Each of those is marked low. 140 is the second edge cut. It finds faint flowers the 205 cut missed, and it also finds more junk, which is why it is not the first try.

![bee clip](img/bee_clip.gif)

![bee debug](img/bee_debug.gif)

*The path, red while the bee sits. The number is the landing count.*

On that clip the six counts are 4, 4, 0, 4, 0, 2. Two zeros. The one near (296, 134) is wider than 1.4 times its height, so it fails the flower test. The one near (151, 230) passes the area floor and the aspect cut, and its count is 0. That is the click. The click on this clip was (151, 235).

## Related to a category

The items stand still and fade in and out. No frame shows all of them. A pixel that changes over the clip is where an item is. The change has to clear a fixed spread, or the whole frame is an item.

Pixels whose brightness rises and falls together are one item, even if their boxes touch. The link is a correlation above 0.85 after each pixel's curve has been normalised. 0.85 is two curves that share the fade and not much else. Below that, neighbouring items that happen to overlap in space stay separate.

Each item is taken at the frame where its crop is sharpest, the mean absolute gradient inside the box. That crop is embedded. The score is the cosine with the category prompts, minus the cosine with three bland prompts ("a cartoon object" and two phrasings of the same idea). The subtraction removes the similarity that every sticker has with every "a picture of" sentence. What is left is the extra similarity to the category.

The highest score is the click. It is called low when the gap to the second score is under 0.008. The scores live near zero. A gap of a few thousandths is two items the embedding cannot separate. 0.008 is above that jitter.

If the winning box is over 1.8 times the median item area, two items were merged. The box is split along its long side, both halves are scored, and the better half is the click. 1.8 is larger than one item's ordinary size variation and smaller than two items.

![category clip](img/category_clip.gif)

![category debug](img/category_debug.gif)

*Each item at its sharpest frame, with the score.*

On that clip the six scores are about -0.0009, -0.0114, -0.0010, +0.0209, -0.0077, -0.0033. The best is 0.0209. The next is -0.0009.

`0.0209 - (-0.0009) = 0.0218`

0.0218 is past 0.008, so the lead is not jitter. The click is that item's centre, (185, 169). The click on this clip was (181, 172).

## Odd one out

Items are found the same way as in the category clip. For an icon question the places come from the change map itself, blobs where the spread over time is above 0.30 of its own 99.5th percentile, at least 900 pixels. The 0.30 keeps the cores of the icons and drops the faint halo around them.

Each item is embedded. Its similarity to itself is set aside. The number kept is its best cosine with any other item. A repeated animal has a near twin, and that cosine sits near 0.95. The odd one has no twin, and its best cosine is lower.

The click is the lowest of those best-cosines. It is called high when the gap up to the second-lowest is at least 0.04. 0.04 is the gap between "has a twin" and "does not". Two items both a little lonely sit closer than that, and the lower one might be the wrong lonely item.

![oddone clip](img/oddone_clip.gif)

![oddone debug](img/oddone_debug.gif)

*The sheep's closest match is 0.827. The roosters sit near 0.95.*

On that clip the best-match scores are 0.949, 0.949, 0.948, 0.948, 0.827, 0.892. The lowest is 0.827. The next is 0.892.

`0.892 - 0.827 = 0.065`

0.065 is past 0.04. The sheep is the click, at (114, 228). The click on this clip was (116, 231). The 0.892 is another animal that is not a perfect twin of the roosters. It is still 0.065 above the sheep, so it is not the odd one.

## Lays eggs

The same places, the same sharpest frame. Each crop is named against six classes, the animals these boards actually use: manatee, capybara, otter, whale, sea lion, and a crocodile in a hat. A class is the average of three prompts. The probability is a softmax of `100 × cosine` over the six, the same spreading used on the tile boards.

The crocodile is the only one of the six that lays eggs. The click is the item whose top class is crocodile. That is called high, because the top class is the egg-layer and not merely the least-bad cosine. If no item is named crocodile, the click is the highest crocodile probability, and it is marked low.

A board is two clips. Each clip is read on its own. A box over 1.8 times the median area is split the same way as in the category reading, for the same reason.

![eggs clip](img/eggs_clip.gif)

![eggs debug](img/eggs_debug.gif)

*The crocodile's egg probability is about 0.93. The others are near 0.03.*

On the first page the five crops are named sea lion, sea lion, whale, crocodile, otter. Their crocodile probabilities are 0.048, 0.018, 0.032, 0.927, 0.006.

0.927 is the share of the softmax on crocodile for that crop. 0.032 on the whale means the whale's cosine, after the times 100, lost to the whale class and the leftover on crocodile is a few percent. Only one item has crocodile as its top class. Its centre is (233, 199). The click on this page was (234, 203).

The other page is the same split. Crocodile near 0.91, every other crop at about 0.10 or under. The click is (242, 115).
