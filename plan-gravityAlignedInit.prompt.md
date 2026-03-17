# Plan: Gravity-Aligned World Frame Initialization

**TL;DR**: Modify IMU initialization to compute and apply a rotation that aligns the measured gravity vector with world -Z, ensuring flat ground appears level regardless of robot tilt at startup. This uses the existing gravity measurement but properly initializes `init_state.rot` instead of leaving it as identity.

## Steps

1. In `IMU_Processing.hpp`, after computing `mean_acc`, calculate the rotation from IMU frame to a gravity-aligned world frame:
   - Measured gravity in IMU frame: `g_imu = mean_acc.normalized()`
   - Desired gravity in world frame: `g_world = [0, 0, -1]`
   - Compute rotation `R` such that `R * g_imu = g_world`
   - This rotation has **no yaw component** (heading remains free/arbitrary)

2. Set `init_state.rot = R` (instead of leaving it as identity)

3. Keep gravity as `S2(0, 0, -G_m_s2)` since world Z is now aligned with true vertical

4. Optionally add a config parameter `gravity_align: true` in `mid360.yaml` to enable/disable this behavior

## Verification

- Start robot on tilted mount
- Run FAST_LIO, drive forward on flat ground
- Confirm Z position stays near 0.0 (±noise) as robot moves on level floor
- Check `/Odometry` topic shows roll/pitch ~0 when on flat ground

## Decisions

- **Yaw handling**: Initial heading will remain arbitrary (robot's forward = world +X at start). This is standard for IMU-only initialization.
- **No LiDAR ground detection needed**: Pure IMU gravity measurement is sufficient for <5° tilt correction
