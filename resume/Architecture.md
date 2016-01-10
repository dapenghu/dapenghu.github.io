# Player SDK Architecture

It is a wetwork vidio playback SDK.

## Requirement / Key features

Support multiple protocol

### Producer - Consumer 

Key feature:
- Constant speed to consume video and audio stream data
- Sync video/audio/system time
- Reduce time delay as far as possible
- Multiple bitrates handover to adapt network speed.
- GPU Acceleration
- Easy of Use 

## Challendge 

### CPU - Intensive 

### Logic Complexity
- Support multiple protocols: HLS, RTMP, RTSP. 
- Support various video/audio/subtitle format
- Handle video and audio and subtitle stream synchronously.
- Manage Cache.


## Modules

## Accomplishment / Achievement

## Further improvement
- GPU Acceleration
- P2P to reduce bandwith requirement
- H.265 to reduce bitrate

## Vitamio

Vitamio is an open multimedia framework or library for Android and iOS, with full and real hardware accelerated decoder and renderer. It's the simple, clean and powerful API of Vitamio that makes it famous and popular in multimedia apps development for Android and iOS.
According to the developers' feedback, Vitamio has been used by more than 10,000+ apps and 500 million users around the world.

Vitamio can play 720p/1080p HD mp4,mkv,m4v,mov,flv,avi,rmvb,rm,ts,tp and many other video formats in Android and iOS. Almost all popular streaming protocols are supported by Vitamio, including HLS(m3u8), MMS, RTSP, RTMP, and HTTP.

### Network Protocols 
The following streaming protocols are supported for audio and video playback:

- MMS
- RTSP (RTP, SDP), RTMP
- HTTP progressive streaming
- HLS - HTTP live streaming (M3U8)
And yes, Vitamio can handle on demand and live videos in all above protocols.

### Media formats 

Vitamio used FFmpeg as the demuxers and main decoders, many audio and video codecs are packed into Vitamio beside the default media format built in Android platform, some of them are listed below.

- DivX/Xvid
- WMV
- FLV
- TS/TP
- RMVB
- MKV
- MOV
- M4V
- AVI
- MP4
- 3GP

### Subtitles 

Vitamio support the display of many external and embedded subtitle formats.

- SubRip(.srt)
- Sub Station Alpha(.ssa) / Advanced Sub Station Alpha(.ass)
- SAMI(.smi/.sami)
- MicroDVD(.sub/.txt)
- SubViewer2.0(.sub)
- MPL2(.mpl/.txt)
- Matroska (.mkv) Subtitle Track

### More features 

There are more wonderful features available in Vitamio.

- Support wide range screens from small phone to large tablet
- Multiple audio tracks support
- Mutitiple subtitles support, including external and embedded ones
- Processor optimization for many platforms
- Buffering when streaming
- Adjustable aspect ratio
- Automatically text encoding detection


# LSS Architecture
Microsoft Azure Media Services is a scalable platform for building media workflows and distributing content to nearly any screen, on any network.

Based on a solid and proven media platform, you can encode, protect, and stream media to PCs, iOS, Android, Windows Phone, TVs, game consoles, and other devices. With Azure Media Services, you can:

Use Microsoft or third-party components for uploading, encoding, protecting/encrypting, streaming, and distributing
Stream live or on-demand content to a multi-device audience, mobile, connected TV, game console and web-based video applications, and secure video portals for the enterprise
Enable broadcast quality media workflows that are easy to setup and maintain
Pay only for what you use and scale easily to match usage to actual demand.


## Requirement

