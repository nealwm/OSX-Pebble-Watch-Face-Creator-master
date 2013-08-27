# Pebble Notes

Most of this is probable already documented somewhere but I always like to have a look at how it works first before trying to dig though the various forums and online documentation.

One of the primary reasons I got a Pebble is so I could write some of my own apps that could display on the device. At this point there is no SDK available. I decided to try and figure out how the Pebble works. Here is what I have seen thus far.

Related Open Source Projects:
* [pbw-tool] [1] - Pebble Resource Scripts (Watchface)
* [pebble-tools] [2] - Binary header parser
* [libpebble] [3] - Serial Comms Lib
* https://github.com/dattas/pebble-notifier

Based on all this information creating a new watch face may be very easy. I am not quite sure of the format of this file *pebble-app.bin*.

It maybe easier to decode the firmware file first and that should provide the necessary details to create new watch faces or even possible other apps..


## Watch Faces

With the [pbw-tool] [1] you can download all watch faces and unpack the pbw files. The PBW files are simply ZIP formatted files which contains the "app directory" with the following contents:
* manifest.json - JSON file that describes applications meta data
* pebble-app.bin - the watch binary format. Not quite sure of the full format
* app_resources.pbpack - Resource files like images

The next sections assume you have already download the [pbw-tool] [1].

### Download All Watch Faces

This is an example of running *download_watchfaces.py*.


```SHELL
[faces]$ python ../../pbw-tools/download_watchfaces.py
[INFO    ] Downloading watchfaces linked from http://pebble-static.s3.amazonaws.com/watchfaces/index.html
[INFO    ] Downloading http://pebble-static.s3.amazonaws.com/watchfaces/apps/simplicity.pbw -> simplicity.pbw
[INFO    ] Downloading http://pebble-static.s3.amazonaws.com/watchfaces/apps/times_square.pbw -> times_square.pbw
[INFO    ] Downloading http://pebble-static.s3.amazonaws.com/watchfaces/apps/brains.pbw -> brains.pbw
[INFO    ] Downloading http://pebble-static.s3.amazonaws.com/watchfaces/apps/segment_six.pbw -> segment_six.pbw
[INFO    ] Downloading http://pebble-static.s3.amazonaws.com/watchfaces/apps/just_a_little_bit.pbw -> just_a_little_bit.pbw
[INFO    ] Downloading http://pebble-static.s3.amazonaws.com/watchfaces/apps/big-time-12.pbw -> big-time-12.pbw
[INFO    ] Downloading http://pebble-static.s3.amazonaws.com/watchfaces/apps/big-time-24.pbw -> big-time-24.pbw
[INFO    ] Downloading http://pebble-static.s3.amazonaws.com/watchfaces/apps/tic_tock_toe.pbw -> tic_tock_toe.pbw
[faces]$ ls
big-time-12.pbw		big-time-24.pbw		brains.pbw		just_a_little_bit.pbw	segment_six.pbw		simplicity.pbw		tic_tock_toe.pbw	times_square.pbw

```
### Unpack a watch face

Either unzip a .pbw file or use the script *extract_pbw.py* to convert the files to their individual files. 

Here is an example using the script:

```SHELL
[watchFaces] python ../../../pbw-tools/extract_pbw.py ../brains.pbw 
[INFO    ] Opening ../brains.pbw
[INFO    ] Unpacking to brains
[watchFaces]$ cd brains
[brains]$ ls
app_resources.pbpack	manifest.json		pebble-app.bin

```

### Unpack the watch face resource files

You can unpack the resource files with *unpack_pbpack.py*. Here is a sample output.

