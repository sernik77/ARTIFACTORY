---
title: Default Filename Patterns
kind: cheatsheet
status: stable
summary: "Default file naming patterns of phones, cameras, recorders, scanners and other devices, with examples."
tags: [files, naming, devices, forensics, cameras, audio]
created: 2026-11-10
updated: 2026-11-10
---

# Default Filename Patterns

*What file names devices and apps give their output by default: video, images, screenshots, documents and audio.*

## TL;DR

- Cameras mostly use a short prefix plus a 4-digit counter, the scheme the DCF camera standard defines (`IMG_1234`, `DSC_0001`, `GOPR1234.MP4`).
- Phones and newer apps use timestamps: `VID_20240718_153000.mp4`, `PXL_20240911_212453178.mp4`, `Screenshot_20240718-153000`.
- Office, editors and OS tools fall back to a generic word plus a counter: `Document1.docx`, `Untitled.txt`, `Recording (2).m4a`.
- Use the patterns to identify a file's source device, sort mixed dumps, or write rename and import rules.

**How to read an entry:** **Device** – Type / .EXT, then `PATTERN` ~ `example`, then a note. `####` is a zero-padded counter, `YYYYMMDD` / `HHMMSS` are date and time fields.

## Contents

- [Video](#video)
- [Images & photos](#images--photos)
- [Screenshots](#screenshots)
- [Documents & text](#documents--text)
- [Audio & voice](#audio--voice)

## Caveats

- Compiled reference, not individually verified. Firmware versions, regional builds and user settings change the defaults; obscure and niche entries are the least certain.
- Imported from the `sernik77/Default-Filename-Patterns` README (2025-08-25). A duplicated block of four sections was removed and the last audio section was normalised to the common entry format.

## Video
### Cell Phones
- **Apple iPhone (iOS)** – Video / .MOV  
  `IMG_SERIAL.MOV` ~ `IMG_1234.MOV`  
  > "Default iOS camera uses the 'IMG_' prefix for both photos and videos (serial number increments)."

- **Samsung Galaxy (Android)** – Video / .MP4  
  `VID_YYYYMMDD_HHMMSS.mp4` ~ `VID_20240718_153000.mp4`  
  > "Many Samsung models use date/time filenames with a 'VID_' prefix. Some firmware variants use `MOV_####.mp4` or even an `IMG_YYYYMMDD_HHMMSS.mp4` format for videos."

- **Google Pixel (Android)** – Video / .MP4  
  `PXL_YYYYMMDD_HHMMSSmmm.mp4` ~ `PXL_20240911_212453178.mp4`  
  > "Pixel phones (Google Camera) use `PXL_` prefix plus date and time (with milliseconds) for video filenames."

- **Sony Ericsson (2000s Feature Phones)** – Video / .3GP  
  `MOV#####.3gp` ~ `MOV00001.3gp`  
  > "Older Sony Ericsson phones (e.g. K750/K800 series) saved videos as .3gp files with a sequential `MOV` prefix numbering."

### Digital Cameras (DSLR & Compact)
- **Canon (PowerShot/EOS)** – Video / .MOV  
  `MVI_SERIAL.MOV` ~ `MVI_0001.MOV`  
  > "Canon cameras use the 'MVI' (Movie) prefix for video files (e.g. `MVI_1234.MOV`), distinguishing them from still images (`IMG_1234.JPG`). Newer models may save videos as .MP4 but keep the `MVI_` naming."

- **Nikon (Coolpix/DSLR)** – Video / .MOV  
  `DSC_SERIAL.MOV` ~ `DSC_1234.MOV`  
  > "Nikon typically uses the same 'DSC_' prefix for video files as for photos (no special video prefix). For example, a Nikon video might be `DSC_0001.MOV` just like an image would be `DSC_0001.JPG`."

- **Fujifilm (FinePix/X Series)** – Video / .MOV  
  `DSCF####.MOV` ~ `DSCF1001.MOV`  
  > "Fujifilm cameras use the 'DSCF' prefix (as with images). Video files continue the numbering sequence with the same prefix (e.g. `DSCF0001.MOV`)."

- **Panasonic Lumix** – Video / .MP4 (.MTS)  
  `P<folder><seq>.MP4` ~ `P1000001.MP4`  
  > "Lumix cameras incorporate the folder ID into filenames. For example, in folder 100, a video might be `P1000001.MP4`. (In AVCHD mode, video files appear as `00000.MTS` in the AVCHD folder structure.)"

- **Pentax DSLR** – Video / .MOV  
  `IMGP####.MOV` ~ `IMGP0001.MOV`  
  > "Pentax uses the 'IMGP' prefix (same as for images) for video files. A Pentax video will continue the image sequence numbering with `IMGPxxxx.MOV`."

- **Casio Exilim** – Video / .AVI  
  `CIMG####.AVI` ~ `CIMG0001.AVI`  
  > "Casio compact cameras prefix files with 'CIMG' for both images and videos. Older models often saved videos as AVI files (e.g. `CIMG0002.AVI`)."

### Action Cameras & Drones
- **GoPro (HERO2–HERO5)** – Video / .MP4  
  `GOPR####.MP4` ~ `GOPR1234.MP4`  
  > "Early GoPros name videos with a 'GOPR' prefix. If a recording is long and gets split into segments, the first file is `GOPRxxxx.MP4` and subsequent segments use `GP{{chapter}}xxxx.MP4` (e.g. `GP011234.MP4` for the second part of clip 1234)."

- **GoPro (HERO6/HERO7)** – Video / .MP4  
  `GH##$$$$.MP4` ~ `GH011234.MP4`  
  > "GoPro HERO6/7 introduced naming with prefixes like `GH` or `GX`. The format `GH01####.MP4` includes a two-digit chapter index (`01`, `02`, etc.) and a four-digit clip number. For example, `GH011234.MP4` is clip 1234, first segment."

- **DJI Drones (Mavic/Phantom)** – Video / .MP4  
  `DJI_####.MP4` ~ `DJI_0001.MP4`  
  > "DJI drone cameras (and Osmo series) use a `DJI_` prefix with a four-digit sequence for videos (e.g. `DJI_0250.MP4`). The counter resets after 9999 or when the SD card is reformatted."

### Camcorders & Handycam
- **JVC Everio (HDD Camcorder)** – Video / .MOD  
  `MOV###.MOD` ~ `MOV001.MOD`  
  > "JVC Everio camcorders (which produce .MOD files) name videos as `MOV001.MOD`, `MOV002.MOD`, etc. (After `MOV009.MOD`, names continue in hex: e.g. `MOV00A.MOD` … up to `MOV00F.MOD`, then `MOV010.MOD`)."

- **Sony Handycam HDR series** – Video / .MP4  
  `MAH#####.MP4` ~ `MAH00001.MP4`  
  > "Many Sony Handycam and Action Cam models create MP4 video proxies with names like `MAH00001.MP4`. (Full-quality footage may be in AVCHD format, with separate MTS files, but any MP4 clips follow the `MAH` naming sequence.)"

### Dash Cams
- **Nextbase Dash Cam** – Video / .MP4  
  `YYMMDD_hhmmss_###_CQ.MP4` ~ `240915_143027_001_FH.MP4`  
  > "Nextbase cams embed date/time into the filename. Format breakdown: e.g. `240915_143027_001_FH.MP4` = 2024-09-15 14:30:27, file 001, Front camera, High quality."

- **Vantrue E2 (3CH Dash Cam)** – Video / .MP4  
  `YYYYMMDD_hhmmss_nnnn_X_Y.MP4` ~ `20230128_140633_0008_N_A.MP4`  
  > "Vantrue (E2) uses a verbose format: date/time + sequence + markers. `X` denotes type (e.g. N=normal, E=event, P=parking, T=time-lapse) and `Y` is camera (A=front, B=inside, C=rear). Example: `..._N_A.MP4` = normal front camera footage."

- **BlackVue Dashcam (Dual Channel)** – Video / .MP4  
  `YYYYMMDD_HHMMSS_[Type][Cam].mp4` ~ `20250722_110221_NF.mp4`  
  > "BlackVue filenames start with date and time. The suffix codes indicate recording mode and camera: e.g. `NF` = Normal Front, `NR` = Normal Rear, `EF` = Event Front, etc. (In the example, `2025-07-22 11:02:21, Normal mode, Front camera`.)"

### Security & Doorbell Cameras
- **Ring Doorbell (Clip Download)** – Video / .MP4  
  `Ring_<CamName>_YYYYMMDD_HHMMSS.mp4` ~ `Ring_FrontDoor_20210403_124511.mp4`  
  > "When downloading individual Ring camera videos, the default name includes the device name and timestamp (e.g. camera “FrontDoor”, date 2021-04-03, 12:45:11). Batch downloads, however, may use a different generic naming scheme."

- **CCTV DVR/NVR (Generic)** – Video / .MP4  
  `ch<Channel>_YYYYMMDD_HHMMSS.mp4` ~ `ch01_20230815_213000.mp4`  
  > "Many security camera systems export video files with names containing the channel number and start timestamp. For example, `ch01_20230815_213000.mp4` represents Channel 1 footage starting at 21:30:00 on 2023-08-15."

### Smartphones (More Brands)
- **Huawei (P/Mate series)** – Video / .MP4  
  `VID_YYYYMMDD_HHMMSS.mp4` ~ `VID_20230911_194530.mp4`  
  > "Huawei adopts the Samsung-like `VID_` + timestamp pattern for default video names."

- **Xiaomi / Redmi / Poco** – Video / .MP4  
  `VID_YYYYMMDD_HHMMSS.mp4` ~ `VID_20230521_145210.mp4`  
  > "Default MIUI camera app uses `VID_` + timestamp. Sometimes includes milliseconds (e.g. `VID_20230521_145210123.mp4`)."

- **Oppo / Realme** – Video / .MP4  
  `VID_DATE_TIME.mp4` ~ `VID_20240314_152030.mp4`  
  > "Oppo/Realme (ColorOS) follows a nearly identical scheme to Xiaomi/Huawei."

- **Motorola (Moto G/X series)** – Video / .MP4  
  `VID_YYYYMMDD_HHMMSS.mp4` ~ `VID_20221209_174550.mp4`  
  > "Motorola uses a timestamp format prefixed by `VID_`."

- **LG (pre-2021 models)** – Video / .MP4  
  `VID_YYYYMMDD_HHMMSS.mp4` ~ `VID_20190830_221003.mp4`  
  > "LG smartphones also adopted the common `VID_` timestamp pattern for videos."

- **Nokia Lumia (Windows Phone)** – Video / .MP4  
  `WP_YYYYMMDD_HH_MM_SS_Pro.mp4` ~ `WP_20160321_12_45_55_Pro.mp4`  
  > "Windows Phones often prefixed files with `WP_`, followed by date/time. Some included `_Pro` if captured with the Lumia Pro Camera app."

### Camcorders (Older)
- **Panasonic DV/AVCHD Camcorders** – Video / .MTS  
  `00000.MTS` ~ `00012.MTS`  
  > "Panasonic AVCHD cameras save clips sequentially in the `PRIVATE/AVCHD/BDMV/STREAM/` folder. Filenames are 5-digit numbers."

- **Hitachi DVD Camcorders** – Video / .VRO  
  `VR_MOVIE.VRO` (auto-increment in subdirectories)  
  > "Early DVD-based Hitachi camcorders recorded directly to DVD using `.VRO` files with simple names like `VR_MOVIE.VRO`."

- **Sharp Viewcam (1990s Digital Models)** – Video / .AVI  
  `MVC####.AVI` ~ `MVC0001.AVI`  
  > "Some Sharp Viewcams used `MVC` prefix followed by sequential numbers."

### Action / Sports Cameras
- **Yi Action Camera** – Video / .MP4  
  `YDXJ####.MP4` ~ `YDXJ1234.MP4`  
  > "Yi (Xiaomi-backed) action cameras default to `YDXJ` prefix plus 4-digit sequence."

- **Sony Action Cam (AS series)** – Video / .MP4  
  `C#######.MP4` ~ `C0001.MP4`  
  > "Sony Action Cams often used a single-letter prefix (`C`) plus padded sequence numbers."

- **Insta360 One X** – Video / .INSV  
  `VID_YYYYMMDD_HHMMSS_00_###.insv` ~ `VID_20210101_103000_00_001.insv`  
  > "Insta360 cameras append multiple indices for stitching (`_00_001`)."

### CCTV / Security
- **Hikvision NVR/DVR** – Video / .MP4  
  `ch<Channel>_YYYYMMDDhhmmss.mp4` ~ `ch02_20231004_134500.mp4`  
  > "Hikvision uses channel-based filenames with precise timestamp."

- **Dahua NVR** – Video / .DAV (proprietary)  
  `YYYYMMDDhhmmss.dav` ~ `20230507101530.dav`  
  > "Dahua exports default proprietary `.dav` files named by exact start timestamp."

- **Arlo Cameras** – Video / .MP4  
  `arlo-<serial>-YYYYMMDD-HHMMSS.mp4` ~ `arlo-ABCD1234-20220101-153000.mp4`  
  > "Arlo embeds camera serial or ID plus timestamp."

- **Wyze Cam** – Video / .MP4  
  `recordYYYYMMDD-HHMMSS.mp4` ~ `record20230721-141502.mp4`  
  > "Wyze local SD recordings follow `record` + timestamp naming."

- **Eufy Security Cam** – Video / .MP4  
  `YYYYMMDD_HHMMSS.mp4` ~ `20230820_092500.mp4`  
  > "Eufy doorbells/cams store plain timestamp-named clips."

### Dashcams (More)
- **Garmin Dash Cam** – Video / .MP4  
  `YYYY-MM-DD-HHMMSS.mp4` ~ `2023-05-20-104522.mp4`  
  > "Garmin embeds full date/time separated by dashes."

- **Viofo A129 Duo** – Video / .MP4  
  `YYYYMMDD_HHMMSS_A.MP4` ~ `20240718_093025_A.MP4`  
  > "`A`/`B` suffix indicates front or rear channel footage."

- **Thinkware Dash Cam** – Video / .MP4  
  `YYYYMMDD_HHMMSS_F.MP4` ~ `20220830_180505_F.MP4`  
  > "`F` = front, `R` = rear, filenames based on timestamp."

- **70mai Dash Cam (Xiaomi ecosystem)** – Video / .MP4  
  `YYYYMMDD_HHMMSS.mp4` ~ `20230205_090010.mp4`  
  > "Simple timestamp default (loops when card full)."

### Misc / Special Devices
- **Flip Video Camcorder (2000s)** – Video / .MP4  
  `VID####.MP4` ~ `VID0001.MP4`  
  > "The Cisco/Flip handheld camcorders used a `VID` prefix with sequential numbering."

- **Microsoft Kinect (Video Captures)** – Video / .AVI  
  `kinect####.avi` ~ `kinect0001.avi`  
  > "Kinect developer captures often saved as `kinect` + sequence."

- **Logitech Webcams (Recording Software)** – Video / .AVI/.MP4  
  `Logi_YYYYMMDD_HHMMSS.mp4` ~ `Logi_20240505_120300.mp4`  
  > "Logitech’s default recording utility prefixes `Logi_` with timestamp."

- **PSP (PlayStation Portable) Camera)** – Video / .MP4  
  `MAQ####.MP4` ~ `MAQ0001.MP4`  
  > "The PSP-300 camera accessory saved clips with `MAQ` prefix."

### Obscure & Niche Devices

- **Kodak EasyShare (early 2000s)** – Video / .MOV  
  `K#######.MOV` ~ `K8600001.MOV`  
  > "Kodak used `K` + model indicator + sequence. Videos often stored alongside photos in the same numeric series."

- **Olympus Camedia (early digicams)** – Video / .AVI  
  `P#######.AVI` ~ `P1010023.AVI`  
  > "Olympus shared photo/video sequence; both prefixed with `P` plus 7-digit numbers (folder + sequence)."

- **Minolta Dimage (2000s)** – Video / .MOV  
  `PICT####.MOV` ~ `PICT0056.MOV`  
  > "Minolta’s compact Dimage cams stored videos under the same `PICT####` scheme as stills."

- **Kyocera / Sanyo Flip Phones (2000s)** – Video / .3GP  
  `MOV####.3GP` ~ `MOV0123.3GP`  
  > "Carrier-locked Japanese/US flip phones often defaulted to `MOV` + 4-digit sequence."

- **Nokia Series 40 (Classic)** – Video / .3GP  
  `Video####.3gp` ~ `Video0001.3gp`  
  > "Nokia feature phones saved clips with plain `Video0001.3gp`, continuing sequentially."

- **Nokia N-Series (Symbian)** – Video / .MP4  
  `VID####.mp4` ~ `VID00023.mp4`  
  > "Higher-end Nokias used `VID` prefix instead of generic `Video`."

- **Siemens SX1 / early Symbian Siemens** – Video / .3GP  
  `Clip####.3gp` ~ `Clip0005.3gp`  
  > "Siemens branded Symbian devices stored `.3gp` files with `Clip` prefix."

- **Palm Treo 650 / 700w** – Video / .3GP  
  `mov####.3gp` ~ `mov0001.3gp`  
  > "PalmOS/WinMobile Treo smartphones kept Apple-style `mov` prefix in lowercase."

- **Game Boy Camera (Unofficial mods)** – Video exports / .AVI  
  `gbc####.avi` ~ `gbc0001.avi`  
  > "Community export software for Game Boy Camera hacks often adopted simple lowercase prefixes like `gbc`."

- **Fisher-Price Kid-Tough Camera** – Video / .AVI  
  `PICT####.AVI` ~ `PICT0001.AVI`  
  > "The toy-grade Kid-Tough camera followed the generic `PICT` scheme, same as still images."

- **Mattel Vidster (2005 toy camcorder)** – Video / .AVI  
  `VID####.AVI` ~ `VID0001.AVI`  
  > "Cheap toy camcorder with iconic clear case. Very basic filename system."

- **Creative Vado Pocket Cam** – Video / .AVI  
  `VDO####.AVI` ~ `VDO0001.AVI`  
  > "Creative’s Flip competitor used `VDO` prefix sequential numbering."

- **Pure Digital Flip (pre-Cisco)** – Video / .AVI  
  `VID####.AVI` ~ `VID0001.AVI`  
  > "Early Flip (before MP4 era) defaulted to AVI with same VID prefix."

- **Aiptek PenCam / DV Toys** – Video / .AVI  
  `FILE####.AVI` ~ `FILE0001.AVI`  
  > "Super low-cost mini cams (`PenCam`, `PocketDV`) used generic `FILE` prefix."

- **Oregon Scientific ATC Mini Action Cam** – Video / .AVI  
  `DSC####.AVI` ~ `DSC0123.AVI`  
  > "Helmet/‘action’ cams from Oregon used `DSC` prefix identical to cheap digicams."

- **Tamagotchi Video Diary (Bandai 2008)** – Video / .AVI  
  `MOV####.AVI` ~ `MOV0001.AVI`  
  > "Odd collectible device that used `MOV` prefix with increment counter."

- **Archos Media Players (with cam add-on)** – Video / .AVI  
  `ARCH####.AVI` ~ `ARCH0001.AVI`  
  > "Archos PMP recorders used their brand name as prefix."

- **Zoom Q3 Handy Video Recorder** – Video / .MOV  
  `ZOOM####.MOV` ~ `ZOOM0001.MOV`  
  > "Zoom’s portable audio/video recorders used `ZOOM` prefix."

- **Pocket DV RexCam (generic clones)** – Video / .AVI  
  `DCFN####.AVI` ~ `DCFN0001.AVI`  
  > "Chinese no-brand digicams often shipped with obscure four-letter prefixes."

- **Camcorder toys (Disney Pix Click)** – Video / .AVI  
  `Pix####.AVI` ~ `Pix0001.AVI`  
  > "Children’s novelty cameras used simplified `Pix` prefix."

## Images & photos

### Smartphones

- **Apple iPhone (iOS)** – Photo / .JPG, .HEIC  
  `IMG_SERIAL.JPG` ~ `IMG_1234.JPG`  
  > "Standard since early iOS. Newer iOS defaults to `.HEIC` but keeps the same `IMG_` prefix."

- **Samsung Galaxy (Android)** – Photo / .JPG  
  `IMG_YYYYMMDD_HHMMSS.jpg` ~ `IMG_20240718_153000.jpg`  
  > "Samsung also sometimes uses `20240718_153000.jpg` (without prefix) depending on region/firmware."

- **Google Pixel (Android)** – Photo / .JPG, .HEIC  
  `PXL_YYYYMMDD_HHMMSS.jpg` ~ `PXL_20240911_212453.jpg`  
  > "Google Camera uses `PXL_` prefix plus timestamp. HEIC follows the same scheme."

- **Huawei (Android)** – Photo / .JPG  
  `IMG_YYYYMMDD_HHMMSS.jpg` ~ `IMG_20230911_194530.jpg`  
  > "Mirrors Samsung’s format."

- **Xiaomi / Redmi / Poco** – Photo / .JPG  
  `IMG_YYYYMMDD_HHMMSS.jpg` ~ `IMG_20230521_145210.jpg`  
  > "Identical to Huawei/Samsung scheme."

- **Nokia Series 40 (Feature Phones)** – Photo / .JPG  
  `Image####.jpg` ~ `Image0001.jpg`  
  > "Classic Nokia phones used `Image` prefix with 4-digit sequence."

- **Nokia Symbian (N-Series/E-Series)** – Photo / .JPG  
  `Photo####.jpg` ~ `Photo0123.jpg`  
  > "Higher-end Nokias defaulted to `Photo` prefix."

- **Sony Ericsson (Feature Phones)** – Photo / .JPG  
  `DSC####.jpg` ~ `DSC0001.jpg`  
  > "Borrowed the DSLR-style `DSC` prefix."

- **Motorola RAZR V3 / similar** – Photo / .JPG  
  `pic####.jpg` ~ `pic0001.jpg`  
  > "Simple lowercase `pic` prefix."

### DSLR & Mirrorless Cameras

- **Canon (PowerShot/EOS)** – Photo / .JPG, .CR2, .CR3  
  `IMG_SERIAL.JPG` ~ `IMG_1234.JPG`  
  > "Universal Canon photo scheme. RAW files share same naming (`IMG_1234.CR2`)."

- **Nikon (DSLR/COOLPIX)** – Photo / .JPG, .NEF  
  `DSC_SERIAL.JPG` ~ `DSC_0001.JPG`  
  > "DSC prefix is Nikon’s signature. RAW files: `DSC_0001.NEF`."

- **Sony Alpha / Cyber-shot** – Photo / .JPG, .ARW  
  `DSC####.JPG` ~ `DSC01234.JPG`  
  > "Sony sticks with `DSC` prefix (Digital Still Camera). RAW: `DSC01234.ARW`."

- **Fujifilm (FinePix/X Series)** – Photo / .JPG, .RAF  
  `DSCF####.JPG` ~ `DSCF1001.JPG`  
  > "`DSCF` stands for ‘Fuji Camera File’. RAW = `DSCF1001.RAF`."

- **Olympus (Camedia/OM-D)** – Photo / .JPG, .ORF  
  `P#######.JPG` ~ `P1010023.JPG`  
  > "7-digit P-number scheme. RAW uses `.ORF`."

- **Pentax (DSLR)** – Photo / .JPG, .PEF, .DNG  
  `IMGP####.JPG` ~ `IMGP0001.JPG`  
  > "Pentax prefixes stills with `IMGP`. RAW can be `.PEF` or `.DNG`."

- **Leica Digital** – Photo / .JPG, .DNG  
  `L#######.JPG` ~ `L1000123.JPG`  
  > "Leica uses `L` + 7-digit padded numbers."

### Compact / Point-and-Shoot

- **Casio Exilim** – Photo / .JPG  
  `CIMG####.JPG` ~ `CIMG0001.JPG`  
  > "Classic Casio scheme."

- **Kodak EasyShare** – Photo / .JPG  
  `DSCN####.JPG` ~ `DSCN0001.JPG`  
  > "Borrowed Nikon’s `DSCN` scheme."

- **Minolta Dimage** – Photo / .JPG  
  `PICT####.JPG` ~ `PICT0056.JPG`  
  > "Shared across photos and videos."

- **Panasonic Lumix** – Photo / .JPG, .RW2  
  `P#######.JPG` ~ `P1000001.JPG`  
  > "Uses same folder+sequence format as for videos."

- **Olympus Stylus** – Photo / .JPG  
  `PCDD####.JPG` ~ `PCDD0001.JPG`  
  > "Some Olympus compacts prefixed with `PCDD`."

### Action Cameras & Drones

- **GoPro (all generations)** – Photo / .JPG, .GPR (RAW)  
  `GOPR####.JPG` ~ `GOPR1234.JPG`  
  > "Stills use the same `GOPR` prefix as videos. RAW: `GOPR1234.GPR`."

- **DJI Drones (Mavic/Phantom)** – Photo / .JPG, .DNG  
  `DJI_####.JPG` ~ `DJI_0001.JPG`  
  > "Standardized across drones and Osmo line."

- **Insta360** – Photo / .insp  
  `PANO_YYYYMMDD_HHMMSS.insp` ~ `PANO_20211001_103000.insp`  
  > "Specialized panoramic photo format."

### Obscure / Toy / Niche

- **Fisher-Price Kid-Tough Camera** – Photo / .JPG  
  `PICT####.JPG` ~ `PICT0001.JPG`  
  > "Shared with videos."

- **Mattel Pixter / Pix Click** – Photo / .JPG  
  `Pix####.JPG` ~ `Pix0001.JPG`  
  > "Toy cam used ‘Pix’ prefix."

- **Game Boy Camera** – Photo / .SAV exports  
  `GBC####.bmp` ~ `GBC0001.bmp`  
  > "Unofficial export software creates BMP files with GBC prefix."

- **Polaroid i-Zone Digital** – Photo / .JPG  
  `PDC####.JPG` ~ `PDC0001.JPG`  
  > "Cheap Polaroid digicams used `PDC` prefix."

- **Creative Webcam (early 2000s)** – Photo / .BMP, .JPG  
  `snapshot####.jpg` ~ `snapshot0001.jpg`  
  > "Creative’s bundled software exported snapshots this way."

- **Logitech QuickCam (early)** – Photo / .BMP  
  `image####.bmp` ~ `image0001.bmp`  
  > "Simple lowercase prefix."

- **HP Photosmart (early digicams)** – Photo / .JPG  
  `HPIM####.JPG` ~ `HPIM0001.JPG`  
  > "HP prefix stood for Hewlett Packard Image."

- **Aiptek PenCam** – Photo / .JPG  
  `FILE####.JPG` ~ `FILE0001.JPG`  
  > "Generic toy/cheap camera scheme."

### Scanners & Document Cameras

- **HP ScanJet (1990s/2000s)** – Image / .TIF, .JPG, .PDF  
  `scan####.jpg` ~ `scan0001.jpg`  
  > "HP’s bundled software defaulted to lowercase `scan` prefix."

- **Canon CanoScan** – Image / .JPG, .PDF  
  `img####.jpg` ~ `img0001.jpg`  
  > "CanoScan software saved as `img` followed by sequential numbering."

- **Epson Perfection Scanners** – Image / .JPG  
  `imgYYYYMMDD####.jpg` ~ `img20230305_0001.jpg`  
  > "Epson often embedded date + padded counter."

- **Visioneer PaperPort Scanners** – Image / .MAX, .TIF  
  `page####.max` ~ `page0001.max`  
  > "Old PaperPort suite used `page` prefix."

- **Elmo Visual Presenter (Doc Cam)** – Image / .JPG  
  `ELMO####.JPG` ~ `ELMO0001.JPG`  
  > "Classroom doc cams auto-stamped filenames with brand name."

### Webcams & Capture Cards

- **Microsoft LifeCam (2000s)** – Image / .JPG  
  `Picture####.jpg` ~ `Picture0001.jpg`  
  > "LifeCam app default snapshot prefix."

- **Logitech QuickCam (2000s)** – Image / .JPG  
  `Logi####.jpg` ~ `Logi0001.jpg`  
  > "Later Logitech models branded prefixes with `Logi`."

- **Genius WebCam Eye** – Image / .BMP  
  `Snap####.bmp` ~ `Snap0001.bmp`  
  > "Low-end bundled cam software used `Snap`."

- **Dazzle Video Creator (capture cards)** – Image / .BMP  
  `cap####.bmp` ~ `cap0001.bmp`  
  > "Frame grabber cards labeled stills `cap`."

- **Pinnacle Studio Capture** – Image / .JPG  
  `frame####.jpg` ~ `frame0001.jpg`  
  > "Default export for captured frames."

### Consoles / Handhelds

- **Nintendo 3DS (Camera app)** – Image / .JPG  
  `HNI_####.JPG` ~ `HNI_0001.JPG`  
  > "3DS photos use `HNI` prefix."

- **Nintendo DSi Camera** – Image / .JPG  
  `PHOTO####.JPG` ~ `PHOTO0001.JPG`  
  > "DSi’s basic camera app saved as `PHOTO`."

- **PSP Go!Cam (Chotto Shot)** – Image / .JPG  
  `DSC####.JPG` ~ `DSC0001.JPG`  
  > "Sony carried the same `DSC` prefix over to PSP camera module."

- **PlayStation Vita Camera** – Image / .JPG  
  `IMG####.JPG` ~ `IMG0001.JPG`  
  > "Kept with generic IMG scheme."

- **Game Boy Printer (exports via mod)** – Image / .PNG  
  `GBP####.png` ~ `GBP0001.png`  
  > "Homebrew export utilities created `GBP` prefix files."

### Medical & Industrial Gear

- **GE Ultrasound Systems** – Image / .JPG, .DCM  
  `US####.jpg` ~ `US0001.jpg`  
  > "Exported frames labeled `US` (Ultrasound)."

- **Philips MRI/CT Consoles** – Image / .DCM  
  `IMG####.dcm` ~ `IMG0001.dcm`  
  > "DICOM imaging defaults to `IMG` prefix with padded numbers."

- **Olympus Endoscope Cameras** – Image / .JPG  
  `ENDO####.jpg` ~ `ENDO0001.jpg`  
  > "Captured endoscopic stills used `ENDO` prefix."

- **Zeiss Microscope Capture** – Image / .TIF  
  `Zeiss####.tif` ~ `Zeiss0001.tif`  
  > "Exported microscopy images carried brand prefix."

### Misc. Obscure Electronics

- **Casio Label Printer (with camera add-on)** – Image / .BMP  
  `CIMG####.BMP` ~ `CIMG0001.BMP`  
  > "Yes, even label printers with imaging modules recycled Casio’s CIMG scheme."

- **Oregon Scientific Digital Binocular Camera** – Image / .JPG  
  `DC####.JPG` ~ `DC0001.JPG`  
  > "Digital binoculars w/ camera attached used `DC` prefix."

- **Disney Pixter Digital Drawing Cam** – Image / .JPG  
  `Pix####.JPG` ~ `Pix0001.JPG`  
  > "Pix branding extended to drawing tablets."

- **Mattel Juice Box Camera Add-On** – Image / .BMP  
  `JB####.BMP` ~ `JB0001.BMP`  
  > "Children’s multimedia player add-on saved images with `JB` prefix."

### Law Enforcement & Vehicle-Mounted

- **L3 Mobile Vision DashCam (Police)** – Image / .JPG  
  `MV####.jpg` ~ `MV0001.jpg`  
  > "Mobile-Vision (widely used in US squad cars) exported stills as `MV` + 4-digit counter."

- **WatchGuard Vista (Body/Dash Cams)** – Image / .JPG  
  `WG####.jpg` ~ `WG0001.jpg`  
  > "WatchGuard systems marked every frame export with `WG` prefix."

- **Axon/Taser Body Cams** – Image / .JPG  
  `Axon####.jpg` ~ `Axon0001.jpg`  
  > "Body cam stills exported with brand prefix."

- **Panasonic Arbitrator 360HD (Police Dashcam)** – Image / .JPG  
  `ARBT####.jpg` ~ `ARBT0001.jpg`  
  > "Arbitrator dashcams used in patrol cars embedded ‘ARBT’ prefix."

- **Kustom Signals Eyewitness Dashcam** – Image / .JPG  
  `KSI####.jpg` ~ `KSI0001.jpg`  
  > "Kustom brand cams exported stills with `KSI` prefix."

- **Motorola MW810 (In-Car Video)** – Image / .JPG  
  `MW####.jpg` ~ `MW0001.jpg`  
  > "Vehicle-mounted rugged PC camera modules defaulted to `MW` + number."

### Scientific & Industrial

- **CCTV Traffic Enforcement Cameras** – Image / .JPG  
  `camYYMMDD_HHMMSS.jpg` ~ `cam240101_121500.jpg`  
  > "Red-light/speed cams generally name files with `cam` prefix + timestamp."

- **ANPR / LPR Cameras (License Plate Readers)** – Image / .JPG  
  `LP_YYYYMMDD_HHMMSS.jpg` ~ `LP_20230605_153000.jpg`  
  > "Plate readers standardize filenames with `LP_` + datetime."

- **Thermal Imagers (FLIR Systems)** – Image / .JPG, .FFF  
  `FLIR####.jpg` ~ `FLIR0001.jpg`  
  > "FLIR infrared cameras embed `FLIR` prefix. Raw radiometric: `.FFF`."

- **Scientific CCD Cameras (Andor, Hamamatsu)** – Image / .TIF  
  `image####.tif` ~ `image0001.tif`  
  > "Lab CCD captures default to `image` prefix, sequential numbering."

- **Scanning Electron Microscopes (SEM JEOL/Hitachi)** – Image / .TIF  
  `sem####.tif` ~ `sem0001.tif`  
  > "SEM software exports labeled simply `sem`."

- **Astronomy CCD (SBIG / Meade)** – Image / .FIT, .FITS  
  `img####.fit` ~ `img0001.fit`  
  > "Astro CCD imagers use `img` + number for FITS images."

- **Drone Survey / Mapping Payloads** – Image / .JPG  
  `survey_YYYYMMDD_HHMMSS.jpg` ~ `survey_20240404_124500.jpg`  
  > "Mapping payloads mark captures with `survey_` prefix and timestamp."

- **Industrial Machine Vision (Basler/IDS cameras)** – Image / .BMP  
  `frame####.bmp` ~ `frame0001.bmp`  
  > "Inspection systems export frames sequentially with `frame` prefix."

### Kiosks & Public Machines

- **Photo Booths (DNP / Mitsubishi)** – Image / .JPG  
  `PHOTO####.JPG` ~ `PHOTO0001.JPG`  
  > "Digital photo booths output images as `PHOTO0001.JPG` and onward."

- **Instant Print Kiosks (Kodak Picture Maker)** – Image / .JPG  
  `KPM####.JPG` ~ `KPM0001.JPG`  
  > "Kodak kiosks label exported/printed files with `KPM`."

- **ID Card Printing Systems** – Image / .JPG  
  `ID####.jpg` ~ `ID0001.jpg`  
  > "Most ID/badge stations default to simple ID-based prefixes."

- **ATM / Bank Surveillance Snapshots** – Image / .BMP, .JPG  
  `atmYYMMDD_HHMMSS.jpg` ~ `atm240601_093015.jpg`  
  > "ATMs embed timestamp directly into snapshot filename."

### Printers & Copiers

- **Xerox WorkCentre Scan-to-Email** – Image / .PDF, .JPG  
  `Scan####.pdf` ~ `Scan0001.pdf`  
  > "Multi-function Xerox machines use capitalized `Scan` prefix."

- **Ricoh Aficio Copier Scans** – Image / .PDF, .JPG  
  `ScanYYMMDD####.pdf` ~ `Scan2406010001.pdf`  
  > "Ricoh devices add date + counter."

- **Kyocera TASKalfa Copiers** – Image / .PDF  
  `SKMBT_C####YYMMDD####.pdf` ~ `SKMBT_C2202406011234.pdf`  
  > "Kyocera MFP devices use a verbose `SKMBT_C` prefix, followed by codes."

- **Canon imageRUNNER Copiers** – Image / .PDF, .JPG  
  `IMG####.pdf` ~ `IMG0001.pdf`  
  > "Canon copiers reuse the generic `IMG` prefix."

### Super Weird / Rare

- **TSA Baggage Scanner X-ray Export** – Image / .TIF  
  `XRAY####.tif` ~ `XRAY0001.tif`  
  > "Exported suspicious baggage captures labeled XRAY."

- **End-of-Line Automotive Factory Test Cams** – Image / .BMP  
  `test####.bmp` ~ `test0001.bmp`  
  > "Cameras on assembly lines often export diagnostic stills as `test` + sequence."

- **Slot Machine Internal Camera (Casino)** – Image / .JPG  
  `slot####.jpg` ~ `slot0001.jpg`  
  > "Machine audit/export software stored snapshots with `slot` prefix."

- **Weather Station Cams (Time-lapse)** – Image / .JPG  
  `wx_YYYYMMDD_HHMM.jpg` ~ `wx_20231011_1430.jpg`  
  > "Default weather capture software uses `wx_` prefix and timestamp."

### Aerospace & Satellites

- **NASA Hubble Data Exports** – Image / .FITS  
  `hst####.fits` ~ `hst1234.fits`  
  > "Hubble images follow `hst` + sequence for raw FITS archives."

- **Landsat Satellite (USGS)** – Image / .TIF  
  `LC08_L1TP_<pathrow>_<YYYYMMDD>_<proc>.tif` ~ `LC08_L1TP_015033_20220514_20220526.tif`  
  > "Structured filenames with satellite ID, path/row, date."

- **ESA Sentinel-2** – Image / .JP2  
  `T<tile>_<YYYYMMDDTtime>.jp2` ~ `T33UWP_20221014T104021.jp2`  
  > "Tiles named by UTM grid + timestamp."

- **NOAA Weather Satellites (HRPT/AVHRR)** – Image / .RAW, .PNG  
  `NOAA##_YYYYMMDD_HHMM.png` ~ `NOAA18_20230405_1230.png`  
  > "Decoded weather passes embed sat number + timestamp."

### Radiology & Medical Imaging

- **Siemens CT/MRI Systems** – Image / .DCM  
  `CT####.dcm` ~ `CT0001.dcm`  
  > "CT scans default to `CT` prefix in DICOM export."

- **Philips X-ray Systems** – Image / .DCM  
  `XR####.dcm` ~ `XR0001.dcm`  
  > "X-ray images exported sequentially with XR prefix."

- **GE PACS Systems** – Image / .DCM  
  `IMG####.dcm` ~ `IMG0001.dcm`  
  > "Plain IMG naming inside DICOM archive."

- **Ophthalmology Fundus Cameras** – Image / .JPG  
  `EYE####.jpg` ~ `EYE0001.jpg`  
  > "Retinal imaging systems use EYE prefix."

### Game Consoles & Dev Kits

- **Nintendo Switch Screenshot** – Image / .JPG  
  `YYYYMMDDHHMMSSss.jpg` ~ `2023090415301212.jpg`  
  > "Switch saves screenshots with full timestamp down to centiseconds."

- **PlayStation 4 Screenshot** – Image / .PNG  
  `YYYYMMDDHHMMSS.png` ~ `20230904153012.png`  
  > "PS4 auto-labels by date-time only."

- **Xbox One Screenshot** – Image / .PNG  
  `Screenshot_YYMMDD-HHMMSS.png` ~ `Screenshot_230904-153012.png`  
  > "Xbox adds Screenshot_ prefix."

- **Sega Dreamcast Dev Kit (Katana SDK)** – Image / .BMP  
  `dc####.bmp` ~ `dc0001.bmp`  
  > "Debug screenshot captures defaulted to `dc` prefix."

- **PlayStation 2 TOOL Dev Kit** – Image / .BMP  
  `snap####.bmp` ~ `snap0001.bmp`  
  > "Sony’s dev kit utilities called images `snap`."

### Industrial & Scientific Oddities

- **Electron Beam Lithography Systems** – Image / .TIF  
  `ebeam####.tif` ~ `ebeam0001.tif`  
  > "Exported micrographs use ebeam prefix."

- **Nuclear Power Plant CCTV Snapshots** – Image / .JPG  
  `npp_YYYYMMDD_HHMM.jpg` ~ `npp_20230901_1315.jpg`  
  > "Timestamp + `npp` (nuclear power plant)."

- **Traffic Radar Speed Cameras** – Image / .JPG  
  `RADAR####.JPG` ~ `RADAR0123.JPG`  
  > "Off-the-shelf radar cams mark stills RADAR."

- **Scientific Weather Balloon Cameras** – Image / .JPG  
  `balloon_YYYYMMDD_HHMMSS.jpg` ~ `balloon_20230721_141502.jpg`  
  > "Research balloon payloads timestamp filenames."

- **Telescopes (Amateur Observatory CCD)** – Image / .FIT  
  `light####.fit` ~ `light0001.fit`  
  > "Captured frames saved as ‘light’ exposures."

### Office & Consumer Relics

- **Fax Machines with Scan-to-File (Sharp, Brother)** – Image / .TIFF  
  `fax####.tif` ~ `fax0001.tif`  
  > "MFP fax scan exports labeled fax."

- **Old Digital Copiers (Ricoh 1990s)** – Image / .TIFF  
  `doc####.tif` ~ `doc0001.tif`  
  > "Simple doc-based prefix."

- **Palm Pilots w/ Camera Module (PalmPix)** – Image / .JPG  
  `palm####.jpg` ~ `palm0001.jpg`  
  > "PalmPix accessory tagged every file with palm."

- **IBM NetCam (1996)** – Image / .JPG  
  `net####.jpg` ~ `net0001.jpg`  
  > "One of the first consumer IP cams."

- **Kodak DC Series (1995-1998)** – Image / .JPG  
  `DCP####.jpg` ~ `DCP0001.jpg`  
  > "Early Kodak digital still cameras."

- **Apple QuickTake (1994)** – Image / .PCX, later .JPG  
  `QT####.pcx` ~ `QT0001.pcx`  
  > "Apple’s first digicam saved QuickTake stills with QT prefix."

### Military & Recon Systems

- **Predator / MQ-1 UAV Recon Feeds** – Image / .JPG, .PNG  
  `MQ1_YYYYMMDD_HHMMSS.jpg` ~ `MQ1_20230911_153000.jpg`  
  > "Exported stills from MQ-1 UAVs often embed platform ID + timestamp."

- **Reaper / MQ-9 UAV** – Image / .JPG  
  `MQ9_YYYYMMDD_HHMMSS.jpg` ~ `MQ9_20230821_221045.jpg`  
  > "Similar to Predator, but `MQ9` prefix. Filenames follow UTC capture time."

- **Raven / Puma UAS (hand-launched)** – Image / .JPG  
  `UAS####.jpg` ~ `UAS0001.jpg`  
  > "Short-range UAV systems export sequential UAS images."

- **Lockheed U-2 Recon Systems (modern digital)** – Image / .TIF  
  `U2_YYYYMMDD####.tif` ~ `U2_20230911_0001.tif`  
  > "High-altitude reconnaissance captures with aircraft ID."

- **SR-71 Digital Recon Upgrades (late service)** – Image / .TIF  
  `SR71####.tif` ~ `SR710001.tif`  
  > "Cold War recon digitizations often carried `SR71` prefix."

### Spy Satellites & Aerial Recon

- **KH-11 Kennen (Electro-Optical Recon)** – Image / .TIF  
  `KH11_YYYYMMDD_HHMMSS.tif` ~ `KH11_20230911_101530.tif`  
  > "Processed imagery often tagged with KH11 identifier and capture time."

- **Corona Film Digitization (KH-4 series)** – Image / .TIF  
  `COR####.tif` ~ `COR0123.tif`  
  > "Declassified Corona satellite film reels digitized with COR prefix."

- **WorldView / GeoEye Commercial Recon** – Image / .TIF  
  `WV##_YYYYMMDD_hhmm.tif` ~ `WV03_20230821_1215.tif`  
  > "Maxar/GeoEye imagery embeds satellite ID + timestamp."

- **Military Weather Satellites (DMSP)** – Image / .RAW/.PNG  
  `DMSP_F##_YYYYMMDD_HHMM.png` ~ `DMSP_F18_20230405_1530.png`  
  > "Defense Meteorological Satellite Program filenames include spacecraft ID."

### Forensic & Intelligence Equipment

- **EnCase Forensic Image Export (Cameras)** – Image / .JPG  
  `enc####.jpg` ~ `enc0001.jpg`  
  > "Forensic capture tools default to enc prefix sequential numbering."

- **XRY Mobile Forensic Toolkit** – Image / .JPG  
  `xry_YYYYMMDD_HHMMSS.jpg` ~ `xry_20230911_153000.jpg`  
  > "XRY extractions from mobile devices timestamp filenames."

- **Cellebrite UFED (Phone Imaging)** – Image / .JPG  
  `cel####.jpg` ~ `cel0001.jpg`  
  > "Cellebrite evidence snapshots default to cel prefix."

- **Field Tactical Cameras (Ruggedized Olympus/Canon mods)** – Image / .JPG  
  `MIL####.JPG` ~ `MIL0001.JPG`  
  > "DoD-issued modified consumer cameras stamped MIL prefix."

### Covert / Spy Gear

- **Button / Pen Spy Cams (Generic Chinese OEMs)** – Image / .JPG  
  `PIC####.JPG` ~ `PIC0001.JPG`  
  > "Cheap covert cams almost universally use PIC#### scheme."

- **LawMate Spy Cams (Professional covert gear)** – Image / .JPG  
  `LM####.JPG` ~ `LM0001.JPG`  
  > "LawMate devices (police/intel grade) export with LM prefix."

- **Body Wire Cams (90s–2000s)** – Image / .JPG  
  `BW####.jpg` ~ `BW0001.jpg`  
  > "Body-worn surveillance gear labeled stills BW."

- **Hidden Smoke Detector Cams** – Image / .JPG  
  `SDC####.jpg` ~ `SDC0001.jpg`  
  > "Consumer/professional hidden cams default to SDC (Smoke Detector Cam)."

- **ATM Skimmer Internal Cameras** – Image / .JPG  
  `cam####.jpg` ~ `cam0001.jpg`  
  > "Criminally used skimmer cams defaulted to plain cam####."

### Intelligence Analysis Exports

- **PhotoDNA & Hash DB Export Tools** – Image / .JPG  
  `hash####.jpg` ~ `hash0001.jpg`  
  > "Analysis suites export matched evidence files under hash prefix."

- **CIA Declassified Archive (CREST scans)** – Image / .PDF, .TIF  
  `doc####.tif` ~ `doc0001.tif`  
  > "Declassified CIA doc scans appear as doc#### by default."

- **Interpol Database Image Export** – Image / .JPG  
  `INT####.JPG` ~ `INT0001.JPG`  
  > "Interpol systems export case images under INT prefix."

## Screenshots

### Smartphones

- **Apple iPhone (iOS)** – Screenshot / .PNG  
  `IMG_SERIAL.PNG` ~ `IMG_1234.PNG`  
  > "Screenshots saved in same sequence as photos, no special prefix."

- **Samsung Galaxy (Android)** – Screenshot / .PNG  
  `Screenshot_YYYYMMDD-HHMMSS.png` ~ `Screenshot_20240718-153000.png`  
  > "Samsung standard since Galaxy S3."

- **Google Pixel (Android)** – Screenshot / .PNG  
  `Screenshot_YYYYMMDD-HHMMSS.png` ~ `Screenshot_20240911-212453.png`  
  > "Almost identical to Samsung, with hyphenated timestamp."

- **Huawei / Honor** – Screenshot / .PNG  
  `Screenshot_YYYYMMDD-HHMMSS.png` ~ `Screenshot_20230911-194530.png`  
  > "Huawei follows Android convention."

- **Xiaomi / Redmi / Poco (MIUI)** – Screenshot / .PNG  
  `Screenshot_YYYYMMDD-HHMMSS.png` ~ `Screenshot_20230521-145210.png`

- **Oppo / Realme / OnePlus (ColorOS/OxygenOS)** – Screenshot / .PNG  
  `Screenshot_YYYYMMDD-HHMMSS.png` ~ `Screenshot_20240314-152030.png`

- **Nokia Lumia (Windows Phone)** – Screenshot / .PNG  
  `WP_YYYYMMDD_HHMMSS.png` ~ `WP_20160321_124555.png`  
  > "Windows Phone prepended `WP_`."

### Desktop Operating Systems

- **Windows 10/11 (Snip & Sketch / Print Screen)** – Screenshot / .PNG  
  `Screenshot (n).png` ~ `Screenshot (1).png`  
  > "Screenshots numbered sequentially."

- **Windows 7/8 (Snipping Tool)** – Screenshot / .PNG  
  `Capture.PNG` or `Screenshot.PNG`  
  > "Default name unless changed."

- **macOS (Monterey/Big Sur)** – Screenshot / .PNG  
  `Screenshot YYYY-MM-DD at HH.MM.SS.png` ~ `Screenshot 2023-09-04 at 15.30.12.png`  
  > "Classic Mac format with human-readable timestamp."

- **macOS (Classic OS X)** – Screenshot / .PNG  
  `Picture n.png` ~ `Picture 1.png`  
  > "Older OS X used 'Picture #' naming."

- **Linux (GNOME Shell)** – Screenshot / .PNG  
  `Screenshot from YYYY-MM-DD HH-MM-SS.png` ~ `Screenshot from 2023-09-04 15-30-12.png`  

- **Linux (KDE Plasma)** – Screenshot / .PNG  
  `Screenshot_YYYYMMDD_HHMMSS.png` ~ `Screenshot_20230904_153012.png`  

- **Linux (XFCE)** – Screenshot / .PNG  
  `screenshot.png`, `screenshot_1.png`, etc.  
  > "XFCE defaults to lowercase with incremental numbering."

- **Steam (Game Screenshots)** – Screenshot / .JPG  
  `YYYYMMDDHHMMSS_1.jpg` ~ `20230904153012_1.jpg`  
  > "Steam overlays store with UNIX-style timestamp."

### Game Consoles

- **Nintendo Switch** – Screenshot / .JPG  
  `YYYYMMDDHHMMSSss.jpg` ~ `2023090415301212.jpg`  
  > "Includes centiseconds."

- **PlayStation 4/5** – Screenshot / .PNG, .JPG  
  `YYYYMMDDHHMMSS.png` ~ `20230904153012.png`

- **Xbox One / Series X|S** – Screenshot / .PNG  
  `Screenshot_YYMMDD-HHMMSS.png` ~ `Screenshot_230904-153012.png`

- **PlayStation Vita** – Screenshot / .PNG  
  `YYYYMMDDHHMMSS.png` ~ `20160321124555.png`

- **Nintendo 3DS** – Screenshot / .JPG  
  `HNI_####.JPG` ~ `HNI_0001.JPG`  
  > "Screenshots saved with same prefix as photos."

### Misc / Obscure

- **Android Emulators (Bluestacks, Nox)** – Screenshot / .PNG  
  `Screenshot_####.png` ~ `Screenshot_0001.png`

- **Chrome OS** – Screenshot / .PNG  
  `Screenshot YYYY-MM-DD at HH.MM.SS.png` ~ `Screenshot 2023-09-04 at 15.30.12.png`  
  > "Almost identical to macOS convention."

- **Ubuntu (old GNOME 2)** – Screenshot / .PNG  
  `Screenshot.png`, `Screenshot-1.png`, `Screenshot-2.png`  

- **Sun Solaris (X11 desktop)** – Screenshot / .XWD  
  `screenshot.xwd` ~ `screenshot0001.xwd`  
  > "X Window Dump format."

- **BeOS (1990s)** – Screenshot / .BMP  
  `screenshot####.bmp` ~ `screenshot0001.bmp`

- **Amiga Workbench (with grabber tools)** – Screenshot / .IFF  
  `grab####.iff` ~ `grab0001.iff`  

- **IBM OS/2 Warp** – Screenshot / .BMP  
  `Screen####.BMP` ~ `Screen0001.BMP`  

- **NeXTSTEP** – Screenshot / .TIFF  
  `ScreenShot####.tiff` ~ `ScreenShot0001.tiff`  
  > "Yes, NeXT used full 'ScreenShot' string."

## Documents & text

### Office Suites

- **Microsoft Word (2000–2016)** – Document / .DOC  
  `DocumentX.doc` ~ `Document1.doc`  
  > "Classic Word defaults before Office 365."

- **Microsoft Word (Office 365)** – Document / .DOCX  
  `DocumentX.docx` ~ `Document1.docx`  
  > "Modern Word default with .docx extension."

- **Microsoft Excel (2000–2016)** – Spreadsheet / .XLS  
  `BookX.xls` ~ `Book1.xls`

- **Microsoft Excel (Office 365)** – Spreadsheet / .XLSX  
  `BookX.xlsx` ~ `Book1.xlsx`

- **Microsoft PowerPoint** – Presentation / .PPTX  
  `PresentationX.pptx` ~ `Presentation1.pptx`

- **LibreOffice / OpenOffice Writer** – Document / .ODT  
  `UntitledX.odt` ~ `Untitled1.odt`

- **LibreOffice Calc** – Spreadsheet / .ODS  
  `UntitledX.ods` ~ `Untitled1.ods`

- **Google Docs (Export)** – .DOCX, .PDF, etc.  
  `Untitled document.docx`  
  > "Default until renamed in browser."

- **Apple Pages** – Document / .pages  
  `Untitled.pages`  
  > "Plain 'Untitled' default."

- **Apple Numbers** – Spreadsheet / .numbers  
  `Untitled.numbers`

- **Apple Keynote** – Presentation / .key  
  `Untitled.key`

### Plain Text & Editors

- **Windows Notepad** – Text / .TXT  
  `Untitled.txt`  
  > "Unless explicitly saved with another name."

- **macOS TextEdit** – Text / .TXT, .RTF  
  `Untitled.txt` or `Untitled.rtf`

- **Linux gedit** – Text / .TXT  
  `Untitled Document X` ~ `Untitled Document 1.txt`

- **Vim / Emacs temp files** – Swap / .SWP, .TMP  
  `.filename.swp` ~ `.myfile.txt.swp`  
  > "Auto-generated recovery files."

- **Nano (temporary)** – .save files  
  `.#filename` or `filename.save`

- **Notepad++** – Unsaved tabs  
  `new X` ~ `new 1`, `new 2`  
  > "Numbered unsaved buffers."

- **Sublime Text** – Unsaved buffer  
  `untitled.txt`

- **Visual Studio Code** – Untitled buffer  
  `Untitled-X` ~ `Untitled-1`

### Scanners / OCR

- **HP ScanJet** – Text / .TXT, .PDF  
  `scan####.txt` ~ `scan0001.txt`  

- **Canon Scan/OCR software** – .TXT, .PDF  
  `img####.txt` ~ `img0001.txt`

- **Epson Scan** – .PDF  
  `docYYYYMMDD####.pdf` ~ `doc202309150001.pdf`

- **ABBYY FineReader** – OCR export / .DOCX, .TXT, .PDF  
  `DocumentX.docx` ~ `Document1.docx`

### Legacy / Niche Systems

- **WordPerfect (DOS/Win)** – .WPD  
  `DOC####.WPD` ~ `DOC0001.WPD`

- **Lotus WordPro** – .LWP  
  `NOTE####.LWP` ~ `NOTE0001.LWP`

- **Lotus 1-2-3** – .WK1, .123  
  `BOOK####.123` ~ `BOOK0001.123`

- **Corel Quattro Pro** – Spreadsheet / .QPW  
  `QPW####.qpw` ~ `QPW0001.qpw`

- **IBM DisplayWrite** – Document / .DCA  
  `DW####.DCA` ~ `DW0001.DCA`

- **WordStar (CP/M)** – Text / .WS  
  `WS####.WS` ~ `WS0001.WS`

- **Palm OS MemoPad Export** – Text / .TXT  
  `memo####.txt` ~ `memo0001.txt`

- **BlackBerry Notes Export** – Text / .TXT  
  `Note####.txt` ~ `Note0001.txt`

- **Psion Series 5 Word** – Document / .WPD  
  `Doc####.WPD` ~ `Doc0001.WPD`

- **Amiga FinalWriter** – Document / .FW  
  `FW####.fw` ~ `FW0001.fw`

- **Atari ST 1st Word** – Document / .DOC  
  `DOC####.DOC` ~ `DOC0001.DOC`

### Weird / Special Cases

- **Windows Sticky Notes Export** – .SNT, .TXT  
  `StickyNotes.snt`  

- **Evernote Export** – .ENEX  
  `My Notes.enex`  

- **OneNote Export** – .ONE  
  `Untitled Section.one`

- **LaTeX editors (TeXworks, TeXShop)** – .TEX  
  `untitled-x.tex` ~ `untitled-1.tex`

- **Markdown editors (Typora, Obsidian)** – .MD  
  `Untitled.md`

- **Google Keep Export (Takeout)** – .HTML  
  `Keep-YYYYMMDDTHHMMSS.html`

- **Windows WordPad** – .RTF  
  `Document.rtf`

- **MS-DOS EDIT** – .TXT  
  `EDIT.TMP` (before manual save)

- **Unix `vi` scratch files** – .swp  
  `.filename.swp`  
  > "Temporary lock/swap during editing."

### Legacy & Niche Word Processors

- **Commodore 64 (GEOS Write)** – Document / .SEQ  
  `WRITER####.SEQ` ~ `WRITER0001.SEQ`  
  > "GEOS environment word processor default."

- **Apple II ProDOS (AppleWorks)** – Document / .AWD  
  `AW####.AWD` ~ `AW0001.AWD`  
  > "AppleWorks saved text as AWD files with sequential numbers."

- **Atari Portfolio (Palmtop PC)** – Document / .DOC  
  `PF####.DOC` ~ `PF0001.DOC`  
  > "Early palmtop default naming scheme."

- **ZX Spectrum +3 (Tasword)** – Document / .DOC  
  `TSW####.DOC` ~ `TSW0001.DOC`

- **Tandy TRS-80 (Scripsit)** – Document / .DOC  
  `DOC####.DOC` ~ `DOC0001.DOC`

- **DEC VAX WPS-PLUS** – Document / .WPD  
  `WPS####.WPD` ~ `WPS0001.WPD`

- **IBM DisplayWrite** – Document / .DOC, .DCA  
  `DW####.DOC` ~ `DW0001.DOC`

- **Brother Word Processor Typewriters (90s)** – Document / .WP  
  `BRO####.WP` ~ `BRO0001.WP`  
  > "Electronic typewriters with floppy disk export."

### Note-Taking & Journals

- **Lotus Notes** – Database / .NSF  
  `names.nsf`, `notes.nsf`  
  > "Default Notes storage files."

- **Microsoft OneNote** – Section File / .ONE  
  `Untitled Section.one`  
  > "Default if user doesn’t rename notebook section."

- **Evernote (ENEX Export)** – Note / .ENEX  
  `My Notes.enex`  
  > "Bulk export name, not per note."

- **Simplenote (JSON Export)** – Note / .JSON  
  `Simplenote-Export-YYYY-MM-DD.json`  

- **Zoho Notebook Export** – .HTML  
  `Notebook-YYYYMMDD.html`

- **Joplin** – Note Export / .JEX  
  `My notes.jex`

- **Obsidian Vault** – Markdown / .MD  
  `Untitled.md`  
  > "Default until manually renamed."

- **Bear App (macOS/iOS)** – Export / .TXT, .MD  
  `Note YYYY-MM-DD.txt` ~ `Note 2023-09-15.txt`

### E-Book & Publishing

- **Amazon Kindle Highlights Export** – .TXT  
  `My Clippings.txt`  
  > "Universal Kindle export for highlights/notes."

- **Calibre Default Export** – .EPUB, .MOBI  
  `metadata.opf`, `cover.jpg`  
  > "Generated automatically for library."

- **Scrivener Projects** – .SCRIV  
  `Untitled.scriv`  
  > "Default project container."

- **Final Draft** – Script / .FDX  
  `Untitled.fdx`  

### Email / Messaging Notes

- **Outlook Drafts** – .MSG, .EML  
  `Draft.msg`  

- **Thunderbird Saved Drafts** – .EML  
  `msg####.eml` ~ `msg0001.eml`

- **Lotus cc:Mail** – .CCM  
  `cc####.ccm` ~ `cc0001.ccm`

- **Unix Mail Export (mbox)** – .TXT  
  `mbox`  
  > "Default file for archived mail."

- **ICQ Chat History** – .HTM  
  `History####.htm` ~ `History0001.htm`

- **AOL Instant Messenger Logs** – .HTML  
  `aimlog####.html` ~ `aimlog0001.html`

- **Skype Chat Export** – .CSV  
  `messages.csv`

### Cloud & Sync Systems

- **Dropbox Paper Export** – .MD  
  `Untitled.md`

- **Google Keep Takeout** – .HTML  
  `Keep-YYYYMMDDTHHMMSS.html`

- **Apple Notes Export** – .HTML  
  `Note.html`  

- **Microsoft Sticky Notes Export (Win10)** – .SNT  
  `StickyNotes.snt`

- **Simplify3D Notes (3D printing software)** – .FFF  
  `Process1.fff`  
  > "Process profile defaults."

### Miscellaneous / Obscure

- **Neo FreeNote (Digital Note Device)** – .TXT  
  `NOTE####.TXT` ~ `NOTE0001.TXT`

- **AlphaSmart Dana / Neo (school word processors)** – .TXT  
  `fileX.txt` ~ `file1.txt`

- **Psion Organizer II** – .ODB  
  `DATA####.ODB` ~ `DATA0001.ODB`

- **Sharp Wizard PDA Notes** – .TXT  
  `NOTE####.TXT`

- **Palm OS MemoPad** – Export .TXT  
  `memo####.txt` ~ `memo0001.txt`

- **BlackBerry Notes** – .TXT  
  `Note####.txt`

- **Nintendo DS Digital Diary (Japan)** – .TXT  
  `DIARY####.TXT` ~ `DIARY0001.TXT`

## Audio & voice

### Smartphones

- **Apple iPhone (Voice Memos app)** – Audio / .M4A  
  `New Recording.m4a` ~ `New Recording 1.m4a`  
  > "Defaults to English locale; localized versions use e.g. `Neue Aufnahme` (German)."

- **Samsung Galaxy (Voice Recorder)** – Audio / .M4A  
  `Voice ####.m4a` ~ `Voice 001.m4a`  

- **Google Pixel (Recorder app)** – Audio / .M4A  
  `Recording YYYY-MM-DD HHMMSS.m4a` ~ `Recording 2023-09-04 153012.m4a`  

- **Huawei / Honor (Sound Recorder)** – Audio / .AMR, .M4A  
  `recordingYYYYMMDD_HHMMSS.amr` ~ `recording20230911_194530.amr`  

- **Xiaomi / MIUI Recorder** – Audio / .M4A  
  `rec_YYYYMMDD_HHMMSS.m4a` ~ `rec_20230521_145210.m4a`  

- **Oppo / Realme Recorder** – Audio / .M4A  
  `RECYYYYMMDDHHMMSS.m4a` ~ `REC20240314153030.m4a`  

- **Nokia Symbian (Series 60 Recorder)** – Audio / .AMR  
  `Sound####.amr` ~ `Sound0001.amr`  

- **BlackBerry Voice Notes** – Audio / .AMR  
  `VNM####.amr` ~ `VNM0001.amr`  

### Digital Voice Recorders (IC Recorders)

- **Sony ICD Series (ICD-PX, ICD-UX)** – Audio / .MP3, .WAV  
  `ICD####.mp3` ~ `ICD0001.mp3`  
  > "Sony IC recorders embed model prefix."

- **Olympus WS/DM Series** – Audio / .WMA  
  `WS####.WMA` ~ `WS0001.WMA`  

- **Olympus VN Series** – Audio / .WMA  
  `VN####.WMA` ~ `VN0001.WMA`  

- **Zoom H1 Handy Recorder** – Audio / .WAV  
  `ZOOM####.WAV` ~ `ZOOM0001.WAV`  

- **Zoom H4n / H5 / H6** – Audio / .WAV  
  `ZOOM####.WAV` ~ `ZOOM0001.WAV`  

- **Tascam DR Series** – Audio / .WAV  
  `DR####.WAV` ~ `DR0001.WAV`  

- **Roland R-05/R-09** – Audio / .WAV, .MP3  
  `ROL####.MP3` ~ `ROL0001.MP3`  

### Answering Machines / Call Recorders

- **Panasonic Digital Answering Machines** – Audio / .VMS, .WAV  
  `MSG####.VMS` ~ `MSG0001.VMS`  
  > "Stored as message files (MSG)."

- **AT&T Answering Machines (2000s)** – Audio / .WAV  
  `msg####.wav` ~ `msg0001.wav`  

- **Cisco VoIP Call Manager Voicemail** – Audio / .WAV  
  `msg####.wav` ~ `msg0001.wav`  

- **Avaya Voicemail Systems** – Audio / .WAV  
  `vm####.wav` ~ `vm0001.wav`  

- **Skype Call Recorder (3rd party)** – Audio / .MP3  
  `SkypeCallYYYYMMDD-HHMMSS.mp3` ~ `SkypeCall20230904-153012.mp3`

- **Google Voice Voicemail Export** – Audio / .MP3  
  `voicemail-YYYYMMDD-HHMMSS.mp3` ~ `voicemail-20230904-153012.mp3`

### Dictation / Office Recorders

- **Philips Digital Pocket Memo (DPM)** – Audio / .DSS  
  `DPM####.dss` ~ `DPM0001.dss`  

- **Olympus Professional Dictation (DS Series)** – Audio / .DSS, .DS2  
  `DS####.dss` ~ `DS0001.dss`  

- **Grundig Digta** – Audio / .DSS  
  `DCTA####.DSS` ~ `DCTA0001.DSS`  

- **Dragon NaturallySpeaking Recorded Notes** – Audio / .WAV  
  `NatSpeak####.wav` ~ `NatSpeak0001.wav`  

### Cars, Dashcams, Misc

- **BlackVue Dashcam (Audio Exports)** – Audio / .MP3  
  `YYYYMMDD_HHMMSS_A.mp3` ~ `20250722_110221_A.mp3`  

- **Nextbase Dash Cam (Audio Notes)** – Audio / .MP4 (muxed)  
  > "Audio integrated into looped video clips, no separate file."

- **Garmin Dash Cam** – Audio / .WAV  
  `YYYY-MM-DD-HHMMSS.wav` ~ `2023-05-20-104522.wav`  

- **Toyota / Lexus Infotainment Voice Memo** – Audio / .WAV  
  `MEMO####.WAV` ~ `MEMO0001.WAV`

### Toys, Obscure, Weird

- **Yak Bak (Tiger Electronics, 90s)** – Audio (internal) / .VCE  
  `YAK####.VCE` ~ `YAK0001.VCE`  
  > "Hacked dumps of Yak Bak memory."

- **Fisher-Price Kid-Tough Recorder** – Audio / .WAV  
  `KID####.WAV` ~ `KID0001.WAV`  

- **Sony My First Recorder (90s kids)** – Audio / .WAV  
  `REC####.WAV` ~ `REC0001.WAV`  

- **Pocket Memo Toys (various OEMs)** – Audio / .WAV  
  `FILE####.WAV` ~ `FILE0001.WAV`

- **Nintendo 3DS Sound App** – Audio / .M4A  
  `SDAT####.m4a` ~ `SDAT0001.m4a`

- **Tamagotchi Music Diary (Bandai)** – Audio / .WAV  
  `TAMA####.WAV` ~ `TAMA0001.WAV`

### Audio / Voice (obscure & specialized devices)

- **Zoom H1n** – Voice/Field Recorder / .WAV, .MP3  
  `ZOOM####.(WAV|MP3)` or `YYMMDD-HHMMSS.(WAV|MP3)` ~ `ZOOM0001.WAV` / `240718-153000.WAV`  
  > Option toggled in File Name menu.

- **Zoom F3** – Field Recorder / .WAV (32-bit float)  
  `YYMMDD_###.WAV` (mono) or `YYMMDD_###_Tr#.WAV` (per-track) ~ `240315_001_Tr1.WAV`  
  > “Rec File Name” supports date or custom prefix.

- **Zoom F8n / F8n Pro** – Multitrack / .WAV  
  `TakeName-T###.WAV` (poly) / `TakeName_T###_Tr#.WAV` (mono/stereo split) ~ `Scene001-T001.WAV`  
  > Scene/Take naming auto-increments.

- **TASCAM DR-40** – Handheld / .WAV, .MP3  
  `TASCAM_####[S12|D12].WAV` or `YYMMDD_000#.WAV` ~ `TASCAM_0001S12.WAV` / `240718_0001.WAV`  
  > S/D suffix denotes mode & channels.

- **Sound Devices MixPre (3/6/10 II)** – Field Recorder / .WAV  
  `<prefix>-###.WAV` (default prefix `MixPre`) ~ `MixPre-001.WAV`  
  > User-editable base; take increments.

- **NAGRA LINO** – Dictation/Reportage / .WAV  
  `USER####.WAV` ~ `USER0001.WAV`  
  > Factory default sequence.

- **NAGRA PICO** – Pocket Recorder / .WAV  
  `USER####.WAV` ~ `USER0001.WAV`  
  > Records in 16-bit WAV by default.

- **NTi Audio XL2** – Sound Level Meter / .WAV + logs  
  `YYYY-MM-DD_SLM_###_Audio_... .WAV` ~ `2017-03-16_SLM_000_Audio_... .WAV`  
  > Stored alongside text log files.

- **Philips DPM8000 Pocket Memo** – Dictation / .DS2, .DSS, .WAV  
  `<AuthorOrPrefix>####.(ds2|dss|wav)` ~ `Wats0001.dss` / `john0001.ds2`  
  > File name formula = Author or 1–4 char prefix + sequence.

- **Marantz PMD661** – Field/ENG / .WAV, .MP3  
  `PMD####.(WAV|MP3)` ~ `PMD0001.WAV`  
  > If “Encrypted,” extensions become `.WAS` / `.MPS`.

- **Wildlife Acoustics Song Meter SM4** – Bioacoustics / .WAV  
  `<Prefix>_YYYYMMDD_HHMMSS.wav` ~ `SITEA_20240718_153000.wav`  
  > Prefix often set to site/recorder name.

- **Wildlife Acoustics Song Meter Mini 2 / Micro 2** – Bioacoustics / .WAV  
  `<RecorderName>_YYYYMMDD_hhmmss.wav` ~ `12345678_20240601_140505.wav`  
  > Default RecorderName = serial; tz in metadata.

- **Titley Anabat SCOUT** – Bat Detector / .ZC, .WAV  
  `YYYY-MM-DD HH-MM-SS.(zc|wav)` ~ `2024-07-18 23-15-00.zc`  
  > Zero-crossing (.zc) or full-spectrum (.wav).

- **Titley Anabat Swift** – Bat Detector / .ZC, .WAV  
  `YYYY-MM-DD HH-MM-SS.(zc|wav)` ~ `2024-07-18 23-15-00.wav`  
  > Names include start date/time.

- **Ocean Instruments SoundTrap ST300/ST400/ST600** – Hydrophone Logger / .WAV, .SUD  
  `<Serial>_YYMMDDHHMMSS.wav` ~ `ST4300_240718153000.wav`  
  > Serial + start timestamp; continuous splits at size limits.

- **Loggerhead DSG** – Underwater Logger / .DSG → .WAV  
  `<SourceName>... .dsg` → converted WAV inherits base ~ `DEPLOY01_20240718_1530.wav`  
  > `DSG2WAV` derives .wav names from .dsg + optional suffix.

- **AudioMoth (Open Acoustic Devices)** – Bioacoustics / .WAV  
  `YYYYMMDD_HHMMSS.wav` (optional prefix/suffix) ~ `20200501_195401.wav`  
  > Firmware ≥1.2.2 adds timestamp; prefix/suffix configurable.

- **Edirol/Roland R-05** – Handheld / .WAV, .MP3  
  `R05_####.(WAV|MP3)` ~ `R05_0001.WAV`  
  > Sequential counter by folder.

- **Olympus DS-3400/3500** – Dictation / .DS2, .DSS, .WAV  
  `<FolderLetter><####>.ds2` or DSS per device settings ~ `A0001.ds2`  
  > Device/app can force DSS Classic; encryption may alter ext to `.$dss`.

- **iOS Voice Memos (exported on older/legacy clients)** – Phone / .M4A  
  `YYYY-MM-DDThhmmssZ.m4a` ~ `2023-08-11T07:53:31Z.m4a`  
  > With iOS 17+ metadata encryption, older devices show ISO date as title.

- **Windows 10/11 Voice Recorder** – PC / .M4A  
  `Recording (n).m4a` or `YYYYMMDD hhmmss.m4a` (per locale) ~ `Recording (2).m4a`  
  > Saves to “Sound recordings” folder.
