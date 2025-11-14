# Image Caption Generator

This project implements an end-to-end **image captioning model** that generates natural-language descriptions from images.  
It combines **CNN-based image feature extraction**, **GloVe-based word embeddings**, and an **RNN/sequence model** for caption generation.  
Both **Greedy Search** and **Beam Search** decoding are supported.

---

# 1. Project Overview

**Goal:**  
Automatically generate captions such as:

> *“a dog swims in a pool”*  
> *“students walk to school”*

This task is similar to Machine Translation but instead maps  
**image → sentence** rather than **text → text**.

---

# 2. Tools Used

- Python  
- scikit-learn  
- TensorFlow / Keras  
- NumPy  
- Matplotlib  
- Visual Studio / VS Code  

---

# 3. Dataset

The dataset consists of:
- Images (e.g., `33108590_d685bfe51c.jpg`)
- Corresponding captions stored in `.txt` files:
image_id #0 caption1
image_id #0 caption2
<img width="1490" height="418" alt="image" src="https://github.com/user-attachments/assets/38322cd3-7737-4f28-bfff-ccc51e904650" />

...
Multiple captions exist per image.

---

# 4. Text Preprocessing

Steps:
1. Remove special characters: `. , ? ! ~`
2. Convert all text to lowercase
3. Add **start** and **end** tokens to each caption
4. Build mapping structures:
 - `descriptions = { image_id : [caption1, caption2, ...] }`
 - `word_int = { word : index }`
 - `int_word = { index : word }`
5. Calculate:
 - total number of unique words  
 - maximum caption length  
6. Apply **zero padding** to shorter sequences

Example:
"start practical deep learning ... edge end"
→ padded to match max_length
---

# 5. Word Embedding (GloVe)

Words are converted into vector representations using pretrained **GloVe embeddings**.

- Vocabulary size: ~2000 words  
- Embedding matrix created where:
index → embedding_vector (e.g., 200-dim)
This embedding matrix is fed into the captioning model.

---

# 6. Image Feature Extraction

Images are passed through a CNN (e.g., VGG, Inception, or similar) to generate a fixed-length feature vector.

Pipeline:
Image → CNN → Dense Layer → 2048-dim image feature vector
These vectors form the image input to the captioning model.

---

# 7. Data Generator (Image + Text)

For each caption:
- Convert sentence into integer tokens  
- Build training pairs:
X1: image feature vector (repeated for each word position)
X2: partial caption sequence
y : next word to predict

Example:
Input sequence: start student goes to school end
Training samples:
[start] → student
[start student] → goes
[start student goes] → to
...
---

# 8. Model Architecture

Two inputs:
- **Image vector** (2048-dim)
- **Caption sequence** (padded word tokens)

Components:
- Embedding layer (initialized with GloVe)
- RNN/LSTM decoder
- Concatenation of image features + text features
- Dense output layer over vocabulary size

Regularization:
- Tested dropout values: 0.3, 0.4, etc.

---

# 9. Training

- Model trained on `(image, partial_caption)` pairs  
- Optimized to predict the next word at each step  
- Evaluated using validation captions  
- Tuned dropout, concatenation strategy, and embedding dimensions  

---

# 10. Caption Generation (Inference)

Two decoding methods are implemented:

### **1. Greedy Search**
Select the highest-probability word at each step.
- Fast, simple
- Often less diverse

### **2. Beam Search**
Keep the top-k candidate sequences at each step.
- Produces more accurate, natural captions
- Slower but higher quality

Example difference:
- Greedy: *“boy is jumping into water”*
- Beam:   *“a young boy is jumping into the water”*

---

# 11. Results

Includes sample outputs for both Greedy and Beam Search.  
Beam Search consistently generates more fluent and meaningful captions.
<img width="1110" height="657" alt="image" src="https://github.com/user-attachments/assets/a42d3365-f2e4-4776-863d-d2d76d02c95c" />
<img width="1314" height="564" alt="image" src="https://github.com/user-attachments/assets/e8ca4abc-5553-4cf3-bda6-ef87d6a94398" />

---

# 12. Summary

- Complete pipeline from raw text + images to final captions  
- Custom text preprocessing and vocabulary building  
- GloVe-based embedding matrix  
- CNN for image feature extraction  
- RNN decoder with Greedy and Beam Search  
- Outputs meaningful natural-language captions

---

# References
- GloVe: Global Vectors for Word Representation  
- TensorFlow / Keras documentation  
- Original image caption datasets (Flickr, MS-COCO style formats)
