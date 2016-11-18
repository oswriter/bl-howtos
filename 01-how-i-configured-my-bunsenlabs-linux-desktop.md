## How I Configured My BunsenLabs Linux Desktop

[BunsenLabs Linux](https://www.bunsenlabs.org/) is a [Debian](https://debian.org)-based distribution that continues the work of the [former CrunchBang project](https://distrowatch.com/table.php?distribution=crunchbang). Phillip Newborough, Crunchbang’s lead developer, ended the project following the release of CrunchBang 11 in May 2013, which was based on Debian 7 (Wheezy). BunsenLabs (BL) celebrated its first release this past April with BunsenLabs Hydrogen, which is based on Debian 8.6 (Jessie).

BL does not use a traditional desktop like [GNOME](http://www.gnome.org/) or [Xfce](http://wiki.xfce.org/). Instead it is a kitbash of components from various open source projects, including the [Openbox](http://openbox.org/wiki/Main_Page) window manager, the [tint2](https://gitlab.com/o9000/tint2) panel, the [Conky](https://github.com/brndnmtthws/conky) system monitor, and the [Thunar](http://docs.xfce.org/xfce/thunar/start) file manager. The BL developers added a number of scripts and simple utilities to help the user configure the desktop to their liking. 

I was a CrunchBang user and currently use BL as the operating system on my main work computer. Overall, BL offers a fast, uncluttered Debian experience. Here is a brief rundown of how I configured the various BL components to best serve my needs.

![bl-desktop](https://github.com/oswriter/bl-howtos/blob/master/images/2016-09-18-bl-desktop.png)

### Openbox

The heart of the BL desktop is Openbox, a window manager that can either function alone or in conjunction with an existing desktop environment. The configuration file for Openbox is located at **~/.config/openbox/rc.xml**. One of the first things I did was edit this file to add keybinds. For example, I like to use **Super+1** to launch LibreOffice Writer, so I added the following lines to the **rc.xml** file:

        <!-- User commands -->
        <keybind key="W-1">
          <action name="Execute">
            <startupnotify>
              <enabled>true</enabled>
              <name>word-processor</name>
            </startupnotify>
            <command>libreoffice --writer</command>
          </action>
        </keybind>

BL comes with a number of pre-configured keybinds, which are listed in the default Conky display. You can view all active key bindings by activating the built-in Openbox menu. Simply right-click the mouse on a free area on the desktop or enter **Super+Space** on the keyboard. There is a a **Display Keybinds** sub-menu option at the bottom.

#### Menu 

Speaking of the menu, Openbox uses this floating right-click menu in lieu of the more commonplace taskbar menu (i.e., the Windows-style "Start" button). The BL developers extensively customized Openbox with a set of [pipemenus](https://github.com/BunsenLabs/bunsen-pipemenus) that provide a roadmap to the entire system. Among other things, the pipemenus allow the user to one-click install select software packages, such as Google Chrome.

But unlike the menu system in desktop environments like Xfce, the Openbox menu does not automatically update itself when new software is installed (with the exception of applications installed through the BL pipemenus). This means the user is responsible for manually updating the menu. This can be done either through the configuration file, located at **~/.config/openbox/menu.xml**, or through a graphical menu editor called [obmenu](http://obmenu.sourceforge.net/). You can access obmenu from the BL menu by clicking **Preferences --> Openbox --> GUI Menu Editor**.

![bl-obmenu](https://github.com/oswriter/bl-howtos/blob/master/images/2016-09-18-bl-obmenu.png)

In my case, I used obmenu to delete most of the Openbox menu. I prefer to use the keybinds and taskbar to launch applications. So I removed everything except the items for Places, Recent Files, Preferences, et cetera.

#### Autostart

The autostart script sets up the user environment and runs specified applications when the user logs into BL. The autostart file is located at **~/.config/openbox/autostart**. BL includes a default autostart file. 

I made a few modifications to the default file. The first was to have two additional applications launch at startup: [tilda](http://tilda.sourceforge.net/tildaabout.php) and [Syncthing](https://syncthing.net/). Tilda is a drop-down terminal; Syncthing, as I've [discussed in a previous post](http://opensourcewriter.com/what-is-your-foss-backup-plan/), is a utility that share files between computers that I designate. To make sure these programs run correctly, I added the following lines to the autostart file:

        ## Start drop-down terminal
        tilda -h &

        ## Start Syncthing
        syncthing --no-browser & 

The text after the hash marks are comments ignored by the system. The **-h** flag after the **tilda** command is an instruction to start the drop-down terminal hidden from view. Similarly, the **--no-browser** flag bypasses displaying Syncthing's web-based interface. The ampersands (&) instruct the system not to wait for each command to finish before running the next one.

My second modification to the autostart file was a rather lengthy string designed to reconfigure the default display settings:

        xrandr --output DP3 --off --output DP2 --off --output DP1 --off --output HDMI3 --off --output HDMI2 --off --output HDMI1 --off --output LVDS1 --off --output VGA1 --mode 1280x1024 --pos 0x0 --rotate normal &&

I use my laptop as a desktop by plugging it into an external VGA monitor. In order to ensure BL uses only the external monitor and not the laptop display, I use a program called [xrandr](https://www.x.org/wiki/Projects/XRandR/). The various flags in the above command shuts off all display outputs except VGA1--my monitor--which is set at a resolution of 1280 x 1024. You'll see the laptop display listed under LVDS1.  

Incidentally, if you want to temporarily configure multiple displays on your own PC without writing the changes to the autostart file, I would recommend installing a graphical utility called [lxrandr](https://wiki.lxde.org/en/LXRandr), which was developed as part of the [LXDE](https://wiki.lxde.org/en/Main_Page) desktop.

Finally, I changed an autostart setting that always bugs me. For some reason many Linux developers insist on enabling the "tap-to-click" setting on laptop touchpads. This makes it virtually impossible to type since the second your finger strikes the touchpad, the cursor jumps to another part of the screen. To disable this annoying feature, you look for the following line in the autostart file:

        synclient VertEdgeScroll=1 HorizEdgeScroll=1 TapButton1=1 2>/dev/null

and change the last item to: **TapButton1=0**.

### tint2

The [tint2](https://github.com/BunsenLabs/tint2) panel and taskbar is designed to work with Openbox. The default BL desktop includes a single tint2 panel at the top of the screen that contains an application launcher, a taskbar displaying two workspaces and their active windows, a system tray, and a clock. Each of these items is represented by a single letter in the tint2 configuration file: 

- L for the launcher
- T for the taskbar
- S for the system tray
- C for the clock.

#### Application Launcher

The configuration file is located at **~/.config/tint2/tint2rc**. BL also includes an identical configuration file for a bottom panel in the same subdirectory under the filename **tint2rc-bottom**. I took advantage of this to create a bottom panel just for the application launcher.

I edited **tint2rc-bottom** by changing the item labeled **panel_items** from **LTSC** to just **L**. Next I added applications to the launcher:

        # Each launcher_item_app must be a full path to a .desktop file
        # this will have to be made:
        # launcher_item_app = /usr/share/applications/bl-www-browser.desktop
        # launcher_item_app = /usr/share/applications/bl-file-manager.desktop
        # launcher_item_app = /usr/share/applications/bl-text-editor.desktop
        # launcher_item_app = /usr/share/applications/bl-terminal-emulator.desktop
        launcher_item_app = /usr/share/applications/brasero.desktop
        launcher_item_app = /usr/share/applications/calibre-gui.desktop
        launcher_item_app = /usr/share/applications/catfish.desktop
        launcher_item_app = /usr/share/applications/deja-dup.desktop
        launcher_item_app = /usr/share/applications/evince.desktop
        launcher_item_app = /usr/share/applications/filezilla.desktop
        launcher_item_app = /usr/share/applications/geany.desktop
        launcher_item_app = /usr/share/applications/hexchat.desktop
        launcher_item_app = /usr/share/applications/idle3.desktop
        launcher_item_app = /usr/share/applications/lxappearance.desktop
        launcher_item_app = /usr/share/applications/lxrandr.desktop
        launcher_item_app = /usr/share/applications/minetest.desktop
        launcher_item_app = /usr/share/applications/nitrogen.desktop
        launcher_item_app = /usr/share/applications/retext.desktop 

For this task it may be helpful to install tint2 0.12 from the [Jessie Backports](https://wiki.debian.org/Backports) repository. BL ships with tint2 0.11. The main difference in 0.12 is that you don't need to type out the full file pathname for desktop icons. I actually have 0.12 installed but still chose to type out the full pathnames. You'll also notice I commented out (#) the first four items, which are the default BL applications. All of these applications have keybinds, so I found the launcher icons redundant. Here is what my modified launcher bar looks like:

![bl-tint2-launcher](https://github.com/oswriter/bl-howtos/blob/master/images/2016-09-18-bl-tint2-launcher.png)

To display this bottom panel, I opened the BL Tint2 Manager by selecting **Preferences --> Tint2 --> Tint2 Chooser** from the Openbox menu and checked the box next to **tint2/tint-bottom**.

#### Taskbar & Clock

Next, I modified the top panel. I changed the panel items from **LTSC** to **TCS**, eliminating the now-unnecessary taskbar and moving the clock before the system tray. The taskbar displays all of the virtual workspaces available. BL sets 2 workspaces by default. I prefer 4. To add desktops, click on **Preferences --> Openbox --> GUI Config Tool**. This opens the aptly named Openbox Configuration Manager [ObConf](http://openbox.org/wiki/ObConf:About), which includes a tab for changing the number of workspaces.

![bl-obconf](https://github.com/oswriter/bl-howtos/blob/master/images/2016-09-18-bl-obconf.png)

I also changed **taskbar_name = 1** to **taskbar_name = 0**. This displays the number of each workspace (1-4) on the taskbar.

The final item of note on the taskbar is the clock. In most Linux desktop environments, the clock display is set using a program called [strftime](https://linux.die.net/man/3/strftime). Each element is represented by a "conversion specification" that starts with a percentage (%) sign. For example, my clock is set in tint2 as follows:

        time1_format = %a-%d-%b, %l:%M %p
        
This displays the current date and time as **Wed-21-Sep, 3:16 PM**. Each conversion specification acts as follows:

- %a is the first three letters of the day of the week (Wed);
- %d is the day of the month as a decimal number (21);
- %b is the first three letters of the month (Sep);
- %l (a lowercase "L," not an uppercase "I") is the hour expressed as a number from 1-12 without the leading zero (i.e., 3 rather than 03 or 15 for the afternoon hour);
- %M is the minute as a decimal number (16); and
- %p is the AM/PM designation. 

![bl-tint2-top](https://github.com/oswriter/bl-howtos/blob/master/images/2016-09-18-bl-tint2-top-768x18.png)

### Conky

Conky is a system monitor that can display a wide variety of information on your desktop. BL retains the classic Conky configuration used by Crunchbang, which displays basic system information--disk space, RAM, CPU, and a list of pre-configured keyboard shortcuts--in a transparent box on the right side of the screen. In addition to this default setup, BL includes a graphical Conky tool that offers a number of additional pre-configured Conky displays.

![bl-conky-manager](https://github.com/oswriter/bl-howtos/blob/master/images/2016-09-18-bl-conky-manager.png)

I opted to replace the Crunchbang-style Conky with the simpler **Top.conkyrc** provided by the manager. 

![bl-conky-1](https://github.com/oswriter/bl-howtos/blob/master/images/2016-09-18-bl-conky.png)

### Nitrogen

BL offers a limited choice of wallpapers, mostly variants on a simple gray background with the BunsenLabs "flame" logo. There is an optional package, **bunsen-images-extra**, available from the BL repository that contains some additional wallpapers. But if you want to add or change the wallpaper, you need to use [Nitrogen](http://projects.l3ib.org/nitrogen/), a simple background browser and setting tool. 

![bl-nitrogen](https://github.com/oswriter/bl-howtos/blob/master/images/2016-09-18-bl-nitrogen.png)

You must configure Nitrogen to display the contents of directories containing the images you want to use as backgrounds. Click on **Preferences** at the bottom of the Nitrogen window, and a dialog box appears that allows you to add or remove directories. 

In case you're wondering, my background is an image of Venice downloaded from [WallpapersWide.com](http://wallpaperswide.com/search.html?q=venice).

### File Manager

BL ships with the Thunar file manager from the Xfce desktop. While Thunar is a good file manager with a great bulk rename feature, I decided to install [Caja](http://wiki.mate-desktop.org/applications:caja) instead. Caja is the default file manager in the [MATE](http://www.mate-desktop.com/) desktop. The main reason I went with Caja is that it enables a dual-pane display, which makes it easy to move files around. Thunar does not support this feature. 

To install Caja, just open a terminal and enter:

        sudo apt install caja
        
![bl-caja](https://github.com/oswriter/bl-howtos/blob/master/images/2016-09-18-bl-caja.png)

### System Theme and Icons

BL uses a modified version of the [Faenza](https://www.gnome-look.org/content/show.php/Faenza?content=128143) icon theme for GNOME. I prefer the [Vibrancy Colors](http://www.ravefinity.com/p/vibrancy-colors-gtk-icon-theme.html) icon theme published by [RAVEfinity](http://www.ravefinity.com/), which offers 14 different color palettes in 5 variations. The Caja image above features the "Dark" variant with a yellow color palette. 

Vibrancy works with any GTK-based desktop such as Openbox. You can download a Debian package containing the entire icon set at [this link](https://launchpad.net/~ravefinity-project/+archive/ubuntu/ppa/+files/vibrancy-colors_2.7~xenial~Noobslab.com_all.deb). To install the icons, simply open a terminal, change into in the directory containing the Vibrancy package and enter:

        sudo dpkg -i vibrancy-colors_2.7~xenial~Noobslab.com_all.deb

To browse and select from among the Vibrancy icon sets, run [lxappearance](https://wiki.lxde.org/en/LXAppearance), a program imported from the LXDE desktop that lets you customize the look and feel of the desktop.  

![bl-lxappearance](https://github.com/oswriter/bl-howtos/blob/master/images/2016-09-18-bl-lxappearance.png)

I also used lxappearance to change the system theme from the default "Bunsen" to "Bunsen-Blue."

### Conclusion

If you would like to learn more about how to customize BL and its components, visit the [BunsenLabs Linux Forums](https://forums.bunsenlabs.org/index.php). You can also find excellent documentation of many of the packages used in BL at the [Arch Linux Wiki](https://wiki.archlinux.org/index.php/Main_page).
