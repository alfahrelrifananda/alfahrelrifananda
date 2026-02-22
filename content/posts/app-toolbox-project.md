---
title: "Toolbox"
date: 2026-02-08T11:30:00+07:00
description: "I made Toolbox for my own use as an all-in-one app for random stuff. Password generation, fake data, quick utilities—everything runs locally on your device."
tags: [Flutter, Project]
---

I made Toolbox for my own use as an all-in-one app for random stuff. Password generation, fake data, quick utilities—everything runs locally on your device.

## What It Does

Toolbox combines several utility tools into one app. It generates secure passwords, creates random identities and fake data for testing, provides quick games like dice rolling and coin flipping, and includes utilities like EXIF metadata removal and unit conversion.

Everything runs offline. No sign-ups, no data collection, no internet connection required. Your data never leaves your device.

{{< figure src="https://raw.githubusercontent.com/alfahrelrifananda/toolbox/main/screenshot/screenshot1.png" >}}
{{< figure src="https://raw.githubusercontent.com/alfahrelrifananda/toolbox/main/screenshot/screenshot2.png" >}}

## Why I Built It

I got tired of installing separate apps for password generation, random number generation, unit conversion, and other small tasks. Each app wanted permissions, some showed ads, others required accounts. I just wanted simple tools that work offline without the overhead.

So I built Toolbox. One app with all the random utilities I actually use, designed to work completely offline with minimal permissions.

## The Features

The generators section creates passwords with customizable length and character sets, random email addresses for testing, unique usernames, fake phone numbers, device names for privacy, complete fake identities for filling test forms, and Lorem Ipsum placeholder text.

The games section includes random number generation in any range, virtual dice rolling, coin flipping, and a spinning wheel to help you decide between options.

The utilities section has an EXIF eraser to remove metadata from photos before sharing them, quick tiles for Android to add shortcuts for screenshots and screen controls, a unit converter for different measurement systems, and device info display showing your hardware and battery details.

## Cross-Platform Support

Toolbox runs on Android, iOS, Windows, Linux, and macOS. I built it with Flutter, so the same codebase works everywhere. This means you can use the same tools on your phone, tablet, and desktop without switching apps or syncing data.

The app keeps a small file size and requests only essential permissions. On Android, it needs storage access for the EXIF eraser and system settings access for quick tiles. That's it. No network permissions, no location access, no contact scanning.

## Privacy First

All operations happen locally. When you generate a password or create fake data, nothing gets sent to any server. The app doesn't connect to the internet at all. It's completely offline-capable by design.

There's no analytics, no crash reporting, no telemetry. The code is open source under GPL v3.0, so you can verify this yourself. What you see in the source code is exactly what the app does.

## Built With Flutter

I chose Flutter because it compiles to native code for each platform while sharing the same codebase. The app uses Dart as the language and Material Design 3 for the interface. The architecture focuses on privacy and offline functionality.

For developers who want to build from source, you need the Flutter SDK. Clone the repository, run flutter pub get to install dependencies, then flutter run to launch. To build for specific platforms, use flutter run -d windows, flutter run -d macos, or flutter run -d linux.

## What It Doesn't Include

Toolbox doesn't sync data between devices. It doesn't back up to the cloud. It doesn't require an account or connect to servers. If you want those features, there are other apps that provide them.

This app is intentionally simple and offline. It does one thing well—provides local utilities without connectivity or complexity.

## Contributing

The project welcomes contributions. If you want to add features, report bugs, or help with translations, the repository is open for issues and pull requests. Fork the project, create a feature branch, make your changes, and submit a pull request.

## Why It Might Be Useful

I don't know if Toolbox will be useful for you. I made it for my own needs—quick utilities that work offline without tracking. If you need similar tools and value privacy, it might work for you too.

Download it, try it, and see if it fits your workflow. If it doesn't, no problem. If it does, feel free to use it.

[Download from GitHub](https://github.com/alfahrelrifananda/toolbox/releases)
