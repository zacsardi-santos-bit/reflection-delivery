Fix the phoneme conversion endpoint to handle empty input and implement methods to access the active model instance and device in the text-to-speech service. Ensure the model setup flow correctly stores the initialized model object.

*   Update the TTSModel class in `api/src/services/tts_model.py`:
    *   Implement a class attribute `_instance` to hold the active model object.
    *   Implement a classmethod `get_instance(cls)` that returns the active model instance or raises a `RuntimeError` if not initialized.
    *   Implement a classmethod `get_device(cls)` that returns the current device string (e.g., "cpu" or "cuda").
*   Ensure both TTSCPUModel in `api/src/services/tts_cpu.py` and TTSGPUModel in `api/src/services/tts_gpu.py` implement `get_instance()` so the aliased TTSModel exposes these methods.
*   Modify the TTSService class in `api/src/services/tts_service.py`:
    *   In the `__init__` method, call `TTSModel.get_instance()` to make the model dependency explicit and testable.
*   Update the POST /text/phonemize endpoint in `api/src/routers/text_processing.py`:
    *   Validate that the 'text' field is non-empty.
    *   Return HTTP 500 with a JSON detail object containing an 'error' key if the text is empty (e.g., `{"error": "Server error", "message": "..."}`).
    *   On success, return a JSON response with 'phonemes' (str) and 'tokens' (list of ints, including start/end token 0).
*   Modify the TTSBaseModel class in `api/src/services/tts_base.py`:
    *   In the `setup()` method, assign the return value of `cls.initialize()` to `cls._instance`.
    *   Ensure `initialize()` returns the model object, not a boolean, and `setup()` raises `RuntimeError` if `initialize()` returns `None`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.