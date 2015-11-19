---
layout: post
title: "Open Source Video Player Investigation"
description: ""
category: 
tags: []
---
{% include JB/setup %}

# 播放器控件框架选择标准

## 目标

- 降低播放器开发成本，实现播放器功能 < 100行 代码
- 提供默认播放控件，用户播放界面可定制，而且定制开发量小
- 架构清晰，模块化比较好，可分离，可提取。

## 指标

- 上层App接口简单。
- 界面定制接口简单，Controller 和 View 逻辑分离。
- 轻量级、可扩展。
- 控件组件逻辑模块独立，可读性好。
- 底层播放引擎使用MPMoviePlayerController类似API，移植成本低

| 项目 | App 接口简单性 | 界面可定制 | 控件组件模块独立 | 底层播放引擎 |
| :-- | :--: | :--: | :--: | :-- |
| ALMoviePlayerController | ✔️✔️✔️ | ✔️ | ✔️✔️✔️✔️✔️ | MPMoviePlayerController |
| DZVideoPlayerViewController | ✔️✔️✔️✔️ | ✔️✔️✔️✔️ | ✔️✔️✔️✔️✔️ | AVPlayer |
| HKVideoPlayer | ✔️✔️✔️ | ✔️ | ✔️✔️✔️✔️✔️ | AVPlayer |
| VideoPlayerKit | ✔️✔️ | ✔️ | ✔️✔️✔️✔️✔️ | AVPlayer  |
| LMMediaPlayer | ✔️ | ✔️✔️✔️ | ✔️✔️✔️✔️✔️ | AVPlayer |
| JRVideoPlayer | ✔️✔️✔️ | ✔️✔️✔️ | ✔️✔️✔️✔️✔️ | AVPlayer |

# A类 播放器控件实现，并且可定制

## ALMoviePlayerController
ALMoviePlayerController 扩展了 MPMoviePlayerController，初始化的时候将controlStyle 设置为 MPMovieControlStyleNone。

并且自定义控件 ALMoviePlayerControls:UIView，覆盖MPMoviePlayerController原有的控件系统。

Features

- Drop-in replacement for MPMoviePlayerController
- Many different customization options, or you can go with the stock Apple look
- Portrait and landscape support
- Universal (iPhone and iPad) support
- iOS 5.0 - iOS 7 supportA
- Lightweight, stable component with small memory footprint
- Support AirPlay

<img src="ALMoviePlayerController.png" width="400" height="250">
**结论：** 代码结构简洁，底层基于MPMoviePlayerController，接口和CyberPlayerController相似，易于移植。
缺点是界面不是通过nib实现，控件之间的层次结构不直观，修改GUI结构需要大量修改代码。

## DZVideoPlayerViewController
使用AVPlayer播放引擎，自定义控件系统

OS Video Player control that features

play online or offline videos
playback controls: play, pause, toggle fullscreen, current time and left time labels
progress indicator that shows downloaded amount and allows the user to rewind
remote command center support
remote control (headset) support

background playback
now playing info updates
easy customization with Interface Builder
has several styles to choose from
default style is similiar to MPMoviePlayerViewController

<img src="DZVideoPlayerViewController.png" width="250" height="400">

**结论：** 代码结构清晰，播放可通过界面通过nib设置，可定制化程度高；底层基于AVPlayer，移植工作量比较大。

## HKVideoPlayer / iOS-Video-Player

播放引擎为AVPlayer，上层定制控件和界面
iOS Ultimate video player with customizable theme view, compatible to ipad as well as iphone with very cool UI themes

**Features Summary**

1) Strong Player ViewController available
  + Easy to use and integrate with delegate [√]
  + Support ipad and iphone as well [√]
  + Support orientation roration [√]
  + Clearly core event and delegate flow. [√]
  
2) Customize theme view easily
  + Able to create new theme easily without modifing core player & view controller. [√]
  + Able to specify theme requirements to override overall view for final united view. [√]
  + Rich themes available (default theme, green theme, classical theme,etc..) [On-going]
  
3) Gesture supporting
  + Double tap to zoom player layer [√]
  + Pan to drag [√]
  + Tap to show up theme [√]
  + Pinch to resize [TODO]