```SHELL
brains]$ python ../../../../pbw-tools/unpack_pbpack.py 
Processing "app_resources.pbpack"
<PBPack ACC2177A "dev_0.0" [7]>
Generating IMAGE_MENU_ICON.png
<Resource 1 @ 00000000:0000007c CRC:caca1926 <BMP 24x28 (4 scanlines) size 112>>
Generating IMAGE_BACKGROUND.png
<Resource 2 @ 0000007c:00000da8 CRC:f1dcb294 <BMP 144x168 (20 scanlines) size 3360>>
Generating IMAGE_HOUR_HAND.png
<Resource 3 @ 00000da8:00000e70 CRC:56900cc4 <BMP 8x47 (4 scanlines) size 188>>
Generating IMAGE_MINUTE_HAND.png
<Resource 4 @ 00000e70:00000f90 CRC:60316f57 <BMP 8x69 (4 scanlines) size 276>>
Generating IMAGE_SECOND_HAND.png
<Resource 5 @ 00000f90:000010b0 CRC:361723a9 <BMP 8x69 (4 scanlines) size 276>>
Generating IMAGE_CENTER_CIRCLE.png-trans
<Resource 6 @ 000010b0:000010fc CRC:750fe68d <BMP 16x16 (4 scanlines) size 64>>
Generating UNKNOWN-6.resource
<Resource 7 @ 000010fc:00001148 CRC:653770ac <BMP 16x16 (4 scanlines) size 64>>
[brains]$ ls
IMAGE_BACKGROUND.png		IMAGE_HOUR_HAND.png		IMAGE_MINUTE_HAND.png		UNKNOWN-6.resource		manifest.json
IMAGE_CENTER_CIRCLE.png-trans	IMAGE_MENU_ICON.png		IMAGE_SECOND_HAND.png		app_resources.pbpack		pebble-app.bin

```

### Validate the resources

```SHELL
[brains]$ python ../../../../pbw-tools/validate_pbpack.py
Processing "app_resources.pbpack"
CACA1926 | CACA1926
<Resource  1 @ 0000:007c CRC:caca1926 <BMP 24x28  4B/scanline size 112>>
F1DCB294 | F1DCB294
<Resource  2 @ 007c:0da8 CRC:f1dcb294 <BMP 144x168 20B/scanline size 3360>>
56900CC4 | 56900CC4
<Resource  3 @ 0da8:0e70 CRC:56900cc4 <BMP 8x47  4B/scanline size 188>>
60316F57 | 60316F57
<Resource  4 @ 0e70:0f90 CRC:60316f57 <BMP 8x69  4B/scanline size 276>>
361723A9 | 361723A9
<Resource  5 @ 0f90:10b0 CRC:361723a9 <BMP 8x69  4B/scanline size 276>>
750FE68D | 750FE68D
<Resource  6 @ 10b0:10fc CRC:750fe68d <BMP 16x16  4B/scanline size 64>>
653770AC | 653770AC
<Resource  7 @ 10fc:1148 CRC:653770ac <BMP 16x16  4B/scanline size 64>>
<PBPack ACC2177A "dev_0.0" [7]>

```

### View an image from the resource files

You can view one of the extracted images by running *view_pebble_image.py*.

```SHELL
[brains]$ python ../../../../pbw-tools/view_pebble_image.py IMAGE_MINUTE_HAND.png
<BMP 8x69 (4 scanlines) size 276>
0
```

Note you will see an GUI popup that contains the image.


### Invert the image file

I am not quite sure what the value of this is but you can invert the image with *invert_pebble_image.py*.

```SHELL
[brains]$ cp IMAGE_MINUTE_HAND.png IMAGE_MINUTE_HAND_INVERTED.png 
[brains]$ python ../../../../pbw-tools/invert_pebble_image.py IMAGE_MINUTE_HAND_INVERTED.png 

```

### pebble-app.bin

When running [pebble-tool] [2] against *pebble-app.bin* the following is output:

```SHELL
[brains]$ ~/working/pebbleInfo/pebble-tools/appinfo pebble-app.bin 
magic: PBLAPP
Version: 8
Sdk Version: 1
App Version: 1
size: 3172 (0xc64)
Entry Point: 1488 (0x5d0)
crc: 3670398314
name: Brains Watch
company: Pebble Technology
unknown3: 1
jump_table address: 2300 (0x8fc)
flags: 1
relocation list address: 3172 (0xc64)
number of relocations: 6

```


This is the format of the pebble-app.bin header

```C
  char magic[8]; // PBLAPP\0\0
  unsigned short version;
  unsigned short sdk_version;
  unsigned short app_version;
  unsigned short size;
  unsigned int entry_point;
  unsigned int crc;
  char name[32];
  char company[32];
  unsigned int unknown3; // Always seems to be one?
  unsigned int jump_table; // offset in file
  unsigned int flags;
  unsigned int reloc_list; // offset in file
  unsigned int num_relocs;
```

