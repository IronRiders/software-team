# How to create an FRC field

We don't have our own practice field.  This is a problem because it's difficult to do things like develop accurate [pose estimation](https://docs.wpilib.org/en/stable/docs/software/advanced-controls/state-space/state-space-pose-estimators.html).

So we need to create a test field in the space available.  How to go about that?

**TL/DR** configure PathPlanner and PhotonVision with files from this directory, then hang up 6 april tags in a 13' x 30' area

In the fast-paced world of FIRST Robotics sometimes documentation gets a little haphazard.  Fortunately, as fast-paced developers we're ready to roll with what's available.

Rather than digging around on Chief Delphi and inside [wpilib](https://github.com/wpilibsuite/allwpilib/tree/main/apriltag/src/main/native), let's go straight to the source.  We'll inspect Reefscape's JSON files and create our own documentation as we go.

## What we need

A full definition of our field requires the following configuration:

- [x] A top-down picture of the field like [this one](https://github.com/wpilibsuite/allwpilib/blob/main/fieldImages/src/main/native/resources/edu/wpi/first/fields/2025-field.png)
- [x] A JSON file associated with the rendering like [this one](https://github.com/wpilibsuite/allwpilib/blob/main/fieldImages/src/main/native/resources/edu/wpi/first/fields/2025-reefscape.json)
- [x] A JSON file that describes [AprilTag](https://docs.wpilib.org/en/stable/docs/software/vision-processing/apriltag/apriltag-intro.html) positions like [this one](https://github.com/wpilibsuite/allwpilib/blob/main/apriltag/src/main/native/resources/edu/wpi/first/apriltag/2025-reefscape.json)

## Field rendering

This is a PNG image.  Simple enough.

### Reefscape

![reefscape field](https://raw.githubusercontent.com/wpilibsuite/allwpilib/refs/heads/main/fieldImages/src/main/native/resources/edu/wpi/first/fields/2025-field.png?raw=true)

### Us

Say we repurpose the hallway outside our build area.  Let's keep it simple with 6 tags, three on each side of the hall.

![iron hallway](./iron-hallway.png)

Not so fancy but easy to build!

## Field definition

This format seems to be documented [here](https://docs.wpilib.org/en/stable/docs/software/pathplanning/pathweaver/adding-field-images.html) but it's also easy to figure out just by looking at a standard file.

We just need:

- game name
- link to the field rendering
- field dimensions
- bounding box for playable area

### Reefscape

See [this JSON file](https://github.com/wpilibsuite/allwpilib/blob/main/fieldImages/src/main/native/resources/edu/wpi/first/fields/2025-reefscape.json) from WPILib git.

### Us

Download: [iron-hallway.field.json](./iron-hallway.field.json)


## AprilTag layout

Our robot can guess where it is but needs to see [AprilTags](https://docs.wpilib.org/en/stable/docs/software/vision-processing/apriltag/apriltag-intro.html) positions like [this one](https://github.com/wpilibsuite/allwpilib/blob/main/apriltag/src/main/native/resources/edu/wpi/first/apriltag/2025-reefscape.json) to be sure.

Here's the AprilTag layout from the [2025 game manual](https://firstfrc.blob.core.windows.net/frc2025/Manual/2025GameManual.pdf).

![image](./2025%20april%20tag%20locations.png)

How do we tell our bot where the tags on our field are?

### Step 1: Decipher Reefscape

Here's [Reefscape's JSON](https://github.com/wpilibsuite/allwpilib/blob/main/apriltag/src/main/native/resources/edu/wpi/first/apriltag/2025-reefscape.json).

This clearly just a list of tags.  But what do the properties of each tag mean?

#### Step 1a: Identify tags to compare

Flags 2 and 12 are in opposite positions.  Lets see what comparing them tells us.

Reefscape defines 12 (lower left) as:

```json
    {
      "ID": 12,
      "pose": {
        "translation": {
          "x": 0.851154,
          "y": 0.65532,
          "z": 1.4859
        },
        "rotation": {
          "quaternion": {
            "W": 0.8910065241883679,
            "X": 0.0,
            "Y": 0.0,
            "Z": 0.45399049973954675
          }
        }
      }
    },
```

And 2 (upper right) is:

```json
    {
      "ID": 2,
      "pose": {
        "translation": {
          "x": 16.697198,
          "y": 7.3964799999999995,
          "z": 1.4859
        },
        "rotation": {
          "quaternion": {
            "W": -0.45399049973954675,
            "X": -0.0,
            "Y": 0.0,
            "Z": 0.8910065241883679
          }
        }
      }
    },
```

OK, looks like each tag has a position and orientation.

#### Step 1b: Identify coordinate system

Lets compare each property of `pose.translation`:

- `x` and `y` are both bigger for ID 2 (upper right) than for ID 12 (lower left).  This means that coordinate (0, 0) must be the lower-left hand of the field
- For tag 12, `x` is greater than `y`.  So `x` must be the horizontal axis and `y` is the vertical axis
- The field definition says the field is 57.573'; the `X` for tag 2 is 16.7.  So looks like the units in the JSON file are meters.
- `z` is positive for all of these tags so larger numbers must move you farther above the floor

So lets define a _field coordinate_ as an `(x, y, z)` value (in meters, I guess) from an origin in the lower-left of the field at floor level.

#### Step 1c: Determine location encoding

Now we know units but we also need to understand how the coordinate in the tag JSON file corresponds physical tags.

The 2025 game manual says that AprilTag markers are ~21cm x ~21cm and tags 2 and 12 are ~135 cm from the floor.  The `Z` for these tags is ~1.49.  1.35 + .21 / 2 = 1.455.  Accounting for rounding errors that's halfway up the tag.

So lets assume the tag's `pose.translation` is a field coordinate in the center of the plane defined by the corresponding AprilTag.

#### Step 1d: Determine orientation encoding

WTF quaternion??

If you've studied 3d modeling you know more than I.  Let's see if I can survive with some quick googling and maintain my blissful ignorance:

- In 3d space it's common to model orientation using a [3x3 matrix](https://en.wikipedia.org/wiki/Rotation_matrix)
- A [quaternion](https://en.wikipedia.org/wiki/Quaternion) is a more efficient way to model the same thing using [imaginary numbers](https://en.wikipedia.org/wiki/Complex_number)
- Let's just ignore that and copy the quarternion for standard tags in the same orientation as our custom tags

### Step 2: Encode tags

Our field rendering doesn't show heights.  Lets use 2', 6' and 4' for tags 1/6, 2/5 and 3/4 respectively.

Now our field is fully specified.  We just need to explain to the computer.

#### Step 2a: Think a bit

For translation we need to figure configure `pose.translation` in meters.  We want maximal accuracy so we can perform these calculations using javascript numbers; they're the same precision JSON.

For `y` we need to account for the thickness of the tag material.  Lets assume tags printed on paper so thickness is effectively 0.

For `z` lets consider "tag height" to be the distance from the floor to the bottom of the visual portion of the tag.  Then it doesn't matter how big the paper we print on is or where the tag is on the paper.  The tag should print as a 6.5" x 6.5" square.

For rotation, note that tags 1-3 face downward along Y.  Let's copy Reefscape tag 3.  Tags 4-6 face up along Y so let's copy Reefscape 16.

#### Step 2b: Delegate further thinking

We're lazy programmers so let's avoid all that math and write some JS to do it for us.  Then we can just pass to our browser's dev tools to get our JSON.

```js
// Borrowed from Reefscape tag 3
const FACING_DOWN = {
    "W": -0.7071067811865475,
    "X": -0.0,
    "Y": 0.0,
    "Z": 0.7071067811865476
}

// Borrowed from Reefscape tag 16
const FACING_UP = {
    "W": 0.7071067811865476,
    "X": 0.0,
    "Y": 0.0,
    "Z": 0.7071067811865476
}

// Borrowed from internet
const FEET_PER_METER = 3.28084;

// 6.5" borrowed from game manual; convert to feet and divide by two to get center point
const TAG_CENTER_OFFSET = 6.5 / 12 / 2;

const tags = [];

let nextTag = 1;

function defineTag(x, y, z, quaternion) {
    x /= FEET_PER_METER;
    y /= FEET_PER_METER;
    z = (z + TAG_CENTER_OFFSET) / FEET_PER_METER;

    tags.push({
        ID: nextTag++,
        pos: {
            translation: { x, y, z },
            rotation: { quaternion }
        }
    })
}

// Define tags as x, y, z (in feet) and orientation
defineTag(5, 13, 2, FACING_DOWN)
defineTag(15, 13, 6, FACING_DOWN)
defineTag(25, 13, 4, FACING_DOWN)
defineTag(25, 0, 4, FACING_UP)
defineTag(15, 0, 6, FACING_UP)
defineTag(5, 0, 2, FACING_UP)

console.log(JSON.stringify({ tags }, undefined, 2));
```

Download: [iron-hallway.tags.json](./iron-hallway.tags.json)
