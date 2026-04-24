---
tags: [system-design, case-study, youtube, video-streaming]
links: [[CDN]], [[Storage Systems]], [[Message Queues]], [[Databases]]
---

# 📺 Design YouTube / Video Streaming

> [!abstract] Summary
> Design a video platform supporting upload, processing, storage, and streaming of videos at massive scale.

---

## 📋 Requirements

### Functional Requirements
- ✅ Upload videos
- ✅ Watch / stream videos
- ✅ Search videos
- ✅ Like, comment, subscribe
- ✅ Video recommendations
- ✅ View counts, analytics

### Non-Functional Requirements
- 2B DAU, 500 hours of video uploaded per minute
- Videos should load within 2 seconds
- Smooth playback with adaptive bitrate
- High availability (99.99%)
- Support multiple resolutions (360p → 4K)

---

## 📐 Capacity Estimation

```
Uploads:   500 hr/min = 30,000 sec of video/min
           At 10 MB/sec raw video = 300 GB raw upload/min = ~18 TB/hour

After transcoding to 5 resolutions (~0.3x compression):
Storage needed: ~5 TB/hour → 120 TB/day

Views: 2B users × 5 videos/day = 10B views/day = ~115,000 views/sec
Read:write ratio = 10B:500 = ~200:1 (read-heavy!)
```

---

## 🏗️ High-Level Architecture

```
               ┌──────────┐
  Upload ──►   │  Upload  │──► S3 (raw video)
               │ Service  │──► Kafka (upload event)
               └──────────┘         │
                                     ▼
                              [Transcoding Workers]
                              (FFmpeg, distributed)
                                     │
                               S3 (processed)
                                     │
                                  [CDN]
                                     │
  Watch ◄───────────────────────────┘
```

---

## 📤 Video Upload Flow

```
1. Client requests upload URL (pre-signed S3 URL)
2. Client uploads raw video directly to S3 (bypass app server)
3. S3 triggers event → Kafka "video.uploaded" topic
4. Transcoding workers pick up job from Kafka queue
5. Workers transcode to multiple formats/resolutions
6. Transcoded videos stored in S3
7. Metadata updated in DB (video "published")
8. CDN cache warmed for popular videos
```

> [!tip] Pre-signed URLs
> Generate a temporary S3 upload URL with limited permissions. Client uploads directly to S3 — your app server never handles the large file!

---

## 🎬 Video Transcoding

### Why Transcode?
- Raw video is huge and in proprietary formats
- Different devices need different formats (mp4, WebM, HLS)
- Different network speeds need different bitrates

### Output Formats
| Resolution | Bitrate | Use Case |
|-----------|---------|----------|
| 360p | ~0.5 Mbps | Mobile, slow connection |
| 480p | ~1 Mbps | Standard quality |
| 720p | ~3 Mbps | HD |
| 1080p | ~8 Mbps | Full HD |
| 4K | ~25 Mbps | Premium |

### Transcoding Architecture
```
[Kafka: video.uploaded]
        │
        ▼
[Job Scheduler]
   ├──► [Worker 1: 360p job]
   ├──► [Worker 2: 720p job]
   └──► [Worker 3: 1080p job]
        (parallel processing)
```

- Each worker picks a resolution job from the queue
- Workers are stateless → horizontal scaling
- Use spot/preemptible instances (cheap, OK if interrupted)
- Store intermediate segments in S3

---

## 📡 Video Streaming

### HLS (HTTP Live Streaming) ⭐
```
Video split into small segments (2-10 sec each)
Manifest file (.m3u8) lists all segments + quality levels

Player:
1. Fetch manifest → see available qualities
2. Start at appropriate quality
3. Monitor bandwidth → switch quality up/down (Adaptive Bitrate)
```

### Adaptive Bitrate Streaming (ABR)
```
Good connection:  [360p] → [720p] → [1080p] → [4K]  (ramp up)
Poor connection:  [1080p] → [720p] → [360p]           (ramp down)
Buffer runs low:  always drop to lowest → never pause
```

---

## 💾 Storage Architecture

| Data | Storage | Size |
|------|---------|------|
| Raw video | S3 (cold tier) | Huge, rarely accessed |
| Transcoded video | S3 (standard) | Hot, served via CDN |
| Thumbnails | S3 + CDN | Small, frequently accessed |
| Video metadata | MySQL/PostgreSQL | Structured |
| Comments | Cassandra | High write throughput |
| View counts | Redis (counter) + async flush to DB | Real-time counter |
| Search index | Elasticsearch | Full-text search |

---

## 🔍 Search & Recommendations

### Search
- Index: title, description, tags, transcript (auto-generated) → **Elasticsearch**
- Ranking: relevance + view count + recency + user history

### Recommendations
- **Collaborative filtering**: Users who watched X also watched Y
- **Content-based**: Similar videos by tags, description
- Served from **pre-computed recommendations** stored in Redis
- Updated periodically by **ML pipeline** (Spark, TensorFlow)

---

## 📊 View Count (Counter Design)
```
Video watched:
1. Increment Redis counter: INCR video:123:views
2. Every N seconds / N views → flush to DB asynchronously
   (avoids DB bottleneck on viral videos)
```

---

## 🔑 Key Design Decisions

| Decision | Choice | Reason |
|----------|--------|--------|
| Upload | Pre-signed S3 URL | Skip app server for large files |
| Transcoding | Queue + workers | Async, scalable, cheap spot instances |
| Streaming | HLS + ABR | Adaptive quality, standard protocol |
| Distribution | CDN | Low latency globally |
| View counts | Redis → async DB | Handle viral video traffic spikes |

---

## 🔗 Related Notes
- [[CDN]] — Video chunks served from CDN
- [[Storage Systems]] — S3 for video storage
- [[Message Queues]] — Kafka for transcoding job queue
- [[Caching]] — Redis for view counters, recommendations
- [[Scalability]] — Stateless transcoding workers

