# ManhwaViewer 1.6.2
 If you want to use ManhwaViewer v1.6.2, just download the latest release (https://github.com/adalfarus/Manhwa-Viewer-1.6.2/releases).

## For Users
Basics:
- At the top is a search bar that can be lowered, if the current "Provider" cannot support search it will be grayed out.
- The side menu includes all important features, but you won't be needing it much if you don't change stories often.
- The optimal settings often are:
    - Chapter rate set to 0.5 as loading a missing chapter often won't impact performance (turn this to 1.0 if you don't want extra chapters without story)
    - Provider type: There is indirect and direct. Direct is either better or nessesary.
    - The only other nessesary things are the scale buttons should be self-explainatory and the width just means the default width if no other sizing rules apply at the moment (The first images width is used if it isn't set).
    - Options check-boxes Borderless, Hide Scrollbar, Stay On Top are mostly just window modifications and don't really impact the reading/viewing experience
    - You can change your current provider at the Provider: dropdown at the relative top of the side menu. The included ones are (AsuraToon, CoffeManga, HariManga, MangaQueen (Can be slow) and ManhwaClan). More can be added through modding, any in the same "style" as ManhwaClan can be added by just specifing the website name, but they may not work due to additional security imposed by the website owner.

To read anything type the relevant keywords into the search and double click the result you wish to read. This will automatically set the chapter to 1 and change the current title. If that doesn't happen you need to restart it once (bug). Otherwise you can try to manually enter the title into the Title: field in the side menu, but it could lead to errors.

Exporting chapters and reading them is not recommended as each chapter gets written to the same json file which is read whole everytime you switch chapters (Basically really bad performance).
-> Just ignore anything related to ManhwaFile and Exporting.

This program takes a little bit 1-2 seconds to first load. After a chapter is finished loading the images are loaded while you can scroll, which I found better than another loading bar.

Modding:
Firstly: In order to mod you'll have to have at least a basic understanding of the Python Programming Language this programm is written in.

Secondly: Locate your app-folder, for me that is: Right-Click on Shortcut or ManhwaViewer result in search and select "Open File location", done! The folder you're searching for is _internal in the explorer window that just opened and open the extensions folder, you can create a new file or add to an existing one. If you want to know anything in more detail you can roam in the _interal/modules/AutoProviderPlugin.py file.

Thirdly: Modules aren't easy to come by, if you want to add any you can try just adding them to the _interal folder, but no garuntee that that will work. The modules that are included are everything the main program and the modules use (Just look, I won't list them all here, if you really need a module you can just add it's code to the file). You NEED to have two things for this to work. Add this line (from modules.AutoProviderPlugin import AutoProviderPlugin, AutoProviderBaseLike) at the top, it can be marked as an error, but that needs to be there. Then all actual extensions need to be a direct subclass of AutoProviderPlugin or AutoProviderBaseLike to be recognized as such.

The python api/interface for extensions. We have two classes to make it easier for you to create extensions:

	AutoProviderPlugin -> AutoProviderBaseLike
	(For all extensions)  (Inherits from AutoProviderPlugin, for Manhwa websites like ManhwaClan, integrates everything easily)

For a simple base-like you just need to subclass the AutoProviderBaseLike. Your init method needs to accept the parameters (title, chapter, chapter_rate, data_folder, cache_folder and provider). These will be gives to the Parent classes init call as the first parameters, after that you need (specific_provider_website (the website without ANYTHING else just "manhwaclan.com", no https, www or other), logo_path (is just where you want your logo, the default is "./data/{THEWEBSITENAME}Logo.{IMAGEFORMAT}"), logo_url_or_data (the logo url or a base64 string of the logo), logo_img_format, logo_img_type("url", or "base64"))
AAAAnd your done! Restart the app and look for your provider, if you've done everything correctly it should show up, if something doesn't work look in the logs (./_internal/data/logs.txt).

For the more serious modders that want custom sites like AsuraToon to work you'll have to subclass the AutoProviderPlugin class instead. It accepts the same arguments as the other class as the init and passes as the first few arguments to the Parents init call. The other arguments are (specific_provider_website (same as for the other class) and logo_path (same as for the other class)). Next up is something that is "Good practice":

        if not os.path.isfile(os.path.join(data_folder, "AsuraToonLogo.png")):
            try:
                # Using base64 is better as it won't matter if the url is ever changed, otherwise pass the url and
                # img_type="url"
                self._download_logo_image(
                    'data:image/png;base64,iVBOR...',
                    "AsuraToonLogo", img_format='png', img_type="base64")
            except Exception as e:
                print(f"An error occurred {e}")
                return

This tries to download the logo image to the specified path if it doesn't exist already and if there is an error (e.g. the image location on the website has changed) there won't be an error that causes the program to crash :) How nice is that.

