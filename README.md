[![Multi-Modality](agorabanner.png)](https://discord.gg/qUtxnK2NMf)

# Gemini

![gemini](gemini.png)

The open source implementation of Gemini, the model that will "eclipse ChatGPT", it seems to work by directly taking in all modalities without an encoder for some kind which means that the encoding is built into the modal.

input sequences {texts, audio, imgs, video} -> [tokens] -> transformer -> conditional decoding for img gen

This architecture looks very similiar to Fuyu's architecture just extended to many modalities, where instead of an vit encoder you just pass in the img embeddings into the transformer.

The token inputs to gemini will most likely be denoted by special modality tokens `[IMG] or <img> or [AUDIO] or <audio>`

Codi also has conditional generation leverages the tokenized outputs.

To implement this, I plan to cover the img embedding first make sure that works well and then go onto the audio embeddings and then the video.


# Install
`pip3 install gemini-torch`


## Usage

### Gemini Transformer Usage
- No multi-modal yet
- Just language
- Rope, xpos, alibi, etc, multi grouped queries, qk_norm
```python
import torch 
from gemini_torch import Gemini

# Initialize the model
model = Gemini(
    num_tokens=50432,
    max_seq_len=8192,
    dim=2560,
    depth=32,
    dim_head=128,
    heads=24,
    use_abs_pos_emb=False,
    alibi_pos_bias=True,
    alibi_num_heads=12,
    rotary_xpos=True,
    attn_flash=True,
    attn_kv_heads=2,
    qk_norm=True,
    attn_qk_norm=True,
    attn_qk_norm_dim_scale=True,
)

# Initialize the randint
x = torch.randint(0, 50432, (1, 8192))

# Apply model to y
y = model(x)

# Print logits
print(y)
```
--------

### Multi-Modal with Imgs
- Processes Img natively no encoders, we can definitely optimize this like Fuyu

```python
import torch
from gemini_torch.model import Gemini

# Initialize model
model = Gemini(
    num_tokens=50432,
    max_seq_len=8192,
    dim=2560,
    depth=32,
    dim_head=128,
    heads=24,
    use_abs_pos_emb=False,
    alibi_pos_bias=True,
    alibi_num_heads=12,
    rotary_xpos=True,
    attn_flash=True,
    attn_kv_heads=2,
    qk_norm=True,
    attn_qk_norm=True,
    attn_qk_norm_dim_scale=True,
)

# Text shape: [batch, seq_len, dim]
text = torch.randint(0, 50432, (1, 8192))

# Img shape: [batch, channels, height, width]
img = torch.randn(1, 3, 256, 256)

# Apply model to text and img
y = model(text, img)

# Output shape: [batch, seq_len, dim]
print(y.shape)

```



# References
* Combine Reinforcment learning with modular pretrained transformer, multi-modal capabilities, image, audio, 
* self improving mechanisms like robocat
* PPO? or MPO
* get good at backtracking and exploring alternative paths
* speculative decoding
* Algorithm of Thoughts
* RLHF
* [Gemini Report](https://storage.googleapis.com/deepmind-media/gemini/gemini_1_report.pdf)
* [Gemini Landing Page](https://deepmind.google/technologies/gemini/#introduction)


# Todo
- [ ] Implement the img feature embedder and align imgs with text and pass into transformer
- [ ] Implement the audio processing by making an audio processor that intakes in audio embeddings and reshapes it to match language embeddings dimension shape [B, SEQLEN, Dim]
- [ ] Do the same for video