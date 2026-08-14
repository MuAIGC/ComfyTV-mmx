# 3D Director

> A previz blocking stage: place low-poly actors on a ground plane, run them along paths, cut between camera shots, then capture stills or record the whole sequence as camera/blocking reference for video models.

## What this node does

**3D Director** is a previsualization console. You populate a simple 3D stage with **actors** — characters, horses, cars, dogs, plus set dressing like trees, houses, rocks, walls, pillars and props — give the moving ones **paths** to run along, and define **shots**: camera moves with their own tracks, each holding for its duration before cutting to the next. Press play to preview the whole sequence, then **capture** a frame per shot or **record** the full timeline to a video.

The point of the output is *reference, not render*: the recorded low-poly video pins down camera trajectory, timing and character blocking for a reference-to-video model (feed it as `@video_0` alongside styled character images), and captured frames pin per-shot composition for image models.

## When to use it

- Multi-shot scenes where camera moves and actor blocking must stay consistent across takes
- Producing a motion reference for reference-to-video workflows (the model copies your camera, you keep full control)
- Quick spatial thinking: staging chases, dialogues, reveals before spending GPU time

## How ComfyTV designed this

- **Actors on tracks**: each mobile actor follows a spline path across the whole timeline with an editable speed curve; set dressing stays put. Mounts let a character ride a horse or car.
- **Shots cut, actors persist**: shots are camera setups with their own path and duration; the world keeps moving through cuts, like a real multi-camera shoot. A shot can **lock** onto an actor to keep it framed while the camera travels.
- **Sun, ground, aspect**: a sun direction for readable shadows, ground styles, and an aspect preset that letterboxes the viewport to your delivery format.
- **Everything in one widget**: the full scene (actors, paths, shots, sun, ground) serializes into `previz_state`, so it saves and copies with the workflow.
- **Agent access**: the [MCP tools](https://github.com/jtydhr88/ComfyTV/blob/main/docs/mcp.md) `previz_get` / `previz_edit` / `previz_capture` / `previz_record` drive the same stage, including laying exact paths from coordinate lists and warning about overlapping actors.

## Types (COMFYTV_* vs native ComfyUI)

| ComfyTV type | What it is | vs ComfyUI |
|---|---|---|
| `COMFYTV_IMAGE` | Image URL snapshot | Bridge to/from `IMAGE` |
| `COMFYTV_IMAGES` | Image batch snapshot | Bridge to `IMAGE` batch |
| `COMFYTV_VIDEO` | Video URL snapshot | Bridge to/from `VIDEO` |

## Parameters

### width / height
Capture and recording resolution (default 1280×720).

### The editor
Add actors from the toolbar, drag them into place, draw paths for the movers; add shots, shape each camera track, set per-shot duration and an optional actor lock; scrub or play the timeline to check the cut.

## Outputs

| Output | Type | Meaning | Downstream |
|---|---|---|---|
| **image** | `COMFYTV_IMAGE` | Latest captured frame | Image workflows, references |
| **video** | `COMFYTV_VIDEO` | Recorded sequence | Reference-to-video workflows |
| **images** | `COMFYTV_IMAGES` | All captured frames | Shot-by-shot image generation |

## Step by step

1. Add **3D Director**; clear the demo actors if you don't need them.
2. Place your cast and set dressing; draw paths for anything that moves.
3. Add one shot per camera setup; shape its track, set duration, lock it to the subject if needed.
4. Play to verify blocking and cut rhythm.
5. **Record** the sequence → wire **video** into a reference-to-video stage as `@video_0`, together with styled character images (`@image_0`, `@image_1`).
6. In the video prompt, describe which mannequin maps to which character and state the output is photorealistic from the first frame — the previz look must never leak through.

## Full guides (recommended reading)

| Guide | Contents |
| --- | --- |
| [Generate](https://github.com/jtydhr88/ComfyTV/blob/main/docs/generate.md) | Video stages, references, @mentions |
| [Agent access (MCP)](https://github.com/jtydhr88/ComfyTV/blob/main/docs/mcp.md) | Driving the 3D Director from an agent |

## Repository and workflows

| Resource | Link |
| --- | --- |
| **GitHub repository** | https://github.com/jtydhr88/ComfyTV |
| **User guides index** | https://github.com/jtydhr88/ComfyTV/tree/main/docs |
| **Built-in workflows** | https://github.com/jtydhr88/ComfyTV/tree/main/workflows |

## FAQ

**Q: The video model's first second shows the checkerboard ground and grey mannequins.**  
A: Tell the model in the prompt that the reference is low-poly previz for camera and blocking only, and that output must be fully photorealistic from the very first frame — name which mannequin becomes which character.

**Q: What's the difference from Scene 3D?**  
A: Scene 3D is a single-camera look-development stage (lighting, materials, animation clips, capture channels). 3D Director is multi-shot narrative blocking — actors on paths, shots that cut.

**Q: How long can the sequence be?**  
A: The timeline spans your shots' total duration; record produces a video matching it. Keep reference clips near your video model's segment length for best adherence.

## Related nodes

- **Scene 3D** — single-shot look development and capture channels
- **Video Stage** — consumes the recorded reference
- **Director** — assembles the generated shots into a film
