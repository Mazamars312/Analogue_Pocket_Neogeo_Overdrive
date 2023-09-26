# NeoGeo Overdrive for Pocket (0.8.0)

This is the port of the NeoGeo Core made by [Furrtek](https://www.patreon.com/furrtek/posts) for the Analogue Pocket using the APF framework and some of my own code.

### NOTE! Read the installation and usage instructions as the default Bios file has changed, and saves can cause issues. Also, not all games will be compatible with this core, so revert to the standard NeoGeo core for the pocket if needed.

## Bug fixes in 0.8.0

* Increased memory access from the ASYNC CRAM cores.
* Modified the PLL timing and clocks for the cores. Changed how the B1 chip now gets its video clock from the LSPC chip. Audio still has its own clock from the PLL.
* AES and MVS modes and clock changes.
* Redid the V2 Masking and offset to get all the PCM audio working on some games, such as Blue's Journey, NAM-74, and The Super Spy.
* Added some bug fixes that paulb-nl discovered, including the C1 waits and audio issues.
* On-the-fly aspect ratio changes in the interact menu.
* Mappable buttons in the menu - the player type options have been removed.
* Reloadable bios and game JSONs within the core.
* Reset has been moved from the left trigger to the interact menu.
* 320 or 304 output for certain games that don't utilize the full screen.
* Overclocked the CPU to 16MHz and dual-ported BRAM for Fast and Slow VDP memory access.

## Installation and Usage 

If you have version 0.7.5 or below installed, it is recommended to uninstall it and install the new release.

* An added file, `sfix.sfix`, is required for this installation. Please see details below.

The current Darksoft packs will work with this, having the following file names:
*(Working on the *.neo files soon)
* 68K PROG Asset: prom
* 68K PROG1 Asset: prom1 for the 7 games that require it.
* Z80 Asset: m1rom
* CROM Asset: crom0
* SFIX Asset: srom
* Voice Asset: vroma0
* Bios: `uni-bios_4_0.rom` - This needs to be in the `/assets/ng/common`. I upgraded to "4_0", so this might cause issues with your pocket. Follow the instructions on how to use a different BIOS below.
* Lo Bios: `000-lo.lo` - This should be placed in the `/assets/ng/common`.
* Sfix: `sfix.sfix` - Ensure this is in the `/assets/ng/common`.
* Copy the folders from the Github (dist) or the release .zip to the root of your SD card.
* Place your game files, grouped by game, into individual directories in the `/assets/ng/common` folder.
* If you encounter an error 257, the save files in the `/saves/ng/Mazamars312/` directory might need deletion as the maximum size of the saves can be 8Kbytes.

The Autoloading JSONs provided by terminator2k2 are all compatible with games using the Dark Soft directory names in the `/assets/ng/common` folder.

## How to change the game 
* While in the game, press the home button and navigate to **_Core Settings_** > **_Load Game JSON Setup_**, then select a new game.

## How to use a different BIOS for the core 
You have two options:
* Change the filename to the firmware name you want to autoload in the `/assets/ng/common` folder (Line 46 in the `/Cores/Mazamars312.NeoGeo/data.json`).
* While in the game, press the home button, go to **_Core Settings_** > **_Load Bios_**, and select another BIOS file.

## Controllers 
All are mapped by the **_Interact_** menu with the Analogue Key Mapper. The old player controller types have been removed as they are now unnecessary.

## Are Memories supported?
No, at the moment. There are many moving parts in the NeoGeo.

## Will you support NEO files?
I plan to in future versions. I don't fully understand these files currently, so this is a limitation on my end at the moment.

## Do I need to build JSON Files for each game?
No, not every game. Terminator2k2 has built every game in the ROM set, which also configures the core for special chips. Huge thanks to him. His code, written in Python, can be found in the provided folders. He has also added the Xeno Crisis JSON, so please support [Bitmap Bureau](https://bitmapbureau.com/) and get their fantastic game.

## [Pocket APF](https://www.analogue.co/developer/docs/overview) Modules
I've created the following modules, which are free to use without any license. You have full permission from me to create with them :-)
* CRAM (ASYNC) Access
* SDRAM (SYNC) Access
* SDRAM (SYNC) Access
* SRAM (ASYNC) Access
* I2S Audio Bus

## Can I play in PAL mode?
Yes. Once in the game, press the Analogue Pocket's Home button then navigate to the **Core Settings**. In there, you can switch between NTSC and PAL on the fly. You can even move the screen around.

## Can I play in AES mode?
Yes. Once in the game, press the Analogue Pocket's Home button, navigate to **Core Settings**, and then to MVS/AES. In there, you can toggle between MVS and AES. This does alter the clock speeds, but it causes a reboot due to the clock change. This will not be reflected in the UniBios startup display since that's a feature of the BIOS. To adjust this, go into the UniBios setup and modify it there :-) Thanks!

## Are there bugs?
Yes. The current known bugs include issues in _AOF 1_ where some of the sprites don't display correctly (I suspect a masking issue). Additionally, I've noticed that The _Super Spy_'s logo sometimes doesn't appear. I hope to address these in the next build. Some bugs have also been introduced due to the faster clock speed. For instance, _KOF2003_ might drop its audio, and in MGS mode, the Video RAM isn't read accurately by the CPU. You can bypass this warning from the BIOS for now.

## Why is the refresh different from the MiSTer core?
It's not anymore. I'm off by only .001 of a frame now!!! YAY.

## Can this be ported to the MiSTer core?
I'm confident it could be, but I won't undertake that task right now. If someone were to attempt it, here are my changes:
- Dual-ported BRAM - one port for the standard 24MHz clock for the video side.
- The lspc2 code at the top with the new BRAM signals for dual-port access.
- A clock mux for the desired clock frequencies. That output should go to the signal wire in the `Neogeo.sv` file that I've named `CPU_CLOCK`. Cores that use this include: `Main_ram`, `Backup`, `cpu_68K`, `neo_pvc`, `neo_sma`, `memcard`, `com`, `fast_ram_vdp`, `slow_ram_vdp`, `lspc2_a2`.
- In the 68K processor, I've utilized the "12MHz" clock for the `CLK_68KCLK`, but I've renamed it `M68K_CLKEN`. Cores that utilize this are `syslatch` and `neo_c1`.
There might be significant bugs in this approach, but it was an intriguing challenge to increase the speed.

## Could you make this faster than 16MHz?
Yes, but currently, I'm using the SRAM in the pocket for the main memory. I'd need to transfer the LO-ROM to that and relocate the Main memory back into the BRAM to achieve higher speeds. Also, some games frequently poll the Z80, which might necessitate a slight overclock as well.

## Some games are cut off on the sides of the image.
Try running in High-Res mode, as that will run the game at 320 pixels. Then adjust both the screen X and Y for the optimal image output.

## What caused the slowdowns in the previous build?
This was due to my decision to move the SROMS into the CRAM memory, which houses both the 68K and Z80 programs, with the SROM getting the highest priority. As a result, the 68K's access was limited.
- I've not only reduced the CRAM data retrieval time from approximately 11 clock cycles to about 7-8 at 96MHz, but I've also made the video core more accommodating to minor SROM data delays if the 68K requires access first.
- As I see it, this has greatly improved the core. Except for _Metal Slug 2_. We all know its inherent slowdowns, even on genuine hardware. Try _MSlug X_ or _2 Turbo_ and notice the difference.

## Aspect ratios are weird?
Yes, that's why I've configured the core to offer four options between 304 and 320 H pixel outputs. By default in the list, there are:
- 19:15 - Best for 304 output 
- 4:3 - Best for 320 output 
- 160:144 - Best for full screen on the Pocket
- 16:9 - Best for fullscreen on a 1080P screen 

## No, the aspect ratios are REALLY weird!
If you're not satisfied with them, you can modify them yourself in the following JSON files:
- `Video.json` - The top four entries are for the 304 resolution, and the bottom four are designed for 320. Adjust the `aspect_h` and `aspect_v` entries as per your preference. Ensure you modify this for both the 304 and 320 groups. Or mix and match, it's your choice!
- `interact.json` - Search for `"name": "Video Scaler output"`, `"id": 7`. In that list, only four entries work with the top and bottom four entries in the `Video.json``. You can rename these for your reference. Play around; it only impacts the scaler. You can even try rotation values of 90 or 180 for a unique challenge!

## The clock does not work.
Yes, I need to build the RTC and timer for the core. It's on the to-do list for the next build.

## Why were you late with this release and the one before?
I've been relocating and renovating simultaneously. I didn't want dust to harm my computer and Pocket. Removing 20 square meters of tiles is entertaining but dusty. Add painting every room, constructing a new kitchen, developing the Amiga core, and working on an MPU for Media access on the pocket to the mix.

## License
- All the code I've developed is freely accessible and open to everyone to contribute to core building.
- All other code by respective authors adheres to their licenses, so please support them.

## How can we support you?
I'd appreciate it if you'd support individuals like [Furrtek](https://www.patreon.com/furrtek/posts) and other developers since they've invested significant effort into these cores. I aim to support them by sharing my code modules, which should simplify core development for the Pocket. However, I do have a penchant for coffee. So, if you're inclined, you can buy me a [coffee](https://www.buymeacoffee.com/Ultrafp64).

## Are you getting compensated by Analogue for all this work?
No, not for any of the cores I've crafted for the pocket. Although they've provided me with hardware and have always been supportive when I sought assistance. I'm genuinely grateful to them. I primarily work on the pocket because I relish having a portable device for my gaming needs (second only to my love for coffee). The primary driving force is my passion for programming and the joy I derive from vintage consoles.

## Why not MiSTer?
I plan to release MiSTer-related content soon. I'd love to see more cores out there. At the moment, I'm already swamped and not prepared to venture into additional projects.

## Credits
* [Furrtek](https://www.patreon.com/furrtek/posts) - Please support Furrtek as much as you can; they have contributed significantly to the community. I particularly enjoy their de-cap processes.
* [Jotego](https://www.patreon.com/topapate) - Renowned for his cycle-accurate [JT12](https://github.com/jotego/jt12) and [JT49](https://github.com/jotego/jt49) implementations used in this core.
* [Jorge Cwik](fx68k@fxatari.com) - Responsible for the FX68K M68000, a cycle-accurate, fully synchronous CPU.
* [sorg - Alexey Melnikov](https://github.com/sorgelig) - Developed many of the special chips in the core and manages the Mister distribution.
* [MiSTer team](https://github.com/MiSTer-devel) - Thanks to everyone committed to building outstanding cores.
* [paulb-nl](https://github.com/paulb-nl) - Identified issues with the last sprites not rendering, provided JT11 bug fixes for sound in cores, and highlighted the C1 wait stat bugs.
* Please advise if there are others involved with this core. I want to express my gratitude and ensure they receive proper acknowledgment.

## Special Thanks
* Electron Ash - Mate, thanks for the laughs and the advice on various topics.
* retrocaster and thehughhefner - Grateful for guiding me to more names of contributors who invested efforts into this core.
* terminator2k2 - Shared insights on the key mappings for certain controllers. (I'm still partial to the SNES button layout, though. LOL.) Also contributed to building the instant.json file.
* alexcom - Has a deep knowledge of NeoGeo games and tested them while I was working on the core. And, about that pixel clock—hilarious!
* Bitmap Bureau - Thoroughly enjoyed playing Xeno Crisis. It proved instrumental in identifying the audio masking issue. I'm looking forward to building the 32Mbyte Stereo version soon!

## Fun Facts
Everyone mistakenly assumes I'm Australian because I created my Github account shortly after relocating to Aussie around 12 years ago. However, I'm actually from New Zealand, affectionately known as the land of the Kiwis. I won't let the Aussies take credit for our achievements, much like they attempt with the band Crowded House and the renowned cake, Pavlova. Those are ours!
But hey, Aussies, you're welcome to claim Russell Crowe! HAHAHA.

* Every time a Kiwi relocates to Australia, both nations experience an uptick in average IQ.
* Contrary to popular belief, Fosters Beer, promoted as an Australian brand, isn't brewed in Australia. Now that's some clever marketing!
* Despite the light-hearted banter between Kiwis and Aussies, we always find common ground over a pint and share countless laughs.

