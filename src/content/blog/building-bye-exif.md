---
title: "Building bye-exif: a simple privacy tool"
description: "How I built a containerized EXIF metadata stripper and what I learned about Docker in the process."
date: "2025-02-15"
tags: ["projects", "docker", "security"]
---

I take a photo on my phone, send it to someone, and that image carries a surprising amount of metadata with it. GPS coordinates, device model, timestamps, sometimes even the software version. Most people don't think about this, but it's a real privacy concern, especially if you're posting images publicly.

I wanted a simple tool that strips all of that out. No complicated UI, no cloud service, just a utility I can run locally. That's `bye-exif`.

## What it does

You give it an image (or a batch of images), it removes all EXIF metadata and spits out clean files. That's it. No GPS data, no camera info, no timestamps. The pixel data stays identical. It only touches the metadata layer.

## Technical decisions

I went with Python for the core logic using the `Pillow` library for image processing. Pillow makes it straightforward to open an image, strip the EXIF data, and save a clean copy.

The more interesting part was the Docker setup. I wanted this to be something anyone could run without worrying about Python versions or dependency conflicts. So I containerized it:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
ENTRYPOINT ["python", "strip.py"]
```

Slim base image to keep it light. Mount your image directory as a volume, run the container, done. No installation, no environment setup on the host machine.

## What I learned

**Docker volumes are essential for this use case.** The images live on the host machine, and the container needs to access them without copying everything in. Bind mounts made this clean. The container reads from and writes to a mounted directory, so the workflow is transparent to the user.

**Metadata is more complex than I expected.** EXIF isn't the only metadata standard. There's also XMP, IPTC, and various maker notes from different camera manufacturers. Pillow handles EXIF well, but for more comprehensive stripping I had to look into `exiftool` as a fallback. The lesson: if you're building a security tool, being thorough matters more than being clever.

**Error handling matters more in CLI tools.** When there's no UI to display errors gracefully, your tool needs to handle bad inputs, unsupported formats, and permission issues with clear, useful messages. I spent more time on error handling than on the actual stripping logic, which felt backwards at first but was the right call.

## What I'd do differently

If I were building this again, I'd probably add a simple web interface: a drag-and-drop page that strips metadata in the browser using JavaScript's Canvas API. That would remove the Docker dependency entirely for casual users while keeping the containerized version for batch processing.

I'd also add support for video files. Video metadata is a whole separate world, but the privacy concerns are the same.

## Takeaway

This project was small in scope but it taught me a lot about Docker workflows, image processing, and thinking about edge cases in security tools. The repo is on [GitHub](https://github.com/ax00z/bye-exif) if you want to check it out or build on it.
