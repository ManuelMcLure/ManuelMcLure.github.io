# Configuring Marlin for Unified Bed Leveling
## (or Limits and Margins and Insets, Oh My!)

Many of the common questions seen on the
[Marlin Discord](https://discord.gg/n5NJ59y) involve setting up the
firmware for for UBL (Unified Bed Leveling). Users find that Marlin
tries to probe outside the bed or doesn't probe the whole bed. This
is usually due to a misunderstanding about how to configure the
leveling process, and the interactions between `X_MIN_POS`, `X_MAX_POS`,
`Y_MIN_POS`, `Y_MAX_POS`, `X_BED_SIZE`, `Y_BED_SIZE`, `MESH_INSET`,
`NOZZLE_TO_PROBE_OFFSET`, and `PROBING_MARGIN`, as well as not
understanding the philosophy behind UBL.

The purpose of this guide is to try to explain how UBL works with
respect to determining the area to probe, and how to set up Marlin for
the best results when using UBL.

Note that this guide only explains how to set up UBL on cartesian
printers (motion systems such as CoreXY are included in the definition of
"cartesian"). It does not include information on setting up UBL for deltas
or SCARA printers.

### The world of UBL - Machine limits and bed position

The most important part of the configuration is ensuring that Marlin has
a proper understanding of where the nozzle can safely move and where the
bed is in relation to those limits.

As an example, let's take a look at my printer. It has a physical bed size
of 220mmx220mm, but the nozzle can safely move to positions outside the
bed on all four sides. When X is homed, the nozzle is 20mm to the left of
the left edge of the bed, when Y is homed the nozzle is 21mm in front of
the front edge of the bed. The nozzle can safely move 15mm past the
right edge of the bed, and 5mm past the back of the bed.

How do we tell Marlin these dimensions? The important concept is that
everything is relative to the front left corner of the bed. That is
coordinate 0,0. We need to define the machine limits with respect to that
position. In the case of my printer, I use the following configuration:

```
#define X_BED_SIZE 220
#define Y_BED_SIZE 220
...
#define X_MIN_POS -20
#define Y_MIN_POS -21
#define X_MAX_POS (X_BED_SIZE + 15)
#define Y_MAX_POS (Y_BED_SIZE + 5)
```
I could also have used
```
#define X_MAX_POS 235
#define Y_MAX_POS 225
```
but decided to take advantage of the preprocessor available when
compiling Marlin to make it easier to see how the limits relate to the
bed size.

This results in the following layout:

![Machine Limits Example](assets/images/MachineLimits1.svg)

The light blue area shows the machine limits, i.e. where the nozzle
can reach. The pink area is the actual bed. The most important
thing to understand is that in this case (and indeed this is the case
for many printers) the minimum X and Y positions are _negative_,
because they are relative to the 0,0 position which is defined as
being the front left corner of the bed.
