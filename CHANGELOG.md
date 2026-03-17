# Changelog

## [Unreleased] - 2026-03-08

### Added
- `publish.map_decay_time` parameter to control how long points are retained in `/Laser_map` topic
  - Default: `-1.0` (infinite accumulation, original behavior)
  - Set to positive value (e.g., `3.0`) to remove points older than N seconds
  - Enables near-real-time dynamic obstacle removal from the map visualization

### Fixed
- Map decay now uses relative timestamps (seconds since first scan) instead of absolute Unix timestamps to avoid float32 precision loss
