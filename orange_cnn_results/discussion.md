## 12. Discussion and conclusion

### 1. Round 1: a large dataset limits overfitting
The basic CNN has **8,483,397 parameters** (99% of them in its first fully-connected layer) and is trained on **10,500 images** with no augmentation, BatchNorm or dropout. It overfits only **mildly**:
- Training accuracy reaches **99.9%** and training loss **0.003**. Validation accuracy averages **98.7%** over the last 5 epochs (best **99.38%** at epoch 16).
- After epoch 4, validation loss barely improves and fluctuates (0.024 to 0.097), while training loss keeps falling. At the last epoch validation loss is 0.088, about 30× the training loss, and validation accuracy drops to 97.38%.

So the model fits its training images more tightly than new ones, but with this many images the effect is small. Our earlier mango experiment (2,100 training images) showed a far larger gap: about 11 points.

### 2. Round 2: augmentation, BatchNorm and dropout
| | Round 1 (basic) | Round 2 (improved) |
|---|---|---|
| Parameters | 8,483,397 | **4,585,221** (−46%) |
| Best validation accuracy | 99.38% (epoch 16) | 99.02% (epoch 29) |
| Validation accuracy at last epoch | 97.38% | **98.93%** |
| Validation accuracy variation, last 10 epochs | ±0.7 points | **±0.3 points** |
| Final train − validation gap | +2.5 points | −1.2 points |
| **Test accuracy** | **99.33%** (2,235/2,250) | **99.11%** (2,230/2,250) |
| Macro F1 | 0.9933 | 0.9911 |
| Training time | 2.9 min | 10.9 min |

**The two models perform the same on the test set.** Round 2 is 0.22 points lower, which is just **5 images out of 2,250**. That is within the random variation expected from a single test split: one standard error is about 0.2 points at this accuracy. The real differences are elsewhere:
- **Stability:** Round 2's validation accuracy is steady at the end of training (±0.3 points); Round 1's jumps around (±0.7 points, ending at 97.38%). Round 1's good test score depends on picking exactly the right epoch.
- **Size:** Round 2 reaches the same accuracy with **46% fewer parameters**.
- **Not fully converged:** Round 2's training accuracy was still rising (97.7%) when the learning rate reached its minimum. Augmented images are harder to fit, so more epochs would likely help it more than they would Round 1.

On this large and fairly clean dataset, the regularization tools mostly make training **more stable and the model smaller**, rather than more accurate. On small datasets they matter much more (our mango experiment: +7.3 points).

### 3. Test-set performance of the final model (Round 2)
- **Test accuracy 99.11%** (2,230 / 2,250), macro precision 0.9912, macro recall 0.9911, **macro F1 0.9911**.
- **green_orange: 100%** precision and recall. Its colour makes it the easiest class.
- **rotten_orange** 99.8% recall, **fresh_orange** 99.1%, **blackspotted_orange** 99.3%.
- **Worst class: cankered_orange** (97.3% recall, 438/450).
- **14 of the 20 errors are between blackspotted_orange and cankered_orange**: 11 cankered → black-spotted and 3 black-spotted → cankered. That is why black-spotted has the lowest precision (0.976).
- The other errors: 4 fresh → rotten, 1 rotten → fresh, 1 cankered → fresh.

### 4. Why these errors happen (from the misclassified images)
- **Black-spotted vs cankered (14 errors).** In the misclassified grid, the oranges from both classes look almost the same: dull greenish-orange fruit on a grey or white background with a few small dark marks. At 128×128 pixels the difference between flat black spots and raised, corky canker lesions is mostly lost. Several of these test images also **appear to show the same orange photographed from slightly different angles**.
- **Fresh → rotten (4 errors).** All four are unusual, studio-style photos: oranges with peeled segments on a pure white background, or a dark black background with a label sticker. Most fresh oranges in the dataset are plain photos on white, while the rotten class uses many different backgrounds (wood, blue cloth, plates). The model seems to have partly learned **"unusual background → rotten"**, which is a shortcut, not a fruit feature.
- **Rotten → fresh (1 error, 98% confidence).** A shrivelled, wrinkled orange with no visible mould. Its colour is normal orange, and the model relies heavily on colour and mould patches.
- **Confidence:** none of the 20 wrong predictions was below 50% confidence, and only 3 were above 80%. Most fall between 51% and 76%, so the model was usually **unsure** when it was wrong. In practice, a confidence threshold (e.g. sending predictions below 80% to a person) would catch most of these errors.

### 5. Limitations
- **Possible near-duplicates.** The duplicate check found no identical files, but the misclassified grid suggests some oranges were photographed several times. If shots of the same orange ended up in both training and test sets, the test accuracy would be slightly optimistic. Splitting by individual fruit would need fruit IDs, which the dataset doesn't provide.
- **Background shortcuts.** Classes partly differ in photo style and background (see the fresh → rotten errors), so accuracy on photos taken in a different setting may be lower.
- Each image shows one side of the fruit, so a defect on the hidden side can't be seen.
- Images are downscaled from 224×224 to 128×128, which removes fine peel texture. This hurts mainly the black-spotted vs cankered decision.
- One random 70/15/15 split: each test error changes accuracy by 0.04 points, and the two rounds differ by only 5 images. Several random seeds or k-fold cross-validation would be needed to rank them reliably.

### 6. Future work
- Train at the full 224×224 resolution to keep spot and lesion texture.
- Train Round 2 for more epochs (it was still improving).
- Use transfer learning (e.g. MobileNetV2 for on-phone grading) and Grad-CAM to check whether the model looks at the peel or at the background.
- Use background-removal or background-randomizing augmentation to break the background shortcut.
- Use k-fold cross-validation with several seeds.

### 7. Conclusion
A CNN trained from scratch classifies the five orange quality classes with **99.11% test accuracy (macro F1 0.9911)**. Green, rotten and fresh oranges are almost perfectly separated. The remaining difficulty is telling **black-spotted from cankered** oranges, whose small dark marks look alike at low resolution. With 10,500 training images even the basic CNN overfits only mildly and reaches the same test accuracy (99.33%, a 5-image difference). Augmentation, BatchNorm, dropout and a cosine learning-rate schedule give a **more stable model with 46% fewer parameters** instead of a higher score. This contrasts with the small mango dataset, where the same techniques added 7.3 points of accuracy.