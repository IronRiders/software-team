# Software Tools

Our department uses all sorts of helpful tools to make the process of  
coding collaboratively much easier.

- [Essentials](#essentials)
- [Libraries & Apps](#libraries-and-apps)

## Essentials

### GitHub

We use Git and GitHub for managing source code files and other information (like this file!)

_Git_ is an open source "source control" system.  It is a tool that runs on your local computer.  You probably already have git installed.

_GitHub_ is a website that offers a central place for teams to manage projects.

[Learn about Git and GitHub here](https://docs.github.com/en/get-started/start-your-journey/about-github-and-git)

You are ready to use GitHub when:

- You've used Git to create a copy of IronRiders code on your local computer
- You are familiar with the following:
  - _cloning a repository_
  - _committing_, _pushing_ and _creating pull requests_
  - _pulling_ and _rebasing_
  - _reviewing pull requests_

IronRiders code is at https://github.com/IronRiders.  You'll need your own GitHub account and permission to access the IronRiders organization.

### VS Code

VS Code is software for writing software.  Normally you would download VS Code from Microsoft and install yourself.  But don't do that!  FRC provides its own version of VS Code.  Unless you know what you're using, just use that.

So, to get VS Code installed see [WPILib](#wpilib) below.

FRC's [VS Code Overview](https://docs.wpilib.org/en/stable/docs/software/vscode-overview/) is a good place to learn more about VS Code.

### WPILib

_WPILib_ refers to both the software libraries that FRC provides for writing robots and a massive (2GB+) installer that installs everything you need to program a robot.

To install WPILib, follow [FRC's instructions](https://docs.wpilib.org/en/stable/docs/zero-to-robot/step-2/wpilib-setup.html).  Be sure to say "yes" when it asks if you want to install [VS Code](#vs-code).

## Libraries and Apps

### PathPlanner

PathPlanner is used to generate paths and autos which the robot follows during the autonomous period of the game (generally the first 15 seconds), and provides a library for running the generated path/auto files in code. The [PathPlanner website](https://pathplanner.dev/home.html) has intructions on everything you need to know about it.

### YAGSL

YAGSL is another library which does all the calculations for the swerve drive required to get it to work. JSON files in the deploy directory give YAGSL the info it needs, then in code we give a target speed/rotation/etc. and it controls each wheel for us. You probably won't be doing much with YAGSL as we don't need to re-code our swerve drive every year. If you do, it's very well documented in [its website](https://yagsl.gitbook.io/yagsl).

### DriverStation

DriverStation actually has very little to do with the code, but it the tool the driver uses to connect with the robot. It is also where the driver specifices the team, the game, and other important info which the code can access.

