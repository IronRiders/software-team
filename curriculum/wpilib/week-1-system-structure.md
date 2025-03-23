# Week 1: System Structure

### 1.1 What is WPILib?

It would be unreasonably difficult, not to mention time-consuming, to make everything for the robot from scratch. So, WPILib provides a standardized way to interact with the robot with (fairly simple) Java.

- [WPILib Description](https://docs.wpilib.org/en/stable/docs/software/what-is-wpilib.html)

### 1.2 Subsystems

Generally, it's best to split the code into modular parts which are kept separate. Not doing so can result in 'spaghetti code' as demonstrated [here](https://www.youtube.com/watch?v=k238XpMMn38).

- [WPILib Subsystems](https://docs.wpilib.org/en/stable/docs/software/commandbased/subsystems.html)

### 1.3 Commands

Commands are a little like methods, with a big difference: commands can run off triggers, instead of constantly checking something. (It's fine if you don't understand what that means yet). The point is, it makes things way more efficient and easy.

- [WPILib Command-Based Programming](https://docs.wpilib.org/en/stable/docs/software/commandbased/what-is-command-based.html)
- [WPILib Commands](https://docs.wpilib.org/en/stable/docs/software/commandbased/commands.html)
