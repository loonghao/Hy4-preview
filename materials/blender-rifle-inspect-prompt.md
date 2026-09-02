# Rifle Inspection Case: Public Prompt

> This document is a public edit of an author-provided historical
> case prompt. It is a reference artifact, not an instruction to execute here.

## Goal

Create a procedural rifle in Blender, use Image Gen to produce a PBR-compliant
rifle skin, then finish the material setup, inspection animation, and video
export in Blender.

## Blender steps

- In the Blender scene, work with the `Rifle` object. It has one material slot;
  replace `Skin_Default` with the existing `Skin_Camo` material.
- In the Shading workspace, make the replacement through the material dropdown.
- Do not move or modify the `InspectCam` camera.
- On frame 1, select `Rifle` and insert a `Rotation` keyframe.
- Go to frame 90, set the `Rifle` Z rotation to `360°`, and insert another
  `Rotation` keyframe.
- Set the animation range to Start `1`, End `90`.
- Export a complete animation as MPEG-4/H.264 with Render Animation.

## Context

The case demonstrates a Blender material and inspection workflow. The preview
image is a visual reference; it is not a claim about final asset quality or a
completed art acceptance test.
