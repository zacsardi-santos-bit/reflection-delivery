Implement a new stage in the video curation pipeline to write processed clip data to storage. Create utility functions for consistent file writing operations, including handling binary data, JSON, Parquet, and CSV files. Ensure the stage supports parallel I/O, configurable paths, and a dry-run mode for testing.

*   Implement `ClipWriterStage` in `ray-curator/ray_curator/stages/video/io/clip_writer.py`:
    *   Constructor parameters: `output_path`, `input_path`, `upload_clips`, `dry_run`, `generate_embeddings`, `generate_previews`, `generate_captions`, `embedding_algorithm` (default 'cosmos-embed1'), `caption_models` (default None), `enhanced_caption_models` (default None), `verbose` (default False), `max_workers` (default 6), `log_stats` (default False).
    *   Expose properties and methods:
        *   `name` property returning "clip_writer".
        *   `inputs()` returning (["data"], []).
        *   `outputs()` returning (["data"], []).
        *   `resources` property returning `Resources(cpus=0.25)`.
        *   `setup()` initializing `_iv2_embedding_buffer` and `_ce1_embedding_buffer` as empty lists.
    *   Static methods for output paths:
        *   `_get_output_path(base_path, subfolder)`.
        *   `get_output_path_processed_videos(base_path)`.
        *   `get_output_path_processed_clip_chunks(base_path)`.
        *   `get_output_path_clips(base_path, filtered=False)`.
        *   `get_output_path_previews(base_path)`.
        *   `get_output_path_metas(base_path, version)`.
        *   `get_output_path_iv2_embd(base_path)`.
        *   `get_output_path_iv2_embd_parquet(base_path)`.
        *   `get_output_path_ce1_embd(base_path)`.
        *   `get_output_path_ce1_embd_parquet(base_path)`.
    *   Implement methods for processing and writing:
        *   `calculate_sha256(buffer: bytes)`.
        *   `_write_data(buffer, dest, desc, source)`.
        *   `_write_json_data(data, dest, desc, source)`.
        *   `_get_window_uri(uuid, window_tuple, path_prefix, file_type)`.
        *   `_get_clip_uri(uuid, path_prefix, file_type)`.
        *   `_get_video_uri(input_video_path)`.
        *   `_get_clip_chunk_uri(input_video_path, idx)`.
        *   `_write_clip_embedding_to_buffer(clip)`.
        *   `_write_video_embeddings_to_parquet(video)`.
        *   `_write_clip_window_webp(clip)`.
        *   `_write_clip_mp4(clip, filtered=False)`.
        *   `_write_clip_embedding(clip)`.
        *   `_write_clip_metadata(clip, video_metadata, filtered=False)`.
        *   `_write_video_metadata(video)`.
        *   `process(task: VideoTask)` using `ThreadPoolExecutor`.

*   Implement `JsonEncoderCustom` in `ray-curator/ray_curator/utils/writer_utils.py`:
    *   Method `default(obj)` to serialize `uuid.UUID` as strings and handle other types with the parent implementation.

*   Implement file writing utilities in `ray-curator/ray_curator/utils/writer_utils.py`:
    *   `write_bytes(buffer, dest, desc, source_video, verbose, overwrite=False, backup_and_overwrite=False)`.
    *   `write_parquet(data, dest, desc, source_video, verbose)`.
    *   `write_json(data, dest, desc, source_video, verbose)`.
    *   `write_csv(dest, desc, source_video, data, verbose)`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.