[Sidecar Specification](#specification) | [Generate Sidecar Files](https://github.com/superkabuki/SCTE-35_Sidecar_Files/blob/main/README.md#generating-sidecar-files) 

# SCTE-35 Sidecar Files
<img width="1054" height="587" alt="image" src="https://github.com/user-attachments/assets/b5b0d7cb-13bb-4bb4-a66a-633b89906c61" />

# SCTE-35 sidecar files are used to insert SCTE-35 in MPEGTS streams or HLS manifests.

__SCTE-35 sidecar files are not part of the specification, but they fully and completely meet the specification__.
SCTE-35 sidecar files are just something I have used for years, and they work will really really well. __threefive, sideways, x9k3, umzz, and m3ufu__ all __support sidecar files__. 
__Right now they are partially documented across several repos, so I decided to put it all here in one place.__

# Why not SCTE-104?
### Have you read the SCTE-104 specification?
I have. Shortly after reading the spec, I came up with SCTE-35 sidecar files.

___

# `Specification`
* [Sidecar Files](#sidecar-files)
* [lines](#lines)
* [insert_pts](#insert_pts)
* [cue](#cue)
* [Additional Details](#details)


---
## __Sidecar files__
```smalltalk
a@fu:~/threefive$ cat ~/sidecar.txt

58000.0,/DAlAAAAAAAAAP/wFAUAAAABf+//NyLhAP4AUmXAAAEAAAAAWia/Iw==
58060.0,/DAgAAAAAAAAAP/wDwUAAAACf0//N3VGwAACAAAAAPWDDDY=
57900.0,/DAlAAAAAAAAAP/wFAUAAAABf+//NpmMwP4Bm/zAAAEAAAAAD7tCMw==
```
  *  Are text files.
   * Can have comments prefixed by '#' and terminated by a new line '\n'
   * Can be appended at any time.
   * Are made of lines.

---

### `Sidecar files are made of Lines`.

```js
    58100.0 , /DAlAAAAAAAAAP/wFAUAAAABf+//N6w1QP4Bm/zAAAEAAAAAASHDdA==
```
---

## __Lines__
  
*  End with a new line '\n'
*  Can have comments prefixed by '#' and terminated by a new line '\n'
*  Can wrap if needed.
*  Contain 1 insert_pts and 1 cue separated by a comma.
---

### `Lines have an insert_pts and a cue`.
---
##  __insert_pts__
  * Is pts in seconds.
  * Has a range of 0 to 95443.717677.
  * Is the pts to insert the SCTE35 Cue.
  * Is absolute, no adjustment is applied.
  * If insert_pts is zero '0', the SCTE35 Cue  is inserted at the next iFrame.
---
## __cue__
   * is a SCTE35 cue.
   * can be Base64, Hex or Integer.


---

## Details
---

1) Sidecar files are used for inserting SCTE35 into MPEGTS,DASH, or HLS.
2)  __Sidecar files can be appended to live__.
3) __Sidecar files should be checked for new information at every iFrame__.
4) __When lines are read from a sidecar file, the lines should be deleted from the sidecar file.__
5) lines with an __insert_pts older than the current pts__ of the video should be __left in the sidecar file and ignored__ until they become relevant after a rollover.
6) __Sidecar files do NOT have to be in chronological order__.
7) __The cue should be inserted at the insert_pts if an iframe is present__.
8) __If an iframe is NOT present at the insert_pts__, then the cue should be inserted at the __closest iframe to the insert_pts__.
9) Setting __insert_pts to 0 indicates a "splice immediate"__ and the cue should be inserted at the __next iframe__.
10) __insert_pts should NOT contain a preroll__.
11) __insert_pts formula__ (Cue Comand pts_time  + Splice Info Section pts_adjustment) % 95443.717677

---

# `End of Specification`


# Generating sidecar files.

* You can write a sidecar file by hand or you can use one of these tools.

## threefive 
* If you want to extract the SCTE-35 from an existing MPEGTS stream into a sidecar file
```js
threefive sidecar video.ts
```
* a sidecar file named 'sidecar.txt' will be created in the same directory.
```js
a@fu:~/threefive$ cat sidecar.txt 
72820.9484,/DBDAAAAAyiYAP/wFAUAAAABf+//hqqjQv4ApMbEmZkBAQAeAhxDVUVJAAAAAH/AAACky4ABCDEwMTAwMDAwNAAAN7GZ7w==
72951.7791,/DAsAAAAAyiYAP/wCgUAAAABf1+ZmQEBABECD0NVRUkAAAAAf4ABADUAAC2XQZU=
```
* if your SCTE-35 is xml or json you can convert it and write it to a sidecar
	* _in this example,  123.45678 is your insert_pts and json.json is a file containing your SCTE-35 cue in json._
```smalltalk
printf "123.45678 %s \n" `threefive < ~/json.json base64 2>&1`  >> sidecar.txt
```
* the above will append this to the sidecar file.
```js
123.45678 /DA5AAAAAyiYAP/wCgUAAAABfx+ZmQH/AB4CHENVRUkAAAAAf4AMDURPT01TdXBlckRvb201AACPN660 
```
* How do you that shell stuff on __Windows?__
   * __I have no idea__. If anyone wants to tell me, I will add it. 


## adbreak3
* adbreak3 is a command line tool for generating SCTE-35 for sidecar files
```js
$ adbreak3 -h
usage: adbreak3 [-h] [-d DURATION] [-e EVENT_ID] [-i] [-o] [-p PTS] [-P]
                [-s SIDECAR] [-v]

options:
  -h, --help            show this help message and exit
  -d DURATION, --duration DURATION
                        Set duration of ad break. [ default: 60.0 ]
  -e EVENT_ID, --event-id EVENT_ID
                        Set event id for ad break. [ default: 1 ]
  -i, --cue-in-only     Only make a cue-in SCTE-35 cue [ default: False ]
  -o, --cue-out-only    Only make a cue-out SCTE-35 cue [ default: False ]
  -p PTS, --pts PTS     Set start pts for ad break. Not setting pts will
                        generate a Splice Immediate CUE-OUT. [ default: 0.0 ]
  -P, --preroll         Add SCTE data four seconds before splice point. Used
                        with MPEGTS. [ default: False ]
  -s SIDECAR, --sidecar SIDECAR
                        Sidecar file of SCTE-35 (pts,cue) pairs. [ default:
                        sidecar.txt ]
  -v, --version         Show version

```
* use adbreak3 like this
```js
a@fu:~/Downloads$ adbreak3 --duration 60 --pts 1234.56789 --event-id 34

Writing to sidecar file: sidecar.txt

		CUE-OUT   PTS:1234.567889   Id:34   Duration: 60.0
		CUE-IN    PTS:1294.567889   Id:35
```
* by default adbreak3 writes to sidecar.txt.
* a different file can be specified with the -s switch.
* if the file already exists, adbreak3 will append to it.

```js

a@fu:~/Downloads$ cat sidecar.txt
1234.56789,/DAlAAAAAAAAAP/wFAUAAAAif+/+Bp9rxv4AUmXAACIAAAAAjjSYpQ==
1294.56789,/DAgAAAAAAAAAP/wDwUAAAAjf0/+BvHRhgAjAAAAAE55tjQ=

```
