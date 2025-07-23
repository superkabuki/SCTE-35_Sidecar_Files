# SCTE-35 Sidecar Files
<img width="1054" height="587" alt="image" src="https://github.com/user-attachments/assets/b5b0d7cb-13bb-4bb4-a66a-633b89906c61" />

# SCTE-35 sidecar files are used to insert SCTE-35 in MPEGTS streams or HLS manifests.

__SCTE-35 sidecar files are not part of the specification, but they fully and completely meet the specification__.
SCTE-35 sidecar files are just something I have used for years, and they work will really really well. __threefive, sideways, x9k3, umzz, and m3ufu__ all __support sidecar files__. 
__Right now they are partially documented across several repos, so I decided to put it all here in one place.__

# Why a sidecar file and not SCTE-104?
### Have you read the SCTE-104 specification?
I have. Shortly after reading the spec, I came up with SCTE-35 sidecar files.

___

# Details

### `Sidecar files are made of lines.`
### `Lines have an insert_pts, and a cue`.


```smalltalk
a@fu:~/threefive$ cat ~/sidecar.txt 
58000.0,/DAlAAAAAAAAAP/wFAUAAAABf+//NyLhAP4AUmXAAAEAAAAAWia/Iw==
58060.0,/DAgAAAAAAAAAP/wDwUAAAACf0//N3VGwAACAAAAAPWDDDY=
57900.0,/DAlAAAAAAAAAP/wFAUAAAABf+//NpmMwP4Bm/zAAAEAAAAAD7tCMw==
58200.0,/DAgAAAAAAAAAP/wDwUAAAACf0//ODWJgAACAAAAAAzmjy0=
58100.0,/DAlAAAAAAAAAP/wFAUAAAABf+//N6w1QP4Bm/zAAAEAAAAAASHDdA==
58400.0,/DAgAAAAAAAAAP/wDwUAAAACf0//OUgyAAACAAAAAOK4vJc=
58300.0,/DAlAAAAAAAAAP/wFAUAAAABf+//OL7dwP4Bm/zAAAEAAAAAvdWUYg
```

* __sidecar files__
   * ascii text files
   * Sidecar files are read on startup.
   * __Order doesn't matter__, the data is sorted by insert_pts every time the sidecar file is checked.
   * To handle live updates, sidecar files are __checked at every iframe__.
   * Sidecar files are usually blanked after the data is read, so that we don't keep reading the same data over and over.
---
### `Sidecar Files have lines`

```js
    58100.0 , /DAlAAAAAAAAAP/wFAUAAAABf+//N6w1QP4Bm/zAAAEAAAAAASHDdA==
```
* __lines__

   *  format  is one  __insert_pts__  , __cue__ pair per line.
   *  __insert_pts__ and __cue__ are separated by a comma.
   *  surrounding white space doesn't matter,
   *  lines end on a new line character '\n'.
---

### `Lines have an insert_pts and a cue`.

```js
95443.717678
```
* __insert_pts__

   * __insert_pts__ is MPEGTS __pts in seconds__.
   * a float accurate to 6 places
   * range  __0 - 95443.717678__.
   * Setting to 0 inserts the cue at the __next iframe__.
---

```js
  /DAlAAAAAAAAAP/wFAUAAAABf+//N6w1QP4Bm/zAAAEAAAAAASHDdA==
```
* __cue__

   * standard SCTE-35. 
   * formats
     * base64
     * bytes
     * hex
     * integer
---





# Why isn't the insert_pts the same as the SCTE-35 pts_time / spice point?

* __With HLS, it is the same__, but with MPEGTS, SCTE-35 is usually inserted 4-10 seconds before the SCTE-35 splice point.

