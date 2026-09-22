# Action Hub · User Guide

[中文详细说明](详细版说明.md) | English

Applies to version 1.1.6. This guide covers installation, reminders, local storage, synchronization, backup, and maintenance. Task-organization methods are intentionally left to the user.

## Choose a usage mode

| Mode | Requirements | Where data is stored |
| --- | --- | --- |
| Single-device offline use | Android APK or a deployed workspace URL | Local storage of the current app or browser |
| Cloud-drive backup and manual migration | Baidu Netdisk, Quark Cloud, or a file manager | JSON backup files saved manually by the user |
| Multi-device cloud sync | Your own cloud-function URL and the same sync key on each device | Local storage plus your own uniCloud database |

The app itself has no software subscription fee. Cloud services, web hosting, domains, and cloud drives may have charges or quotas under their providers' current policies. This project does not promise unlimited free infrastructure. Cloud-drive backup does not require uniCloud, but it cannot replace automatic cross-device merging.

## Install and update

Download the APK or `action-hub-v1.1.6.zip` from the [latest release](https://github.com/xunguangzlj-cloud/action-hub/releases/latest). The full ZIP contains the Android APK, the `web` directory, the optional `uniCloud-aliyun` sync template, and Chinese and English documentation. It contains no personal data, sync key, or deployment-specific URL.

### Android

1. Download `action-hub-android-1.1.6.apk` and follow the phone's installation prompts.
2. Before upgrading, export a backup and install over the old version. Do not uninstall the old app or clear its data first.
3. Releases use the same package name, application ID, and signing certificate. If Android reports a signature conflict, stop and preserve the existing app data.
4. Existing sync settings continue to work after the update.

### Windows and other desktop browsers

Open the HTTPS workspace URL supplied by the deployer in a modern browser. You may install it as a browser app or bookmark it. The `web` directory from the ZIP must be deployed to web hosting first; the GitHub repository page is not the workspace.

For a web update, replace all nine files in the deployed `web` directory. Keep the existing domain, path, and browser profile so that the browser can continue accessing its local data. Export a backup first. If the old interface remains visible, close every workspace window and reopen or refresh it. Do not clear site data first.

### iPhone

Open the HTTPS workspace URL in Safari. Tap **Share**, or **More → Share**, and select **Add to Home Screen**. Enable **Open as Web App** if available, and later launch it from that icon. Complete the first online load before testing offline use.

This is a Safari web app; there is no iPhone APK or native IPA in this release. Use one consistent entry point because Safari tabs and Home Screen web apps may expose different local-storage contexts. Back up before changing entry points, then verify records and sync settings in the new one. See [Apple's Add to Home Screen guide](https://support.apple.com/guide/iphone/iphea86e5236/ios).

## Local saving and offline use

Quick capture, moves, and organization actions are saved locally immediately. Task-form input is committed locally after roughly 500 ms, so forgetting to press a save button does not discard the task. **Done** only closes the form. If the app stops before a draft is committed, the next launch attempts to recover that draft.

Before entering the background, turning off the screen, or closing the page, the app saves the current form locally and immediately attempts a cloud upload. You can continue creating records while offline and synchronize after connectivity returns. Private browsing, clearing site data, uninstalling the app, or changing browsers may make local records unavailable, so export a backup first. Web offline use requires one successful online load and service-worker installation.

## Reminders and system calendar

When creating or editing a task, choose a reminder mode:

- **Automatic:** If both a date and start time exist, remind at the start time. If only a date exists, remind at 08:00, 12:00, 18:00, and 22:00.
- **Custom times:** Enter one or more specific reminder times.
- **No reminder:** Do not create reminders for the task.

The Android app tries to register local system notifications that include the task title and use the system notification sound. Allow notification permission the first time. If there is no sound, open the phone's settings for Action Hub notifications and enable notifications and sound. Also check Do Not Disturb, notification volume, and battery restrictions. Device vendors manage background and scheduled notifications differently; reminders cannot be guaranteed after force-stop, aggressive background cleanup, or revoked notification permission.

**Add to system calendar** opens a new system-calendar event on Android. On desktop and iPhone web versions, it exports or shares an `.ics` calendar file. The system calendar is generally more reliable than a background webpage, so important tasks should also be added to the calendar with calendar reminders enabled.

Desktop browsers and iPhone web apps can show web notifications only while the browser allows them and the app can still run. They cannot guarantee notifications or sound after the app is fully closed. This limitation does not mean task data was lost.

## Connect two devices to an existing sync service

Prepare two values. Do not confuse the **workspace URL**, which opens the interface, with the **cloud-function URL**, which exchanges data.

- **Cloud-function URL:** The complete HTTPS endpoint supplied by the deployer.
- **Sync key:** The key that exactly matches the cloud environment variable. Existing users should retain the current value rather than generating a new one.

On each device, open **Backup**, expand **Advanced → Multi-device cloud sync**, enter both values, select **Save and test**, and wait for **Sync complete**. The installed Android app and a browser version on the same phone are separate clients and must each be configured.

### Everyday sync order

1. After a change on device A, data is saved locally immediately and automatically synchronized about 10 seconds after editing stops.
2. Before entering the background or turning off the screen, the app tries to upload immediately. When it returns to the foreground or reconnects, it retries pending changes.
3. Open device B in the foreground while online to pull cloud updates. Use **Sync now** on both devices when immediate confirmation is required.

Sync is not real-time push. Force-stop, background disconnection, or battery management may interrupt the last request, but local edits remain on the device and are retried the next time the app opens in the foreground while online. If edits occur during an active upload, the app preserves the newer local version and continues syncing instead of marking the older upload as final. Before switching devices, wait until the interface says **Sync complete**.

Avoid editing the same record on two devices at the same time. The current system merges records by their modification timestamps; it is not a collaborative multi-user editor. Data sync does not update the APK or web application files.

### First verification

Create a record named `Sync test` on device A and wait for success. Sync on device B and search for that text. Edit it on B, save and sync, then sync A and confirm the edit. If the record is not visible in the current folder, search for it before assuming synchronization failed.

## Set up sync for the first time

Skip this section if the existing service works. Initial deployment requires a computer, a DCloud account, HBuilderX, and the full release ZIP. A technical helper may complete this once. The supplied template stores one person's dataset per service space; unrelated users should deploy separate spaces.

### 1. Create and link a service space

Sign in to the [uniCloud console](https://unicloud.dcloud.net.cn/) and create an Alibaba Cloud service space. Review the current plan, validity period, and quotas in the console. If a working Basic plan already exists, continue using it instead of recreating the space.

Sign in to [HBuilderX](https://www.dcloud.io/hbuilderx.html) with the same account. Create a normal uni-app project with uniCloud Alibaba Cloud enabled, then link its uniCloud directory to the service space. Merge `uniCloud-aliyun/cloudfunctions` and `uniCloud-aliyun/database` from the release package into the corresponding project directories without adding an extra parent layer. See the [official uniCloud quick start](https://doc.dcloud.net.cn/uniCloud/quickstart).

### 2. Upload the database schema and cloud function

Upload `database/action-hub-state.schema.json` to the linked space. Confirm that the built-in `action-hub-state` collection exists and that the schema's client `read`, `create`, `update`, and `delete` permissions are all `false`. No separate extended MongoDB product or manual task data is required.

Deploy `cloudfunctions/action-sync`. It should appear as a normal cloud function named `action-sync`. The template uses the built-in database and Node.js `crypto`; it needs no AI API key, scheduled trigger, or keep-alive task. Select a Node.js runtime supported by the platform and compatible with the template syntax.

### 3. Configure the key and endpoint once

In the app, open **Backup → Advanced → Multi-device cloud sync**, select **Generate strong key**, and copy it. Add an environment variable to `action-sync` named `ACTION_HUB_SYNC_KEY`, set its value to the complete key, and save. Do not add quotation marks or a variable-name prefix. Never commit the actual key to source code or a public repository.

Enable URL/HTTP access for the function and set the path to `/action-sync`. If the template configuration already applies, copy the complete HTTPS URL rather than constructing it from the service-space ID. This URL is a data endpoint, not a workspace page. Verify it with **Save and test** in the app. See the [official HTTP access guide](https://doc.dcloud.net.cn/uniCloud/http).

### 4. Deploy the workspace website

Desktop and iPhone clients need an accessible web address. On an HTTPS static-hosting service reachable in mainland China, upload these nine files from `web` into one dedicated site directory with `index.html` as the entry page:

`index.html`, `app.js`, `styles.css`, `sw.js`, `manifest.webmanifest`, `icon.svg`, `icon-192.png`, `icon-512.png`, and `apple-touch-icon.png`.

Do not upload the database template, cloud function, APK, or any key as website assets. If you use uniCloud web hosting, check current plan and test-domain limits. Long-term public operation may require a formal domain, HTTPS, and applicable filing under the provider's rules. The web-hosting URL and cloud-function URL are different.

### 5. Verify both directions

Configure two devices as described above. Confirm that the endpoint, client keys, and cloud environment variable match, then test A → B and B → A. If CORS fails, inspect the template's OPTIONS response, response headers, and provider domain configuration. Do not disable browser security checks as a workaround.

## Export, cloud-drive backup, and restore

Open **Backup → Export backup**. Desktop browsers normally download `行动中枢备份-YYYY-MM-DD.json`. The Android app and mobile web/PWA environments that support file sharing open the system share sheet; choose file storage or a backup app. Unsupported browsers fall back to a download. On iPhone, save the file through the system share sheet.

After selecting **Export and open Baidu Netdisk** or **Export and open Quark Cloud**:

- Environments with file sharing open the system share sheet; select the relevant cloud-drive app and complete its save/upload flow.
- Other browsers download the JSON backup and open the cloud-drive website; manually upload the downloaded file.
- If the target app is missing from the share sheet, install and sign in to that app, or save the file first and upload it from the cloud-drive app.

The buttons do not sign in automatically or bypass cloud-drive confirmation. An opened share sheet or an **Exported** message does not prove that the cloud upload finished. Confirm the file in the cloud-drive app. Android sharing cannot reliably report the final destination, so merely opening the share sheet does not update the backup date.

A backup contains tasks, folders, locations, notes, and related records. It does not contain original files dragged into the app, the sync endpoint, or the sync key. Store original files separately. Create a new backup weekly and before device migration, upgrades, or sync-configuration changes.

To restore, select **Backup → Import backup** on the new device and choose the JSON file. After confirmation, this replaces current local records instead of merging item by item. Export existing data first. If sync was already enabled on the new device, disable it, import and verify the backup, then decide whether to reconnect. Reconnecting merges with existing cloud data; it does not roll the cloud back to the backup date.

## Troubleshooting

| Symptom | Action |
| --- | --- |
| Another device does not change | Sync the edited device first, then the receiving device; verify the same URL and key, and use search to rule out folder filtering |
| **Sync now** is missing | Configure and save the URL and key under Backup → Advanced first |
| Invalid key or HTTP 401 | Compare the cloud environment variable with every device; do not casually regenerate a working key |
| `Failed to fetch` | Check connectivity, the complete HTTPS endpoint, service-space status, HTTP access, and CORS preflight; keep local data intact |
| Cloud sync failed or HTTP 500 | Check the function logs, built-in collection, schema, and deployment result |
| Quota exhausted | Use the console's usage, plan, and error logs; changing plans cannot fix a wrong URL or key |
| `ClientIpNotAllowed` | Check public-IP or access restrictions for the hosting test domain |
| New APK still shows the old interface | Confirm version 1.1.6 is installed; data synchronization does not update application files |
| Mobile web still shows the old layout | Deploy the 1.1.6 `web` directory and reopen the site; avoid embedded browsers such as the WeChat in-app browser when possible |
| After saving and turning off the screen, another device is unchanged | Reopen the edited device in the foreground while online and wait for **Sync complete**, then open or manually sync the receiving device |
| Backup is absent from the cloud drive | Return to the share sheet or cloud-drive app, finish the upload, and confirm the filename |
| Cloud-drive button fails on Android | Preserve local records; if sync works, sync first and export from a desktop, or report the full error and device model without uninstalling |
| Phone reminder has no sound | Enable Action Hub notifications and sound in system settings; check Do Not Disturb, notification volume, and battery restrictions; add important tasks to the calendar |
| No reminder after closing the app | Web timers depend on runtime state and Android is subject to vendor background limits; use the system calendar for important tasks |

## Version and verification scope

Version 1.1.6 adds automatic/custom reminders, four default reminder times for date-only tasks, Android notification sound, and system-calendar integration. It also introduces continuous task-form autosave, shortens the normal sync delay, and fixes possible missed uploads when editing during an upload or leaving the foreground. Tests cover reminder rules, draft recovery, lifecycle behavior, mobile backup, simulated two-device sync, and existing functionality. APK version, embedded assets, alignment, and signature were also checked.

Browser simulation cannot reproduce every phone vendor's behavior. After installation, allow notifications, create a reminder a few minutes in the future, and complete a two-device sync test.

Version 1.1.0 previously included a password-protected signing-certificate file. It has been excluded since 1.1.1. The same signing identity is still required for update compatibility; removing later copies cannot revoke files already distributed in old releases. Download new versions only from trusted release links in this repository.
