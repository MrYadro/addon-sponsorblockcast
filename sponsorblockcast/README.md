# addon-sponsorblockcast
A POSIX shell script that skips sponsored Youtube content on all local Chromecasts, using the [SponsorBlock](https://github.com/ajayyy/SponsorBlock) API. It was inspired by [CastBlock](https://github.com/stephen304/castblock) but written from scratch to avoid some of its pitfalls (see [Differences from CastBlock](#differences-from-castblock))

Care was taken to ensure it's fully POSIX-compatible, so it can run on lighter shells such as [Dash](https://wiki.archlinux.org/index.php/Dash).

The script will scan for all Chromecasts on the LAN, and launches a process for each one to efficiently poll it status every second. If a Chromecast is found to be playing a YouTube video, sponsor segments are fetched from the SponsorBlock API and stored in a temporary file. Whenever the Chromecast reaches a sponsored segment, the script tells it to seek to the end of the segment.

## Installation
Add `https://github.com/MrYadro/addon-sponsorblockcast` as addon repository

## Configuration
```
env_vars:
  - name: SBCYOUTUBEAPIKEY
    value: YOUR_API_KEY
  - name: SBCDEBUG
    value: 'true'
```
You can configure the following parameters by setting the appropriate environment values via addon config:
* `SBCPOLLINTERVAL` - Time to wait between each polling of the Chromecasts' status (default=`1`)
* `SBCSCANINTERVAL` - Time to wait between each scan for available Chromecast (default=`300`)
* `SBCDIR` - Directory where temporary files are stored (default=`/tmp/sponsorblockcast`)
* `SBCCATEGORIES` - Space-separated SponsorBlock categories to skip, see [category list](https://github.com/ajayyy/SponsorBlock/blob/master/config.json.example) (default=`sponsor`)
* `SBCYOUTUBEAPIKEY` - [YouTube API key](https://developers.google.com/youtube/registering_an_application) for fallback video identification (required on some Chromecast devices).

## Differences from CastBlock
* Regular scans to find new Chromecasts while the script is running
* Allows configuring parameters
* Specify which SponsorBlock categories to skip
* More efficient polling, through using `go-chromecast`'s `watch` command, avoiding expensive startup costs. This lets us poll much more often, without any large performance costs.
* Full POSIX-compatibility