# Manhwa-Viewer-1.6.2
 If you want to use ManhwaViewer v1.6.2, just download the latest release (https://github.com/adalfarus/Manhwa-Viewer-1.6.2/releases).

## Bugs
There is a small bug, where the title doesn't update internally and you need to restart the program. There also is a bug where the task window keeps popping up if the task failed and you close it too quickly, so either wait a bit before clicking the close button or stop the app with the task manager.

## Compatibility
Currently the program works for Windows 10-2004 to 11-23H2, but I could make it so it works entirely on Windows 10 and 11, just Windows 7 and lower will never work. Linux and Mac would need more modification but are also okay, just that I wouldn't be able to easily test them

## Best experience
Is using the search feature, a chapter rate of 0.5 and the direct provider. The indirect provider is only there if you want to make your own extension but aren't skilled enough for e.g. POST requests. All extensions it ships with either only work or work best using the direct provider.

## Modding how-to
Just look at the file ./modules/extension_base.py, a simple example for the site ManhwaClan is shown there. Otherwise you can look in ./extensions/extra_autoproviders.py to look at the extension code it ships with.
