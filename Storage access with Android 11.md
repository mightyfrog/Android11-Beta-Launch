# Storage access with Android 11

[Storage access with Android 11](https://www.youtube.com/watch?v=RjyYCUW-9tY&list=PLWz5rJ2EKKc9hqKx4qZWolQxy59Bt20t_&index=8&t=0s)

## Scoped Storage Changes introduced in Android 10 1:30

1. Unrestricted access to your own app storage
2. Unrestricted media and downloads contributions
3. Runtime permission only gives read access to media
4. User confirmation required for modifying media
5. Location metadata gated by new permission

Apps targeting Android 10 can use **requestLegacyExternalStorage** to opt out Scoped Storage.

```xml
<manifest
    ...
    android:requestLegacyExternalStorag="true"
>
...
</manifest>
```

Mandatory for all apps targeting Android 11. No opt out.

## Scoped Storage Improvements 3:30

### New features in Android 11

1. Enabled File Path APIs
2. Bulk media modification APIs
3. All Files Access
4. Private app storage

## Using File Paths 4:15

- Alternative to MediaStorage APIs
- Better compatibility
- Java Files API
- Native Libraries (C, C++)
- Same scoped storage access
- Immediately indexed by MediaStore
- Comparable performance

## Modifying Media 7:20

- createWriteRequest
- createDeleteRequest
- createTrashRequest: gives a user a chance to recover later
- createFavoriteRequest

## All Files Access 9:30

MANAGE_EXTERNAL_STORAGE

- Write access to all shared files
- Special app access
- Google Play manual review

## Private App Storage 11:10

No way to read other apps' private storage.

## Tips on Migrating 14:10

- Legacy storage flag
- Storage access framework
  - Redesign app if it requires access to non media files.
- Modifying media
  - Use MediaStore consent API
- Top level directories
  - Don't save files in a custom top level directory. Move the top level directory before migrating to Android 11.
- Content providers
  - To share contents with other apps
