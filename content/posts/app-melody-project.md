---
title: "Melody: A Simple Local Music Player"
date: 2026-02-08T10:30:00+07:00
description: "I built Melody because I wanted a simple music player for my local files. No subscriptions, no ads, just my music collection."
tags: [Android, Project]
---

I built Melody because I got tired of music players that try to do everything. I just wanted something that plays my local music files without the bloat.

## What It Does

Melody is a music player for Android that plays music from your device storage. It has standard controls like play, pause, skip, shuffle, and repeat. It keeps playing in the background when you switch apps, with notification controls so you can change tracks without opening the app again.

The interface uses Material 3 design with dynamic color theming, so it matches whatever wallpaper you're using. It works on Android devices running API 24 and above.

{{< figure src="https://raw.githubusercontent.com/alfahrelrifananda/melody/main/screenshot/screenshot1.png" >}}
{{< figure src="https://raw.githubusercontent.com/alfahrelrifananda/melody/main/screenshot/screenshot3.png" >}}

## Why I Built It This Way

I built Melody with Java and native Android components because they're reliable and well-documented. The app uses MediaPlayer and MediaSession for audio playback, which means it integrates properly with Android's media controls.

There are no dependencies on external services. You don't need an account, internet connection, or subscription. Download the app, grant storage permissions, and your music plays.

The code is open source under GPL v3.0, so you can see exactly what the app does. No tracking, no data collection, no surprises.

## What It Doesn't Do

Melody doesn't stream music from the internet. It doesn't have an equalizer, lyrics display, or social features. If you need those, there are other apps that do them well. Melody focuses on local playback and does that one thing reliably.

## Getting It

You can download Melody from the GitHub releases page. If you want to build it yourself, clone the repository, open it in Android Studio, and run it. The project structure follows standard Android conventions.

The app is currently available in English. If you want to contribute translations or report bugs, the repository is open for issues and pull requests.

Melody works for me because it's simple and gets out of the way. If you have local music files and want something straightforward to play them, it might work for you too.

[Download from GitHub](https://github.com/alfahrelrifananda/melody/releases)
