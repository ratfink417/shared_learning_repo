# The YouTube Downloader Script
`youtube-dl` _the YouTube-Downloader_ is most likely already in your packages for download on your linux machine but if not, you can get it from [their github page](https://github.com/ytdl-org/youtube-dl)

It's a python script that lets you download videos, ad-free and algorithm free from YouTube and some other video sites. It lets you grab thumbnails for the video if you want, select _only audio_ and select from a range of video quality options. It's also very scriptable and comes with a slew of command flags for whatever else you might need to do like using a proxy or bypassing geo-restrictions or bulk downloading entire channels.

There are lots of command flags for just pulling metadata from video pages so the main idea is to alias commands or script things that pull this metadata, grep/sed/awk/cut it to your hearts desire and you're pretty much off to the races from there.

downloading a video with this thing just looks like this
```
youtube-dl "VIDEO_URL"
```
the tool will begin downloading the video at the highest quality unless you specify otherwise with the **-f** option.

After grabbing it, just open it from your favorite video player. mpv is a good option if you want to stay on the command line. 

I've found the best practice to be keeping your videos in some temp folder and having a cron job that empties it out once in a while.

# Recipes
These are just some of the things I've been doing with the tool to keep myself off of the main YouTube site when I don't need to be on it in a browser.

Most of these will be pretty long commands so, you'll want to come up with some comfy aliasing to get them done on your system if you plan on using them regularly.
## Selecting Video Quality
To select video or audio quality, use the **-f** flag which requires a _format code_ to follow it. The format code is just a number identifying one of the quality options offered for the video.

First, get a list of the options with this command.
```
youtube-dl "URL" -F
```
you'll see something like this.
```output
[youtube] YE7VzlLtp-4: Downloading webpage
[info] Available formats for YE7VzlLtp-4:
format code  extension  resolution note
249          webm       audio only tiny   50k , webm_dash container, opus @ 50k (48000Hz), 3.60MiB
250          webm       audio only tiny   66k , webm_dash container, opus @ 66k (48000Hz), 4.74MiB
140          m4a        audio only tiny  127k , m4a_dash container, mp4a.40.2@127k (44100Hz), 9.04MiB
251          webm       audio only tiny  130k , webm_dash container, opus @130k (48000Hz), 9.29MiB
256          m4a        audio only tiny  193k , m4a_dash container, mp4a.40.5@193k (24000Hz), 13.78MiB
258          m4a        audio only tiny  385k , m4a_dash container, mp4a.40.2@385k (48000Hz), 27.40MiB
160          mp4        256x144    144p   61k , mp4_dash container, avc1.4d400c@  61k, 24fps, video only, 4.36MiB
278          webm       256x144    144p   93k , webm_dash container, vp9@  93k, 24fps, video only, 6.64MiB
242          webm       426x240    240p  142k , webm_dash container, vp9@ 142k, 24fps, video only, 10.12MiB
133          mp4        426x240    240p  144k , mp4_dash container, avc1.4d4015@ 144k, 24fps, video only, 10.27MiB
243          webm       640x360    360p  292k , webm_dash container, vp9@ 292k, 24fps, video only, 20.77MiB
134          mp4        640x360    360p  301k , mp4_dash container, avc1.4d401e@ 301k, 24fps, video only, 21.45MiB
244          webm       854x480    480p  479k , webm_dash container, vp9@ 479k, 24fps, video only, 34.13MiB
135          mp4        854x480    480p  498k , mp4_dash container, avc1.4d401e@ 498k, 24fps, video only, 35.47MiB
136          mp4        1280x720   720p  808k , mp4_dash container, avc1.4d401f@ 808k, 24fps, video only, 57.51MiB
247          webm       1280x720   720p  842k , webm_dash container, vp9@ 842k, 24fps, video only, 59.91MiB
18           mp4        640x360    360p  551k , avc1.42001E, 24fps, mp4a.40.2 (44100Hz), 39.19MiB
22           mp4        1280x720   720p  935k , avc1.64001F, 24fps, mp4a.40.2 (44100Hz) (best)
```
under _format code_ find the option that best suits you and specify that in your download command with the **-f** option
## Get Links To The Latest Videos
This command checks for the 5 newest videos of a given youtube channel and gives you output formatted like this ```DATE, TITLE, URL``` so that you can download whichever looks interesting to you.

```
youtube-dl --get-filename --playlist-items 1-5 -o "%(upload_date)s, %(title)s, https://www.youtube.com/watch?v=%(id)s" "http://youtube.com/c/CHANNEL_NAME/videos"
```
You might want to just keep a list of the latest videos from your favorite channels in a folder structure with some files and then create further commands to parse this list.

Maybe have a cron job that runs a couple times a day or use this to update your video listings.

The output looks like this...

```output
$ youtube-dl --get-filename --playlist-items 1-5 -o "%(upload_date)s, %(title)s, https://www.youtube.com/watch?v=%(id)s" "http://youtube.com/c/MentalOutlaw/videos"
20210925, Simple Mobile Tools That Don't Spy On You, https://www.youtube.com/watch?v=SO3porIQ8_8
20210924, Whatsapp Message Spying & Indians Clog the Botnet With Good Morning Texts, https://www.youtube.com/watch?v=xvZ8sPmG5I8
20210923, Google Chrome's Latest Feature Makes It The Spookiest Web Browser., https://www.youtube.com/watch?v=nk-Ghf1L6R4
20210922, Flexin Musl on Void Linux, https://www.youtube.com/watch?v=rRFIlBIYCBY
20210920, ExpressVPN Gets Even Spookier, https://www.youtube.com/watch?v=hfjCB_oPIuo
```
# Metadata
**TAGS:** #Linux #Reference #UsefulCommands #Recipies #WebScraping