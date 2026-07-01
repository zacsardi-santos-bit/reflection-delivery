Implement application-level configuration support for the BBR and CUBIC congestion controllers in the s2n-quic library. Introduce settings structures that allow optional overrides for key parameters, ensuring the controllers use these settings if provided, or default to existing behaviors otherwise.

*   Update constructors:
    *   Implement `BbrCongestionController::new(max_datagram_size: u16, app_settings: ApplicationSettings) -> BbrCongestionController`.
    *   Implement `CubicCongestionController::new(max_datagram_size: u16, app_settings: ApplicationSettings) -> CubicCongestionController`.

*   Define `ApplicationSettings` structs:
    *   For BBR (in `quic/s2n-quic-core/src/recovery/bbr.rs`):
        *   Fields: `initial_congestion_window: Option<u32>`, `loss_threshold: Option<u32>`, `probe_bw_cwnd_gain: Option<u32>`.
        *   Derive `Default` with all fields set to `None`.
    *   For CUBIC (in `quic/s2n-quic-core/src/recovery/cubic.rs`):
        *   Field: `initial_congestion_window: Option<u32>`.
        *   Derive `Default` with the field set to `None`.

*   Modify BBR methods:
    *   `BbrCongestionController::initial_window(max_datagram_size: u16, app_settings: &ApplicationSettings) -> u32`: Use `app_settings.initial_congestion_window` if set, otherwise compute default.
    *   `BbrCongestionController::minimum_window(max_datagram_size: u16) -> u32`: Change to a static method.
    *   `BbrCongestionController::loss_thresh(app_settings: &ApplicationSettings) -> Ratio<u32>`: Return `LOSS_THRESH` if `app_settings.loss_threshold` is `None`.
    *   `BbrCongestionController::inflight_hi_from_lost_packet(size: u32, lost_since_transmit: u32, packet_info: <BbrCongestionController as CongestionController>::PacketInfo, app_settings: &ApplicationSettings) -> u32`: Add `app_settings` parameter.
    *   `BbrCongestionController::is_inflight_too_high(rate_sample: RateSample, max_datagram_size: u16, loss_bursts: u8, loss_burst_limit: u8, app_settings: &ApplicationSettings) -> bool`: Add `app_settings` parameter.
    *   `State::pacing_gain(&self, app_settings: &ApplicationSettings) -> Ratio<u64>`: Add `app_settings` parameter.
    *   `State::cwnd_gain(&self, app_settings: &ApplicationSettings) -> Ratio<u64>`: Add `app_settings` parameter, use `app_settings.probe_bw_cwnd_gain` if set.

*   Modify CUBIC methods:
    *   `CubicCongestionController::initial_window(cubic: &Cubic, max_datagram_size: u16, app_settings: &ApplicationSettings) -> u32`: Use `app_settings.initial_congestion_window` if set, otherwise compute default.

*   Expose constants:
    *   `LOSS_THRESH` in `bbr` module.
    *   `CWND_GAIN` in `probe_bw` submodule.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.