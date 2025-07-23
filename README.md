# SCTE-35_Sidecar_Files
<img width="1054" height="587" alt="image" src="https://github.com/user-attachments/assets/b5b0d7cb-13bb-4bb4-a66a-633b89906c61" />

# SCTE-35 sidecar files are used to insert SCTE-35 in MPEGTS streams or HLS manifests.

__SCTE-35 sidecar files are not part of the specification, but they fully and completely meet the specification__.
SCTE-35 sidecar files are just something I have used for years, and they work will really really well. __threefive, sideways, x9k3, umzz, and m3ufu__ all __support sidecar files__. 
__Right now they are partially documented across several repos, so I decided to put it all here in one place.__

# Why a sidecar file and not SCTE-104?
### Have you read the SCTE-104 specification?
__I have__, and shortly after reading the spec, I came up with SCTE-35 sidecar files.


# Format


A __sidecar file__ has a simple format __insert_pts__  , __cue__

Example:

```smalltalk
a@fu:~/threefive$ cat ~/sidecar.txt 
58000.0,/DAlAAAAAAAAAP/wFAUAAAABf+//NyLhAP4AUmXAAAEAAAAAWia/Iw==
58060.0,/DAgAAAAAAAAAP/wDwUAAAACf0//N3VGwAACAAAAAPWDDDY=
57900.0,/DAlAAAAAAAAAP/wFAUAAAABf+//NpmMwP4Bm/zAAAEAAAAAD7tCMw==
58200.0,/DAgAAAAAAAAAP/wDwUAAAACf0//ODWJgAACAAAAAAzmjy0=
58100.0,/DAlAAAAAAAAAP/wFAUAAAABf+//N6w1QP4Bm/zAAAEAAAAAASHDdA==
58400.0,/DAgAAAAAAAAAP/wDwUAAAACf0//OUgyAAACAAAAAOK4vJc=
58300.0,/DAlAAAAAAAAAP/wFAUAAAABf+//OL7dwP4Bm/zAAAEAAAAAvdWUYg==
58600.0,/DAgAAAAAAAAAP/wDwUAAAACf0//OlragAACAAAAAP6BgyQ=
58700.0,/DAlAAAAAAAAAP/wFAUAAAABf+//OuQuwP4Bm/zAAAEAAAAA8gWewg==
59000.0,/DAgAAAAAAAAAP/wDwUAAAACf0//PIArgAACAAAAAEuzXPE=
```

* I use base64 most of the time, but the format can be base64, bytes, hex, or int.
* I usually name the file __sidecar.txt__, but it can be named anything.

# Why isn't the insert_pts the same as the SCTE-35 pts_time / spice point?

* __With HLS, it is the same__, but with MPEGTS, SCTE-35 is usually inserted 4-10 seconds before the SCTE-35 splice point.

# How do they work? 
* Sidecar files are ascii text files
* the format  is one  __insert_pts__  , __cue__ pair per line.
*  __insert_pts__ and __cue__ are separated by a comma, surrounding white space doesn't matter,
*  Lines end on a new line character '\n'.
* __insert_pts__ is MPEGTS __pts in seconds__. A float accurate to 6 places, in the range  __0-95443.717678__.
* __cue__ is standard SCTE-35 in  base64, bytes,hex,or integer.
* Sidecar files are read on startup.
* __Order doesn't matter__, the data is sorted by insert_pts every time the sidecar file is checked.
* To handle live updates, sidecar files are __checked at every iframe__.
* Setting __insert_pts to 0__ inserts the SCTE-35 cue at the __next iframe__.
* Lines with a insert_pts that has passed are ignored. 
* Sidecar files are usually blanked after the data is read, so that we don't keep reading the same data over and over.


