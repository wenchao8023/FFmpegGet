[转载](https://ffmpeg.org/ffmpeg-filters.html#Description)

### 1. Description

This document describes filters, sources, and sinks provided by the `libavfilter` library.

### 2. Filtering Introduction

Filtering in FFmpeg is enabled through the libavfilter library.

In libavfilter, a filter can have multiple inputs and multiple outputs. To illustrate the sorts of things that are possible, we consider the following filtergraph.