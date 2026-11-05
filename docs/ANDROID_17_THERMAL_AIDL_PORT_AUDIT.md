# PixelExperience-Reborn — Qualcomm Thermal HAL AIDL v3 (Android 17)

**2026-11-05 | P2: hardware/qcom-caf/thermal | Source-integrated, device validation OPEN.**

## Source provenance and actual state

- Historical PE branch: `seventeen`, commit `f6ed211105031013ec0ba806405f92a21ec666e9` (HIDL Thermal 2.0).
- Modern upstream: `LineageOS/android_hardware_qcom_thermal`, `lineage-24.0` commit `e2ff2bbb4a90503f88d2caa103fb6ed483601043`.
- Isolated integration branch: `integrate/a17-qcom-thermal-aidl-20261105`, source head `1b20c1b1bf318a278f34e7501fd98eb6e9e5f5bb`.
- Real Git merge preserved ancestry from both histories; `Android.bp`, `thermal.cpp`, `thermalConfig.cpp` were resolved as a mutually compatible modern AIDL v3 implementation, rather than combining HIDL and AIDL types.
- [GitHub Actions #41989848852](https://github.com/PixelExperience-Reborn/hardware_qcom-caf_thermal/actions/runs/41989848852) **SUCCESS**. Its job is a source-merge/shape/structural check. It is **not** an Android 17 Soong build.

## Verified direct source consumers

- Existing reference-only `LF52406/mondrian_android_device_xiaomi_sm8450-common` at `lineage-24.0`, `common.mk` Thermal block includes `android.hardware.thermal-service.qti`.
- Modern `Android.bp` builds `android.hardware.thermal-service.qti` (vendor), includes `android.hardware.thermal-service.qti.rc` and `android.hardware.thermal-service.qti.xml`, and links `android.hardware.thermal-V3-ndk`, `libbinder_ndk`, `libnl` and other system/vendor libraries.
- The VINTF XML declares AIDL `android.hardware.thermal`, version `3`, `IThermal/default`. The init RC advertises `interface aidl android.hardware.thermal.IThermal/default`.
- `thermalConfig.cpp` maps Waipio SoC IDs 457/482/552 to `waipio_common` and `waipio_specific` configs. This is **source coverage**, not proof that physical mondrian nodes/readings match.
- `Android.bp` requires Soong header library `qti_kernel_headers`. The inspected `LF52406/android_kernel_xiaomi_sm8450` `lineage-24.0` **root `Android.bp` does not itself declare that module**. A definite source/provider for the PE-Reborn Android 17 device graph must be established; do not add duplicate providers or assume AOSP kernel header imports supply the QTI module.

## Integration restrictions / outstanding acceptance gates

1. Keep the default historical `seventeen` branch unchanged and keep this source outside the existing platform-only AOSP17 manifest profiles. The P2 branch is reviewable, not a product-ready replacement.
2. Resolve exactly one `qti_kernel_headers` provider with compatible exported UAPI headers; verify `libnl`, AIDL V3 NDK module visibility, QTI Soong namespace and vendor partition variants.
3. Compare real `/sys/class/thermal`, `/sys/devices/virtual/thermal`, `/sys/devices/soc0/soc_id` and kernel thermal netlink events on **mondrian** with Waipio mappings; verify no missing or double-counted zones. Preserve the device's actual thermal mitigation and thermal-engine behavior.
4. Verify there is only one authoritative Thermal HAL service instance; reconcile product selection, init RC, VINTF fragments and vendor-provided services. Validate `checkvintf` and sepolicy, with SELinux **enforcing** and no neverallow relaxations.
5. Build module `android.hardware.thermal-service.qti` in a complete Android17 Soong/Make device graph. Verify status, shell/thermalservice calls, representative sensors, cooling devices, threshold updates and thermal throttling under sustained load.
6. Do not claim a ROM/OTA/AVB pass, physical sensor correctness, CTS/VTS or device boot until actually tested. Do not change current functioning LineageOS/MistOS device trees under this audit.

**Decision:** P2 source merge accepted as a *review candidate*; Android17 module compile and device thermal runtime remain **OPEN**. Next item in the 135-row matrix is `hardware/qcom-caf/vr`, after recording the thermal result.
