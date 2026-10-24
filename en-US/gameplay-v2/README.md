---
name: Gameplay V2
---

# Gameplay V2

Design Document.

## UI

### Skinning

Gameplay V2 aims to be much more flexible than V1 in UI. The UI arrangement will be specified in a `skin.yml` file. This can come from the following sources (the foremost taking highest priority):

* UI provided in the mod chart
* Depending on the type:
  * Tournament: tournament skin
  * Normal gameplay: player-configured custom skin


When playing a mod chart, the map-provided skin is required to be used, if one exists, unless a mod disables the scripting in its entirety. A mod chart provided skin *never* overrides anything outside of Gameplay skin options, e.g. Selection screen.

Options:

```yaml

FormatVersion: {Version}
Screens:
  Gameplay:
    OverrideOptions:
      - Type: IgnorePlayer (overwrite the entire config with the config in this file) | Normal (simply override configs)
    Layers:
      // Top and Bottom are reserved names
      // Lists from top to bottom
      - HUD
      - Playfield
      - Background
    Layout:
      - !Image
        Name: stage // optional name
        Layer: Playfield // required for top-level elements
        X: 123px | 12vw | 12vh | 12%
        Y: ditto
        Width: ditto
        Height: ditto
        Anchor: (0, 0) to (1, 1) | TopLeft, Center, etc. // anchors X and Y
        Origin: (0, 0) to (1, 1) | TopLeft, Center, etc. // determines center
        Rotation: 90deg
        ImagePath: image.png // exclusive to Image
        Children:
          - ...
      ...
      

```

### Layout



#### Layer

The UI is split into layers. The layers of the UI elements indicate their draw order. UI elements are drawn from bottom layer to top. If there are multiple UI elements attached to a single layer as root, the relative draw order amongst them is not deterministic.



### Size



## Deterministic Behavior

It would be our interest in reducing randomised behaviors. This means:

* Components should have a deterministic order of updates
* Time is not a source for RNG. Modcharts are discouraged to induce nondeterministic behavior based on time information, such as conditioning on whether the current game time has the fractional part $<0.5$.
* External parameters, such as dimensions of the window and scaling, should only affect the UI, and preferably not any internal state of the gameplay. For example, a mod chart should not determine the outcome of a paper-scissor-rock based on the UI dimension - they should be coming from RNG or player input.
* RNG should be provided by a single source. The seed should always come from a number passed into the screen from the outside. 

We will assume this throughout the document. Any violation of the above rules is not advised and will not be supported.
