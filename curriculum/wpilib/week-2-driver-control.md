# Week 2: Driver Control

### 2.1 NetworkTables

NetworkTables lets you post information up in the code and other parts can access it. This enables us to display values to the driver, and make dynamic (changing often) values accessible to every subsystem.

- [WPILib NetworkTables](https://docs.wpilib.org/en/stable/docs/software/networktables/networktables-intro.html)

### 2.2 Command Triggers

WPILib provides handy classes for different controllers which we use that have 'hooks' which you can attach commands to to make them run when a button in pressed, released, held down, etc. We have mostly used an XBox controller in the past (might change soon), which provides these!

- [WPILib Command Triggers](https://docs.wpilib.org/en/stable/docs/software/commandbased/binding-commands-to-triggers.html)

### 2.3 Good Control Systems

Responsive, intuitive controls are important for the driver's performance during competition. Making control systems feel good to use is generally pretty simple. Controller sticks output a value from 0-1 in X and Y. This value linearly goes up/down with the stick position. However, this makes the center too sensitive and the edges not sensitive enough. Video games often use complex curves, but a simple cubic equation is plenty (We just raise the input value to the third power).