The internal variables will be listed here:
        self.title = title  # Current Manhwa title
        self.url_title = self.urlify(title)
        self.chapter = None  # Current chapter, float if decimal, int if whole
        self.chapter_str = None  # String of chapter
        self.chap(chapter)  # Changes the chapter and chapter string variables
        self.chapter_rate = chapter_rate  # float
        self.data_folder = data_folder  # Data folder path (Doesn't really play a role for you)
        self.cache_folder = cache_folder  # Cache folder path (Doesn't really play a role for you)
        self.provider = provider  # The current provider is either "indirect" or "direct" (I removed all search engines as they made problems and weren't very useful)
        self.specific_provider_website = specific_provider_website  # Whatever you passed it in the parents init call
        self.logo_path = logo_path  # Whatever you passed it in the parents init call
        self.blacklisted_websites = [
            "247manga.com",
            "ww6.mangakakalot.tv",
            "jimanga.com",
            "mangapure.net",
            "mangareader.mobi",
            "onepiece.fandom.com"
        ]  # The default blacklisted websites, doesn't matter anymore as search engines are banned anyways
        self.current_url = None  # The current chapter url.

Next up I'll explain the important methods and what they do:
	- validate_image -- Decides which downloaded images are seen as "Chapter Images" and get converted to png. (The Program only loads pngs when loading a chapter) --> Gets image which is the a string like so "image003.png", returns None, None, None if invalid and file_name (it's name, here "image003"), file_extension (the file extension, gets converted to png, here "png"), new_name (what it should be named, e.g. "003" for simplicity)
	- _indirect_provider -- What the indirect provider finds (this should guess based on the current chapter and title), returns a url to the currently selected chapter as a string --> Uses class attributes
	- _direct_provider -- What the direct provider finds (this should use search), returns a url to the currently selected chapter as a string --> Uses class attributes
	- get_search_results -- Returns a List[title, empty_string] of search results from the search feature, return False if not implemented. --> Takes in text (The search text)

That's it, it's recommended to implement a general _search methods to make it easier to support both get_search_results and _direct_provider. Also, don't mess with the other methods, as it could crash the program. You can implement any helper methods you want, but don't use any of these names (__init__, urlify, get_logo_path, set_title, get_title, set_chapter, get_chapter, set_chapter_rate, get_chapter_rate, set_provider, get_provider, set_current_url, get_current_url, set_blacklisted_websites, get_blacklisted_websites, chap, next_chapter, previous_chapter, reload_chapter, _handle_cache_result, _download_logo_image, redo_prep, update_current_url, _get_current_chapter_url, _get_url, _google_provider, _duckduckgo_provider, _bing_provider, _indirect_provider, _direct_provider, get_search_results, _empty_cache, _download_image, download_images, validate_image, process_images, callback, cache_current_chapter).

Also if you don't want to look the non-standard libraries that are included are:

- aplustools==0.1.4.2  # Newer versions had image download speed issues and I'm not touching that again.
- beautifulsoup4==4.12.2
- duckduckgo_search==3.9.6
- Pillow==10.1.0  # There are exploits but only for programmers or modders. --> (https://security.snyk.io/vuln/SNYK-PYTHON-PILLOW-6182918) and (https://security.snyk.io/vuln/SNYK-PYTHON-PILLOW-6514866)
- PySide6==6.6.0
- Requests==2.31.0
- urllib3==2.1.0

To compatabilty: 
- Windows 7 and lower are missing important files
- Windows 10-2004 has a different dark mode reg key (can be fixed).
- All other Windows versions should work.


## For Programmers

### Bugs
There is a small bug, where the title doesn't update internally and you need to restart the program. There also is a bug where the task window keeps popping up if the task failed and you close it too quickly, so either wait a bit before clicking the close button or stop the app with the task manager and start it again.

### Compatibility
Currently the program works for Windows 10-2004 to 11-23H2, but I could make it so it works entirely on Windows 10 and 11, just Windows 7 and lower will never work. Linux and Mac would need more modification but are also okay, just that I wouldn't be able to easily test them.