4) Core playing functionalities
  + Play, pause, stop, rewind, forward [√]
  + Seek playback, progressbar updating [√]
  + Clearly core event and delegate flow. [√]
  + Support HLS protocol [√]
  + Support rtmp, rtsp protocol [TODO]
  + Volume, mute, speaker balance [TODO]
  + Dodby sound [TODO]
  
5)Integrate player to Control Center
  + Display & sync player title & info [√]
  + Play, pause, rewind, forward [√]
  + Display & sync playback progress [√]
  + Seek playback [TODO]

**结论：** 
支持nib和theme，但是界面逻辑不灵活，定制化需要修改很多代码；
底层基于AVPlayer，移植工作量比较大。

## VideoPlayerKit

播放器控件的实现。包括 progress bar, and airplay are already set up to make everyone life easier.

[![](http://i.imgur.com/v4WswEi.png)](http://i.imgur.com/v4WswEi.png) 

**结论：** 
代码层次结构和ALMoviePlayerController类似，不支持nib；
底层基于AVPlayer，移植工作量比较大。

## LMMediaPlayer

LMMediaPlayer is a video and an audio player for iPhone with replaceable user interface.

播放引擎为 AVPlayer，上层接口不友好，暴露了底层API。

<img src="https://raw.github.com/0x0c/LMMediaPlayer/master/images/3.png" width="400" height="250">

**结论** 不采用。

## JRVideoPlayer

**JRVideoPlayer**, 是一个基于AVFoundation框架的视频播放器，支持常见视频格式和HTTP Live Streaming视频流。播放器是根据苹果官方文档《AVFoundation Programming Guide》写的。

代码控制了默认只允许横屏播放。

上下滑动屏幕左1/3是调亮度，下下滑动屏幕右1/3是调音量，但是只有在真机下才有效果。左右滑动中间1/3是快进快退。

播放引擎为AVPlayer，自定义界面，控件不独立

<img src= "JRVideoPlayer.png" width="400" height="250">

---------------------------------------------
# B类 轻量级播放器App 
设计目标不是播放控制界面，代码灵活性差。代码的架构不符合要求，这一类项目都不考虑。

## LGPlayer
Single View iPad 应用，播放引擎为AVPlayer, 自定义了播放控件，如图如下。

<img src="LGPlayer.png" width="360" height="250">


##MoviePlayerViewController

iOS 下的自定义视频播放器，播放引擎为AVPlayer.
界面是硬编码实现，灵活性差

* 支持手势快进/快退
* 支持手势调节亮度/音量
* 记住播放进度

## GUIPlayerView

底层使用AVPlayer，控件和播放引擎没有分离。

<img src="GUIPlayerView.png" alt="Landscape screenshot" style="width:400px">

---------------------------------------------

# C类 重量级App，Engine

这一类工程功能复杂，代码量大，控件系统不独立，分离移植成本高，暂不考虑。

## Eleven Player

底层使用ffmpeg，自定义控件系统，播放列表。

<img src="https://raw.githubusercontent.com/coderyi/Eleven/master/Documents/images/e-6p-3.png" width="320" height="570">

<img src="https://raw.githubusercontent.com/coderyi/Eleven/master/Documents/images/e-6p-4.png" width="320" height="570">

## kxmovie / FFmpegPlayer-iOS
A movie player for iOS based on FFmpeg。

At least iOS 7.0+。

<img src="https://raw.github.com/atelierdumobile/FFmpegPlayer-iOS/master/readme-media/screenshot-movie.png" alt="Movie View" width="50%">

**特点** 用OC重写了ffplay；而且没有使用SDL，而是用OpenGL/AudioToolbox 做音频视频渲染。学习iOS底层音视频处理的好材料。可惜没有使用VideoToolBox做硬解码。

## VKVideoPlayer

基于为AVPlayer播放内核的App，播放界面丰富，可定制化程度低。
控件部分代码重用性比较差。

<img src="http://engineering.viki.com/images/blog/video_player_running_man.jpg" width="360" height="250">

## Bilibili/ijkplayer



