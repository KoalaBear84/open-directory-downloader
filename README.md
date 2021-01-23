# A NodeJS Wrapper around Open Directory Downloader

This is a wrapper around KoalaBear84/OpenDirectoryDownloader, exposing functionality and output of the CLI in JavaScript.

Open Directory Downloader is a CLI tool for indexing so-called *Open Directories*, which are HTTP servers exposing files and folders for browsing and downloading. For more info visit [the subreddit](https://reddit.com/r/opendirectories)

## Compatible Versions of OpenDirectoryDownloader

| Wrapper Version | Supported OpenDirectoryDownloader Versions | Included Version |
| --- | --- | --- |
| 1.0.0 | >1.9.3.1 | 1.9.3.3 |

## Installation

```shell
npm i open-directory-downloader
```

Upon installation, the latest supported OpenDirectoryDownloader binary will automatically be downloaded from GitHub.  
If this should fail, a local installation can be used instead (see below).

## Class OpenDirectoryDownloader

### OpenDirectoryDownloader([executablePath, outputDirectory])

- `executablePath` (`String`) (optional): The full path to the OpenDirectoryDownloader executable.  
  Allows you to use your own installation of OpenDirectoryDownloader.
- `outputDirectory` (`String`) (optional): The full path to the directory where OpenDirectoryDownloader save its scan files.  
  Required if `executablePath` is set.
- Returns: An instance of `OpenDirectoryDownloader`

*Constructor*

### OpenDirectoryDownloader.scanUrl(url[, keepJson = false])  

- `url` (`String`) (required): The URL to scan/index.
- `keepJson` (`Boolean`) (optional): If set to `true`, the JSON-file generated by the OpenDirectoryDownloader binary won't be deleted after the scan.
- Returns: Promise<[ScanResult](#scanresult)>

*Initiates the scan of the provided URL*

#### ScanResult

- `scannedUrl` The URL that was scanned
- `scan` The parsed JSON-Object created by the OpenDirectoryDownloader binary.  
  Can be very large depending on the size of the Open Directory.
- `scanFile` The full path to the created JSON-file.  
  Only available if `keepJson` was set to `true`.
- `reddit` The markdown-formatted table containing the stats for the Open Directory.  
  Does not include the credits.
- `credits` The markdown signature containing a link to KoalaBear84/OpenDirectoryDownloader

## Examples

### Index a URL

```js
const OpenDirectoryDownloader = require(`open-directory-downloader`);

const odd = new OpenDirectoryDownloader();

odd.scanUrl(url, true)
.then(scanResult => {
  console.log(scanResult)
  // {
  //   scannedUrl: `https://example.com/files`,
  //   scan: {...},
  //   scanFile: `/path/to/scan/file`,
  //   reddit: `
  //     |**Url:** https://example.com/files/||[Urls file](https://gofile.io/?c=XXXXX)|
  //     |:-|-:|-:|
  //     |**Extension (Top 5)**|**Files**|**Size**|
  //     |.mp3|14,779|294.68 GiB|
  //     |.flac|1,449|52.59 GiB|
  //     |.mkv|7|33.3 GiB|
  //     |.zip|7|5.61 GiB|
  //     |.mp4|8|4.85 GiB|
  //     |**Dirs:** 3,623 **Ext:** 119|**Total:** 30,357|**Total:** 415.97 GiB|
  //     |**Date (UTC):** 2021-01-16 15:28:53|**Time:** 00:01:13|**Speed:** 1.4 MB/s (12 mbit)|
  //   `,
  //   credits: `
  //     ^(Created by [KoalaBear84's OpenDirectory Indexer](https://github.com/KoalaBear84/OpenDirectoryDownloader/))
  //   `,
  // }
})
.catch(console.error)

```
