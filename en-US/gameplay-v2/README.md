---
name: Gameplay V2
---

# Gameplay V2

Design Document.

## UI

### Skinning

Gameplay V2 aims to be much more flexible than V1 in UI. The UI arrangement will be specified in a `skin.yml` file. This can come from the following sources (the foremost taking highest priority):

* UI provided in the mod chart. This is a separate `skin.yml` file under the map's folder.
* Depending on the type: (these lies in the game's `Skins` directories)
  * Tournament: tournament skin
  * Normal gameplay: player-configured custom skin


When playing a mod chart, the map-provided skin is required to be used, if one exists, unless a mod disables the scripting in its entirety. A mod chart provided skin *never* overrides anything outside of Gameplay skin options, e.g. Selection screen.

Options:

```yaml

FormatVersion: 2
Screens:
  Gameplay:
    OverrideOptions:
      - Type: IgnorePlayer (overwrite the entire config with the config in this file) | Merge (simply override configs)
    Layers:
      # Top, Playfield and Bottom are reserved names
      # Lists from top to bottom
      - HUD
      - Playfield
      - Background
    Layout:
      - !Image
        Name: stage # optional name
        Layer: Playfield #required for top-level elements
        X: 123px | 12% (relative to parent size)
        Y: ditto
        Width: ditto
        Height: ditto
        Anchor: (0, 0) to (1, 1) | TopLeft, Center, etc. # anchors X and Y
        Origin: (0, 0) to (1, 1) | TopLeft, Center, etc. # determines center
        Rotation: 90deg
        HUD: True # If 'Hide Gameplay Overlay' is on, should this be hidden
        ImagePath: image.png # exclusive to Image
        Children:
          - ...
      ...
      

```

### Validation

Layout:

* The Tagged `Type` should always refer to an existing and acceptable type.
* Paths to images and animatable images must exist and be relative to the map directory. It is *not* allowed to address any path outside of the map folder. Paths like `../image.png`, `/dev/zero`, etc. is *not* allowed.
* Names must consist of one or more of alphabetic letters (`a-z` and `A-Z`), digits `0-9`, `-`, `$` or `_`.
* Names must be unique. 

### Layout



#### Layer

The UI is split into layers. The layers of the UI elements indicate their draw order. UI elements are drawn from bottom layer to top. If there are multiple UI elements attached to a single layer as root, the order in which the elements are declared in the config will be the order of drawing, i.e. If two top-level elements are declared with the same attached layer, the element declared first will be drawn first.

The layers `Top` and `Bottom` are always declared implicitly. `Top` is placed before the first declared layer and `Bottom` after the last. They can also be referred to in layout declaration.

### Size



## Deterministic Behavior

It would be our interest in reducing randomised behaviors. This means:

* Components should have a deterministic order of updates
* Time is not a source for RNG. Modcharts are discouraged to induce nondeterministic behavior based on time information, such as conditioning on whether the current game time has the fractional part $<0.5$.
* External parameters, such as dimensions of the window and scaling, should only affect the UI, and preferably not any internal state of the gameplay. For example, a mod chart should not determine the outcome of a paper-scissor-rock based on the UI dimension - they should be coming from RNG or player input.
* RNG should be provided by a single source. The seed should always come from a number passed into the screen from the outside. 

We will assume this throughout the document. Any violation of the above rules is not advised and will not be supported.
