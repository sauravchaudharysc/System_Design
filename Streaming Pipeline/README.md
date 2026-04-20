# Full Streaming Pipeline

Think of streaming as **3 layers working together**:

> **Codec (compression) → Bitrate ladder (quality options) → HLS/DASH (delivery mechanism)**

## 1. Codec = “How video is compressed”

Examples:

- H.264 (most common)
- H.265 (better compression, more CPU)

### What codec actually does:

Raw video is HUGE because:

- Every frame = image
- 30–60 frames per second

Codec reduces size using:

- Removing duplicate data between frames (temporal compression)
- Removing invisible details (perceptual compression)

Output:

- Smaller file
- Still visually acceptable

## 2. Bitrate Ladder = “Same video, multiple qualities”

You don’t just encode once. You encode **multiple versions**:

| Version | Resolution | Bitrate   | Meaning      |
| ------- | ---------- | --------- | ------------ |
| 1080p   | High       | ~5 Mbps   | Best quality |
| 720p    | Medium     | ~2.5 Mbps | Balanced     |
| 480p    | Low        | ~1 Mbps   | Data saving  |

**Bitrate is the REAL driver of quality**, not resolution alone.

- 1080p at low bitrate → looks blurry
- 720p at high bitrate → can look better

So:

> Resolution = frame size
>  Bitrate = amount of detail per second

## 3. Segmentation = “Break video into chunks”

Instead of sending full video:

👉 Split into small pieces (2–10 sec)

Example:

```
720p:
chunk1.ts (0–4s)
chunk2.ts (4–8s)
chunk3.ts (8–12s)
```

------

### Why segmentation?

- Enables **seeking (jump to 1:20 instantly)**
- Enables **adaptive streaming**
- Prevents full re-download on failure

## 4. Manifest = “Index of everything”

This is **“Instruction file for the video player”**

It tells the player:

- What qualities exist
- Where chunks are stored
- In what order to download them ( video ordering )

### HLS (HTTP Live Streaming) - MAC Specific 

There are **2 levels of manifests**:

#### 1. Master Playlist (Top Level)

```
#EXTM3U
#EXT-X-STREAM-INF:BANDWIDTH=5000000,RESOLUTION=1920x1080
1080p.m3u8

#EXT-X-STREAM-INF:BANDWIDTH=2500000,RESOLUTION=1280x720
720p.m3u8

#EXT-X-STREAM-INF:BANDWIDTH=1000000,RESOLUTION=854x480
480p.m3u8
```

This tells:

- Available qualities
- Bitrates
- Links to **variant playlists**

------

#### 2. Variant Playlist (Per Quality)

Example (720p):

```
#EXTM3U
#EXTINF:4.0
seg1.ts
#EXTINF:4.0
seg2.ts
#EXTINF:4.0
seg3.ts
```

This tells:

- Each segment (chunk)
- Duration
- Order

### Dash - **Dynamic Adaptive Streaming over HTTP**

DASH is a protocol that streams video over HTTP while dynamically adjusting quality based on network speed.