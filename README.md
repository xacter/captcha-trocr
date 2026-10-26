# captcha-trocr: Captcha OCR model (TrOCR, safetensors)

[![Model](https://img.shields.io/badge/model-TrOCR--small-blue)](https://huggingface.co/microsoft/trocr-small-printed)
[![Format](https://img.shields.io/badge/format-safetensors-orange)](https://github.com/huggingface/safetensors)
[![Transformers](https://img.shields.io/badge/%F0%9F%A4%97-Transformers-yellow)](https://github.com/huggingface/transformers)
[![Release](https://img.shields.io/github/v/release/xacter/captcha-trocr)](../../releases)

**captcha-trocr** is a text-captcha recognition model: a [TrOCR](https://huggingface.co/docs/transformers/model_doc/trocr)
`VisionEncoderDecoderModel` fine-tuned to read alphanumeric captcha images. Give it an image and it returns the
captcha text. It runs with Hugging Face Transformers and PyTorch, on CPU or GPU, and needs no external OCR API.

- **Task:** captcha OCR / image-to-text (case-sensitive letters and digits)
- **Base model:** [`microsoft/trocr-small-printed`](https://huggingface.co/microsoft/trocr-small-printed) (ViT/DeiT encoder + Transformer decoder)
- **Accuracy:** 79.5% exact-match (whole captcha correct) on a held-out 10% validation split
- **Weights:** `model.safetensors`, ~235 MB, float32

## Download

The model files are attached to the **[latest release](../../releases/latest)**:

| File | Description |
|---|---|
| `model.safetensors` | Model weights (~235 MB) |
| `config.json`, `generation_config.json` | Model configuration |
| `processor_config.json` | Image processor settings (384×384) |
| `tokenizer.json`, `tokenizer_config.json` | Tokenizer |

SHA-256 (`model.safetensors`): `a200f03bb992164083421b63e411235581d34e40ba5740f486dc99ecf449ad48`

With the [GitHub CLI](https://cli.github.com/):

```bash
gh release download --repo xacter/captcha-trocr --dir model
```

## Usage

```bash
pip install torch transformers pillow
```

```python
from PIL import Image
from transformers import TrOCRProcessor, VisionEncoderDecoderModel

processor = TrOCRProcessor.from_pretrained("model")
model = VisionEncoderDecoderModel.from_pretrained("model").eval()

image = Image.open("captcha.png").convert("RGB")
pixel_values = processor(images=image, return_tensors="pt").pixel_values
ids = model.generate(pixel_values, max_new_tokens=16)
print(processor.batch_decode(ids, skip_special_tokens=True)[0])
```

## Limitations

- Most remaining errors are upper/lower-case confusions between similar-looking characters (for example `J`/`j`, `I`/`i`, `B`/`b`).
- The model was trained on one style of text captcha; accuracy on other captcha styles will be lower without further fine-tuning.

## Responsible use

This model is intended for research, OCR experiments and testing captchas on systems you own or are authorized to test.
Do not use it to get around the access controls or terms of service of websites you do not control.
