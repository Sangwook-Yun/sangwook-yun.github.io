---
layout: post
title: “From 1 Gbps to 300 Mbps- Lessons on Speed, Streaming, and Storage"
date: 2026-01-24 09:00:00 +0000
categories: [Software Engineer Life]
tags: [Backend, Authorization, Authentication, JWT]
---

I recently changed my internet provider and reduced my speed from 1 Gbps to 300 Mbps. I was worried since my house has three floors and I wasn't sure if the signal would cover the entire house. Fortunately, it’s working fine, and this also gave me a chance to better understand how internet speed works. It’s also a good exercise for system design practice.

## Bit vs Byte

- **Bit (b):** 0 or 1, the smallest unit of digital data
- **Byte (B):** 1 Byte = 8 bits
- Internet speed is usually measured in **Mbps (Megabits per second)**
- File storage or file size is measured in **MB, GB, etc. (Byte-based)**

---

## Units of Data Size

- **International standard (SI):**
  1 KB = 1,000 Bytes, 1 MB = 1,000 KB, 1 GB = 1,000 MB, 1 TB = 1,000 GB
- Computers often use powers of 2 (1 KB = 1,024 Bytes), but for **network calculations, using 1,000-based units is easier**

| Unit | Bytes    | Bits                |
| ---- | -------- | ------------------- |
| 1 KB | 1,000 B  | 8,000 b             |
| 1 MB | 1,000 KB | 8,000,000 b         |
| 1 GB | 1,000 MB | 8,000,000,000 b     |
| 1 TB | 1,000 GB | 8,000,000,000,000 b |

---

## Average File Size by Category

- **Text**
  - Plain text: 1 KB ~ 100 KB
  - JSON: 1 KB ~ a few hundred KB (depends on complexity)
- **Documents**
  - PDF (single page): 50 KB ~ 500 KB (larger if containing images)
  - Word document: 20 KB ~ 500 KB
- **Images**
  - JPG (web, 1080p): 500 KB ~ 2 MB
  - PNG (with transparency, 1080p): 1 MB ~ 5 MB
  - RAW photos: 10 MB ~ 50 MB
- **Music**
  - MP3 128 kbps, 3 min: ~3 MB
  - MP3 320 kbps, 3 min: ~9 MB
- **Video**
  - YouTube 480p, 5 min: 20 ~ 30 MB
  - 720p, 5 min: 50 ~ 100 MB
  - 1080p, 5 min: 100 ~ 200 MB
  - 4K, 5 min: 400 MB ~ 1 GB+
  - Frame rate and codec (H.264 vs H.265) affect size

> In practice, **for DB storage calculations, it’s safest to plan based on the maximum expected file size**.

---

## DB Storage Recommendations (Practical)

| File Type                | Average / Recommended Maximum DB Storage | Notes                                        |
| ------------------------ | ---------------------------------------- | -------------------------------------------- |
| Text (plain)             | 10 KB                                    | Mostly short strings; simple JSON included   |
| JSON                     | 50 KB                                    | Medium complexity structures                 |
| Documents (PDF, Word)    | 500 KB                                   | Without images                               |
| Images (JPG 1080p)       | 2 MB                                     | Web standard; up to 5 MB if image-heavy      |
| Music (MP3 128~320 kbps) | 5 MB                                     | 3-4 min songs; high quality can reach 10 MB  |
| Video (1080p, 5~10 min)  | 200 MB                                   | Compressed for streaming; 4K can exceed 1 GB |

> Tip: It’s usually better to **store large media files in external storage** (like S3 or Blob storage) and save only URLs in the database.

---

## Internet Speed Calculations

- Internet speed is shown in **Mbps**, but actual download speed is in **MB/s**:
  - 300 Mbps → 300 ÷ 8 ≈ 37.5 MB/s

### Download Time Example

- 1080p video, 150 MB → 150 ÷ 37.5 ≈ 4 seconds

### Streaming Considerations

- Streaming does not require downloading the entire file at once; data is transmitted at a **constant bitrate**
- YouTube bitrate examples (approximate):

| Resolution | Average Bitrate | Data per Second |
| ---------- | --------------- | --------------- |
| 480p       | 500 kbps        | 62.5 KB/s       |
| 720p       | 2 Mbps          | 250 KB/s        |
| 1080p      | 5 Mbps          | 625 KB/s        |
| 4K         | 15~20 Mbps      | 1.9~2.5 MB/s    |

- **Conclusion:** With 300 Mbps, streaming multiple videos at 480p, 1080p, or even 4K is easily supported.
- **Note:** Streaming only requires the network to match the bitrate, not the full download speed.

