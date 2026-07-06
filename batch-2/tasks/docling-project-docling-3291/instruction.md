Ensure that the Rapid OCR feature respects the language setting by loading the appropriate model assets. Implement logic to download both English and Chinese models for offline use, covering all supported backends.

*   Update the `RapidOcrModel` class in `docling/models/stages/ocr/rapid_ocr_model.py`:
    *   Modify the `__init__` method to select model paths based on the `lang` field from `RapidOcrOptions`.
        *   Use English model paths if `lang` contains "en" or "english".
        *   Default to Chinese model paths if no language or a different language is specified.
    *   Ensure the correct model paths are used:
        *   For the `onnxruntime` backend with English:
            *   Detection: `onnx/PP-OCRv4/det/en_PP-OCRv3_det_mobile.onnx`
            *   Recognition: `onnx/PP-OCRv4/rec/en_PP-OCRv4_rec_mobile.onnx`
            *   Rec keys: `paddle/PP-OCRv4/rec/en_PP-OCRv4_rec_mobile/en_dict.txt`
        *   For the `torch` backend with Chinese (default):
            *   Detection: `torch/PP-OCRv4/det/ch_PP-OCRv4_det_mobile.pth`
            *   Recognition: `torch/PP-OCRv4/rec/ch_PP-OCRv4_rec_mobile.pth`
            *   Rec keys: `paddle/PP-OCRv4/rec/ch_PP-OCRv4_rec_mobile/ppocr_keys_v1.txt`

*   Implement the `download_models` classmethod in `RapidOcrModel`:
    *   Accept parameters: `backend`, `local_dir`, `force`, `progress`, and `lang` (default to "chinese").
    *   When called with `backend='onnxruntime'`, `lang='english'`, and `force=True`:
        *   Download files with URLs containing `en_PP-OCRv3_det_mobile.onnx` and `en_PP-OCRv4_rec_mobile.onnx`.
        *   Save to `{local_dir}/onnx/PP-OCRv4/det/en_PP-OCRv3_det_mobile.onnx` and `{local_dir}/paddle/PP-OCRv4/rec/en_PP-OCRv4_rec_mobile/en_dict.txt`.

*   Update the `download_models` function in `docling/utils/model_downloader.py`:
    *   When `with_rapidocr=True`, ensure it calls `RapidOcrModel.download_models` exactly 4 times for each combination of `backend` and `lang`:
        *   ("torch", "chinese")
        *   ("torch", "english")
        *   ("onnxruntime", "chinese")
        *   ("onnxruntime", "english")
    *   Pass `backend` and `lang` as keyword arguments to each call.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.