When I ran this against the firmware bin it did not work. So that files header is a different format.


## Firmware Updates

Pebble Binary Format information: http://pebbledev.org/wiki/Firmware_Updates

Presumable the same process used above can be used to extract/view the resources of the watch.

iPhone app launches and makes this request:

### Latest Version Request
https://pebblefw.s3.amazonaws.com/pebble/ev2_4/release/latest.json

```
GET /pebble/ev2_4/release/latest.json HTTP/1.1
Host: pebblefw.s3.amazonaws.com
Proxy-Connection: keep-alive
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en-us
Connection: keep-alive
User-Agent: PebbleApp/1.0.4 CFNetwork/609.1.4 Darwin/13.0.0
Pragma: no-cache
Cache-Control: no-cache


```

### Latest Version Response
```JSON
{
    "recovery": {
        "url": "https://pebblefw.s3.amazonaws.com/pebble/ev2_4/release/pbz/recovery_ev2_4_v1.5.2.pbz",
        "timestamp": 1356919500,
        "notes": "Official Recovery Firmware",
        "friendlyVersion": "v1.5.2",
        "sha-256": "1ca285d65d80b48b90bab85c5f9e54c907414adffa6f1168beec8aac5d6f32a2"
    },
    "normal": {
        "url": "https://pebblefw.s3.amazonaws.com/pebble/ev2_4/release/pbz/normal_ev2_4_v1.8.1.pbz",
        "timestamp": 1360275561,
        "notes": "Improved backlight control; Notification popups now time out after 3 minutes; Bug fixes",
        "friendlyVersion": "v1.8.1",
        "sha-256": "f3908e60f72122cc97a0ee8eca88605e071211c74bbee08bf3a08e8d54480327"
    }
}
```


## PBZ Info

### File Format

The PBZ file is a zipped file which contains the following:
- manifest.json
- system_resources.pbpack
- tintin_fw.bin

#### manifest.json

