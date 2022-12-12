# CustomFedoraLiveISO
Script that allows one to easily generate a custom Live Fedora ISO image by customizing an existing Live Fedora image.

# USAGE

The script has been designed to make generating a custom Live Fedora ISO image as easy as possible. At the top there are a number of user-tweakable parameters, but these are not reuired to be set. To run this script, you only need to do 2 things:

1) Select an existing ISO image to serve as the "base" ISO. This is what you will start with when you begin customizing the ISO. Do this by either:

* define the variable origIsoSource as something that `wget` can fetch from the internet. e.g., origIsoSource='https://dl.fedoraproject.org/pub/fedora/linux/releases/37/Spins/x86_64/iso/Fedora-KDE-Live-x86_64-37-1.7.iso' OR as a path to a local file via origIsoSource='file:///path/to/base/ISO/file.iso'

* leave the variable origIsoSource blank and you will be prompted to download one of the latest "respin" ISO files from 'https://dl.fedoraproject.org/pub/alt/live-respins/' OR to choose a local ISO file that resides somewhere under $customIsoTmpDir, which is the main top-level working directory for this script.

2) Customize the image to your liking in the same way that you would configure a system in a chroot using the command line. Any changes made (configuration files, packages installed with dnf, etc.) will be included in the ISO image generated by this script.

NOTE: GUI-based setup is not supported - you must be able to customize the system from a CLI interface. BUT, everything else is taken care of for you by the script - there is no need to fool with editing kickstart files or running lorax / livemedia-creator / livecd-creator, etc.

NOTE: is you define customIsoUSBDevPath to be a valid device under /dev, the ISO will be burned to $customIsoUSBDevPath using `livecd-iso-to-disk`

# CODE

The code is broken up into smaller sections, each of which is defined in a function and then that function is run. The code is fairly well commented - refer to it for more details. But, as a simplified high-level overview, this script downloads a live iso (or chooses 1 on disk), unsquashes it, mounts it, and uses systemd-nspawn to boot it into a container where you will be able to customize it from the CLI. After you are done customizing it, the script runs livemedia-creator using a basic kickstart file from the `lorax` git repo and using the freshly-customized rootfs.img as the base filesystem image. This will create a custom Live Fedora ISO that can be burned to disk. You can optionaly burn the ISO to a USB from this from the script using `livecd-iso-to-disk`, though externally using fedora-media-writer or a 3rd party tool like Rufus works as well.

# BUGS

Known: none

Suspected: the script prompts you to install a number of dependencies, but this list is probably not complete. If there are any required packages that dont get automatically installed let me know and I will add them tio the `dnf install` command.

Possible: The mock configuration file on the system I wrote this on has been added to quite extensively. I *think* it will still correctly build the anaconda boot.iso with lorax in mock on systems with the default mock configuration (well almost default - the script already makes 1 small change to the mock configuration so that `dnf` is available in mock). I'd rather not force changing the default mock config more than I need to, but if building the anaconda boot.iso with lorax in mock doesnt let me know and I'll add something to further tweak the mock config.
