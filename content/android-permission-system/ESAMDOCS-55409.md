
## Overview

A defect was identified in the Android permission system where the `PACKAGE_USAGE_STATS` permission's AppOps mode returns `DEFAULT` (value: 3) instead of the expected `ALLOWED` (value: 0) immediately after granting the permission through `PackageManager.grantRuntimePermission()`. This issue affects applications that need to verify the permission status programmatically right after installation, as the AppOps state does not synchronize immediately with the granted permission state.

## Technical Details

**Affected Components:**
- Android AppOps Framework (`AppOpsManager`)
- PackageManager Service
- Permission Management System

**Permission Details:**
- Permission: `android.permission.PACKAGE_USAGE_STATS`
- Protection Level: `signature|privileged|development|appop`
- AppOps Operation: `OPSTR_GET_USAGE_STATS` (code: 43)

**Observed Behavior:**
When an application with `PACKAGE_USAGE_STATS` permission is installed and the permission is granted programmatically:
1. `PackageManager.checkPermission()` returns `PERMISSION_GRANTED` (correct)
2. `AppOpsManager.checkOpNoThrow()` returns `MODE_DEFAULT` (3) instead of `MODE_ALLOWED` (0)
3. The discrepancy persists until a system event triggers AppOps state synchronization

**Code Reference:**
The issue manifests in code that checks both permission and AppOps state:
```java
int permissionStatus = packageManager.checkPermission(
    Manifest.permission.PACKAGE_USAGE_STATS, packageName);
int appOpsMode = appOpsManager.checkOpNoThrow(
    AppOpsManager.OPSTR_GET_USAGE_STATS, uid, packageName);
// permissionStatus == PERMISSION_GRANTED (0)
// appOpsMode == MODE_DEFAULT (3) instead of MODE_ALLOWED (0)
```

## Root Cause

The Android permission system and AppOps framework operate as separate but related subsystems:

1. **Permission Granting:** When `PackageManager.grantRuntimePermission()` is called, the permission state is updated in the PackageManager's internal data structures immediately.

2. **AppOps Synchronization Delay:** The AppOps framework does not automatically synchronize its mode settings when permissions are granted programmatically. The AppOps mode for `PACKAGE_USAGE_STATS` remains at `MODE_DEFAULT` until:
   - A system service queries the permission and triggers synchronization
   - The device reboots
   - The AppOps mode is explicitly set via `AppOpsManager.setMode()`

3. **Special Permission Characteristic:** `PACKAGE_USAGE_STATS` is an appop-backed permission, meaning its enforcement requires both:
   - Permission granted in PackageManager
   - AppOps mode set to `ALLOWED`

The delay in synchronization creates a window where the permission appears granted but the AppOps check fails.

## Workaround

**Explicit AppOps Mode Setting:**
After granting the permission, explicitly set the AppOps mode to `ALLOWED`:

```java
// Grant the permission
packageManager.grantRuntimePermission(
    packageName, 
    Manifest.permission.PACKAGE_USAGE_STATS, 
    UserHandle.of(userId));

// Explicitly set AppOps mode to ALLOWED
appOpsManager.setMode(
    AppOpsManager.OPSTR_GET_USAGE_STATS,
    uid,
    packageName,
    AppOpsManager.MODE_ALLOWED);
```

**Important Note:** Calling `setMode()` requires system-level privileges (`android.permission.UPDATE_APP_OPS_STATS` or running as system UID).

## Impact

**Affected Systems:**
- Package installation workflows that grant `PACKAGE_USAGE_STATS` programmatically
- Privileged app management systems
- Applications that verify permission status immediately post-installation
- Automated testing frameworks that check permission states

**User Impact:**
- Applications may incorrectly report that `PACKAGE_USAGE_STATS` permission is denied
- Features dependent on usage stats access may fail until AppOps synchronization occurs
- Installation validation logic may incorrectly flag successful installations as failed

**Workaround Applicability:**
- System applications and privileged installers can use explicit `setMode()` calls
- Third-party applications cannot work around this issue without system privileges

## Testing Considerations

When testing permission grants for `PACKAGE_USAGE_STATS`, verify:

1. **Permission State Check:**
   ```java
   int status = packageManager.checkPermission(
       Manifest.permission.PACKAGE_USAGE_STATS, packageName);
   // Should return PERMISSION_GRANTED (0)
   ```

2. **AppOps Mode Check:**
   ```java
   int mode = appOpsManager.checkOpNoThrow(
       AppOpsManager.OPSTR_GET_USAGE_STATS, uid, packageName);
   // Should return MODE_ALLOWED (0) after workaround applied
   ```

3. **Functional Verification:**
   - Attempt to query usage stats via `UsageStatsManager.queryUsageStats()`
   - Verify no `SecurityException` is thrown
   - Confirm actual usage data is returned

4. **Test Scenarios:**
   - Fresh installation with permission grant
   - Permission revocation and re-grant
   - Cross-user permission grants (multi-user devices)
   - Post-reboot permission persistence

5. **Timing Considerations:**
   - Test immediately after `grantRuntimePermission()` call
   - Test with and without explicit `setMode()` workaround
   - Allow sufficient time for system synchronization in tests without workaround