```JSON
{
  "manifestVersion": 1,
  "firmware": {
    "name": "tintin_fw.bin",
    "timestamp": 1360275561,
    "crc": 766002693,
    "hwrev": "ev2_4",
    "type": "normal",
    "size": 444643
  },
  "generatedBy": "zulak-air",
  "generatedAt": 1360609232,
  "debug": {
    "resourceMap": {
      "media": [
        {
          "defName": "PUG",
          "type": "png",
          "file": "images\/pug.png"
        },
        {
          "defName": "DIALOG_HELLO",
          "type": "png",
          "file": "images\/hello.png"
        },
        {
          "defName": "MENU_ICON_EYE",
          "type": "png",
          "file": "images\/menu_icon_eye.png"
        },
        {
          "defName": "MENU_ICON_TEXT_WATCH",
          "type": "png",
          "file": "images\/menu_icon_text_watch.png"
        },
        {
          "defName": "MENU_ICON_CLASSIC_WATCH",
          "type": "png",
          "file": "images\/menu_icon_classic_watch.png"
        },
        {
          "defName": "MENU_ICON_FUZZY_WATCH",
          "type": "png",
          "file": "images\/menu_icon_fuzzy_watch.png"
        },
        {
          "defName": "MENU_ICON_TRASH",
          "type": "png",
          "file": "images\/menu_icon_trash.png"
        },
        {
          "defName": "SETTINGS_ICON_RESET",
          "type": "png",
          "file": "images\/settings_icon_reset.png"
        },
        {
          "defName": "SETTINGS_ICON_BLUETOOTH_ALT",
          "type": "png",
          "file": "images\/settings_icon_bluetooth_alt.png"
        },
        {
          "defName": "SETTINGS_ICON_BLUETOOTH",
          "type": "png",
          "file": "images\/settings_icon_bluetooth.png"
        },
        {
          "defName": "SETTINGS_ICON_AIRPLANE",
          "type": "png",
          "file": "images\/settings_icon_airplane.png"
        },
        {
          "defName": "SETTINGS_ICON_ABOUT",
          "type": "png",
          "file": "images\/settings_icon_about.png"
        },
        {
          "defName": "LAUNCHER_ICON_PACKAGE",
          "type": "png",
          "file": "images\/launcher_icon_package.png"
        },
        {
          "defName": "LAUNCHER_ICON_CLOCK",
          "type": "png",
          "file": "images\/launcher_icon_clock.png"
        },
        {
          "defName": "LAUNCHER_ICON_ALARM",
          "type": "png",
          "file": "images\/launcher_icon_alarm.png"
        },
        {
          "defName": "LAUNCHER_ICON_SHUTDOWN",
          "type": "png",
          "file": "images\/launcher_icon_shutdown.png"
        },
        {
          "defName": "LAUNCHER_ICON_RUNKEEPER",
          "type": "png",
          "file": "images\/launcher_icon_runkeeper.png"
        },
        {
          "defName": "LAUNCHER_ICON_SETTINGS",
          "type": "png",
          "file": "images\/launcher_icon_settings.png"
        },
        {
          "defName": "LAUNCHER_ICON_MUSIC",
          "type": "png",
          "file": "images\/launcher_icon_music.png"
        },
        {
          "defName": "GENERIC_ICON",
          "type": "png",
          "file": "images\/generic_icon.png"
        },
        {
          "defName": "FIRST_BOOT",
          "type": "png",
          "file": "images\/first_boot.png"
        },
        {
          "defName": "SIMPLE_MINUTE",
          "type": "png-trans",
          "file": "images\/simple_minute.png"
        },
        {
          "defName": "SIMPLE_HOUR",
          "type": "png-trans",
          "file": "images\/simple_hour.png"
        },
        {
          "defName": "CHECKMARK",
          "type": "png",
          "file": "images\/checkmark.png"
        },
        {
          "defName": "SIMPLE_BG",
          "type": "png",
          "file": "images\/simple_bg.png"
        },
        {
          "defName": "FONT_FALLBACK",
          "type": "font",
          "file": "ttf\/RasterGothic14Cond.ttf"
        },
        {
          "defName": "GOTHIC_14",
          "type": "font",
          "file": "ttf\/RasterGothic14Cond.ttf"
        },
        {
          "defName": "GOTHIC_14_BOLD",
          "type": "font",
          "file": "ttf\/RasterGothic14CondBold.ttf"
        },
        {
          "defName": "GOTHIC_18",
          "type": "font",
          "file": "ttf\/RasterGothic18Cond.ttf"
        },
        {
          "defName": "GOTHIC_18_BOLD",
          "type": "font",
          "file": "ttf\/RasterGothic18CondBold.ttf"
        },
        {
          "defName": "GOTHIC_24",
          "type": "font",
          "file": "ttf\/RasterGothic24Cond.ttf"
        },
        {
          "defName": "GOTHIC_24_BOLD",
          "type": "font",
          "file": "ttf\/RasterGothic24CondBold.ttf"
        },
        {
          "defName": "GOTHIC_28",
          "type": "font",
          "file": "ttf\/RasterGothic28Cond.ttf"
        },
        {
          "defName": "GOTHIC_28_BOLD",
          "type": "font",
          "file": "ttf\/RasterGothic28CondBold.ttf"
        },
        {
          "defName": "GOTHAM_30_BLACK",
          "type": "font",
          "file": "ttf\/GothamBlack30.ttf"
        },
        {
          "trackingAdjust": -2,
          "defName": "GOTHAM_42_BOLD",
          "type": "font",
          "file": "ttf\/Gotham-Bold.otf"
        },
        {
          "trackingAdjust": -3,
          "defName": "GOTHAM_42_LIGHT",
          "type": "font",
          "file": "ttf\/Gotham-Light.otf"
        },
        {
          "trackingAdjust": -3,
          "characterRegex": "[0-9:-]",
          "defName": "GOTHAM_42_MEDIUM_NUMBERS",
          "type": "font",
          "file": "ttf\/Gotham-Medium.otf"
        },
        {
          "trackingAdjust": -2,
          "characterRegex": "[0-9:\\.-]",
          "defName": "GOTHAM_34_MEDIUM_NUMBERS",
          "type": "font",
          "file": "ttf\/Gotham-Medium.otf"
        },
        {
          "trackingAdjust": -2,
          "characterRegex": "[mikm-]",
          "defName": "GOTHAM_34_LIGHT_SUBSET",
          "type": "font",
          "file": "ttf\/Gotham-Light.otf"
        },
        {
          "trackingAdjust": -1,
          "characterRegex": "[minkm\/-]",
          "defName": "GOTHAM_18_LIGHT_SUBSET",
          "type": "font",
          "file": "ttf\/Gotham-Light.otf"
        },
        {
          "defName": "DROID_SERIF_28_BOLD",
          "type": "font",
          "file": "ttf\/DroidSerif28Bold.ttf"
        }
      ],
      "friendlyVersion": "v1.8-HM",
      "versionDefName": "SYSTEM_RESOURCE_VERSION"
    }
  },
  "type": "firmware",
  "resources": {
    "timestamp": 1360275561,
    "crc": 264127307,
    "friendlyVersion": "v1.8-HM",
    "name": "system_resources.pbpack",
    "size": 144662
  }
}
```


