# How to raise an event on a faked object

Let's say - for argument's sake - that we have an interface that has an event defined:

```csharp
public interface IRobot
{ 
    event EventHandler FellInLove;
}
```

Now in a test where we have a faked instance of this interface we can raise that event whenever we want, specifying sender and event args. We could also omit the sender and the fake will be passed as sender to the event handler. There's also a convenience method for raising with empty event args.

```csharp
var robot = A.Fake<IRobot>();
            
robot.FellInLove += (s, e) =>
    {
        Console.WriteLine("Yay!");
    };
         
// Raise the event!
robot.FellInLove += Raise.With(EventArgs.Empty).Now;

// Use the overload for empty event args
robot.FellInLove += Raise.WithEmpty().Now;

// Specify sender explicitly:
robot.FellInLove += Raise.With(sender: robot, e: EventArgs.Empty).Now;
```

## VB.Net

```vb.net
AddHandler robot.FellInLove, AddressOf Raise.With(EventArgs.Empty).Now

'If the event is an EventHandler(Of T) you can use the shorter syntax:
AddHandler robot.FellInLove, Raise.With(EventArgs.Empty).Go
```