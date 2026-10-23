# Tile boards

Nine pictures, and a question. The question is not open. Each wording comes back with the same small set of objects, so the set can be written down once. A tile is then scored against that list, not against the whole world.

The score is a probability. CLIP turns the tile and each text prompt into a vector of length 1. The dot product is the cosine. Cosines between a photo and a sentence sit in a narrow band, so they are multiplied by 100 before the softmax. That 100 is the scale CLIP was trained with. It spreads a difference of 0.02 in cosine into a difference that the softmax can actually separate.

`probability of a class = softmax(100 × cosine with that class)`

A class is the average of its prompts, normalised again. The yes-probability of a tile is the sum of the probabilities of the yes classes. A tile is clicked when that sum is at least 0.5.

0.5 is the point where the yes classes hold as much probability as everything else combined. It is not fit per board. If no tile reaches 0.5, the best tile is clicked anyway, because an empty answer is not a reading, it is a failure of the list.

![class bank](img/1-class-bank.png)

![class bank steps](img/label_binary_steps.gif)

*Nearest object, then the share of probability on a yes object. Green is at or above one half.*

| the question is about | yes | no |
|---|---|---|
| on and off states | a phone | banana, winter coat, ball of yarn |
| buildings that include the element | a house | crow, owl, sports car, and tiles that are mostly the printed watermark |
| kept in the object | pencils | car, van |
| goes inside the container | pencils | cement mixer, auto rickshaw, monster truck, watermark text |
| same room as the reference | an alarm clock | seal, sled, shovel, watermark text |
| things found in a gym | a basketball | hawk, road roller, reindeer |
| costs more than what's shown | an airplane | envelope, broom, basketball |

The no-list is how the probability gets a place to go. If the only class were "phone", every tile would score as a phone to some degree and the softmax would have nothing to push the banana into. The watermark row exists because on some tiles the printed word is what the embedding locks onto, not the object under it. That tile has to be a class of its own or it gets called a house.

On the class-bank picture the nine yes-probabilities are about 0.002, 0, 0.001, 0.003, 0, 0.999, 0.993, 0.004, 0.007.

`0.999` and `0.993` are above 0.5. The other seven are two orders of magnitude under it. The gap is the list doing its job: a phone's cosine with "phone" beats its cosine with "banana" by enough that, after the times-100 softmax, almost all the mass sits on phone.

A heavy purple diamond over a tile hides the object from the embedding. One phone under that overlay scored 0.28. 0.28 is under 0.5, so the half-probability rule misses it. The rule has no separate path for an overlay.

## Absorb

"Absorb liquid" uses the same picture vectors. The prompts are a bad teacher here, because a glass of water is liquid and it does not absorb, and the sentence "absorbs liquid" sits near both. So the decision is a logistic model on the picture vector.

A training row is one tile. The label is whether a person marked it. Yes is a paper towel roll, a sponge, or folded cloths. No is a glass of water, a water bottle, or an iced drink. Those nos are the trap the prompts fall into.

`p = 1 / (1 + exp(-(w · vector + b)))`

`w` is 512 weights, one per dimension of the vector. A tile is clicked at `p` of 0.5 or above, the same half-probability cut. If none reach it, the best `p` is clicked.

Each paraphrase of a prompt is weighted so the paraphrases share one total. Otherwise one wording of "sponge" would count five times and dominate `w`. While the weights are fit, one board is held out at a time, so a tile is not scored by a model that already saw its neighbours on the same board.

## Melt

One bag of "this melts" prompts, one bag of "this never melts". Both bags are averaged into a single vector.

`m = cosine with melts - cosine with never melts`

Melt questions have several yes tiles, and their `m` values sit on top of each other. Clicking only the maximum would drop the rest. Every tile within 0.005 of the best `m` is clicked. 0.005 is the thickness of that tie. It is not a probability. `m` is a difference of cosines, and 0.005 is a small slice of that difference. A tile 0.02 below the best is a different object, not another melter.

## A reference strip

"Vehicles that could be towed by this one", and "heavier than the one shown".

The reference arrives as a strip of four frames. One frame is colour noise. Two are decoys copied from the tiles. One is the reference.

Noise is the frame whose edges are dense. Edge energy is the mean size of the brightness gradient. A flat object frame sits near 10. The noise frame sits near 45. The cut is 25, in the gap between those. On the picture below the four energies are 45.9, 11.1, 12.6 and 8.2, so the first frame is dropped and the other three stay.

Among the three, the reference is the frame least similar to any tile. A decoy is a copy of a tile, so its best cosine with the tiles is high. The real reference is not a tile, so its best cosine is the low one.

Each remaining tile is named from a fixed list. Each entry has a few prompts, a typical mass in kilograms, a kind (road, water, air, construction), and a tow capacity. The name is the entry with the highest cosine, after the direction of printed words has been removed.

That direction is the top components of prompts like the word "car", "wheel", "wings", "bird". A tile vector is projected off those components before it is named, and it is also named without that projection. For a tow question both names have to agree. A word printed on the tile must not be the only reason it was called a car.

Tow or move: kind road, and mass at or under 8000 kg. 8000 is the tow-truck entry's capacity, not a mass that was measured off the reference. The reference often gets read as whatever object it looks like, which may not be the truck. Heavier means mass above the reference's mass. Lighter means mass below it. If the rule selects nothing, the single nearest candidate is returned.

![comparative](img/2-comparative.png)

On that picture the tiles read as four cars at about 1500 kg, an excavator at 25000, a boat at 20000, two ships, and an airplane at 70000. Kind road and mass at or under 8000 leaves the four cars. The excavator is road-adjacent in appearance and fails the kind. The boat is under no tow limit that applies, because its kind is water. The reference itself reads as a boat, which is why the 8000 does not come from the reference's mass.

## A question with no list

The object phrase is taken from the question. The tile's score is its cosine with "a photo of that thing", averaged over three phrasings, minus the best cosine with three bland phrases: a photo of something, a photo of an object, a random photo.

The subtraction is the part that makes the number mean something. A tile correlates a little with any "a photo of" sentence. The bland phrases estimate that floor. What is left is the extra similarity to the named object. A tile is clicked when the extra is above 0. If none are, the best tile is clicked. This is a guess for a wording that has no list. The half-probability rule does not apply, because there is no softmax over a closed set.

## Which reading runs

If the board has a reference picture and the wording is not one of the closed lists, the mass reading runs.

If the question is about orientation, upside down or a rotated image, the lists do not apply. Each tile is compared with the others as a picture, and the one least like the rest is the click. There is no yes-class.

If a list matches, the list runs, even when a reference picture is also on the board.

Otherwise the bland-phrase guess runs.
