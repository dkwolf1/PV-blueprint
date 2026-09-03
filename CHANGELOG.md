# Changelog

## 1.0.1 - 2026-09-03

- Enforce the configured minimum PV on-time for shutdowns initiated by the watchdog or Home Assistant startup recovery.
- Recheck price validity, threshold, automatic control, manual block and switch state after the delay.
- Keep re-enabling PV immediate when the price rises above the threshold.

## 1.0.0 - 2026-08-08

Initial release.

- Improved Inverted selector wording and explanation.

- Configurable electricity price sensor.
- Configurable cutoff threshold.
- Support for inverted PV switching.
- Minimum PV on-time before price-based shutdown.
- Immediate re-enable when price rises above the threshold.
- Five-minute self-healing watchdog.
- Switch-change verification.
- Home Assistant startup recovery.
- Switch temperature warning.
- Two optional notification services.
- Optional persistent fault notification.
