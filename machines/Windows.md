Windows
=======

After every reinstall
---------------------

- Run debloater https://github.com/Sycnex/Windows10Debloater
- Uninstall all non-critical applications from add/remove programs

### Scoop


Open a non-admin powershell

```
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
scoop install git
scoop bucket add extras

# base:
scoop install 7zip draw.io spotify googlechrome greenshot imageglass joplin neovim sumatrapdf

# optional:
scoop install audacity carnac obs-studio steam vlc rufus windirstat
```


### Windows OS tweaks

- Explorer
    - Desktop
        - Right click -> Sort by -> Item type
        - Right click -> View -> Auto arrange icons
    - Go to "Shook" (or other user) folder
        - Double-click 'View' tab to keep ribbon open
        - View tab
            - Show file name extensions
            - Select 'details' view, remove any "group by" options
            - Options
                - Open file explorer to "this PC"
                - Uncheck show recently used files in quick access
                - Uncheck show frequently used folders in quick access
                - View tab -> Apply to Folders
    - Right click on task bar -> Cortana -> Hidden
    - Right click on task bar -> Show task view button
    - Right click on task bar -> Show people on the taskbar
    - Right click on clock -> adjust date/time -> Set time zone
    - Quick access -> unpin all but User, Desktop, Downloads
    - From "this PC", map NAS to Z:/
- Elevated powershell
    - `powercfg -h off` (Disables hibernate)
    - `control userpasswords2` (Autologin; just on desktop, not laptop)
- Start menu
    - "power sleep"
        - Screen: 5 minutes on battery, 15 minutes plugged in
        - Sleep: 1hr on battery, never plugged in
    - "touchpad"
        - Increase touchpad sensitivity to '8'
        - Three-finger tap = middle mouse click
    - "pc name"
        - Rename this PC
    - "arrange windows"
        - Turn off
            - When I snap a window, automatically size it to fill available space
            - When I snap a window, show what I can snap next to it
            - When I resize a snapped window, simultaneously resize any adjacent snapped window
    - "display"
        - Night light on -> Night light settings -> set color, schedule 7PM
    - "wallpaper"
        - select solid color
    - "start"
        - Show recently added apps -> Off

### Application setup

- Chrome
    - Log in to sync
        - Don't sync tabs or history
    - Leechblock
        - reddit.com
        - all days
        - all hours
        - allow overrides
        - redirect to hackernews
    - Bitwarden
        - timeout 4 hours
        - other -> enable autofill on page load
        - other -> default autofill setting = autofill on page load
- Setpoint
    - Increase mouse speed
    - Button 4 = Next
    - Button 5 = Previous
- 7zip
    - Open as an admin
    - Associate all types
    - Add to shell context menu
- Greenshot
    - Open app
    - Right click on system tray icon -> Preferences
    - Start on Windows boot
- Imageglass
    - Open imageglass, set defaults via wizard
- Spotify
    - Enable HQ streaming
- neovim (may just sunset this? see how this goes on laptop.)
    ```
    md ~\AppData\Local\nvim\autoload
    $uri = 'https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
    (New-Object Net.WebClient).DownloadFile($uri, $ExecutionContext.SessionState.Path.GetUnresolvedProviderPathFromPSPath("~\AppData\Local\nvim\autoload\plug.vim"))
    ```
    - From VM, run `cp .vimrc windows/AppData/Local/nvim/init.vim`
    - Add "C:\Users\Shook\scoop\apps\neovim\current\bin;C:\Users\Shook\scoop\apps\git\current\bin" to path


### After installations are done

- Right click on task bar -> Task Manager -> check startup menu (after chocolatey is done)
- "clean"
    - Disk cleanup -> Clean up system files -> check all -> OK