## Watch Face Download

I assume the PBW files are generated with pbw-tools.

HTML Catalog for Watch Faces
* http://pebble-static.s3.amazonaws.com/watchfaces/index.html


### Watch Face Request

```BASH
curl -O http://pebble-static.s3.amazonaws.com/watchfaces/apps/brains.pbw
```

```
GET /watchfaces/apps/brains.pbw HTTP/1.1
Host: pebble-static.s3.amazonaws.com
Referer: http://pebble-static.s3.amazonaws.com/watchfaces/index.html
Accept-Encoding: gzip, deflate
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us
Connection: keep-alive
User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 6_1_2 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Mobile/10B146
Pragma: no-cache
Cache-Control: no-cache
```

### Watch Face Response



#### manifest.json

```JSON
{
  "manifestVersion": 1,
  "generatedBy": "Tiny.local",
  "generatedAt": 1358384585,
  "application": {
    "reqFwVer": 1,
    "timestamp": 1358384584,
    "crc": 3860292302,
    "name": "pebble-app.bin",
    "size": 3196
  },
  "debug": {
    "resourceMap": {
      "media": [
        {
          "defName": "IMAGE_MENU_ICON",
          "type": "png",
          "file": "images\/menu_icon_herr_rams_watch.png"
        },
        {
          "defName": "IMAGE_BACKGROUND",
          "type": "png",
          "file": "images\/brains_watch_background.png"
        },
        {
          "defName": "IMAGE_HOUR_HAND",
          "type": "png",
          "file": "images\/brains_watch_hour_hand.png"
        },
        {
          "defName": "IMAGE_MINUTE_HAND",
          "type": "png",
          "file": "images\/brains_watch_minute_hand.png"
        },
        {
          "defName": "IMAGE_SECOND_HAND",
          "type": "png",
          "file": "images\/brains_watch_second_hand.png"
        },
        {
          "defName": "IMAGE_CENTER_CIRCLE",
          "type": "png-trans",
          "file": "images\/brains_watch_center.png"
        }
      ],
      "friendlyVersion": "dev_0.0",
      "versionDefName": "APP_RESOURCES"
    }
  },
  "type": "application",
  "resources": {
    "timestamp": 1358384584,
    "crc": 1532877025,
    "friendlyVersion": "dev_0.0",
    "name": "app_resources.pbpack",
    "size": 8548
  }
}

```

## Other Things Relating to Pebble
* http://pebbledev.org/ - A very good starting point
* http://hackingpebble.tumblr.com/
* https://github.com/itszero/PebbleNotifier
* https://github.com/begizi/myPebble
* https://github.com/pebble/pebble-3d
* https://github.com/EnJens/NotifyMyPebble
* https://github.com/ajwootto/pStocks
* https://github.com/nmost/trend-WATCHer




[1]: https://github.com/aleksandyr/pbw-tools.git        "pbw-tool"
[2]: https://github.com/PebbleDev/pebble-tools.git      "pebble-tools"
[3]: https://github.com/Hexxeh/libpebble                "libpebble"

