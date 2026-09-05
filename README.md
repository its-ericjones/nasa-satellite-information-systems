# NASA Satellite Information Systems

I'm currently working toward a bachelor's degree in IT Management, and I'm building this project to put what I'm learning into practice and explore the kind of work I want to do. I'm interested in how people, processes, and technology come together to make an information system useful and dependable. I want to be able to step into an unfamiliar system, figure out where information comes from and where it goes, understand why it moves that way, and find practical ways to improve it.

Satellite data gives me a concrete way to practice that. I'm interested in the images themselves, but also in everything that happens between an instrument collecting an observation and someone being able to use it: communications, processing, archives, APIs, storage, and analysis.

Writing code is part of how I'll explore those connections. I also want to practice deciding what a system needs to do, choosing tools that fit, understanding dependencies and risks, and explaining the decisions to someone who needs to use or maintain it.

I'll be working through this as I learn:

> Build → Get stuck → Learn → Improve → Document

This README describes where I'm starting and what I'd like to try. I expect some of the details to change as I get more familiar with the data.

## IT Management in Practice

I'm treating this as a small system that I'll need to plan, operate, and improve. As I build it, I want to work through questions like:

- **Purpose and scope:** Who could use the information, what would they need from it, and what's a reasonable first version?
- **Dependencies and responsibilities:** What do I rely on NASA to provide, what do I manage locally, and how would a change upstream affect my work?
- **Operations and reliability:** How will I know the system is working, notice missing data, and recover when something fails?
- **Data management and security:** How will I keep track of sources, protect any credentials I use, and decide what to store or back up?
- **Resources and tradeoffs:** How much time, storage, and maintenance does an approach need, and is the added complexity worth it?
- **Documentation and communication:** Could someone else understand my choices, run the process, and recognize the limits of the results?

I don't expect to have all of those answers at the start. I'd like to keep a record of the decisions I make and revisit them as I learn what the system needs.

## Why DSCOVR EPIC?

I'm starting with the Deep Space Climate Observatory (DSCOVR) and its Earth Polychromatic Imaging Camera (EPIC). DSCOVR operates near the Sun–Earth L1 point, roughly 1.5 million kilometers from Earth. EPIC observes the sunlit face of Earth, and NASA makes the images and their metadata available through a public API.

From that viewpoint, there's plenty to follow as Earth changes: different regions rotate into view, storms develop and move, and smoke, dust, and seasonal changes appear. The API also provides observation times, captions, viewing coordinates, and information about the positions of the spacecraft, Moon, and Sun.

That gives me a starting point for learning how the observations are organized and how to bring them into a local system I can inspect and work with.

## Starting with One Day

The first step is to build a local image pipeline for one day of natural-color EPIC observations: September 1, 2019. That puts the starting point during Hurricane Dorian, which I'd like to explore in more depth once the pipeline is working.

The question I want to work through is:

> Can I plan and operate a small, reliable system for satellite observations, explain its dependencies, and make its information useful to someone else?

Here's the information flow I'm planning to follow:

```text
DSCOVR spacecraft
        ↓
NASA receiving, processing, and archive systems
        ↓
EPIC metadata API and image archive
        ↓
Local ingestion program
        ↓
Raw responses + SQLite + image files
        ↓
Completeness and status report
```

My code will pick up at NASA's public API and image archive. Learning about the earlier stages is part of understanding the system, even though the API won't show me everything that happens upstream.

### The First Version

I'd like to request the day's metadata, save the original JSON response, organize the relevant fields in SQLite, and download the associated images. Keeping the original response will give me something to refer back to if I need to check my interpretation or process the data differently.

Along the way, I want to track where each image is stored, whether the download worked, its file size and checksum, and when my system first encountered the observation. A small report could help me see what downloaded successfully, what's missing, and how far apart the observations are.

I'll also work through what happens when a response is empty, a record is malformed, or a download fails or gets interrupted. Being able to rerun the process without duplicating observations or damaging existing files is a useful first target.

### Observation Data

These are the fields I'm considering as I get to know the API:

- NASA identifier, image name, and processing version
- Observation timestamp
- Centroid latitude and longitude
- DSCOVR, Moon, and Sun positions, plus attitude metadata
- Original response location and retrieval timestamp
- When the observation was first seen locally
- Image URL and local file path
- Download status, any error message, file size, and checksum

I plan to use NASA's identifier as a unique key to keep repeated runs from creating duplicate observations. I'll keep the observation time separate from the retrieval time so I can tell when something was captured and when my system picked it up.

### First Milestone

I'd call the first version working when I can run one command to retrieve the day's metadata and images, see their status, and run it again without creating duplicates. I'd also like to document one observation's path from NASA's response to its database row and local image file.

For the IT Management side, I'd like to have a simple system map, notes on why I chose the tools, and a short guide to running the process and recovering from common failures. I'll also note the time and storage it takes, any risks or limitations I've found, and what I'd improve next. That should give me a way to judge how manageable the system is alongside whether the code works.

## Next: Hurricane Dorian

Once that works, I'd like to expand to Hurricane Dorian's progression from August 24 through September 7, 2019. The idea is to combine EPIC imagery with NOAA's National Hurricane Center best-track data and build an annotated sequence showing how the storm changed.

EPIC gives a view of the whole sunlit Earth, so I'll use it for the visual account and NOAA's records for the storm's position and intensity. Part of the work will be understanding how the sources line up in time, when the storm is visible, and where gaps or uncertain matches affect what I can say.

For now, I'm starting with one day and following the data through the system.
