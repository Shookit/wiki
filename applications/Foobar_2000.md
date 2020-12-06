Foobar 2000
===========

Foobar GUI
----------

- Get+double click Columns UI component from D:\Utilities\Music management
- Apply, restart Foobar 2000
- Wizard
    - NG playlist + playlist switcher + filters
    - Group with artwork (redo doc when doing on laptop)
- Library -> configure -> media library -> add music dir location


UI customization
----------------

### Columns

Right click on columns -> Preferences -> Columns UI -> Playlist view -> Columns

- New
    - Name = Favorite
    - Width = 20
    - Scripts -> Display
        - $if($stricmp(%favorite%,true),★,☆)
- New column
    - Name = Genre
    - Scripts -> Display
        - %genre%
- Date -> Options tab -> Linked meta field -> ORIGINALDATE -> Scripts tab -> originaldate


### Favorites filter

Library -> Configure -> Columns UI -> Layout Tab -> Right click on last horizontal splitter -> Insert panels -> Panels -> Filter -> Turn off "show caption"

Library -> Configure -> Columns UI -> Filters -> Fields tab -> Add Favorite // $if($stricmp(%favorite%,true),★,☆)

Right click on the new filter on the main UI, select 'Favorite'


Converting
----------

- Conversion string: %album artist%/%album%/\[%discnumber%.\]%tracknumber%. %title%
- Copy \*.jpg upon conversion
- Transfer tags, replaygain, and \*.jpg when finished

### To FLAC

- DO NOT PREPROCESS REPLAYGAIN; this alters the file! Only do post-processing (tag transfer).

### To MP3

- Use preprocess replaygain (alters the file)
- After converted, attach the actual .jpg to the file (important for Google Music \[Google Music doesn't support OGG\]). Right click-> batch attach, include image name.
