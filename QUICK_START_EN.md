# Action Hub · Quick Start

[中文极简说明](极简版说明.md) | English

## Start using the app

- **Android:** Download the APK from the [latest release](https://github.com/xunguangzlj-cloud/action-hub/releases/latest) and install it. Before upgrading, export a backup and install the new APK over the old version. Do not uninstall first.
- **Windows:** Open the HTTPS workspace URL supplied by the person who deployed the web app.
- **iPhone:** Open the same workspace URL in Safari, tap **Share** (or **More → Share**) and choose **Add to Home Screen**. Enable **Open as Web App** if that option is shown.

After the first online load, you can create records offline. Data is saved automatically on the current device. Task-form edits are continuously autosaved; **Done** only closes the form. When the app goes to the background, the screen turns off, or the page closes, the app immediately tries to upload unsynchronized changes.

## Reminders

- If a planned date and start time are set, the reminder is scheduled for the start time.
- If only a date is set, reminders default to 08:00, 12:00, 18:00, and 22:00 on that date.
- Select **Custom times** to enter multiple reminder times, or **No reminder** to disable reminders for that task.
- The Android app can show a system notification and play the system notification sound. Allow notifications, sound, and background operation. You can also add a task to the system calendar.
- iPhone and desktop web versions cannot guarantee reminders after the web app is fully closed. Add important tasks to the system calendar.

## Sync two devices

If a sync service has already been deployed, you only need the **cloud-function HTTPS URL and sync key**:

1. On each device, open **Backup → Advanced → Multi-device cloud sync**.
2. Enter exactly the same cloud-function HTTPS URL and sync key, select **Save and test**, and wait for **Sync complete**.
3. While online, the app synchronizes automatically about 10 seconds after editing stops. It also tries immediately before entering the background and retries after returning to the foreground. When switching devices immediately, select **Sync now** and wait for success.

Offline edits remain on the local device and synchronize after connectivity or foreground operation resumes. The receiving device must be opened in the foreground and online to pull new data. Existing users should keep the current key instead of generating a new one.

If you do not yet have a URL and key, you or a helper must complete [Set up sync for the first time](USER_GUIDE_EN.md#set-up-sync-for-the-first-time) once. The public APK does not include a shared sync account; downloading the APK alone does not enable cloud sync.

## Backup and device migration

Open **Backup** and select **Export backup**. On phones that support file sharing, the system share sheet opens; choose a file-storage or backup app. You can also use the Baidu Netdisk or Quark Cloud buttons. On a new device, select **Import backup** and choose the JSON file.

**Cloud-drive backup is manual backup and restore, not automatic synchronization. Importing replaces the current local records on the receiving device.** Export backups regularly. You are free to organize tasks and folders in any way that fits your workflow.
